# 类型比测试更强大

> 原文：<https://www.sitepoint.com/types-are-mightier-than-tests/>

### 目录 

*   [何时检查类型](#whentochecktypes)
*   [类型可以指定“行为”吗？](#cantypesspecifybehavior)
*   类型检查是否减少了测试的需要？
*   [测试与类型检查](#testingvstypechecking)
*   [减少可能实施的数量](#reducingthenumberofpossibleimplementations)
*   [抽象类型中的控制结构](#abstractingcontrolstructuresintypes)
*   [总结](#summary)
*   [评论](#comments)

> TDD 取代了类型检查器……就像烈酒取代了悲伤一样。布伦特·约尔吉

> 如果你对你正在编写的代码的一个属性有一个正式的证明，而它不能被编码到类型中，那么它应该放在哪里呢？——丹尼尔·斯皮瓦克

> 一个类对另一个类的依赖应该依赖于尽可能小的接口

程序员们总是就强类型系统如何帮助编写好的程序进行长时间的讨论。它们经常导致动态类型语言和静态类型语言之间的比较。有些人甚至认为动态类型语言没有类型系统。

事实上，我们不应该谈论静态和动态类型的语言，而是关于编译时和运行时类型检查。事实上，喜欢运行时类型检查的程序员习惯称他们的语言为“动态类型”，因为“动态”被认为是一种品质，而“静态”通常不是。擅长编译时类型检查的人更喜欢谈论弱类型和强类型语言，因为显然强比弱好。

最后，有一个问题仍然存在:测试在哪里？

## 何时检查类型

只在运行时检查类型给了你更多的自由。但是，大多是乱搞类型的自由。如果你犯了一个类型错误，编译器不会告诉你，错误只会在运行时出现…或者不会。错误不出现有两个主要原因:

*   带有运行时类型检查的语言通常提供一些隐式类型转换功能。如果您没有选择正确的类型，该语言会尝试将您的类型转换为合适的类型。然而你没有理由相信它是正确的，除非你是故意的。

*   如果它不能将你的类型转换成任何合适的类型，它将抛出一个错误。这一错误的后果可能不为人所知。如果你幸运的话，它会让程序崩溃，并告诉你错误是什么。如果不是这样，可能只会使线程崩溃，让应用程序在不确定的状态下运行。此外，如果没有错误发生，你可能会认为程序是正确的。程序中尚未执行的部分可能有错误。这个错误可能在程序投入生产后很久才出现。

由于这些原因，静态检查的类型系统将更有助于编写正确的程序。但另一个问题出现了:类型系统应该有多强？它应该对您的编程施加什么级别的约束？

将类型检查器视为约束是错误的。强大的类型检查器通常会有所帮助。

不过，Java 的类型系统很弱。那是什么意思？这意味着程序“行为”的很少部分被抽象成类型。但是您可以自由地创建自己的类型，或者使用提供新类型的库。

![Specifying behavior with tests.](img/50f7c0afcb737cefff4cbcd22bdab07b.png)

## 类型可以指定“行为”吗？

这是一个有趣的问题，但它提出了新的问题:什么是行为？程序是指定计算机行为的手段吗？

对最后一个问题回答“是”并没有给出任何关于什么程序的信息，而仅仅是关于你正在做的程序的类型。编写指定行为的程序被称为“命令式编程”。编写不这样的程序是另一回事。

函数式编程是“非命令式的”，意思是你不用写一个程序来指定计算机应该如何运行。你写一个代数表达式，当它被求值时，给出预期的结果。但是你没有描述程序将如何做。即使对于命令式程序员来说，这也不足为奇，因为他们每天都在使用它。考虑下面的例子:

```
void displayMessage(String message) {}
    System.out.println(message);
} 
```

当你写这个的时候，你是在指定行为吗？您肯定没有指定计算机应该如何将消息打印到控制台。您只是要求将效果应用于一个值。你甚至没有选择这个值。指定行为就是描述获得结果的方式，这是通过使用控制结构编写指令来完成的。但是函数式编程既不使用控制结构，也不使用指令。

所以对于“类型能否指定行为”这个问题，对于函数式程序员来说，答案是否定的，因为没有行为可以指定。另一方面，对于使用面向对象语言的命令式程序员来说，答案显然是肯定的，因为这就是对象(以及其他事物):封装在类型中的行为。对象不是*只是*类型。但是他们*是*类型。在 Java 中，`String`、`Integer`、`List`都是类型。这些类型指定了行为。不是一个程序的所有行为，而是它的一部分。

## 类型检查是否减少了测试的需要？

让我们举个例子:假设你想连接两个字符串列表。首先你必须检查`null`的参数，然后以某种方式将列表放在一起:

```
List<String> concat(List<String> list1, List<String> list2) {
    if (list1 == null) {
        return list2;
    } else if (list2 == null) {
        return list1;
    } else {
        for (int i = 0; i < list2.size(); i++) {
            list1.add(list2.get(i));
        }
        return list1;
    }
} 
```

这是人们对旧语言可能做的事情。现代语言带来了一些新的抽象概念，允许我们简化这类程序。使用“for each”循环，我们可以编写:

```
List<String> concat(List<String> list1, List<String> list2) {
    if (list1 == null) {
        return list2;
    } else if (list2 == null) {
        return list1;
    } else {
        for (String s : list2) {
          list1.add(s);
        }
        return list1;
    }
} 
```

Java 甚至为我们提供了更好的抽象:

```
List<String> concat(List<String> list1, List<String> list2) {
    if (list1 == null) {
        return list2;
    } else if (list2 == null) {
        return list1;
    } else {
        list1.addAll(list2);
        return list1;
    }
} 
```

或者，使用 Java 8:

```
List<String> concat(List<String> list1, List<String> list2) {
    if (list1 == null) {
        return list2;
    } else if (list2 == null) {
        return list1;
    } else {
        list2.forEach(list1::add);
        return list1;
    }
} 
```

所有这些抽象都很好，(尤其是`addAll`)但是它们并没有改变我们需要的测试水平。事实上，由于我们实现了`null`检查，我们必须测试我们的`null`检查的实现是否有效。所以我们应该测试我们的程序，每个参数都是`null`(当然，两个参数都是`null`)。

我们可以使用一个类型系统来使对`null`的检查变得不必要吗？是的，我们可以。我们可以使用不可空的类型，就像 Kotlin 提供的那样。在科特林，类型`List<String>`不适用于`null`。`List<String>`是`List<String>?`的子类型，可以为空。通过提供一个不可空的版本，它把我们从检查空中解放出来，因此它把我们从测试那些检查中解放出来。

这种带有约束(不为空)的类型是*依赖类型*的最简单形式。依赖类型是依赖于值的类型。例如，对于依赖类型，T 类型的 5 个元素的列表可能与 T 类型的 6 个元素的列表是不同的类型。维基百科有一篇关于依赖类型的[好文章](https://en.wikipedia.org/wiki/Dependent_type)。

在其他语言中，不可空的`List`可能由参数化类型表示，比如`Optional<List<String>>`。这两种方法之间最明显的区别是`Optional<List<String>>`不是`List<String>`的父类型。但是它们有几个相似之处，其中一个事实是，与`null`不同，它们可以表示缺少数据而不丢失类型。最重要的是，他们作曲。

再考虑一下这个契约:你的方法接收一个类型为`List<String>`的参数这一事实难道不意味着你可以安全地对它调用`size()`(或者任何其他的`List`方法)吗？如果你不能信任这种类型，没有大量的测试，你什么都做不好。显然，使用处理这个问题的类型系统使得大量的测试变得不必要。

## 测试与类型检查

但是我们的程序有另一个问题。你可能已经注意到这个程序是有缺陷的。在这种情况下，您可能认为不需要类型检查器来告诉您。但是为什么你想让测试告诉你你的程序是不正确的呢？

不管怎样，我们开始吧。我们可能希望用几个单元测试来测试程序，例如:

```
@Test
public void testAll() {
    List<String> list1 = new ArrayList<>(Arrays.asList("1", "2", "3"));
    List<String> list2 = new ArrayList<>(Arrays.asList("4", "5", "6"));
    List<String> list3 = new ArrayList<>(Arrays.asList("7", "8", "9"));
    List<String> list4 = new ArrayList<>(Arrays.asList("1", "2", "3", "4", "5", "6"));
    assertEquals(list4, concat(list1, list2));
    assertEquals(list1, concat(list1, null));
    assertEquals(list2, concat(null, list2));
    assertNull(concat(null, null));
    assertEquals(
            concat(list1, concat(list2, list3)),
            concat(concat(list1, list2), list3));
} 
```

当然，测试应该是独立的，这样一个失败的测试不会停止后续测试的执行，同时也要确保它们不会相互干扰，但这只是为了节省空间。工作原理是一样的，所有这些测试都通过了。好吗？不是这样的！

如果测试找不到错误，测试还有什么必要？当然，我知道我在测试程序中做错了什么，因为我是故意这样做的。但是我在测试中做错了什么？

这显示了关于测试的一些非常重要的事实:

*   测试并不能证明一个程序是正确的。它甚至不能证明它有时是正确的(对于测试中使用的论点)。这只能证明我们没有聪明到证明它是不正确的。这里我们需要的是测试正确性的度量。也许是为了测试而测试？

*   为了提高测试的效率，我们应该在编写测试时比编写程序时投入更多的智慧。否则，程序通常会击败测试。但是一般来说，我们在程序中加入了尽可能多的智能。所以剩下的测试不多了。

*   相信更多的测试会让项目变得更好就像相信更频繁地称体重会让你减肥一样。(虽然，如果秤在第十层，而你住在第一层，它可能最终不会错。但是这只是一个副作用，所以它对函数式程序员不起作用。)

如果您还没有注意到程序出错的原因，请运行以下命令:

```
@Test
public void testAll() {
    List<String> list1 = new ArrayList<>(Arrays.asList("1", "2", "3"));
    List<String> list2 = new ArrayList<>(Arrays.asList("4", "5", "6"));
    List<String> list3 = new ArrayList<>(Arrays.asList("7", "8", "9"));
    assertEquals(concat(list1, concat(list2, list3)),
                 concat(concat(list1, list2), list3));
    System.out.println(concat(list1, concat(list2, list3)));
    System.out.println(concat(concat(list1, list2), list3));
} 
```

测试通过，但打印输出是:

```
[1, 2, 3, 4, 5, 6, 7, 8, 9, 4, 5, 6, 7, 8, 9, 7, 8, 9, 4, 5, 6, 7, 8, 9, 7, 8, 9]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 4, 5, 6, 7, 8, 9, 7, 8, 9, 4, 5, 6, 7, 8, 9, 7, 8, 9, 4, 5, 6, 7, 8, 9, 7, 8, 9, 7, 8, 9] 
```

当然，现在，很明显。但是依靠类型系统来防止这种情况不是更好吗？我们可以不检查参数是否有变化，而是:

*   制作了一份防御性副本

*   使用不可变类型

*   限制了类型

编译器能检查出我们做了一个防御拷贝吗？大概不会。即使有测试，也只能部分检查。

Java 提供了不可变的列表，但是它们使用起来很麻烦，而且效率不高。另外，它们并不是真正不可变的，而是可变集合的不可变视图。不管怎样，最大的问题是 Java 没有在类型级别区分可变和不可变列表，而只是在行为级别。

Java 不可变列表和可变列表的类型相同。对此我们无能为力，只能创建自己的类型，也就是说在类型系统中表达这种行为。虽然这并不困难(在实际项目中甚至没有必要，因为我们可以使用现有的实现，例如， [PCollections](https://pcollections.org/) 或 [Javaslang](http://www.javaslang.io/) )，但是还有其他方法来实现我们的目标。

`List`行为当然不限于可变性或不可变性:列表上的许多操作都是根据控制结构实现的，例如`for`或`while`循环。这些操作中的一些已经被翻译成类型，比如`Iterable`，或者`Enumeration`，我们能做的就是依靠这些类型而不是原来的`List`类型:

```
static List<String> concat(
        Iterable<String> list1, List<String> list2) {
    if (list1 == null) {
        return list2;
    } else if (list2 == null) {
        // compile error #1 - incompatible types:
        // Iterable<String> cannot be converted to List<String>
        return list1;
    } else {
        // compile error #2 - invalid method reference
        // cannot find method add() on Iterable<String>
        list2.forEach(list1::add);
        // compile error #3 - incompatible types:
        // Iterable<String> cannot be converted to List<String>
        return list1;
    }
} 
```

突然，我们的程序不再编译了！这是一件好事，因为我们有三个错误，不仅是编译错误，还有概念错误！例如，`Iterable`是只读的，但是我们试图添加元素。

我们在这里观察到的是，通过使用只公开所需最小接口的类型，编译器可以帮助我们检测问题。因此，我们将`concat`方法改为:

```
List<String> concat(Iterable<String> list1, List<String> list2) {
    if (list1 == null) {
        return list2;
    } else {
        ArrayList<String> result = new ArrayList<>();
        list1.forEach(result::add);
        if (list2 == null) {
          return result;
        } else {
          result.addAll(list2);
        }       
        return result;
    }
} 
```

现在，代码更加正确了。这并不意味着这是理想的解决方案。这只意味着测试没有帮助我们发现问题。类型系统做到了。

## 减少可能实现的数量

不过，如果有一个更好的类型系统，我们可以走得更远。通常的做法是通过测量[分支覆盖](https://sites.google.com/site/swtestingconcepts/home/test-design-techniques/for-white-box/statement-branch-and-path-coverage)来测量测试的质量。但是分行覆盖率并没有太大意义。一般来说，程序员在获得最高分支覆盖率的同时，试图创建尽可能少的测试。

除此之外，这种测试是依赖于实现的，这意味着当实现改变时，它们必须更新。这是很麻烦的，而且经常不这样做。

一些程序员认为测试不应该依赖于实现。他们甚至认为应该在实现要测试的内容之前编写测试。我完全同意，如果不是因为一个小小的条件:为了有效，这些测试应该测试所有可能的实现，因为我们事先不知道实现会是什么。

人们甚至可以想象一种检查测试套件强度的方法:程序员不应该在测试没有发现的情况下编写不正确的实现。考虑到这一点，编写测试似乎是一项巨大的任务！(如果您正在进行[突变测试](https://blog.codecentric.de/en/2016/01/mutation-testing-watching-watchmen/)，您至少知道如何找到通过测试的不正确的实现。)

虽然不总是。再一次，我们可以让类型系统帮助我们。理想情况下，我们希望限制类型，以便只有一个可能的实现。这样，我们就只需要编写一个测试。可能吗？嗯，有时候是这样。举以下例子:

```
static <A, B, C> Function<A, C> transmogrify(B b, BiFunction<A, B, C> f) {
    // ...
} 
```

你能为这个方法写一个测试吗？你可能认为你不能，因为你不知道这个方法是做什么的。方法名可能对您没有太大帮助。因此，您可能想看看实现，但不幸的是，它不可用。可能还没有实现，你在做 TDD。你能做什么？

事实上很简单:

```
@Test
public void testTransmogrify() {
    Double pound = 0.794546235;
    BiFunction<Integer, Double, Double> multiply = (a, b) -> a * b;
    Function<Integer, Double> toPound = transmogrify(pound, multiply);
    assertEquals(toPound.apply(45), 35.754 , 0.001);
} 
```

这不是魔法。其实只有一种可能的实现。不相信我？请继续阅读！

这在函数式编程中其实是相当常见的。通常，函数式程序员有一个方法要实现，给定它的签名。通常，他们只需要按照类型。

让我们看看这是如何发生的。首先，我们必须返回一个函数，所以我们可以把它写成一个 lambda，带一个参数`A a`(但是我们不需要指明类型):

```
static <A, B, C> Function<A, C> transmogrify(B b, BiFunction<A, B, C> f) {
    return a ->
} 
```

接下来，我们必须创建一个`C`作为函数的返回值。为了做到这一点，我们所拥有的是一个`B`和一个`BiFunction`，它们将从一个`A`和一个`B`产生一个`C`。再简单不过了！我们使用开始 lambda 表达式时使用的`A a`和赋予方法的`B b`，并应用`BiFunction`:

```
static <A, B, C> Function<A, C> transmogrify(B b, BiFunction<A, B, C> f) {
    return a -> f.apply(a, b);
} 
```

就是这样！现在你可以给这个函数起一个更有意义的名字。它所做的是部分应用`BiFunction`的第二个参数，产生一个单一参数的`Function`。(更确切地说，它应用了对中的第二个元素，这是`BiFunction`的唯一参数。)所以我们可以把它命名为`partial2`。

当您想要用相同的“第二个参数”重复应用一个`BiFunction`时，这样的函数非常有用。在我们的测试中，我们将一个在给定金额和汇率的情况下将一种货币转换为另一种货币的函数转换为一个使用特定汇率转换金额的函数。换句话说，我们从一个通用货币转换器创建了一个美元到英镑的转换器(因为美元到英镑的转换率是“内置”在部分应用的函数中的)。

我们真的需要测试吗？使用真正的函数式语言，我们不会。在 Java 中，我们可以用类型来表示`null`值和异常，如果我们想用 Java 进行函数式编程，这正是我们要做的。

唉，我们不能总是避免处理`null`和异常。因此，如果我们看不到实现，我们必须编写一个测试来保证这个方法不返回`null`也不抛出异常。我们不需要测试更多——如果它适用于一个参数值，那么它适用于所有参数值。(不幸的是，这不是绝对正确的，因为实现可能会检查一些外部数据并随机抛出异常或返回`null`，所以只有当方法是[引用透明的](https://wiki.haskell.org/Referential_transparency)时才是正确的，这在函数式编程中应该总是这样)。

在任何情况下，如果我们编写了实现，我们可以看到它既没有抛出异常也没有返回`null`。然而，该测试对于强化代码以应对未来的变化是有用的。如果有人将实现改为抛出异常或返回 null，这很可能会破坏现有的代码，测试会尽早指出这一点。但是请注意，一个单独的测试足以消除这两种情况(返回 null 和抛出异常)。

![Specifying behavior with types](img/6234d7aec38c1d68cb6cf3a3981eb72d.png)

[发表](https://www.flickr.com/photos/khawkins04/5494470398/ "Source on Flickr")由[肯·霍金斯](https://www.flickr.com/photos/khawkins04/ "Author on Flickr")在[下 CC-BY 2.0](https://creativecommons.org/licenses/by/2.0/ "Link to Licence") / SitePoint 改了背景

## 抽象类型中的控制结构

同样的原则也适用于更复杂的问题。类型系统不仅可以用来表示异常或空值，它还可以表示任何其他条件，如基数、[、【未来性】、](http://www.nurkiewicz.com/2013/05/java-8-definitive-guide-to.html)[、](http://blog.codefx.org/techniques/intention-revealing-code-java-8-optional/)、[惰性](https://www.sitepoint.com/lazy-computations-in-java-with-a-lazy-type)、效果、状态、并行性等等。结果是我们不需要*任何*控制结构。

这对于命令式程序员来说似乎难以置信，但是真正的函数式语言没有控制结构。没有`if ... else`，没有`for`或`while`循环，没有`switch ... case`，没有`try ... catch`，什么都没有。那么，您如何管理控制流呢？(在继续读下去之前，请做好准备。)答案是:你没有！没有控制流。

让我们重写没有任何控制流的`concat`例子。当然，Java 没有提供我们需要的类型，所以我们要创建它们。

首先，让我们看看算法。我们有两个列表，我们希望通过获取第二个列表的每个元素并将其附加到第一个列表来连接它们。还记得文章开头的第三句引言吗？

> 一个类对另一个类的依赖应该依赖于尽可能小的接口

如果我们使用最少的所需信息，类型系统将会更有用。`concat`方法的第一个参数不一定是列表。因为我们只想将元素追加到第一个参数，所以它需要是`Appendable`。(因为我们不需要任何其他行为，所以它不必是任何更具体的类型。)下面是`Appendable`式:

```
public interface Appendable<T, U> {

    Appendable<T, U> append(T t);

    U get();
} 
```

`T`是被追加的元素的类型，`U`是保存它们的集合(例如一个`List<T>`)。它有一个方法`append`，返回添加了给定元素的新的`Appendable`(类型本身应该是不可变的)，还有另一个方法`get`，返回当前的项目集合。

回到论点，第二个也不需要是一个列表——它需要是可迭代的。在函数式编程中，我们使用另一个概念:`Foldable`。

为了理解`Foldable`，考虑一个字符列表:我们可以从一个空字符串开始，依次将每个字符追加到字符串中。为此，我们使用一个函数获取一个字符串和一个字符，然后返回一个字符串。如果我们从左边开始，用字符串作为第一个参数，这是一个左折叠。如果我们从右边开始，使用元素作为函数的第一个参数，这是一个右折叠。所以我们可以定义`Foldable`类型:

```
public interface Foldable<T> extends RightFoldable<T>, LeftFoldable<T> {}

public interface LeftFoldable<T> {
    <U> U foldLeft(U seed, BiFunction<U, T, U> f);
}

public interface RightFoldable<T> {
    <U> U foldRight(U seed, BiFunction<T, U, U> f);
} 
```

当然，我们需要一个`List`类型来实现这些接口。函数式的对于本文来说太长了，但是我[已经创建了一个 Java `List`](https://gist.github.com/pysaumont/c1b387c83f6e377984c50dadb6c867e6) 的包装器，你可以试试。(这当然不是生产代码。)然而，实现本身并不相关——在现实生活中，它要么由语言提供，要么由库提供。

重要的是我们必须写的代码*。我们的`concat`方法应该是这样的:*

```
public static <T> List<T> concat(Appendable<T, List<T>> a, LeftFoldable<T> b) {
    return b.foldLeft(a, Appendable::append).get();
} 
```

我们如何测试这种方法？哪种“行为”值得测试？该方法可以返回`null`或者抛出一个异常。它还可以将第二个参数的元素的任意数量的副本追加到第一个参数中。

这似乎允许无限数量的实现。事实上，确实如此，但是所有错误的实现都可以通过一次测试来消除。这可以是对连接两个任意列表的结果的测试(尽管第二个列表不能为空)，但是不需要测试预期的结果。测试结果的长度等于参数长度的总和就足够了。

我们的解决方案的所有其他属性都表示为类型！

## 摘要

在本文中，我们已经看到了为什么测试是检查命令式程序正确性的一个非常弱(但必要)的工具。然后，我们展示了使用强类型系统的函数式编程是如何显著降低测试需求的。在一个完美的函数式编程世界中，测试是完全没有必要的。但是没有什么是完美的，所以我们需要一些测试。但是，比命令式程序员所习惯的要少得多。

顺便说一下，出于同样的原因，函数式编程也降低了对日志记录和调试的需求。

## 分享这篇文章