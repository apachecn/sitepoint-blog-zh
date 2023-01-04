# Java 的 While 和 Do-While 循环在五分钟内完成

> 原文：<https://www.sitepoint.com/javas-while-and-do-while-loops-tutorial/>

### 目录 

*   [While 循环](#whileloop)
*   [例子](#example)
*   [Do-While 循环](#dowhileloop)
*   [例子](#example-1)
*   [总结](#summary)
*   [评论](#comments)

while 循环是一个[控制流](https://en.wikipedia.org/wiki/Control_flow)语句，允许我们多次运行一段代码。像一般的循环一样，`while`循环可以用来重复一个动作，只要满足一个条件。Java 的`while`循环的结构与 [`if`语句](https://www.sitepoint.com/javas-if-statement-tutorial/)非常相似，因为它们都检查一个[布尔表达式](https://www.sitepoint.com/boolean-data-type/)并可能执行一些代码。

为了能够理解，本文期望您理解 Java 中的[变量和数组](https://www.sitepoint.com/beginning-java-data-types-variables-and-arrays/ "Beginning Java: Data types, Variables, and Arrays")。如果你有一些使用[条件表达式](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/op2.html "Equality, Relational, and Conditional Operators")的经验也是很好的。

## While 循环

while 循环是 Java 中最基本的循环结构。它由`while`关键字、循环*条件*和循环*主体*组成。

```
while (condition) {
    // loop body
} 
```

循环体的一次运行被称为一次 [*迭代*](https://en.wikipedia.org/wiki/Iteration#Computing "Iteration in computing") 。在每次迭代之前，循环条件被评估，就像使用 [if 语句](https://www.sitepoint.com/javas-if-statement-tutorial/)一样，只有当循环条件评估为`true`时，才会执行主体。事实上，只要循环条件保持`true`，一个`while`循环体就会重复——你可以把它们想象成`if`语句，其中语句体可以重复。

通常循环的一些执行会改变一些东西，使条件评估为`false`，从而循环结束。一个条件永远不会变成`false`的循环无限运行，通常被称为[无限循环](https://en.wikipedia.org/wiki/Infinite_loop "Infinite loop")。

### 例子

```
int num = 0;
while (num < 10) {
    System.out.println(num);
    num++;
} 
```

本示例打印从 0 到 9 的数字。如果这对你来说很陌生，不要担心。我们会一步一步来。

我们首先将变量`num`初始化为等于`0`。这将是我们的[循环计数器](https://en.wikipedia.org/wiki/For_loop#Loop_counter "Loop Counter")。当程序遇到一个`while`语句时，它的条件将被评估。在我们的例子中,`0 < 10`计算为`true`,循环体被执行。在循环体内，`num`变量被打印出来，然后加 1。这里是第一次迭代结束的地方。

在第一次运行循环体之后，将第二次评估循环条件。`1 < 10`仍然评估为`true`，下一次迭代可以开始。可以想象，同样的过程还会重复几次。

当`num`等于`9`时，最终迭代开始。我们的循环计数器最后一次被打印出来，并增加到等于`10`。然而，这一次，新的迭代无法开始，因为循环条件评估为`false`。`10`不小于`10`。

如您所见，只要循环条件保持`true`，循环就会运行。有了这些知识，您可以创建稍微复杂一点的`while`循环，但另一方面，也更有用。让我们迭代一个数组。

```
String[] names = {"Doc", "Dopey", "Bashful", "Grumpy", "Sneezy", "Sleepy", "Happy"};
int index = 0;
while (index < names.length) {
    System.out.println(names[index]);
    index++;
} 
```

这个例子的一般概念与上一个例子相同。我们初始化一个[循环计数器](https://en.wikipedia.org/wiki/For_loop#Loop_counter "Loop counter in for loops")，并遍历一个数组，直到数组中的所有元素都被打印出来。事实上，迭代数组(或[集合](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html "Collection interface"))是一个非常常见的用例，Java 提供了一个更适合这种情况的循环结构——T5`for`循环。

## Do-While 循环

一个`do-while`循环与一个`while`循环非常相似，但是有一个显著的区别:与`while`循环不同，条件是在每次迭代的*结束*时检查的。这意味着`do-while`循环总是至少执行一次。

```
do {
    // loop body
}
while (condition); 
```

### 例子

一个`do-while`循环首先执行循环体，然后评估循环条件。基于评估的结果，循环或者终止，或者开始新的迭代。为了说明这个想法，让我们来看一个简单的*猜猜我的名字*游戏。

```
Scanner scanner = new Scanner(System.in);
String guess;
do {
    System.out.print("Guess my name: ");
    guess = scanner.nextLine();
}
while (!"Daffy Duck".equals(guess));
System.out.println("Congratulations, you guessed my name correctly!"); 
```

该示例使用一个`Scanner`来解析来自`System.in`的输入。这是标准的输入流，在大多数情况下对应于键盘输入。简单地说，我们将读取玩家输入的文本。

在猜谜游戏中，我们希望至少提示玩家一次答案，直到玩家猜出正确答案。一个`do-while`循环非常适合这里。在循环体中，我们接收来自玩家的输入，然后循环条件检查它是否是正确答案。只要猜测值[不等于](https://www.sitepoint.com/implement-javas-equals-method-correctly/) `Daffy Duck`，循环就必须运行。如果收到一个正确的答案，循环终止，我们祝贺玩家。

![Java's while loop is exiting - but not THAT much](img/4d19af1171224bde3d6530da2232534e.png)

## 摘要

循环允许您多次重复一个代码块。本文介绍了 Java 中的`while`和`do-while`循环。它们相对相似，都检查一个条件并执行循环体，如果它评估为`true`的话，但是它们有一个主要的不同:在每次迭代之前检查一个`while`循环的条件——然而，`do-while`的循环条件是在每次迭代结束时检查的。这意味着`do-while`循环总是至少执行一次。

除了`while`和`do-while`，Java 还提供了[其他的循环结构](https://www.tutorialspoint.com/javaexamples/method_for.htm "For and for-each loops in Java")，这些在本文中没有涉及。如果你有足够的兴趣，你可以看看[递归](https://en.wikipedia.org/wiki/Recursion_(computer_science))。有时可以使用递归函数来代替循环。

## 分享这篇文章