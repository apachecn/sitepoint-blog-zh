# 使用 Java 泛型的自身类型

> 原文：<https://www.sitepoint.com/self-types-with-javas-generics/>

在某些情况下，尤其是在实现构建器模式或创建其他流畅的 API 时，方法会返回`this`。该方法的返回类型很可能与定义该方法的类相同——但有时这并不能解决问题！如果我们想要继承方法，并且它们的返回类型应该是继承类型(而不是声明类型)，那么我们就不走运了。我们需要返回类型类似于“this 的类型”，通常被称为*自身类型*，但是在 Java 中没有这样的东西。

还是有？

## 一些例子

在我们继续之前，让我们看看 self 类型派上用场的一些情况。

### `Object::clone`

一个很好的例子就是隐藏在众目睽睽之下的 [`Object::clone`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#clone--) 。通过一些[黑魔法咒语](https://books.google.de/books?id=ka2VUBqHiWkC&lpg=PA55&ots=yZIkJjn4Q3&dq=effective%20java%20clone&pg=PA54#v=onepage&q&f=false)，它创造出被召唤物体的副本。除了这里我们可以忽略的一个异常,它有如下特征:

```
protected Object clone(); 
```

注意返回类型:`Object`。为什么这么笼统？假设我们有一个`Person`，并且想要暴露`clone`:

```
public class Person {

    // ...

    @Override
    public Person clone() {
        return (Person) super.clone();
    }

} 
```

我们必须重写该方法，使其公开可见。但是我们也要把`super.clone()`的结果，也就是一个`Object`，强制转换成`Person`。暂时把我们从 Java 的类型系统中解放出来，我们可以看到为什么这很奇怪。除了调用方法的同一个类的实例之外，`clone`还能返回什么？

### 建设者

另一个例子出现在使用[构建器模式](http://www.informit.com/articles/article.aspx?p=1216151&seqNum=2)的时候。假设我们的`Person`需要一个`PersonBuilder`:

```
public class PersonBuilder {

    private String name;

    public PersonBuilder withName(String name) {
        this.name = name;
        return this;
    }

    public Person build() {
        return new Person(name);
    }

} 
```

我们现在可以创建一个人，如下所示:

```
Person doe = new PersonBuilder()
    .withName("John Doe")
    .build(); 
```

到目前为止，一切顺利。

现在，假设我们的系统中不仅有人员，还有员工和承包商。当然他们也是人(对吗？)所以`Employee extends Person`和`Contractor extends Person`。因为与`Person`的合作非常顺利，我们决定也为他们创造建造者。

我们的旅程从这里开始。我们如何在`EmployeeBuilder`上设置名称？

我们可以实现一个与`PersonBuilder`同名且代码相同的方法，除了它返回`EmployeeBuilder`而不是`PersonBuilder`之外，我们复制了它。然后我们对`ContractorBuilder`做同样的事情？然后每当我们向`Person`添加一个字段时，我们就向构造器添加了三个字段和三个方法？听起来不太对劲。

让我们尝试一种不同的方法。我们最喜欢的代码重用工具是什么？对，[传承](https://en.wikipedia.org/wiki/Composition_over_inheritance)。(好吧，那是一个糟糕的笑话。但在这种情况下，我会说继承是可以的。)所以我们有`EmployeeBuilder extend PersonBuilder`并且免费得到`withName`。

但是当继承的`withName`确实返回了一个`EmployeeBuilder`时，编译器并不知道——继承的方法的返回类型被声明为`PersonBuilder`。那也不行！假设我们的`EmployeeBuilder`做一些特定于员工的事情(比如`withHiringDate`)，如果我们调用的顺序不对，我们就无法访问这些方法:

```
Employee doe = new EmployeeBuilder()
    // now we have an EmployeeBuilder
    .withName("John Doe")
    // now we have a PersonBuilder
    .withHiringDate(LocalDateTime.now()) // compile error :(
    .build(); 
```

我们可以覆盖`EmployeeBuilder`中的`withName`:

```
public class EmployeeBuilder {

    public EmployeeBuilder withName(String name) {
        return (EmployeeBuilder) super.withName(name);
    }

} 
```

但是这几乎需要和原始实现一样多的代码行。对于每个继承的方法，在`PersonBuilder`的每个子类型中重复这样的片段显然是不理想的。

退一步说，让我们看看我们是如何走到这一步的。问题是`withName`的返回类型被显式固定到声明方法的类:`PersonBuilder`。

```
public class PersonBuilder {

    public PersonBuilder withName(String name) {
        this.name = name;
        return this;
    }

} 
```

因此，如果该方法被继承，例如被`EmployeeBuilder`继承，则返回类型保持不变。但是不应该啊！它应该是调用该方法的类型，而不是声明该方法的类型。

在流畅的 API 中，这个问题很快就会出现，比如 builder 模式，在这种模式中，返回类型对于整个 API 的工作至关重要。

### 递归容器

最后，假设我们想要构建一个图表:

```
public class Node {

    private final List<Node> children;

    public Stream<? extends Node> children() {
        return children.stream();
    }

} 
```

后来，我们意识到我们需要不同种类的节点，而树只能包含一种节点。一旦我们使用继承来建模不同种类的节点，我们就会陷入非常相似的情况:

```
public class SpecialNode extends Node {

    @Override
    public Stream<? extends SpecialNode> children() {
        return super.stream(); // compile error :(
    }

} 
```

A `Stream<? extends Node>`不是`Stream<? extends SpecialNode>`,所以这甚至不能编译。同样，我们有一个问题，我们想说“我们返回了一个调用这个方法的节点流”。

![java-self-types](img/a46c6cc3174f98f0a4a462ee1d8db03d.png)

## 自我拯救

有些语言有自我类型的概念:

**一个*自身类型*指的是一个方法被调用的类型(更正式的称呼是*接收者*)。**

如果在继承的方法中使用了自身类型，那么它代表了声明或继承该方法的每个类中的不同类型——即特定的类*,不管它是声明还是继承了该方法。随便说，它相当于编译时的`this.getClass()`或“这种类型”。*

在这篇文章的剩余部分，我将把它标为`[this]` ( `[self]`也不错，但是用`[this]`我们可以突出显示一些语法)。

## 自身类型的示例

非常清楚的是: **Java 没有自身类型**。但是如果发生了呢？那么我们的例子会是什么样子呢？

### `Object::clone`

应该返回它被调用的实例的副本。该实例当然应该是相同的类型，因此签名可能如下所示:

```
protected [this] clone(); 
```

然后子类可以直接获得自己类型的实例:

```
public class Person {

    // ...

    @Override
    public [this] clone() {
        // no cast required
        // because in this class [this] means Person
        Person clone = super.clone();
        return clone;
    }

} 
```

### 建设者

对于我们上面讨论的构建者，解决方案同样是显而易见的。我们简单地声明所有的`with...`方法来返回类型`[this]`:

```
public class PersonBuilder {

    public [this] withName(String name) {
        this.name = name;
        return this;
    }

} 
```

像以前一样,`EmployeeBuilder::withName`返回一个`EmployeeBuilder`的实例，但是这次编译器知道了，我们以前想要的现在起作用了:

```
Employee doe = new EmployeeBuilder()
    // now we have an EmployeeBuilder
    .withName("John Doe")
    // still an EmployeeBuilder thanks to [this]
    .withHiringDate(LocalDateTime.now()) // works! :)
    .build(); 
```

### 递归容器

最后但同样重要的是，让我们看看我们的图表是如何工作的:

```
public class Node {

    private final List<[this]> children;

    public Stream<? extends [this]> children() {
        return children.stream();
    }

} 
```

现在`SpecialNode::children`返回一个`Stream<? extends SpecialNode>`，这正是我们想要的。

## 用泛型模拟自身类型

虽然 Java 没有自身类型，但是有一种方法可以用泛型来模拟它们。不过，这是有限的，而且有点复杂。

如果我们有一个引用这个类的泛型类型参数，比如说`THIS`，我们可以简单地在上面使用`[this]`的地方使用它。但是我们如何得到`THIS`？简单(几乎)，我们只需添加`THIS`作为类型参数，并让继承类将它们自己的类型指定为`THIS`。

等等，什么？我认为一个例子可以澄清这一点。

```
public class Object<THIS> {

    protected THIS clone();

}

public class Person extends Object<Person> {

    @Override
    public Person clone() {
        // no cast required because
        // in this class THIS was specified as Person
        Person clone = super.clone();
        return clone;
    }

} 
```

如果这看起来可疑(什么意思？)，您已经偶然发现了这种方法的一个弱点，但是我们将在一分钟内讨论这个问题。首先，让我们更深入地研究它，并检查我们的其他例子。

```
public class PersonBuilder<THIS> {

    private String name;

    public THIS withName(String name) {
        this.name = name;
        return (THIS) this;
        // if we do this more often, '(THIS) this'
        // should become its own method
    }
}

public class EmployeeBuilder
        extends PersonBuilder<EmployeeBuilder> { } 
```

现在`EmployeeBuilder::withName`返回一个`EmployeeBuilder`而不需要我们做任何事情。

同样，我们与`Node`的问题也消失了:

```
public class Node<THIS> {

    private final List<THIS> children;

    public Stream<? extends THIS> children() {
        return children.stream();
    }

}

public class SpecialNode extends Node<SpecialNode> { } 
```

与`[this]`一样，我们在`SpecialNode`中不需要额外的代码。

## 局限性和弱点

看起来不算太糟，对吧？但是有些限制和弱点我们必须克服。

### 令人困惑的抽象

就像我上面说的，`Object<Person>`到底是什么意思？它是容纳、创造、处理或以其他方式与人打交道的物体吗？因为这就是我们通常理解泛型类型参数的方式。但它不是——它只是一个“人的对象”,这很奇怪。

### 复杂的类型

现在也不清楚如何声明超类型。是`Node`、`Node<Node>`，还是更多的`Node` s？请考虑以下情况:

```
Node<Node> node = new Node<>();
Stream<Node> children = node.children();
Stream grandchildren = children
    .flatMap(child -> child.children()); 
```

调用`children`两次，我们“用完”了声明为 für `node`的泛型类型，现在我们得到了一个原始流。注意，多亏了`SpecialNode extends Node<SpecialNode>`的递归声明，我们没有这个问题:

```
SpecialNode node = new SpecialNode();
Stream<SpecialNode> children = node.children();
Stream<SpecialNode> grandchildren = children
    .flatMap(child -> child.children()); 
```

所有这些都会让这类用户感到困惑，并最终疏远他们。

### 单级继承

让`SpecialNode`在重复调用`children`时表现出预期行为的技巧意味着它没有自己的自引用类型参数。现在，扩展它的类型不能在任何地方指定自己，所以它的方法返回特殊的节点:

```
public class VerySpecialNode extends SpecialNode { }

VerySpecialNode node = new VerySpecialNode();
// we a want Stream<VerySpecialNode>
Stream<SpecialNode> children = node.children(); // damn :( 
```

### `THIS`太泛了

目前，`THIS`可以是任何类型，这意味着我们必须将其视为`Object`。但是如果我们想用它的实例做一些特定于我们当前类的事情呢？

```
public class Node<THIS> {

    // as before, especially `children`

    public Stream<THIS> grandchildren() {
        // doesn't compile because `child` is no `Node`
        // and hence has no `children` method
        return children.flatMap(child -> child.children());
    }

} 
```

好吧，那太愚蠢了。

## 改进方法

让我们看看我们是否能比以前做得更好，并解决其中的一些弱点。我们确实可以做些什么来解决我们刚刚讨论的所有问题。

### 递归泛型

先说最后的事情，让我们看看`THIS`是否过于一般化。使用递归泛型可以很容易地解决这个问题:

```
public class Node<THIS extends Node<THIS>> {

    // as before, especially `children`

    public Stream<THIS> grandChildren() {
        // hah, now `child` is a `Node`
        return children.flatMap(child -> child.children());
    }

} 
```

(我说的是“轻松”，不是“简单”。)

但是这大大加剧了复杂类型的问题。现在甚至不可能声明一个`Node`，因为编译器总是期望*另一个*类型参数:

```
// doesn't compile
// the fourth `Node` is not
// within its type bounds of `Node<Node>`
Node<Node<Node<Node>>> node = new Node<>(); 
```

### 私人等级制度

不过，我们可以通过另一条弯路来解决这个问题和其他问题。

我们可以创建一个抽象类的层次结构，其中包含了我们到目前为止讨论过的所有代码。我们的客户将使用的具体实现是那个层次的分支。因为没有人会直接使用抽象类，我们可以在继承的每一层使用`THIS`——实现会指定它。

理想情况下，抽象类是包可见的，因此它们的可怕之处对外界是隐藏的。

```
abstract class NodeScaffold<THIS extends NodeScaffold<THIS>> {

    private final List<THIS> children;

    public Stream<THIS> children() {
        return children.stream();
    }

    public Stream<THIS> grandChildren() {
        return children.stream()
            .flatMap(child -> child.children());
    }

}

abstract class SpecialNodeScaffold<THIS extends SpecialNodeScaffold<THIS>>
        extends NodeScaffold<THIS> {
    // special methods
}

abstract class VerySpecialNodeScaffold<THIS extends VerySpecialNodeScaffold<THIS>>
        extends SpecialNodeScaffold<THIS> {
    // more special methods
}

public class Node
        extends NodeScaffold<Node> { }

public class SpecialNode
        extends SpecialNodeScaffold<SpecialNode> { }

public class VerySpecialNode
        extends VerySpecialNodeScaffold<VerySpecialNode> { } 
```

一个小细节:公开可见的类不会相互继承，所以`SpecialNode`不是`Node`。如果我们对构建器做同样的事情，那可能没问题，但是对节点来说就不方便了。为了解决这个问题，我们还需要另一个抽象层，即一些相互扩展并由公共类实现的接口。

现在，公共类的用户看到了清晰的抽象，没有复杂的类型。这种方法适用于任意多个层次的继承，并且`THIS`总是指向最特定的类型。

## `THIS`作为参数类型

您可能已经注意到，所有的例子都使用了`[this]`和`THIS`作为返回类型。我们不能把它作为参数的类型吗？不幸的是不是，因为虽然返回类型是协变的，但参数类型是逆变的——并且`[this]` / `THIS`本质上是协变的。(查看[这个 StackOverflow 问题](http://stackoverflow.com/q/2501023/2525313)获得这些术语的简要解释。)

如果你试着按照上面的方法去做(例如，通过添加`void addChild(THIS node)`)，它似乎会工作，直到你试着创建将`Node`、`SpecialNode`和`VerySpecialNode`带入继承关系的接口。那么`node`的类型不会随着你沿着继承树往下走而变得更加具体。

这篇文章已经够长了，所以我将把细节留给好奇的读者作为练习。

## 摘要

我们已经看到了为什么我们有时需要引用“这种类型”,以及一个语言特性是如何做到这一点的。但是 Java 没有这个特性，所以我们不得不自己想出一些窍门来做这件事:

*   我们在抽象类(`A`)中定义了我们想要继承的方法
*   我们给了他们一个递归的泛型类型参数(`A<THIS extends A<THIS>>`)
*   我们创建了公开可见的具体实现，它们将自己指定为那种类型(`C extends A<C>`)
*   如果需要，我们创建一个接口继承树，由我们的具体类实现

这是否值得所有的努力和欺骗取决于你的决定，取决于你的用例。一般来说，你能以这种方式继承的方法越多，你就能感受到越多的好处(看看 [AssertJ 的 API 实现](https://github.com/joel-costigliola/assertj-core/tree/2c5f011d3c99d86f5d42a743a28238440729ae7f/src/main/java/org/assertj/core/api)中有多少这样的例子)。相反，当您以这种方式创建的类是您代码基础的基础时，理解这种模式的摩擦就会减少——符合“如果它会造成伤害，就更经常地这样做”的原则。如果它仍然是一个边缘解决方案，开发人员将不会知道它的存在，并会无意中意外地陷入其中，更容易被弄糊涂。

你怎么想呢?你在你的代码库中看到用例了吗？很有兴趣听听，留下评论吧。

## 分享这篇文章