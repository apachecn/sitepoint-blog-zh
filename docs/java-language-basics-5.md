# Java 语言基础文章

> 原文：<https://www.sitepoint.com/java-language-basics-5/>

##### 控制结构

不要让名字吓到你；控制结构并没有听起来那么复杂！这两个简单的程序和我们到目前为止看到的所有例子都有一个共同点:它们以一条直线执行，一次一条语句，从上到下。简单地说，控制结构让你打破直线模式，允许你的程序对不同的条件做出不同的反应，并在执行重复任务时节省你的打字时间。

让我们从最基本的控制结构开始，一个 **`if`语句**:

```
if (condition) statement;
```

如果`condition`为真，那么 Java 将执行`statement`。如果没有，什么都不会发生，Java 只是移动到程序的下一行。这里有一个简单的例子:

```
// Check if a and b are equal

boolean abEqual = (a == b);

if (abEqual) System.out.println("a and b are equal!");

This is the practical application of the comparison operators that I promised above! The first line of the above (after the comment) checks if a and b are equal (that is, if they contain the same value). If they are, abEqual is assigned a value of true. If not, abEqual is set to false. On the second line, we have an if statement that uses abEqual as its condition. As you can see, if a and b are equal (and thus abEqual is true), the program prints out a message to that effect. If not (and abEqual is false), then nothing happens.

因为大多数条件只使用一次，所以更常见的是直接在`if`语句中键入条件表达式，而不是使用变量。上面的例子通常是这样的:

```

```
// Check if a and b are equal

if (a == b) System.out.println("a and b are equal!");

As always, use whichever method you are most comfortable with. Here are a few more examples of comparison operators in action as part of if statements:

```

```
if (a > b) System.out.println("a is greater than b");     

if (a < b) System.out.println("a is less than b");     

if (a >= b) System.out.println("a is greater than or equal to b.");     

if (a <= b) System.out.println("a is less than or equal to b.");     

if (a != b) System.out.println("a does not equal b");
```

如果条件为真，您可以指定应该执行的一系列语句，而不是单个语句。要做到这一点，可以将语句放在大括号中:

```
if (userIsKevin) {     

  System.out.println("Hi, Kevin!");     

  // ... more statements here ...     

}
```

如果你想在条件为假而不是为真时做某事，有一个方便的快捷方式可以使用。下面是不使用快捷键时代码的样子:

```
// Print message when myCondition is false     

if (myCondition == false) {     

  System.out.println("myCondition is false!");     

}
```

`==`操作符(和`!=`操作符)可以用于`boolean`值和数值类型。因此，当`myCondition`为假时，上述条件将为真，当`myCondition`为真时，上述条件将为假。我提到的捷径涉及到一种叫做**求反运算符**的东西。通过将该操作符(`!`，一个感叹号)放在变量名之前或一组包含表达式的括号之前，该变量或表达式的值将从 true 变为 false(反之亦然)。因此，上面的例子可以更简单地写成如下:

```
// Print message when myCondition is false     

if (!myCondition) {     

  System.out.println("myCondition is false!");     

}
```

如果`myCondition`为真，`!`操作符将导致条件评估为假，因此不会打印消息。如果`myCondition`为假，则`!`将表达式的值转换为真，并打印消息。值得注意的是，`!`操作符实际上并不改变存储在`myCondition`中的值；它只是为了`if`语句的目的改变了条件的值。

与 if 语句相关的另一个控制结构是 **`if-else`语句**:

```
if (condition) {     

  // statements to be executed if condition is true     

}     

else {     

  // statements to be executed if condition is false     

}
```

在这种情况下，将执行一组或另一组语句。条件决定了。每组括号中的语句也可以是一个单独的语句。事实上，通过使用另一个`if-else`语句代替第二个括号中的语句块，您可以测试多个条件，并根据哪个条件为真来执行操作:

```
if (condition1) {     

  // do this if condition1 is true     

} else if (condition2) {     

  // do this if condition2 is true     

} else if (condition3) {     

  // do this if condition3 is true     

} else {     

  // do this only if none of the conditions is true     

}
```

乍一看，这种代码结构可能有点令人不知所措，但是大声朗读它有助于使它的操作变得不言自明。下面是上面的代码，它可能用英语表达:

> **如果`condition1`为真，执行第一个语句块。否则，如果`condition2`为真，执行第二个语句块。否则，如果`condition3`为真，执行第三个语句块。否则，执行最后一个语句块。**

我将在本文中讨论的其余控制结构必须使您的程序多次执行相同的操作，而不必一次又一次地为该操作键入命令。这些结构通常被称为**回路**。

**Go to page:** [1](https://sitepoint.com/java-language-basics) | [2](https://sitepoint.com/java-language-basics-2/) | [3](https://sitepoint.com/java-language-basics-3/) | [4](https://sitepoint.com/java-language-basics-4/) | [5](https://sitepoint.com/java-language-basics-5/) | [6](https://sitepoint.com/java-language-basics-6/) | [7](https://sitepoint.com/java-language-basics-7/)

## 分享这篇文章