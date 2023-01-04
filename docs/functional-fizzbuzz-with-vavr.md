# 用 Vavr 实现 FizzBuzz 功能的六种方法

> 原文：<https://www.sitepoint.com/functional-fizzbuzz-with-vavr/>

### 目录 

*   [在我们开始之前](#beforewegetstarted)
*   [命令式解决方案和功能推理](#imperativesolutionandfunctionalreasoning)
*   [Vavr 流](#vavrstream)
*   [测试线束](#testharness)
*   [fizz buzz 的六种方式](#6waystofizzbuzz)
*   [1。一个简单的功能](#1asimplefunction)
*   [2。模式匹配](#2patternmatching)
*   [3。组合子模式](#3combinatorpattern)
*   [4。压缩流](#4zippedstreams)
*   [5。压缩转换流](#5zippedtransformationstream)
*   [6。一个轮子](#6awheel)
*   [总结](#summary)
*   [评论](#comments)

去年的某个时候，我偶然发现了一篇关于用 JavaScript 做 FizzBuzz 的二十种方法的精彩文章。我问自己，Java 8 和 [Vavr](http://www.vavr.io/) ( [以前叫 Javaslang](https://twitter.com/vavr_io/status/853014593303609346) )中的一些函数式解决方案会是什么样子。在这篇文章中，我提出了六种不同的解决方案，使用函数数据结构、高阶函数和属性检查。所有这些解决方案都符合我在上一篇文章中介绍的测试工具。

## 在开始之前

我写这篇文章的动机是尝试不同的函数式方法，以适应更函数式的思维。提出的解决方案不一定按照从坏到好的线性顺序排列。它们只是对同一只猫的剥皮方式不同，有不同的取舍，这也正是我想探究的。如果你知道另一个解决方案或想讨论一个已提出的方案，请留下评论。

在我们开始实际的功能解决方案之前，我们将看一个必要的解决方案并讨论它的职责。此外，我们做了一些函数推理，并介绍了我们将广泛使用的流数据结构。最后，讨论了 FizzBuzz 函数应该满足的性质。

### 命令式解决方案和功能推理

一个简单的命令式解决方案准确地说明了如何从 1 数到 100，如何计算模，以及如何决定什么时候打印什么。这就像遵循一个食谱。

```
public void fizzBuzzFrom1to100(){
    for(int i = 1; i <= 100; i++){
        if (i % 3 == 0 && i % 5 == 0) {
            System.out.println("FizzBuzz");
        } else if (i % 3 == 0) {
            System.out.println("Fizz");
        } else if (i % 5 == 0) {
            System.out.println("Buzz");
        } else {
            System.out.println(i);
        }
    }
} 
```

从外部的角度来看，除了打印到控制台之外，该方法不显示任何其他行为。测试是相当乏味的，因为为了验证计算结果，我们需要模拟`System.out`并计算调用次数。但是为了进行嘲讽，我们需要知道内部。

当我们冒险查看方法内部时，我们看到它负责计算和显示结果。我们可以将计算逻辑分解到自己的函数中，并在外层使用辅助方法来打印结果。

```
public List<String> fizzBuzz(int amount) {
    List<String> ret = new ArrayList<>();
    for (int i = 1; i <= amount; i++) {
        if (i % 3 == 0 && i % 5 == 0) {
            ret.add("FizzBuzz");
        } else if (i % 3 == 0) {
            ret.add("Fizz");
        } else if (i % 5 == 0) {
            ret.add("Buzz");
        } else {
            ret.add(String.valueOf(i));
        }
    }
    return ret;
}

public void printList(List<String> list){
    for(String element: list){
        System.out.println(element);
    }
}

printList(fizzBuzz(100)); 
```

测试`fizzBuzz`函数变得很容易，因为函数所做的一切都由返回的列表表示。我们可以检查列表中任何元素的值。

给定一个特定的输入，`fizzBuzz`函数将总是产生相同的输出。我们可以用计算列表替换函数 application `fizzBuzz(100)`,而不改变程序的含义。也就是说，`fizzBuzz`函数是*纯*。

理由是，有副作用的方法总是可以分解成一个纯函数和两个提供输入和处理输出的副作用方法。也就是说，我们将副作用推到了应用程序的边界。所有提出的解决方案都遵循这一原则，将迭代和显示的责任转移到 Vavr 的流数据结构。这些函数的外壳代码如下所示。

```
public static void main(String... args){
    fizzBuzzStream(100).forEach(System.out::println);
}

private static Stream<String> fizzBuzzStream(int amount){
    return Stream.from(1)
        .map(fizzBuzz())
        .take(amount);
} 
```

在下一节中，我们将更深入地研究流，但是让我指出这段代码的重要部分。首先，在`fizzBuzzStream`函数中，我们创建了一个整数流，它从 1 开始计数`from`。然后，我们在一个`fizzBuzz`函数的帮助下`map`这些整数，该函数为一个给定的数字计算一个`FizzBuzz`元素。这是大多数解决方案将实现的部分。接下来，我们通过使用`amount`元素将无限流转换为有限流。在`main`方法中，这些元素被印刷在控制台上。

注意流是如何隐藏细节的，比如迭代和创建。我们在一个非常声明性的层次上操作，在表达我们的程序时变得非常简洁。还要注意，打印元素的副作用是最后执行的操作。实际上，它是我们程序的最外层。

### Vavr 流

[功能数据结构](http://www.vavr.io/vavr-docs/#_data_structures_in_a_nutshell) `Stream`可以是`Empty`或`Cons`。一个`Cons`由一个头部元素和一个懒惰计算的尾部`Stream`组成。无限的`Stream`不包含`Empty`尾。

我们写`Stream.cons(1, () -> Stream.empty())`来定义一个包含 1 作为头和一个空尾的流。按照这种符号，定义 1 的无限流可能看起来像这样:`Stream.cons(1, () -> Stream.cons(1,...))`。然而，这在 Java 中不是有效的语法，而且会相当乏味。因此，我们使用一个递归函数来按需计算下一个 1。

```
Stream<Integer> ones() {
    return Stream.cons(1, () -> ones());
} 
```

Java [在方法被调用之前评估方法参数](https://www.sitepoint.com/java-in-praise-of-laziness/)。在无限流的情况下，这是用一个`Supplier`欺骗的，以防止堆栈溢出。这也允许流的存储器有效表示，因为只有头部被保存在存储器中。这不同于包含所有元素的`List`,也不同于 Java 8 流，Java 8 流是对来自某个源的数据元素序列的组合聚合操作。

### 试验用接线

FizzBuzz 元素流应该满足三个不变量:

*   每三个元素中就应该有一个以 Fizz 开头
*   每个 5 的倍数都必须以 Buzz 结尾
*   每一个非第三和非第五元素都应该是一个数字

在基于属性的测试(PBT)中，每个不变量都与一个样本生成器相结合，形成一个属性。例如，在 Fizz 不变量的情况下，发生器会创建 3 的倍数。对于每个生成的样本，检查不变量。如果一个检查结果为假，那么这个属性就被认为是*伪造的*。否则，就叫*得意*。

关于更全面的介绍和 FizzBuzz 测试工具的构建，请看我的文章《基于属性的 Vavr 测试》。

![Little Mike and Evelyn are happy that they coded a functional solution to FizzBuzz with Vavr](img/1b1dbdf30875178027b35419d185ddd5.png)

## FizzBuzz 的六种方法

前三个解决方案是关于编写计算 FizzBuzz 元素的纯函数。这些功能的核心是将一个数字映射到一个单词。这些信息通过它们的类型公开:`Function<Integer, String>`。事实上，这三种实现中的任何一种都可以用来替换其他任何一种。也就是说，他们是透明的 T2。

在第四个和第五个解决方案中，我们看看如何利用流上的函数来创建 FizzBuzz 解决方案。我们总是停留在流上下文中，返回类型是`Stream<String>`。

第六个也是最后一个解决方案探索了由 [Pierre-Yves](https://www.sitepoint.com/author/pysaumont/) 贡献的另一种方法。

### 1.简单的功能

这是命令式解决方案的计算核心。根据模运算，返回相应的 FizzBuzz 元素。

```
static Function<Integer, String> fizzBuzz(){
    return i -> {
        if (i % 3 == 0 && i % 5==0) {
            return "FizzBuzz";
        } else if (i % 3 == 0) {
            return "Fizz";
        } else if (i % 5 == 0) {
            return "Buzz";
        } else {
           return i.toString();
        }
   }
} 
```

我喜欢这个解决方案的原因是，它非常直接，并且它表明一个纯函数可以从一个有副作用的方法中分解出来。然而，这个解决方案仍然非常不灵活，看起来像是一个强制性的解决方案。例如，如果我们想添加另一个用 *Bizz* 替换 4 的每个倍数的例子，我们需要添加另一个子句。

### 2.模式匹配

这个解决方案与第一个不同，它使用模式匹配而不是 if 语句。把[模式匹配](http://blog.vavr.io/pattern-matching-essentials/)想象成类固醇上的[开关语句](https://www.sitepoint.com/javas-switch-statement/)，它检查表达式的结构，没有*失败*语义。

```
static Function<Integer, String> withPatternMatching(){
    return e -> Match(Tuple.of(e % 3, e % 5)).of(
        Case(Tuple2($(0),$(0)), "FizzBuzz"),
        Case(Tuple2($(0), $()), "Fizz"),
        Case(Tuple2($(), $(0)), "Buzz"),
        Case($(), e.toString())
    );
} 
```

我们将计算结果的元组与案例中描述的模式进行匹配。如果大小写匹配，则返回右侧的字符串。例如，如果一个数既能被 3 整除，又能被 5 整除，则返回`"FizzBuzz"`。

这个解决方案比第一个更简洁，我们去掉了显式的 if 语句。然而，我们变得更加不灵活。例如，为了测试另一个数字，我们现在需要扩展 match 子句并添加另一个 case。

### 3.组合子模式

在这个解决方案中，我使用了[组合子模式](https://gtrefs.github.io/code/combinator-pattern/)。

```
static Function<Integer, String> withCombinator(){
    return fizzBuzz()
        .orElse(fizz())
        .orElse(buzz())
//      .orElse(word("Bizz").ifDivisibleBy(4))
        .orElse(number());
}

interface NumberToWord extends Function<Integer, String> {

    static NumberToWord fizzBuzz(){
        return fizz().and(buzz());
    }

    static NumberToWord fizz(){
        return word("Fizz").ifDivisibleBy(3);
    }

    static NumberToWord buzz(){
        return word("Buzz").ifDivisibleBy(5);
    }

    static NumberToWord word(String word){
        return i -> word;
    }

    static NumberToWord number(){
        return Object::toString;
    }

    default NumberToWord ifDivisibleBy(int modulo){
        return i -> i % modulo == 0 ? apply(i) : "";
    }

    default NumberToWord orElse(NumberToWord other){
        return i -> {
            String result = apply(i);
            return result.isEmpty()
                ? other.apply(i)
                : result;
        };
    }

    default NumberToWord and(NumberToWord other){
        return i -> {
            String first = this.apply(i);
            String second = other.apply(i);
            return (first.isEmpty() || second.isEmpty())
                ? "";
                : (first + second);
        };
    }
} 
```

combinator 模式帮助我们用 FizzBuzz 领域的术语编写一点嵌入式领域特定语言。更重要的是，语言的形成方式允许我们在不改变核心类型的情况下为数字添加新单词。

我喜欢这个解决方案的声明性，但是在简洁性方面有所欠缺。不过，可读性的提高(假设您内化了 combinator 模式)足以弥补这一点。

### 4.压缩流

在这个解决方案中，我们对 FizzBuzz 问题有一个不同的观点，允许我们根本不计算任何元素。这个想法是将每个 FizzBuzz 子序列，例如走向`nothing, nothing, "Fizz"`的子序列，视为它自己的流。

```
static Stream<String> withZippedStreams(){
    return Stream.of("","","Fizz").cycle()
            // creates a Stream<Tuple2<String, String>>
            .zip(Stream.of("","","","","Buzz").cycle())
            // creates a Stream<Tuple2<Tuple2<String, String>, Integer>>
            .zip(Stream.from(1))
            // flattens Tuple2<Tuple2<String, String>, Integer> to String
            .map(flatten());
}

private static Function<Tuple2<Tuple2<String, String>, Integer>, String>
        flatten() {
    return tuple -> {
        String fizzBuzz = tuple._1._1 + tuple._1._2;
        String number = tuple._2.toString();
        return fizzBuzz.isEmpty()
            ? number
            : fizzBuzz;
    };
} 
```

每第三个元素应该是 Fizz 被表示为在两个空元素和第三个元素是“Fizz”的序列上循环的流。这导致了一个无限重复这个序列的流。同样的想法也适用于 Buzz 元素。

使用`zip`功能合并两个流。这个函数的名字说明了它的作用:它将两个流绑定成一个元组流(参见 [zipper](https://en.wikipedia.org/wiki/Zipper) )。

这个压缩流再次与另一个包含从`1`开始的整数的流合并。`flatten`函数通过决定实际元素应该是什么来映射结果。例如，如果 Buzz 和 Fizz 流中的两个元素都为空，则该元素将成为数字。

我喜欢这个解决方案，因为它遵循了一种打破常规的思想。如果我所有的一切都是一条小溪呢？

### 5.压缩转换流

与之前的解决方案不同，我们现在正在压缩函数流。

```
static Stream<String> withFunctions(){
    return fizzBuzzTransformations()
            .zip(Stream.from(1))
            .map(functionAndNumber ->
                    functionAndNumber._1.apply(functionNumber._2));
}

private static Stream<Function<Integer, String>> fizzBuzzTransformations() {
    Function<Integer, String> empty =  i -> "";
    return Stream.of(empty, empty, i -> "Fizz").cycle()
            // creates Stream<Tuple2<Function<Int, Str>, Function<Int, Str>>>
            .zip(Stream.of(empty, empty, empty, empty, i -> "Buzz").cycle())
            // flattens Tuple2<Function<Int, Str>, Function<Int, Str>>
            // to Function<Int, Str>
            .map(flatten());
}

private static Function<
            Tuple2<Function<Integer, String>, Function<Integer, String>>,
            Function<Integer, String>>
        flatten() {
    return functions -> i -> {
        String fizzBuzz = functions._1.apply(i) + functions._2.apply(i);
        return fizzBuzz.isEmpty()
            ? i.toString()
            : fizzBuzz;
    };
} 
```

例如，Fizz 元素被转换成一个流，在三个函数的序列中循环。前两个元素是空函数，对于给定的整数不返回任何值。最后一个元素是 Fizz 函数，它返回给定数字的 Fizz。蜂鸣元素被等效地翻译。

有趣的部分是我们如何映射函数的元组流。对于给定的元组，我们返回一个函数，该函数接受一个数字并应用元组中的函数。也就是说，对于流中的每个元组，我们组成一个新函数，它使用前面的函数来决定实际的元素是什么。例如，如果两个元素都是空函数，则实际元素变成数字。这类似于上一个解决方案中的`flatten`函数。

合成函数的结果流与从`1`开始计数的流一起压缩。剩下唯一要做的就是将每个元组中的函数应用到数字上。

我非常喜欢这个解决方案，因为它展示了函数是可以传递的值。

### 6.一个轮子

在我们的[内部审查会议](https://www.sitepoint.com/behind-the-scenes-sitepoints-peer-review-program/)期间， [Pierre-Yves](https://www.sitepoint.com/author/pysaumont/) 注意到并指出一个*轮子*是两个基于流的解决方案的基本模式。轮子是元素周期序列的隐喻。重复的子序列从 *1* 开始，以第一个 *FizzBuzz* 结束。当您将子序列的元素平均分布在一个轮子上时，确定下一个元素就是将轮子推进到下一个元素。Pierre-Yves 用下面的代码形象化了这个想法。

```
public static void main(String... args) {
    String[] wheelArray = new String[] {
            "", "", "Fizz", "", "Buzz",
            "Fizz", "", "", "Fizz", "Buzz",
            "", "Fizz", "", "", "FizzBuzz"};
    Stream<String> fizzBuzz = fizzBuzzStream(wheelArray);
    fizzBuzz.take(30).forEach(System.out::println);
}

private static Stream<String> fizzBuzzStream(String[] wheelArray) {
    return Stream
            .iterate(new Wheel(wheelArray), Wheel::next)
            .map(Wheel::getValue);
}

static class Wheel {

    private final String[] wheel;
    private final int numValue;
    private final String value;

    private Wheel(String[] wheelArray) {
       this(wheelArray, 0);
    }

    private Wheel(String[] wheelArray, int n) {
        this.wheel = wheelArray;
        this.numValue = n + 1;
        int index = n % wheel.length;
        this.value = wheel[index].length() == 0
            ? Integer.toString(numValue)
            : wheel[index];
    }

    private String getValue() {
        return this.value;
    }

    private Wheel next() {
        return new Wheel(this.wheel, this.numValue);
    }
} 
```

有趣的部分是使用一个不可变的类`Wheel`来跟踪状态，以及[方法引用一个任意类型的实例方法](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html)。轮子有一个初始的元素序列。然后，它计算数值、索引和由车轮位置确定的值。如果轮数组中位于索引位置的元素不为空，它将成为值。否则，数字值会变成值。

构造的流迭代轮子，由`next`函数返回，每一轮都比前一轮更进一步。然后使用`Wheel::getValue`将这个无限的轮子流映射到计算出的 FizzBuzz 元素。

## 摘要

FizzBuzz 问题很容易理解。尽管如此，还是有很多方法可以解决这个问题。在本文中，我们集中讨论了使用 [Vavr](http://www.vavr.io/) 库的功能解决方案。

我们首先讨论了一个必要的解决方案，以及如何构建一个功能核心。这让我们对如何使用函数式程序进行推理进行了更广泛的讨论。这里的关键是，每一个非函数方法都可以转化为一个纯函数和两个提供输入和处理输出的方法。副作用被推到了我们程序的边界。

在文章的第二部分，我们看了 FizzBuzz 问题的 6 种不同的解决方案。前三个解决方案侧重于计算给定数字的 FizzBuzz 元素，而后两个解决方案侧重于在流上使用函数。所有解决方案都有一些优点和缺点。这里的关键见解是，跳出框框思考可能会带来意想不到的解决方案。

你知道另一个功能性的 FizzBuzz 解决方案或者一个简单的问题可以用不同的方法解决吗？留言评论！

## 分享这篇文章