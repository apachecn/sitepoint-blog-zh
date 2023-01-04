# Java 语言基础文章

> 原文：<https://www.sitepoint.com/java-language-basics/>

Java 是一种强大的、跨平台的、面向对象的编程语言，适用于编写任何东西，从运行在公司网络上的分布式应用程序到托管个人照片库的数据库驱动的网站。为了使它更容易学习，Java 语言被设计成类似于当今使用的一些最流行的编程语言，最著名的是 C/C++。然而，如果你不是 C/C++专家(大多数 Web 开发人员都不是)，这种语言可能会有点吓人。在本文中，我将带您快速了解 Java 语言的基本语法，包括变量、数据类型、操作符和控制结构。

本文是 Java 编程语言教学系列的第二篇，着眼于它在 Web 设计和开发中的应用。具体来说，本系列的目标是教您使用 JavaServer Pages (JSP)和 Java Servlets 创建动态网站所需的所有知识。本文从本系列的前一篇文章[开始使用 Java](http://www.webmasterbase.com/article.php/364) 的地方开始。

##### 变量

以下是我在上一篇文章中帮助您创建的程序清单:

```
1  /** 

2   * Hello.java 

3   * A simple Java program 

4   */ 

5 

6  class Hello { 

7    public static void main(String[] args) { 

8      // Print a couple of one-line messages 

9      System.out.println("Hello, World!"); 

10     System.out.println("This is a test."); 

11   } 

12 }
```

就 Java 程序而言，这是一个非常简单的程序，我相信你不需要我来告诉你它毫无用处。一个每次运行都打印出同样两行的程序有什么用？至少，程序应该执行某种有用的计算，对吗？

为了能够执行计算和做有用的工作，程序必须能够以某种方式操作数据。像大多数编程语言一样，Java 允许将数据存储在变量中。变量可以简单地认为是内存中存储数据的指定位置。由于不同种类的数据可能有不同的存储需求，Java 要求您为创建的每个变量指定一个数据类型。

让我们看一个基本的例子来帮助你巩固这个概念。假设您正在编写一个程序，为一个实验室实验执行温度计算。许多这样的实验在它们的计算中都考虑到了室温，所以你的程序可能需要将室温存储在一个变量中。下面的代码创建了一个名为`roomTemp`的用于存储整数的变量，然后给它赋值 20:

```
int roomTemp;     // Create integer variable 

roomTemp = 20;    // Assign the variable a value of 20
```

对于观众中习惯看到华氏温度的美国人来说，20 摄氏度就是 68 华氏度。`int`代表**整数**(整数的编程行话)，是 roomTemp 变量的数据类型。如您所见，创建变量就像键入数据类型后跟变量名称一样简单。Java 中的变量名，像方法一样(正如我们在上一篇文章中看到的)，按照惯例是不大写的，但是在变量名中使用大写字母是可以的，这样可以使它们更易读。这就是为什么我选择将变量命名为`roomTemp`，而不是`RoomTemp`或`roomtemp`，或者主题的其他变体。

第二行为刚刚创建的变量赋值；具体来说，它将数字 20 存储在变量中。等号(`=`)被称为**赋值运算符**，因为它被用来给变量赋值。在本文的过程中，您将会遇到许多其他的操作符，它们对变量和它们存储的值做一些奇怪而奇妙的事情。

在给变量赋值之前，你必须先创建一个变量，而且在使用变量之前，你通常需要给变量赋值。试图给一个不存在的变量赋值会导致 Java 编译器在你试图编译你的程序时抛出一个错误信息。不像其他编程语言那样自动为您创建变量，Java 错误地认为您输入了错误的变量名。这有助于避免由于简单的输入错误而导致的错误。

新创建的变量没有值。例如，在我们给它赋值 20 之前，人们可能会认为`roomTemp`变量有一些默认值，比如零。事实上，它没有。没有值的变量被称为 **null** 。如果你很难理解没有任何值的东西，你可以把`null`看作是一个特殊的值，它被赋给所有新创建的变量，不管它们的数据类型如何。试图使用一个空变量，就好像它包含一个值一样，这是导致程序崩溃的最常见的编程错误之一。这不仅适用于 Java 程序，也适用于用 C/C++等其他语言编写的程序。

创建一个变量并在同一行上给它赋值有一个快捷方式。下面的代码相当于我们上面看到的两行:

```
int roomTemp = 20;     // Create variable and assign value
```

**Go to page:** [1](https://sitepoint.com/java-language-basics) | [2](https://sitepoint.com/java-language-basics-2/) | [3](https://sitepoint.com/java-language-basics-3/) | [4](https://sitepoint.com/java-language-basics-4/) | [5](https://sitepoint.com/java-language-basics-5/) | [6](https://sitepoint.com/java-language-basics-6/) | [7](https://sitepoint.com/java-language-basics-7/)

## 分享这篇文章