# 使用控制反转的三种设计模式

> 原文：<https://www.sitepoint.com/three-design-patterns-that-use-inversion-of-control/>

### 目录 

*   [揭开国际奥委会模式的神秘面纱](#demystifyingtheiocparadigm)
*   [实现控制反转](#implementinginversionofcontrol)
*   [IoC 通过依赖注入](#iocthroughdependencyinjection)
*   [国际奥委会通过观察员模式](#iocthroughtheobserverpattern)
*   [IoC 通过模板方法模式](#iocthroughthetemplatemethodpattern)
*   [总结](#summary)
*   [评论](#comments)

对于许多开发人员来说，[控制反转](https://en.wikipedia.org/wiki/Inversion_of_control) (IoC)是一个模糊的概念，在现实世界中几乎没有应用。在最好的情况下，它被认为只是简单的等同于[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection) (DI)。然而， *IoC = DI* 等式只有在双方都提到反转依赖关系管理的控制时才成立。虽然依赖注入实际上是 IoC 的一种众所周知的形式，但事实是 IoC 是一种更广泛的软件设计范例，可以通过几种[模式](https://en.wikipedia.org/wiki/Design_Patterns)来实现。在本文中，我们将了解依赖注入、观察者模式和模板方法模式是如何实现控制反转的。

与其他许多设计模式一样，实现 IoC 对开发人员来说是一种权衡:

*   高度解耦组件的设计和应用程序逻辑在一个地方的封装是实现 IoC 的直接和自然的结果。
*   另一方面，实现需要构建至少一层间接的[层，在某些用例中，这可能是多余的。](https://en.wikipedia.org/wiki/Indirection)

查看一些具体的实现将有助于您在这些属性之间进行权衡。

## 揭开国际奥委会模式的神秘面纱

控制反转是一个有几个倾斜的模式。IoC 的一个典型例子是 Martin Fowler 在下面这个从控制台收集用户数据的简单程序中给出的[:](https://martinfowler.com/bliki/InversionOfControl.html)

```
public static void main(String[] args) {
    while (true) {
        BufferedReader userInputReader = new BufferedReader(
                new InputStreamReader(System.in));
        System.out.println("Please enter some text: ");
        try {
            System.out.println(userInputReader.readLine());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
} 
```

在这种情况下，*程序的控制流由`main`方法*定义:在一个无限循环中，它读取用户输入并将其打印到控制台。这里，该方法完全控制何时读取用户输入以及何时打印它。

现在，考虑该程序的一个改进版本，它使用一个[图形用户界面](https://en.wikipedia.org/wiki/Graphical_user_interface) (GUI)通过一个文本框、一个按钮和一个绑定到它的动作监听器来收集输入数据。在这种情况下，每次用户单击按钮时，输入的文本都会被监听器收集并打印到面板上。

在这个版本的程序中，它实际上是在事件监听器模型(在这种情况下，就是框架)的控制下调用开发人员编写的代码来读取和打印用户输入。简单来说，框架会调用开发者的代码，而不是相反。该框架实际上是一个可扩展的结构，为开发人员提供了一组注入定制代码片段的特定点。

*从这个意义上来说，控制权已经被有效逆转*。

从更一般的角度来看，每个由框架定义的可调用扩展点，无论是以[接口实现](https://docs.oracle.com/javase/tutorial/java/concepts/interface.html)、[实现继承](https://en.wikipedia.org/wiki/Inheritance_%28object-oriented_programming%29)(又名子类化)的形式，都是 IoC 的良好定义形式。

考虑一个简单的 [Servlet](https://en.wikipedia.org/wiki/Java_servlet) 的情况:

```
public class MyServlet extends HttpServlet {

    protected void doPost(
            HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // developer implementation here
    }

    protected void doGet(
            HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // developer implementation here
    }

} 
```

这里的`HttpServlet`类(属于框架)是对程序有完全控制权的元素，而不是`MyServlet`子类。在被 servlet 容器创建后，`doGet()`和`doPost()`方法中的代码被 servlet 自动调用以响应 [GET](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) 和 [POST](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) HTTP 请求。

*与典型的继承观点相比，子类拥有控制而不是基类，控制被颠倒了*。

事实上，servlet 的方法是[模板方法模式](#iocthroughthetemplatemethodpattern)的实现，我们将在后面深入讨论。

在框架通过提供可扩展的 API 来坚持[开放/封闭原则](https://en.wikipedia.org/wiki/Open/closed_principle)的情况下，使用框架的开发人员的角色归结为定义他们自己的定制类集，或者通过实现框架提供的一个或多个接口，或者通过从现有的基类继承。反过来，类的实例直接由框架实例化和调用。

引用福勒的话:

> 框架调用开发人员，而不是开发人员调用框架。

因此，国际奥委会通常被称为好莱坞原则:

> 别打电话给我们，我们会打给你的。

![Inversion of Control used by Dependency Injection, Observers, and the Template Method Pattern](img/4830e70855816e4438e270beea076ad8.png)

## 实现控制反转

在这一点上，很明显有几种实现控制反转的方法。让我们总结一下如何实现最常见的方法。

### 通过依赖注入的 IoC

如前所述，DI 只是 IoC 的一种形式，很可能是面向对象设计中最普遍使用的一种。但是让我们想一想:DI 实际上是以哪种方式反转控制的呢？

为了回答这个问题，让我们创建一个简单的例子:

```
public interface UserQueue {

    void add(User user);

    void remove(User user);

    User get();

}

public abstract class AbstractUserQueue implements UserQueue {

    protected LinkedList<User> queue = new LinkedList<>();

    @Override
    public void add(User user) {
        queue.addFirst(user);
    }

    @Override
    public void remove(User user) {
        queue.remove(user);
    }

    @Override
    public abstract User get();

}

public class UserFifoQueue extends AbstractUserQueue {

    public User get() {
        return queue.getLast();
    }

}

public class UserLifoQueue extends AbstractUserQueue {

    public User get() {
        return queue.getFirst();
    }

} 
```

`UserQueue`接口定义了一个简单队列的公共 API，该队列存储用户对象(为了简洁起见，这里省略了`User`类的实现)，而`AbstractUserQueue`提供了一些更低层次的共享实现。最后，`UserFifoQueue`和`UserLifoQueue`实现基本的 [FIFO](https://en.wikipedia.org/wiki/FIFO_%28computing_and_electronics%29) 和 [LIFO](https://en.wikipedia.org/wiki/Stack_%28abstract_data_type%29) 队列。

这是实现[子类型多态性](https://en.wikipedia.org/wiki/Polymorphism_%28computer_science)的有效方式。但具体来说，这给我们带来了什么？实际上，差不多。

通过创建一个客户端类，声明对一个`UserQueue`抽象类型(在 DI 术语中也称为服务)的依赖，可以在运行时注入不同的实现，而无需重构使用客户端类的代码:

```
public class UserProcessor {

    private UserQueue userQueue;

    public UserProcessor(UserQueue userQueue) {
        this.userQueue = userQueue;
    }

    public void process() {
        // process queued users here
    }

} 
```

简单地说明了为什么直接投资实际上是国际奥委会的一种形式。

我们可以通过一些硬编码的`new`操作符，在构造函数中直接实例化它们，从而控制如何获取对`UserProcessor`中队列的依赖。但是这是典型的代码味道，它在客户端类和它们的依赖项之间引入了强耦合，并导致了可测试性的毁灭。我听到一些警钟在我耳边响起！不是吗？是的，那将是糟糕的设计。

相反，该类在构造函数中声明了对抽象类型`UserQueue`的依赖，因此*不再受其控制，无法通过构造函数*中的`new`操作符来查找其合作者，相反，依赖是从外部注入的，要么通过使用阿迪框架( [CDI](https://www.sitepoint.com/introduction-contexts-dependency-injection-cdi/) 和 [Google Guice](https://github.com/google/guice) 是*外部注入者*的典型例子，要么只是简单的老派[工厂](https://en.wikipedia.org/wiki/Factory_method_pattern)和[构建者](https://en.wikipedia.org/wiki/Builder_pattern)。

简而言之，有了 DI，对客户端类如何获取依赖关系的控制不再存在于这些类中；它存在于喷射器中，而不是:

```
public static void main(String[] args) {
     UserFifoQueue fifoQueue = new UserFifoQueue();
     fifoQueue.add(new User("user1"));
     fifoQueue.add(new User("user2"));
     fifoQueue.add(new User("user3"));
     UserProcessor userProcessor = new UserProcessor(fifoQueue);
     userProcessor.process();
} 
```

这将按预期工作，注入`UserLifoQueue`实现也非常简单。很明显，DI 只是实现控制反转的一种方式(在这种情况下，DI 是实现 IoC 的一个间接层)。

### 通过观察者模式的国际奥委会

实现 IoC 的另一种直接方式是通过[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)。从更广泛的意义上来说，观察者颠倒控件的方式类似于 GUI 环境中的动作监听器。在动作监听器的情况下，它们被调用来响应特定的用户事件(鼠标点击、几个键盘/窗口事件等等)，而观察器通常用于跟踪[模型-视图上下文](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)中模型对象的状态变化。

在一个经典的实现中，一个或多个观察者被绑定到可观察的对象(在模式的术语中也称为*主体*)，例如通过调用一个`addObserver`方法。一旦主体和观察者之间的绑定被定义，观察者就会被调用以响应主体状态的变化。

为了更好地理解这个概念，考虑下面的例子:

```
@FunctionalInterface
public interface SubjectObserver {

    void update();

} 
```

每当值发生变化时，就会调用这个非常简单的观察器。在现实生活中，它应该提供更丰富的 API，例如通过包含更改的实例或新旧值，但这些并不是观察模式运行所必需的，所以我保持简单。

这里出现了一个可观察的类:

```
public class User {

    private String name;
    private List<SubjectObserver> observers = new ArrayList<>();

    public User(String name) {
        this.name = name;
    }

    public void setName(String name) {
        this.name = name;
        notifyObservers();
    }

    public String getName() {
        return name;
    }

    public void addObserver(SubjectObserver observer) {
        observers.add(observer);
    }

    public void deleteObserver(SubjectObserver observer) {
        observers.remove(observer);
    }

    private void notifyObservers(){
        observers.stream().forEach(observer -> observer.update());
    }

} 
```

`User`类只是一个简单的域类，当它的状态通过 setter 方法改变时，它会通知附加的观察者。

有了`SubjectObserver`接口和主题，下面是如何观察一个实例:

```
public static void main(String[] args) {
    User user = new User("John");
    user.addObserver(() -> System.out.println(
            "Observable subject " + user + " has changed its state."));
    user.setName("Jack");
} 
```

每次通过 setter 修改`user`对象的状态时，观察者都会得到通知，然后它会向控制台输出一条消息。到目前为止，这只是观察者模式的一个相当普通的应用。然而，更重要的是，看看在这种情况下控件是如何反转的。

在观察者模式中，主体变成了“框架”,控制着谁在什么时候被调用。观察者的控制权被拿走了，因为他们对何时被调用没有任何影响(只要他们保持与主题的注册)。这意味着我们实际上可以发现控制被颠倒的那条线——当观察者被束缚于主体时:

```
user.addObserver(() -> System.out.println(
            "Observable subject " + user + " has changed its state.")); 
```

这简单地说明了为什么观察者模式(或者 GUI 驱动环境中的动作监听器)是实现 IoC 的一种相当简单的方式。正是在这种软件组件的分散式设计中，控制发生了逆转。

### IoC 通过模板方法模式

[模板方法模式](https://en.wikipedia.org/wiki/Template_method_pattern)背后的动机是通过几个抽象方法(又名算法步骤)在基类中定义一个通用算法，并让子类提供它们的具体实现，同时保持算法结构不变。

我们可以应用这个概念，定义一个处理领域实体的通用算法:

```
public abstract class EntityProcessor {

    public final void processEntity() {
        getEntityData();
        createEntity();
        validateEntity();
        persistEntity();
    }

    protected abstract void getEntityData();
    protected abstract void createEntity();
    protected abstract void validateEntity();
    protected abstract void persistEntity();

} 
```

`processEntity()`方法是定义处理实体的算法结构的模板方法，抽象方法是算法步骤，必须由子类实现。通过根据需要多次子类化`EntityProcessor`,并提供抽象方法的不同实现，可以创建算法的几个版本。

虽然这显示了模板方法模式背后的动机，但有人可能会想为什么该模式是 IoC 的一种形式。

在继承的典型实现中，子类调用基类中定义的方法。在这种情况下，实际上发生了相反的情况:子类实现的方法(算法步骤)通过模板方法在基类中被调用。因此，*它在控件实际驻留的基类中，而不是在子类*中。

这是 IoC 的另一个经典例子，通过层级结构实现。在这种情况下， *template method* 只是定义可调用扩展点的一个花哨名称，开发人员使用它来提供他们自己的一组实现。

## 摘要

尽管控制反转在 Java 生态系统中很普遍，特别是在许多可用的框架和无处不在的依赖注入中，但对于许多开发人员来说，这种模式仍然很模糊，很大程度上仅限于注入依赖。在本文中，我通过展示在现实世界中实现 IoC 可以遵循的几种方法来阐明这个概念。

*   *依赖注入:*对客户端类如何获取依赖的控制不再驻留在这些类中。而是驻留在底层的注入器/ DI 框架中。

*   *观察者模式:*当对变化做出反应时，控制权从观察者转移到主体。

*   *模板方法模式:*控件驻留在定义模板方法的基类中，而不是在子类中，实现算法的步骤。

像往常一样，如何以及何时使用 IoC 应该根据每个用例进行评估，而不要陷入无意义的盲目崇拜。

## 分享这篇文章