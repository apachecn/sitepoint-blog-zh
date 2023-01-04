# 琥珀计划将如何革新 Java

> 原文：<https://www.sitepoint.com/project-amber-will-revolutionize-java/>

### 目录 

*   [革命战士](#revolutionaryfighters)
*   [局部变量类型推理](#localvariabletypeinference)
*   [增强枚举](#enhancedenums)
*   [λ剩菜](#lambdaleftovers)
*   [数据类别](#dataclasses)
*   [模式匹配](#patternmatching)
*   [琥珀项目](#projectamber)
*   [发布日期](#releasedate)
*   [关于那个名字……](#aboutthatname)
*   [评论](#comments)

这是我们每隔一周的周五发出的 SitePoint Java Channel 时事通讯的社论。[在此订阅！](https://www.sitepoint.com/newsletter/)

本周早些时候，甲骨文公司的 Java 语言架构师 Brian Goetz[正式宣布了](http://mail.openjdk.java.net/pipermail/amber-dev/2017-March/000000.html)[*Amber*](http://openjdk.java.net/projects/amber/)项目，对此我非常兴奋！它将延续 Java 8 开创的事业，让 Java 成为一种更简洁、更有趣的语言。类型推理、大规模简化的数据类、模式匹配……所有这些在最近几个月都在[冒泡](https://www.sitepoint.com/what-java-might-one-day-look-like/)，但是很高兴看到它正式上线。

(虽然这对于 Java 来说可能是新的，但非 Java 开发人员可能会嘲笑并瞧不起我们，比如说，“我们已经用了十年了。”嗯，对你来说很好，但没有理由给我们泼冷水，所以 stfu！)

## 革命战士

我相信你迫不及待地想看到我承诺的那些改进，所以让我们先做那个。一旦我们探索了它们，我将花一些时间在琥珀计划和发布日期推测上。

(如果你有兴趣看看这些提议和值类型如何一起发挥作用，看看[未来的 Java 可能会是什么样子](https://www.sitepoint.com/what-java-might-one-day-look-like/))。

### 局部变量类型推理

从 Java 5 开始，Java 就已经完成了类型推断(用于泛型方法中的类型见证)，并且在 Java 7(菱形运算符)、8 (lambda 参数类型)和 9(匿名类上的菱形)中扩展了该机制。在 Amber 项目下，计划将它扩展到局部(！idspnonenote)的类型声明。)变量:

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

这里，`url`、`conn`和`reader`的类型非常明显。因此，编译器可以推断出它们，使我们没有必要指定它们。

注意，类型推断不是动态类型化——它仍然是强类型化，只是减少了类型化(Brian 的双关语，可能是有意的)。类型信息仍然会出现在字节码中，ide 也能够显示它们——只是我们不再需要把它们写出来。

更多信息，请看一下 [JEP 286](http://openjdk.java.net/jeps/286) 。

### 增强型枚举

目前，枚举不能是泛型的，这意味着不能给单个枚举实例指定特定类型的字段:

```
// now
public enum FavoriteNumber {

    INTEGER(42),
    FLOAT(3.14);

    // wouldn't it be nice if this were more specific than Number?
    public final Number favorite;

    FavoriteNumber(Number favorite) {
        this.favorite = favorite;
    }

} 
```

Amber 项目考虑让枚举拥有类型参数:

```
// maybe in the future
public class FavoriteNumber<T extends Number> {

    INTEGER<Interger>(42),
    FLOAT<Float>(3.14);

    // wouldn't it be nice if this were more specific than Number?
    public final T favorite;

    FavoriteNumber(T favorite) {
        this.favorite = favorite;
    }

} 
```

最棒的是，编译器将执行严格的类型检查，并且*知道*一个特定的枚举实例具有哪些泛型类型:

```
// maybe in the future
float favorite = FavoriteNumber.FLOAT.favorite; 
```

更多信息，请看一下 [JEP 301](http://openjdk.java.net/jeps/301) 。

### λ剩菜

很棒的名字，嗯？这些是对 lambda 表达式和方法引用的一些小改进。首先，在必须选择若干重载之一的情况下，编译器会更好地选择 lambda 表达式或方法引用的目标:

```
// now; compile error: "ambiguous method call"
method(s -> false)

private void method(Predicate<String> p) { /* ... */ }
private void method(Function<String, String> f) { /* ... */ } 
```

奇怪的是，编译器认为这是不明确的，因为对我们来说，它根本不是。当 lambda 剩余物被实现时，编译器同意。

Java 8 不赞成将`_`作为变量名，也不允许将其作为 lambda 参数名。Java 9 也不允许使用它作为变量名，所以它可以自由地获得特殊的含义，即标记未使用的 lambda 参数(是的，在同一个表达式中不止一个):

```
// maybe in the future

// ignore the parameter marked as `_`
BiFunction<Integer, String, String> f3 = (i, _) -> String.valueOf(i);

// if there were a TriFunction:
BiFunction<Integer, String, String, String> f3 = (i, _, _) -> String.valueOf(i); 
```

最后，Amber 项目探索了让 lambda 参数在封闭范围内隐藏变量的可能性。这将使它们的命名变得稍微容易一点，因为照目前的情况来看，这可能有点麻烦:

```
private Map<String, Integer> wordLengthCache;

// now
public int computeLength(String word) {
    // can't reuse `word` in lambda, so... maybe `w`?
    wordLengthCache.computeIfAbsent(word, w -> w.length());
}

// maybe in the future
public int computeLength(String word) {
    // can't reuse `word` in lambda, so... maybe `w`?
    wordLengthCache.computeIfAbsent(word, word -> word.length());
} 
```

更多信息，请看一下 [JEP 302](http://openjdk.java.net/jeps/302) 。

### 数据类别

我们都创建了大量简单的数据容器类，这些类需要几十行代码用于字段、构造函数、访问器、`equals`、`hashCode`、`toString`。虽然 ide 很乐意生成所有这些内容，即使在今天也不需要输入，但仍然需要理解代码(构造函数做任何验证吗？)并进行维护(最好不要忘记将这个新字段添加到 equals 中)。

为了减少样板文件，编译器可能会在不需要我们动手的情况下动态生成所有这些东西！

```
// maybe in the future
public class User(String firstName, String lastName, DateTime birthday) { } 
```

我们可以免费获得我上面提到的所有东西，并且只需要实际实现非标准的东西(也许用户有一个单独决定相等性的 ID，所以我们想要一个相应的`equals`实现)。去掉所有这些代码将极大地提高可维护性！

(这个提议还没有 JEP，但是 Amber 会尽快采纳。)

### 模式匹配

Java 目前的`switch`语句相当弱。你可以把它用于原语，枚举和字符串，但仅此而已。如果你想做任何更复杂的事情，你要么求助于`if`—`else`—`if`链，或者，如果你不能把四人帮的书从你的脑海中抹去，你可以求助于[访客模式](https://en.wikipedia.org/wiki/Visitor_pattern)。

琥珀计划探索的是通常所说的模式匹配。它适用于所有类型，可以有比相等检查更复杂的条件，并且是一个表达式，这意味着它产生一个可以赋值的值。几个月前，Brian [在比利时 Devoxx】展示了一个例子:](https://www.youtube.com/watch?v=oGll155-vuQ)

```
// maybe in the future
String formatted = switch (constant) {
    case Integer i -> String.format("int %d", i);
    case Byte b: //...
    case Long l: // ...
    // ...
    default: formatted = "unknown"
} 
```

(这个提议还没有 JEP，但是 Amber 会尽快采纳。)

![Project Amber. Because, why not?](img/4d74b05c0dad31548bef216f58ab93e0.png)

## 琥珀计划

既然我们已经知道了琥珀项目目前正在探索哪些特性，那么是时候更仔细地研究一下项目本身了。在[欢迎邮件](http://mail.openjdk.java.net/pipermail/amber-dev/2017-March/000000.html)中，它被描述为“选定的面向生产力的 Java 语言 jep 的孵化地”不严格地说，它的范围被定义为专家组认为有趣的任何 JEP，并且与总体使命声明一致:

> 为了被琥珀项目考虑采用，一个特性应该首先由一个 [JEP](http://openjdk.java.net/jeps/1) 来描述。这意味着这不是一个讨论随机语言特性想法的地方(互联网的其余部分仍然是可用的)；让我们把重点放在已经采用的具体特性上。

阅读[邮件](http://mail.openjdk.java.net/pipermail/amber-dev/2017-March/000000.html)获得 jep、邮件列表和回购的链接。

### 出厂日期

一个自然的问题是，这个项目什么时候发布？你什么时候能开始写我一直承诺的那个很棒的代码？最负责任的回答是没人知道。

抛开责任，我们可以稍微推测一下。带来 Lambda 表达式和流的 Project Lambda 从 2009 年 12 月开始到 2014 年 3 月首次发布花了四年多一点的时间。[以延期闻名的拼图项目](http://openjdk.java.net/projects/jigsaw)，早在一年前的 2008 年 12 月就开始了，直到现在才发货，耗时近九年。不过，请记住，甲骨文在 2010 年收购了 Sun，我只能假设工程工作受到了转型的负面影响。此外，在我看来，这两个项目都比 Amber 复杂和庞大得多(尽管从外部很难判断)。

Amber 项目更多的是为各种变化提供一个平台，这些变化将语言推向一个共同的方向，但不一定要马上推出才有意义。在这种假设下，并根据 Lambda 和 Jigsaw 猜测项目的开发时间，一些变化将使其进入 Java 10 似乎是合理的。

问之前:没人知道 10 什么时候出！再次猜测一下，早在 2012 年，Java 平台组的首席架构师 Mark Reinhold[提出了两年的发布周期](http://mreinhold.org/blog/late-for-the-train)，由于延迟，这个周期变成了三年。从两个数据点推断未来(幸运的是我在这里不受科学标准的约束)，2020 年看起来是个不错的猜测。

请记住，[项目 Valhalla](http://openjdk.java.net/projects/valhalla) 始于 2014 年 7 月，承担了实现[通用专门化和值类型](https://www.sitepoint.com/jvmls-2016#projectvalhalla)的巨大任务。它还没有任何版本的目标，但看起来它在进入 10 的时间框架内。如果是这样的话，由于其复杂性，它很可能会推迟发布。

所以底线是:没人知道。如果要我打赌，我会赌 2020 年。手指交叉。

### 关于那个名字…

哦，对了，为什么它被称为琥珀项目？因为有光泽？因为它保存了死去的东西？我不知道。

我没有花不必要的时间去思考这个问题，而是选择去问布莱恩本人，但他要么非常神秘，要么非常坦率:

> [@nipafx](https://twitter.com/nipafx) 有何不可？
> 
> —布莱恩·戈茨(@ BrianGoetz)[2017 年 3 月 17 日](https://twitter.com/BrianGoetz/status/842741782517628928)