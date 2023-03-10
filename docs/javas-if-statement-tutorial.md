# 五分钟后 Java Else If 语句

> 原文：<https://www.sitepoint.com/javas-if-statement-tutorial/>

### 目录 

*   [`if`声明](#theifstatement)
*   [`if else`声明](#theifelsestatement)
*   [`if else if`声明](#theifelseifstatement)
*   [总结](#summary)
*   [评论](#comments)

条件语句，如 Java `if else`语句，是命令式编程语言(包括 Java)的基础。`if else`语句用于指示一个程序根据某事是真还是假做出不同的行为。Java 的`if`语句是最基本的条件语句——它评估一个布尔表达式，并根据其结果执行代码。

要继续学习，您需要理解[等式、关系和条件](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/opsummary.html "Summary of Operators in Java")操作符，以及如何用它们构成[布尔表达式](https://www.sitepoint.com/boolean-data-type/ "Boolean expressions")。当`num`等于除`5`之外的任何值时，如果你知道为什么`1 > 0`计算为`true`而`num == 5`计算为`false`，你就可以开始了。

## `if`声明

`if`语句或`if then`语句是最基本的控制流语句。一旦你理解了`if`的陈述，其他的就容易了。本质上，一个`if`语句告诉一个程序只有在伴随的条件为真时才执行下面的代码块。

在这里，您可以看到对`if`语句的剖析:

```
int num = 5;

if (num == 5) {
    System.out.println("This message gets printed because num is 5.");
} 
```

变量`num`被声明并被设置为`5`。接下来是`if`声明。

`if`语句以关键字`if`开始，后跟一对括号。在括号之间，你需要提供一个*条件*。条件是一个布尔表达式——计算结果为`true`或`false`的东西。它可以是`[boolean](https://www.sitepoint.com/boolean-data-type/)`、[等式、关系或条件](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/opsummary.html "Summary of Operators in Java")表达式(如`num == 5`)类型的变量，甚至是返回`boolean`的方法调用。[布尔对象包装器](https://docs.oracle.com/javase/8/docs/api/java/lang/Boolean.html)也有效。

在括号后面，你可以看到一对花括号定义了一个代码块，通常称为 *if 块*或 *if 分支*。该代码仅在条件评估为`true`时执行**。**

缩进你的`if`语句是常见的做法，因为它为读者提供了一个视觉提示。对于只包含单行代码的代码块，你*可以*省略花括号——你*是否应该*是[另当别论](http://stackoverflow.com/q/8020228)。

### `if else`声明

在许多情况下，您可能希望根据条件是否为真来做两件不同的事情。这就是通常所说的`if` `else`语句，或者`if` *然后* `else`语句。

**如果**条件成立，**则**执行下面的命令块，否则( **else** 执行不同的块，通常称为 *else 块*或 *else 分支*。

使用 Java `if else`语句，您可以选择提供一个替代的执行路径，如果条件评估为`false`，将遵循该路径。

```
int num = 5;

if (num > 10) {
    // if branch
    System.out.println(
            "This will not be printed, since num is not greater than 10.");
} else {
    // else branch
    System.out.println(
            "This will be printed because the condition is false.");
} 
```

这个例子与上一个类似——再次声明`num`并将其设置为`5`。但是这次布尔测试略有不同！只有当`num`大于`10`时，表达式才会计算为`true`。由于情况并非如此，测试评估为`false`。根据我们之前定义的`if else`语句规则，下面的代码块是不能执行的。相反，程序的执行直接跳转到`else`块来处理`false`情况。

### `if-else-if`声明

有时您可能想要测试多个布尔表达式，所以一个简单的`if` `else`语句并不能完全满足它。例如，**如果**一个条件成立，**那么**做某事，**否则如果**另一个条件成立，**那么**做那另一件事。当然，你可以在一个`else`块中嵌套一个新的`if`语句，但是嵌套的条件块变得很难阅读。

相反，Java 中的常见做法是将第二个`if`拉到与`else`相同的行中，从而将语句链接在一起。这允许您检查多个条件，并挑选第一个通过布尔测试的代码块。

```
String callsign = "Maverick";

if ("Iceman".equals(callsign)) {
    System.out.println("You must be Lt. Tom Kazanski");
}
else if ("Maverick".equals(callsign)) {
    System.out.println("You must be Lt. Pete Mitchell");
}
else {
    System.out.println("I'm sorry, I believe we have not met");
} 
```

在这个例子中，我们有一个字符串变量`callsign`。程序首先检查它是否等于`"Iceman"`。这显然返回`false`，所以执行跳转到第一个`else`，在那里新的`if`等待。然后将`callsign`与`"Maverick"`进行比较。这会返回`true`并执行下面的代码块。

您可以利用`else`语句来提供一个默认的执行路径，如果没有一个条件评估为`true`，那么将执行该路径。在一个`if-else-if`链中，只有一个代码块被执行，其他的被忽略。

![If statements are junctions in your code](img/f8a5e21d34de0072051d19c500bce3ad.png)

## 摘要

`if`语句是最基本的条件语句。它检查一个条件，可以是任何布尔表达式，如果为真，就运行一段代码。Java `if else`语句中的`else`关键字提供了一个替代的执行路径，如果条件为假，则执行该路径。要测试多个条件，您可以用`else if`链接`if`条件。

尽管本文概述了`if`语句的变体，但并没有涵盖 Java 中所有可用的条件语句。首先，我们没有谈到[三元运算符](https://en.wikipedia.org/wiki/%3F:#Java "Ternary operator in Java")，它可以概括为`if`语句的一个快捷方式。长的`else if`链有可能被[开关语句](https://www.sitepoint.com/javas-switch-statement/ "Java’s Switch Statement in Three Minutes")替代。有时甚至可以使用[动态分派](https://en.wikipedia.org/wiki/Dynamic_dispatch "Dynamic dispatch")来完全避免`if`语句。

## 分享这篇文章