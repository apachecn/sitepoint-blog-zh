# 抽象课程:FP 能教给 OOP 什么

> 原文：<https://www.sitepoint.com/oop-learn-about-abstraction-from-fp/>

### 目录 

*   [抽象面向对象编程中的计算](#abstractingthecomputationinobjectorientedprogramming)
*   [使用策略模式](#usingthestrategypattern)
*   [使用控制反转(IoC)](#usinginversionofcontrolioc)
*   [函数式编程中的抽象](#abstractinginfunctionalprogramming)
*   [抽象计算](#abstractingcomputation)
*   [抽象系列的创作](#abstractingthecreationofacollection)
*   [抽象迭代](#abstractingiteration)
*   [将抽象应用于更复杂的问题](#applyingabstractiontomorecomplexproblems)
*   [初始问题](#theinitialproblem)
*   [抽象集合处理](#abstractingcollectionhandling)
*   [抽象计算](#abstractingthecomputation)
*   [抽象类型](#abstractingthetype)
*   [使用不同类型的返回值](#usingadifferenttypeforthereturnvalue)
*   [小心可变列表](#bewareofmutablelists)
*   [总结](#summary)
*   [评论](#comments)

> 抽象是人类发明的最伟大的幻想工具之一，用来想象、破译和描绘世界。杰瑞·萨尔茨

> 我希望尽可能接近真理，因此我将一切抽象化，直到我达到物体的基本质量。――皮耶·蒙德里安

编程中最被提倡的“最佳实践”之一是 DRY 原则:*不要重复自己*。许多技术可以用来实施这个原则:封装、参数化、控制反转等等。这些技术之一是*抽象*，函数式编程(FP)和面向对象编程(OOP)之间的一个主要区别是应用抽象的方式。OOP 中的一个常见做法是将抽象限制到手头问题的最少有用程度。在 OOP 中，过早的抽象通常被认为是一个错误，就像过早的优化一样。

另一方面，在 FP 中，抽象通常被尽可能地推进。每个问题都被分解成一系列最简单的可能函数，然后组合这些函数来构建问题的解决方案。识别这些抽象通常是解决问题最重要的部分。事实上，FP 程序员经常花更多的时间去寻找他们应该解决什么问题，而不是去解决它们。当然，从一个问题到下一个问题，这些函数似乎都是相同的。只是它们的构成方式不同。这就是为什么抽象是 FP 程序员使用的最有价值的技术之一。

在本文中，我们将比较 OOP 和 FP 如何在一个特定的简单问题中处理抽象:计算 1 和任意值 *n* 之间的整数之和。使用命令式编程来解决这个问题是如此简单，以至于似乎没有什么值得学习的地方。下面是如何在命令式 Java 中实现的:

```
static int sum(int n) {
    int sum = 0;
    for (int i = 1; i <= n; i++) {
        sum += i;
    }
    return sum;
} 
```

似乎没有更简单更高效的方法了。然而，对于 OOP 程序员和 FP 程序员来说，问题有趣的部分还在后面。但是这些程序员中每一个可能会走一条非常不同的路。我们也可以注意到(并演示！)n 个第一整数之和等于`(n * (n + 1) / 2)`。正如您将看到的，这将是另一条路，走向尽可能少的抽象。

## 面向对象编程中的计算抽象

FP 和 OOP 程序员都会立即注意到一个可能的抽象，即应用于构造结果的操作。这里，我们添加值来构造结果。如果我们被要求返回乘积而不是总和会怎么样？

在 OOP 中，有两种显而易见的方法来抽象计算:使用*策略*模式，或者*控制反转* ( *IoC* )。

### 使用策略模式

Java 将计算抽象成可以交给程序的形式的方法是创建一个包含执行计算的方法的对象。(如果你很幸运，只有一种方法，lambda 表达式可以使这变得更简单，但方法是一样的。)为了使这成为一个抽象，我们必须将抽象部分变成一个接口，将具体部分变成一个实现:

```
static int compute(int n, Computer computer) {
    int result = computer.identity();
    for (int i = 1; i <= n; i++) {
        result = computer.compute(result, i);
    }
    return result;
}

interface Computer {

    int compute(int a, int b);

    int identity();

}

static class Adder implements Computer {

    @Override
    public int compute(int a, int b) {
        return a + b;
    }

    @Override
    public int identity() {
        return 0;
    }

}

static class Multiplier implements Computer {

    @Override
    public int compute(int a, int b) {
        return a * b;
    }

    @Override
    public int identity() {
        return 1;
    }

}

public static void main(String... args) {
    System.out.println(compute(5, new Adder()));
    System.out.println(compute(5, new Multiplier()));
} 
```

这里，我们抽象了计算，现在可以用不同的计算策略重用程序，比如乘法。注意，我们将用作计算起始值的`identity`元素放入各种`Computer`实现中，因为它对于不同类型的计算是不同的(`0`用于加法，`1`用于乘法)。

坦率地说，这似乎不是一个很大的改进，许多程序员不会看到将计算从迭代中分离出来的好处。此外，这种方法没有考虑到我们实际上有两个嵌套的可能的抽象:操作本身(加法或乘法)和与操作配对的特定值的*身份*概念，并且不会通过该操作将该值更改为与之相关联的值。值`0`是加法的恒等式，`1`是乘法的恒等式。许多其他操作都有一个标识值。空字符串是字符串连接的标识，空列表是列表连接的标识。

当然，我们可以对身份再次使用策略模式，但是这将创建一个非常混乱的解决方案，并且大多数程序员可能会认为使用具有两个方法的接口已经足够抽象了。正如你将在后面看到的，FP 程序员不这么想。

### 使用控制反转(IoC)

另一种更面向对象的抽象计算技术叫做 [*控制反转*](https://www.sitepoint.com/three-design-patterns-that-use-inversion-of-control) 。策略模式解决方案使用组合来解决问题，而控制反转是基于继承的。

通过这种技术，我们构建了一个执行循环的抽象类，调用一个抽象方法来获取起始值，调用另一个抽象方法来进行计算:

```
static abstract class Computer {

    public final int compute(int n) {
        int result = identity();
        for (int i = 1; i <= n; i++) {
            result = doCompute(result, i);
        }
        return result;
    }

    abstract int doCompute(int a, int b);

    abstract int identity();

}

static class Adder extends Computer {

    @Override
    public int doCompute(int a, int b) {
        return a + b;
    }

    @Override
    int identity() {
        return 0;
    }
}

static class Multiplier extends Computer {

    @Override
    public int doCompute(int a, int b) {
        return a * b;
    }

    @Override
    int identity() {
        return 1;
    }
}

public static void main(String... args) {
    Computer adder = new Adder();
    System.out.println(adder.compute(5));
    Computer multiplier = new Multiplier();
    System.out.println(multiplier.compute(5));
} 
```

这种方法可能是最面向对象的一种，它可能看起来比策略模式干净得多，因为它似乎也将迭代抽象到了`Computer`类中。(顺便说一句，IoC 的这种特定用法已经被赋予了特定的名称:[*模板方法*模式](https://www.sitepoint.com/three-design-patterns-that-use-inversion-of-control#iocthroughthetemplatemethodpattern)。)但实际上我们并没有抽象迭代！我们只是在计算机课上封装了整个程序。现在让我们看看函数式程序员如何处理这个问题。

## 函数式编程中的抽象

我们现在要看看函数式程序员是如何处理抽象问题的。原则上，抽象计算与我们对策略模式所做的没有什么不同。然而，函数式编程进一步推动了抽象。

### 抽象计算

在 FP 中，实际的计算将以一种非常类似于我们对策略模式所做的方式进行抽象。我们再次创建一个`Computer`接口:

```
@FunctionalInterface
interface Computer {

    int compute(int a, int b);

} 
```

但这次`Computer`是一个*函数接口*，这或多或少意味着它只有一个抽象方法。这就是为什么它有时被称为 *SAM* 接口的原因。在 Java 中，它被称为*函数接口*，因为它可以用来表示一个函数。

因为我们使用的是 Java 8，所以不需要`Computer`接口，因为它可以用一个`IntBinaryOperator`来代替，这也将两个`int`映射到一个`int`:

```
static int compute(int n, int identity, IntBinaryOperator computer) {
    int result = identity;
    for (int i = 1; i <= n; i++) {
        result = computer.applyAsInt(result, i);
    }
    return result;
} 
```

如果我们使用带有 lambdas 的语言，比如 Java 8，我们不需要创建实现`IntBinaryOperator`的类:

```
public static void main(String... args) {
    System.out.println(compute(5, 0, (a, b) -> a + b));
    System.out.println(compute(5, 1, (a, b) -> a * b));
} 
```

### 抽象出一个系列的创作

一个函数式程序员会立即注意到，我们确实在创建一个从 1 到 n 的整数集合，并从种子开始对当前结果和每个元素连续进行计算。集合的创建可以抽象为一种方法:

```
static int[] range(int n) {
    int[] result = new int[n];
    for (int i = 0; i < n; i++) {
        result[i] = i + 1;
    }
    return result;
} 
```

### 抽象迭代

应用计算的迭代也可以抽象成一种方法:

```
static int fold(int[] collection, int identity, IntBinaryOperator computer) {
    int result = identity;
    for(int i : collection) {
        result = computer.applyAsInt(result, i);
    }
    return result;
} 
```

如果我们把这些抽象放在一个库中，我们的程序就变得简单如:

```
static int compute(int n, int identity, IntBinaryOperator computer) {
    return fold(range(n), identity, computer);
}

public static void main(String... args) {
    System.out.println(compute(5, 0, (a, b) -> a + b));
    System.out.println(compute(5, 1, (a, b) -> a * b));
} 
```

顺便说一下，我们放在一个单独的库中的这些抽象是多余的，因为 Java 8 已经有了它们，尽管略有不同:

*   `range`方法可以用 [`IntStream::range`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html#range-int-int-) 代替，它采用两个参数，开始和结束值，产生一个`IntStream`而不是一个数组。
*   这个`fold`抽象称为 [`reduce`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html#reduce-int-java.util.function.IntBinaryOperator-) 。

由此产生的程序非常简单:

```
public static void main(String... args) {
    System.out.println(IntStream.rangeClosed(1, 5).reduce(0, (a, b) -> a + b));
    System.out.println(IntStream.rangeClosed(1, 5).reduce(1, (a, b) -> a * b));
} 
```

你不认为国际奥委会的例子更简单吗？(开个玩笑。)

![Abstraction in object-oriented and functional programming](img/b2679b8a609d63cfd2da6b1be8a73b1d.png)

## 将抽象应用于更复杂的问题

前面的例子很简单，因为 Java 8 已经提供了所有使用的抽象。

但是请注意，我们还没有将抽象推到极限！我们可以抽象出`int`类型。毕竟，我们可能希望将相同的计算应用于其他类型，比如`double`，或者`BigDecimal`，甚至`String`，或者`List`。我们也可以使用一个计算来产生一个不同于集合元素类型的结果。例如，我们可以对一个字符列表应用一个操作，将每个字符添加到一个开始的`String`(顺便说一下，它可能是也可能不是空的)。

让我们看一个更复杂的例子，而不是去那里。我们将从一个非常具体的计算开始，并尽可能地抽象事物。

### 最初的问题

最初的问题是计算前一个问题的中间结果的列表。考虑到从 1 到`n`的整数之和，我们现在想要产生这个计算的中间结果的列表。例如，如果 *n = 5* ，我们要获取列表 *1，3，6，10，15* 。

这些数字被称为三角数[](https://en.wikipedia.org/wiki/Triangular_number)*，但是对于我们的例子，你需要知道的是如何计算它们:将所有小于或等于所考虑的整数相加。那么，前五个三角形数字是:*

```
1
1 + 2 = 3
1 + 2 + 3 = 6
1 + 2 + 3 + 4 = 10
1 + 2 + 3 + 4 + 5 = 15 
```

计算三角数的程序的一个简单实现可以是:

```
private static List<Integer> triangular(int length) {
    List<Integer> result = new ArrayList<>();
    for(int i = 1; i <= length; i++) { // <1>
        result.add(result.size() == 0
                ? i
                : result.get(result.size() - 1) + i); // <2>
    }
    return result;
}

public static void main(String... args) {
    System.out.println(triangular(5));
} 
```

我们可以看到，在`<1>`中，我们创建了一个 1 到 *n* 之间的整数列表，包括 1 和 T3。在`<2>`中，我们使用`result.get(result.size() - 1)`来获取列表的最后一个元素。作为第一个抽象，我们应该将这部分移到一个方法中，或者使用一个提供这种功能的集合。

### 抽象集合处理

让我们从为`List`创建`last`操作开始。我们的问题是，虽然在这个特定的例子中，当访问最后一个元素时，列表从不为空(因为我们检查大小不是`0`)，但这种情况可能发生在抽象中。万一单子空了，我们就不知道怎么办了。只有抽象方法的调用者知道，所以我们只需要让他们告诉我们做什么。这里，我们将提供传递默认值的可能性。

但是我们将进一步推进抽象。没有理由将我们自己局限于整数列表，所以我们将使用一个通用方法:

```
private static <T> T last(List<T> list, T defaultValue) {
    return list.size() == 0 ? defaultValue : list.get(list.size() - 1);
} 
```

有了这个抽象概念，我们的程序就变成了:

```
private static List<Integer> triangular(int length) {
    List<Integer> result = new ArrayList<>();
    for(int i = 1; i <= length; i++) {
        result.add(last(result, 0) + i);
    }
    return result;
} 
```

接下来，我们将抽象连续整数列表的创建。尽管 Java 提供了这个(使用`IntSream::range`方法)，我们将创建我们自己的:

```
private static List<Integer> range(int start, int end) {
    List<Integer> result = new ArrayList<>();
    for(int i = start; i <= end; i++) {
        result.add(i);
    }
    return result;
  } 
```

我们还将抽象出`fold`操作，就像我们在前面的例子中所做的那样，但是我们将关注更一般的情况，即返回值与列表元素的类型不同。回想一下，`fold`意味着迭代每个元素，对当前结果和给定元素应用一个操作，从某个初始值开始。在前面的例子中，我们称这个值为“identity ”,因为它是 givent 操作的标识。但是，我们并不局限于身份价值。我们可以选择任何值来开始计算。作为一个额外的抽象，我们将使该方法在任何可迭代的集合上工作，而不仅仅是列表:

```
private static <T, U> U fold(
        Iterable<T> elements, U initial, BiFunction<U, T, U> f) {
    U result = initial;
    for (T t : elements) {
        result = f.apply(result, t);
    }
    return result;
} 
```

我们的计划现在变成了:

```
private static List<Integer> triangular(int length) {
    return fold(range(1, length), new ArrayList<>(), (a, b) -> {
        a.add(last(a, 0) + b);
        return a;
    });
} 
```

在这个版本中，`new ArrayList<>()`是初始值，计算包括将当前值加到最后一个结果上，并将新结果追加到列表中。

关于列表，我们可能想抽象出另外两件小事:生成空列表和在返回结果列表时向列表添加值。

```
private static <T> List<T> emptyList() {
    return new ArrayList<>();
}

private static <T> List<T> append(List<T> list, T t) {
    list.add(t);
    return list;
} 
```

下面是我们使用这些抽象的程序版本:

```
private static List<Integer> triangular(int length) {
    return fold(
        range(1, length),
        emptyList(),
        (a, b) -> append(a, last(a, 0) + b));
} 
```

### 抽象计算

正如我们在前面的例子中所做的那样，我们可能希望将计算抽象化，以便不仅能够使用加法，而且能够在其他选项中使用乘法:

```
private static List<Integer> scan(
        int length, Integer z, BinaryOperator<Integer> op) {
    return fold(
        range(1, length),
        emptyList(),
        (a, b) -> append(a, op.apply(last(a, z), b)));
}

private static List<Integer> triangular(int length) {
    return scan(length, 0, (a, b) -> a + b);
}

private static List<Integer> factorial(int length) {
    return scan(length, 1, (a, b) -> a * b);
} 
```

注意`z`是约定俗成的用法，意为“零”，不是指`0`，而是指给定运算的 [*恒等式*或*中性元素*](https://en.wikipedia.org/wiki/Identity_element) ，以此类推`0`就是加法的恒等式。

我们要解决的一个问题就是如何调用这个方法。碰巧的是，这是函数式编程中一个众所周知的抽象，叫做`scan`。用乘法而不是加法来命名调用这个函数的方法很简单:它就是`factorial`套件。

### 抽象类型

当然，我们的程序可以用于任意类型，而不仅仅是数字，所以我们想对类型进行抽象:

```
private static <T> List<T> scan(
        Iterable<T> elements, T z, BinaryOperator<T> op) {
    return fold(
        elements,
        emptyList(),
        (a, b) -> append(a, op.apply(last(a, z), b)));
} 
```

注意，因为类型是通用的`scan`不能再用`range`创建元素本身，所以现在调用者需要这样做:

```
private static List<Integer> triangular(int length) {
    return scan(range(1, length), 0, (a, b) -> a + b);
}

private static List<Integer> factorial(int length) {
    return scan(range(1, length), 1, (a, b) -> a * b);
} 
```

### 对返回值使用不同的类型

同样，返回类型没有理由与集合元素类型相同，我们可以使我们的函数更加通用:

```
private static <T, U> List<U> scanLeft(
        Iterable<T> elements, U z, BiFunction<U, T, U> f) {
    return fold(
        elements,
        emptyList(),
        (a, b) -> append(a, f.apply(last(a, z), b)));
}

private static List<Integer> triangular(int length) {
    return scanLeft(range(1, length), 0, (a, b) -> a + b);
}

private static List<Integer> factorial(int length) {
    return scanLeft(range(1, length), 1, (a, b) -> a * b);
} 
```

在这里，我们从左到右扫描(意味着从列表的第一个元素开始)并带有一个`BiFunction<U, T, U>`。这个操作是左扫描，因此得名`scanLeft`。(我们可以从右边开始(虽然不是用`Iterable`，而是用`BiFunction<T, U, U>`，产生一个不同的抽象，叫做`scanRight`。)

我们现在可以将这个抽象函数用于产生字符串列表的字符列表:

```
private static List<String> constructString(List<Character> characters) {
    return scanLeft(characters, "", (s, c) -> s + c);
}

System.out.println(constructString(Arrays.asList(
    'H', 'e', 'l', 'l', 'o', ',', ' ', 'W', 'o', 'r', 'l', 'd', '!'))); 
```

该程序显示:

```
[H, He, Hel, Hell, Hello, Hello,, Hello, , Hello, W, Hello, Wo, Hello, Wor, Hello, Worl, Hello, World, Hello, World!] 
```

当然，如果在`Collection`界面中加入`scanLeft`功能就方便了。既然不是，我们就简单地把它添加到我们的函数库。

## 小心可变列表

理论上，我们也可以使用`scan`函数来产生一个给定列表的所有可能的“起始”子列表，例如:

```
private static <T> List<List<T>> starts(List<T> list) {
    return scanLeft(list, emptyList(), (lst, elem) -> append(lst, elem));
}

System.out.println(starts(Arrays.asList(1, 2, 3, 4, 5))); 
```

该程序应显示:

```
[[1], [1, 2], [1, 2, 3], [1, 2, 3, 4], [1, 2, 3, 4, 5]] 
```

但这不起作用，并产生:

```
[[1, 2, 3, 4, 5], [1, 2, 3, 4, 5], [1, 2, 3, 4, 5], [1, 2, 3, 4, 5], [1, 2, 3, 4, 5]] 
```

这里，我们陷入了*到位突变*的问题。众所周知，FP 更喜欢不可变的持久对象，而不是可变的持久对象(比如 Java 的各种集合实现)。当可变状态要在线程之间共享时，这通常是并发程序的一大优势，这意味着不同的线程可以同时访问该状态。在这里，我们面临一个更常见的问题:同一个线程在不同的时间访问可变数据，但是数据发生了变异，尽管它不应该发生变异。

问题出在`append`方法中，该方法将 as `List`和一个元素作为其参数，并返回一个带有附加元素的`List`。但是作为参数接收的列表被变异并返回，这意味着变异在方法之外是可见的。为了得到正确的结果，我们必须将方法重写为:

```
private static <T> List<T> append(List<T> list, T t) {
    List<T> result = new ArrayList<>(list);
    result.add(t);
    return result;
} 
```

这个问题被称为[](https://www.sitepoint.com/what-is-referential-transparency/)*，可能是函数式编程和其他编程范式之间最重要的区别。函数式编程不惜一切代价加强引用透明性。但这是[的另一个故事](https://www.sitepoint.com/what-is-referential-transparency/)。*

 *## 摘要

在本文中，我们看到了函数式编程如何将抽象推向极限。这不仅仅是复用性的问题！抽象每个概念可以让我们理解这些概念是如何关联的。它显示了初看起来完全不同的操作中的相似之处。有了这些新知识，我们理解了以前看似不相关的概念之间的关系。

那么，过早的抽象是否像过早的优化所说的那样是邪恶的呢？注意到抽象和优化(为了性能)是两个相反的东西确实很有趣。优化通常是通过只处理手头问题的特殊性来完成的。另一方面，抽象包括寻找问题的最一般的表示。

考虑到初始问题(直到`n`的整数之和)，使用公式`(n * (n + 1) / 2)`确实是一种优化，考虑到应用于折叠整数列表的操作的特定性质，以及这些整数的特定性质。好的一面是这是计算结果最快的方法。不好的一面是，如果你改变运算(例如计算整数的乘积直到`n`，它没有任何帮助，你必须写一个完整的新程序。更糟糕的是，与抽象的解决方案不同，这个解决方案不能计算任何其他整数列表的总和。

使用抽象的解决方案，您只需更改操作和标识，就可以将它应用于任何整数列表，甚至任何类型的任何元素列表。实际上，您所做的是发现一种特定的操作，它可以应用于任何类型的列表和函数。这个操作叫做扫描。计算从`1`到`n`的整数之和就是用加法扫描这个整数列表。计算`n!`是用乘法扫描同一个列表。并且任何类型为`List<T>`的列表都可以用类型为`BiFunction<U, T, U>`的函数来扫描，以获得类型为`List<U>`的结果。

结论是，无论您是在进行函数式编程还是命令式编程，理解手头问题的本质通常会更好，这就是将抽象推向极限将带给您的结果。然后，您可以选择另一个更具体和更快的解决方案，但这应该仍然是优化。

本文提出的一个概念是，有些集合是“可折叠”的。如果我们有更多的空间(可能在另一篇文章中)，分析这个概念如何与 Java 8 中的`Optional.orElse`相关可能会很有趣。然后，我们可以认识到，这两个概念实际上是两个更简单的普通概念的两种不同组合。顺便提一下，我们还发现了函数和非函数范式之间的一个重要区别，称为参照透明性。* 

## *分享这篇文章**