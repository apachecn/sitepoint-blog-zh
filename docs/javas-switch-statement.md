# Java 的 Switch 语句三分钟

> 原文：<https://www.sitepoint.com/javas-switch-statement/>

### 目录 

*   [使用开关](#usingswitch)
*   [开关语法](#syntaxofswitch)
*   [跌落](#fallthrough)
*   [返回内部开关](#returninsideswitch)
*   [其他类型](#othertypes)
*   [总结](#summary)
*   [评论](#comments)

Java 的 switch 语句允许根据变量值从许多执行路径中选择一个。变量必须是 enum、`String`或类似`int`的整数类型。对于`switch`,它会与一系列`case`标签进行比较，每个标签指定一个值——一旦第一个标签匹配，就会执行相应的语句块。switch 语句的工作方式很像一条长长的`if`–`else`–`if`链，通常可以用来替换它。

这篇文章只需要关于[整数](https://docs.oracle.com/javase/8/docs/api/java/lang/Integer.html)和[字符串](https://docs.oracle.com/javase/tutorial/java/data/strings.html)的工作知识，但是你对 [`if`了解得越多，特别是`if`–`else`–`if`](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/if.html)你将从中获益越多。拥有其他[数字类型](https://docs.oracle.com/javase/tutorial/java/data/numberclasses.html)、[枚举](https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html)和[方法](https://docs.oracle.com/javase/tutorial/java/javaOO/methods.html)的经验是额外的奖励。

## 使用`switch`

让我们直接从一个例子开始吧！以下 switch 语句将前三个自然数的文本表示形式写入控制台:

```
// any number is fine
int number = 2;
switch (number) {
    case 0:
        System.out.println("zero");
        break;
    case 1:
        System.out.println("one");
        break;
    case 2:
        System.out.println("two");
        break;
    default:
        System.out.println("many");
        break;
} 
```

仅仅从外观上看，你认为会发生什么？

开关将查看当前为`2`的`number`，并将其与`case`关键字后面的每个值进行比较。不是`0`，不是`1`，是`2`。[宾果！](https://www.youtube.com/watch?v=UMRo5XCKddQ)所以它开始叫`System.out.println("two")`。

### `switch`的语法

您可以将`switch`语句与类型为`int`、`byte`、`short`、`char`(注意`long`不起作用)、`String`或 enum(或正式名称为的*枚举类型)的变量一起使用。它是这样工作的:*

```
switch (<variable>) {
    case <value>:
        // statements
        break;
    case <other-value>:
        // statements
        break;
    case <more-values>:
        // statements
        break;
    default:
        // statements
        break;
} 
```

您使用关键字`switch`，后跟您想要用*切换的变量*(通常的说法)和一对花括号。在这些花括号中，你可以列出任意多的分支。

每个常规分支由关键字`case`、一个匹配变量类型的值(意味着它可以被赋给变量)和一个冒号组成。这三样东西合在一起被称为*开关标签*。如果变量有特定的值，那么后面是您想要执行的语句。除非你有非常好的理由，否则每个开关分支都应该以`break`结束。(我一会儿解释，为什么。)

如果您需要一个在标签都不匹配的情况下执行的分支，您可以用`default`创建一个。它的工作方式很像一个普通的分支，但是没有值。

### 失败

值得注意的是，switch 语句不仅执行匹配的分支。相反，它*开始在匹配的标签处执行代码*。如果它没有碰到一个`break`，它将直接进入下一个分支。这叫做*穿越*，可以有意使用。

以下语句从给定的数字开始计数:

```
// any number is fine
int number = 1;
switch (number) {
    case 0:
        System.out.println("zero");
        // fall-through intended
    case 1:
        System.out.println("one");
        // fall-through intended
    case 2:
        System.out.println("two");
        // fall-through intended
    default:
        System.out.println("many");
} 
```

在这种情况下，输出为`one two many`，因为`number`与`1`匹配，并且 switch 执行所有块，因为没有中断。注释“fall-through intended”告诉你的程序员同事，你是故意这样做的，而不仅仅是忘记添加`break`。

### 返回内部`switch`

许多开发人员认为重复`break`很难看，并试图将开关推入方法中，以便它们可以返回值。因为一个`return`结束了该方法的执行，所以没有失败的风险，因此不需要`break`。

对于第一个示例，代码可能如下所示:

```
public String toEnglish(int number) {
    switch (number) {
        case 0: return "zero";
        case 1: return "one";
        case 2: return "two";
        default: return "many";
    }
} 
```

短多了，不是吗？执行`System.out.println(toEnglish(2))`将会打印`two`。

### 其他类型

我已经提到过，你可以切换的不仅仅是`int` s。其他允许切换的原语有`byte`、`short`和`char`。您还可以切换包装器类型`Integer`、`Byte`、`Short`和`Character`，但是请注意，如果变量是`null`，您将得到一个`NullPointerException`。

您也可以切换到`String` …

```
public int toNumber(String number) {
    switch (number) {
        case "zero": return 0;
        case "one": return 1;
        case "two": return 2;
        default: throw new IllegalArgumentException();
    }
} 
```

…和枚举。

```
public enum LittleNumber { ZERO, ONE, TWO }

public int toNumber(LittleNumber number) {
    switch (variable) {
        case ZERO: return "zero (0)";
        case ONE: return "one (1)";
        case TWO: return "two (2)";
        default: return "unknown";
    }
} 
```

![](img/9a776a53ac81c39cbafd6ad1b63cd549.png)

## 摘要

您了解了 switch 语句是如何与`switch`关键字、标签(`case`、一个值和一个冒号或者`default`和一个冒号)以及代码块放在一起的。在第一次匹配时，代码被执行，直到它遇到一个`break`，这可能意味着它落入下面的分支。为了保持开关简短，通常将它们放入方法中并返回结果，从而避免中断。

有了这些知识，你就可以更深入地探索。如果你想知道为什么不能使用`long`，试试[这个 StackOverflow 问题](http://stackoverflow.com/q/2676210/2525313)。此外，您可能很快就会希望有办法给`case`分支更多的表达能力，例如通过检查一个数值是否比另一个小。然后你会想到模式匹配，这是一个 Java 还不直接支持的特性[，但可能很快就会支持](https://www.sitepoint.com/what-java-might-one-day-look-like/#patternmatching)。在那之前，库实现可以作为替代品，Javaslang 有一个很好的选择。

## 分享这篇文章