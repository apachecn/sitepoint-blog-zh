# 未来的 Java 会是什么样子

> 原文：<https://www.sitepoint.com/what-java-might-one-day-look-like/>

11 月的第二周是欧洲最大的 Java 大会——比利时 Devoxx 大会，每年社区的名人都会出席。其中一位是甲骨文公司的 Java 语言架构师 Brian Goetz，他做了我认为是大会最激动人心的演讲:[“Java 语言和平台的未来:一瞥”](https://www.youtube.com/watch?v=oGll155-vuQ)。在这篇文章中，他提出了 JDK 团队目前正在讨论的想法。好家伙，是管道充满了伟大的东西！一旦 Java 被公开，它看起来就不一样了。

### 目录 

*   [速成班](#crashcourse)
*   [数据对象](#dataobjects)
*   [值类型](#valuetypes)
*   [专业仿制药](#specializedgenerics)
*   [多类型推断](#moretypeinference)
*   [模式匹配](#patternmatching)
*   [总结](#summary)
*   [评论](#comments)

那会是什么时候？没人知道。而且那不是*没人*如在*甲骨文*之外的没人，那是*没人*如在*没人知道[任意的`n`](https://en.wikipedia.org/wiki/Happy_ending_problem)* 是否存在美好结局。Brian 不遗余力地强调以下所有内容是多么的投机，以及有多少事情可能会发展或简单地放弃。他甚至让所有观众都签字承认这件事(只是在精神上，但仍然如此)，并明确禁止任何耸人听闻的推文。

嗯……首先，这不是推特，其次，我不在观众中。所以我们开始吧！(不过，严肃地说，把这当成它本来的样子:对许多许多可能的未来的一瞥。)

## 速成班

在我们逐一讨论这些想法之前，让我们直入主题，看看使用了所有设想的特性的代码会是什么样子。下面的类是一个简单的链表，它使用两种类型的节点:

*   包含值并链接到下一个节点的
*   只包含一个值

一个特别有趣的操作是`reduce`，它接受一个种子值和一个 [`BinaryOperator`](https://docs.oracle.com/javase/8/docs/api/java/util/function/BinaryOperator.html) ，并将其应用于种子和所有节点的值。这是将来某一天可能的样子:

```
public class LinkedList<any T> {

    private Optional<Node<T>> head;

    // [constructors]
    // [list mutation by replacing nodes with new ones]

    public T reduce(T seed, BinaryOperator<T> operator) {
        var currentValue = seed;
        var currentNode = head;

        while (currentNode.isPresent()) {
            currentValue = operator.apply(currentValue, currentNode.get().getValue());
            currentNode = switch (currentNode.get()) {
                case InnerNode(_, var nextNode) -> Optional.of(nextNode);
                case EndNode(_) -> Optional.empty();
                default: throw new IllegalArgumentException();
            }
        }

        return currentValue;
    }

    private interface Node<any T> {
        T getValue();
    }

    private static class InnerNode<any T>(T value, Node<T> next) implements Node<T> { }

    private static class EndNode<any T>(T value) implements Node<T> { }

} 
```

哇！几乎不再是 Java 了，对吧？！除了省略的构造函数之外，只有代码实际上*做*一些事情——我的意思是，样板文件在哪里？如果我告诉你，除此之外，性能会比现在好得多呢？听起来像是免费的午餐，见鬼，就像是免费的自助餐！

以下是最新消息:

*   泛型类型参数用`any`标记——这是怎么回事？
*   `reduce`中`currentValue`和`currentNode`的型号信息在哪里？
*   那个`switch`几乎认不出来了。
*   类`InnerNode`和`EndNode`看起来，呃，是空的。

让我们看看这个例子中的所有想法。

## 数据对象

你上一次创建一个本质上是哑数据容器的域对象是什么时候，可能有一两个重要的方法，仍然需要一百行构造函数、静态工厂方法、访问器、 [`equals`](https://www.sitepoint.com/implement-javas-equals-method-correctly/) 、 [`hashCode`](https://www.sitepoint.com/how-to-implement-javas-hashcode-correctly/) 和`toString`。(你说现在？别担心，我不做评判。)尽管 ide 很乐意生成所有这些内容，即使在今天也不需要输入，但它仍然是需要理解的代码(构造函数做任何验证吗？)并进行维护(最好不要忘记将那个新字段添加到`equals`)。

为了减少样板文件，编译器可能会在不需要我们动手的情况下动态生成所有这些东西！

用户可能是这样的:

```
public class User(String firstName, String lastName, DateTime birthday) { } 
```

我们可以免费获得我上面提到的所有东西，并且只需要实际实现非标准的东西(也许用户有一个单独决定相等性的 ID，所以我们想要一个相应的`equals`实现)。去掉所有这些代码将极大地提高可维护性！

查看链表的例子，我们可以看到`InnerNode`和`EndNode`依赖于这个特性。

## 值类型

当 Java 被创建的时候，一个算术运算和一个来自主存的加载需要大约相同的周期数(这里是以数量级来说)。在过去的 20 多年里，这种情况发生了很大的变化，内存访问速度大约慢了三倍。

所有抽象 Java 类型都是对象，通过引用相互链接，这需要寻找指针，并使问题变得更糟。好处是这样的类型有身份，允许可变性，继承，和一些其他的东西…我们实际上并不总是需要。这是非常不令人满意的，需要做点什么！

Valhalla 项目即将到来，作为其中的一部分，值类型正在被开发。它们可以概括为自定义原语。这里有一个简单的例子:

```
value class ComplexNumber {

    double real;
    double imaginary;

    // constructors, getters, setters, equals, hashCode, toString
} 
```

看起来像一个普通的类——唯一的不同是这里的关键字`value`。

像基元一样，值类型既不会导致内存开销，也不会导致间接内存开销。一个自定义的`ComplexNumber`，就像上面那个有两个`double`字段`real`和`imaginary`的，无论在哪里使用，都会被内联。像基元一样，这些数字没有同一性——虽然可以有两个不同的值为 5.0 的`Double`对象，但不能有两个不同的双精度 5.0。这排除了我们喜欢对对象做的一些事情:将它们设置为 null、继承、变异和锁定。反过来，它将只需要这两个双精度所需的内存，一个复数数组实质上是一个实/虚数组。

像类一样，值类型可以有方法和字段，封装内部，使用泛型，实现接口(但不能扩展其他类)。因此有一句口号:[“代码像一个类，工作起来像一个整数。”](http://cr.openjdk.java.net/~jrose/values/values-0.html)这将允许我们不再权衡我们喜欢的抽象和我们需要的性能(我们想象的)。

谈到性能，它的优势是相当可观的，几乎可以提高任何代码的速度。例如，在`HashMap`中，节点可以变成值类型，从而加速 Java 最普遍的数据结构之一。但是这并不是一个只有铁杆库开发者想要使用的低级特性！它允许我们所有人选择正确的抽象，并通知编译器和我们的同事，我们的一些对象实际上不是对象而是值。

顺便说一下，我个人的猜测是，编译器会像处理数据对象和集成构造函数、getters、setters 等一样有用。：

```
value class ComplexNumber(double real, double imaginary) { } 
```

如果这不是非常明显的话:这是一个深刻的变化，基本上与一切都相互作用:

*   语言(泛型、通配符、原始类型等等)
*   核心库(收藏、流)
*   JVM(类型签名、字节码等等)

那么……在链表的例子中，值类型到底出现在哪里？诚然，它们起不了多大作用。如果我足够聪明，能够编写一个[持久数据结构](https://en.wikipedia.org/wiki/Persistent_data_structure)，节点可以是值类型(记住，它们必须是不可变的)，这可能会非常有趣。

但是这里有一种可能的值类型:`Optional`。在 Java 8 中，它已经被标记为基于值的类，有一天它可能会成为值类型或其包装。这使它变得扁平，并消除了内存间接性和可能的缓存缺失。

## 专用仿制药

随着每个人和他们的狗创建类似原语的值类型，有必要看看它们如何与参数多态性交互。如你所知，泛型不适用于原语——不可能有`ArrayList<int>`。这对于八个原语来说已经很痛苦了(参见流或库 [Trove](https://bitbucket.org/trove4j/trove) 的[原语专门化),但是当开发人员可以定义更多原语时，这就变得难以忍受了。如果值类型必须被装箱才能与泛型交互(就像今天的基元一样)，它们的使用将会相当有限，并且它们将是不可行的。](http://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/java/util/stream/IntPipeline.java)

所以我们希望能够[使用具有值类型的泛型](http://cr.openjdk.java.net/~briangoetz/valhalla/specialization.html)——而原语可以顺其自然。最后，我们不仅希望实例化一个`ArrayList<int>`或`ArrayList<ComplexNumber>`，我们还希望它分别得到一个`int[]`或`ComplexNumber[]`的支持。这就是所谓的专业化，并打开了一个全新的蠕虫罐。(为了更好地了解这些蠕虫，请观看 Brian 在 JVMLS 2016 上发表的演讲[“参数多态性的冒险”](https://www.youtube.com/watch?v=Tc9vs_HFHVo)。那篇文章还包含一个演讲列表，如果你想更深入地了解，可以看看。)

不仅要泛型化引用类型，还要泛型化值类型的代码必须用`any`标记各自的类型参数。你可以看到`LinkedList`、`Node`和它的实现就是这样做的。这意味着在一个`LinkedList<int>`中，节点实际上有`int`字段，而不是像现在的`LinkedList<Integer>`那样用`Object`字段来保存`Integer`字段。

![binocular-future-of-java](img/fb6ba133f30e2f7546f237015aa1b59e.png)

## 更多类型推理

从 Java 5 开始，Java 就已经完成了类型推断(用于泛型方法中的类型见证)，并且在 Java 7(菱形运算符)、8 (lambda 参数类型)和 9(匿名类上的菱形)中扩展了该机制。在 Java X 中，它可能很好地涵盖了变量声明。布莱恩的例子是这样的:

```
// now
URL url = new URL("...")
URLConnectoin conn = url.openConnection();
Reader reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));

// maybe in the future
var url = new URL("...")
var conn = url.openConnection();
var reader = new BufferedReader(new InputStreamReader(conn.getInputStream())); 
```

这里，`url`、`conn`和`reader`的类型非常明显。因此，编译器可以推断出它们，使我们没有必要指定它们。一般来说，类型推断可以减少样板文件，但也隐藏了重要信息。如果您认为变量名比它们的类型更重要，您会喜欢这样，因为它完美地对齐了名称，同时丢弃了多余的信息。

注意，类型推断是*而不是*动态类型——它仍然是强类型，只是类型更少(Brian 的双关语——大概是有意的)。类型信息仍然会出现在字节码中，ide 也能够显示它们——只是我们不再需要把它们写出来。

推导类型的自动过程意味着代码的改变会改变计算的结果。虽然局部变量通常可以改变它的类型(例如，改变它的超类型)，但对于字段、方法参数或返回值等来说，情况就不一样了。相反，这里的任何更改都可能导致二进制不兼容，这将导致针对旧版本编译的代码在运行时无法链接。不好，因此被禁止。

因此，只推断局部变量的类型更多地是为了保护生态系统免受不稳定代码的影响，而不是保护开发人员免受不可读代码的影响。

## 模式匹配

[Java 目前的`switch`声明](https://www.sitepoint.com/javas-switch-statement/)相当弱。你可以把它用于原语，枚举和字符串，但仅此而已。如果你想做任何更复杂的事情，你要么求助于 if-else-if 链，要么，如果你不能把四人帮的书从你的脑海中抹去，就求助于[访问者模式](https://en.wikipedia.org/wiki/Visitor_pattern)。

但是仔细想想，这些限制并没有真正的内在原因。在更高的层次上，开关可以被描述为使用变量来评估一些条件，并选择匹配的分支，评估它在那里找到的内容-为什么变量的类型应该如此有限，而条件只检查相等性？想想看，为什么只有`switch`做的事情，而*做*的事情。沿着这条线索，我们以模式匹配结束，它没有这些限制。

首先，各种变量都是允许的。第二，条件可以更广泛。例如，他们可以检查类型，甚至解构整个数据对象。最后但同样重要的是，整个`switch`应该是一个表达式，对匹配条件分支中的表达式求值。

以下是布莱恩的例子:

```
// matching types
String formatted;
switch (constant) {
    case Integer i: formatted = String.format("int %d", i); break;
    case Byte b: //...
    case Long l: // ...
    // ...
    default: formatted = "unknown"
}

// used as an expression
String formatted = switch (constant) {
    case Integer i -> String.format("int %d", i);
    case Byte b: //...
    case Long l: // ...
    // ...
    default: formatted = "unknown"
}

// deconstructing objects
int eval(ExprNode node) {
    return switch (node) {
        case ConstantNode(var i) -> i;
        case NegNode(var node) -> -eval(node);
        case PlusNode(var left, var right) -> eval(left) + eval(right);
        case MulNode(var left, var right) -> eval(left) * eval(right);
        // ...
    }
} 
```

对于链表，我也将它用作一个表达式来解构节点:

```
currentNode = switch (currentNode.get()) {
    case InnerNode(_, var nextNode) -> Optional.of(nextNode);
    case EndNode(_) -> Optional.empty();
    default: throw new IllegalArgumentException();
} 
```

比现在看起来要好得多:

```
if (currentNode.get() instanceof InnerNode) {
    currentNode = Optional.of(((InnerNode) currentNode.get()).getNext());
} else if (currentNode.get() instanceof EndNode) {
    currentNode = Optional.empty();
} else {
    throw new IllegalArgumentException();
} 
```

(是的，我知道，这个特殊的例子可以用多态来解决。)

## 摘要

又来了，哇！数据对象、值类型、通用专门化、更多类型推理和模式匹配——这是 JDK 团队正在开发的一系列巨大功能。我等不及他们出来了！(顺便说一下，虽然我在这里介绍了所有的功能，但 Brian 提供了更多有趣的背景——你绝对应该看看整个演讲。)

你怎么想呢?你想用 Java 编码吗？

喜欢这篇文章吗？想了解更多关于 Java 的现在和未来吗？订阅我们的[提要](https://www.sitepoint.com/java/)或[时事通讯](https://go.sitepoint.com/h/y/43CDB766C5398A3B)。

## 分享这篇文章