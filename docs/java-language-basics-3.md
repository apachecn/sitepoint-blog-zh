# Java 语言基础文章

> 原文：<https://www.sitepoint.com/java-language-basics-3/>

##### 数据类型

在前面的例子中，我们将`roomTemp`变量标识为`int`数据类型，这意味着它可以存储整数(整数)值。现在让我们假设你保持你的实验室比大多数的温度高一点，所以你想记录一个 22.5 度的温度。您可以修改`PrintRoomTemp.java`的第 8 行，如下所示:

```
 int roomTemp = 22.5; // Room temperature
```

但是，如果您尝试编译这个修改过的程序，您将会看到一个类似于下面这样的错误消息:

```
D:javaJavaLanguageBasics>javac PrintRoomTemp.java   

PrintRoomTemp.java:8: possible loss of precision   

found   : double   

required: int   

    int roomTemp = 22.5; // Room temperature   

                   ^   

1 error
```

Java 告诉您,`int`数据类型不能存储值 22.5，您应该使用`double`类型，这是用于存储**浮点数**的 Java 数据类型(需要小数点的数字)。消息“可能丢失精度”指的是这样一个事实，即`int`数据类型可以存储 22 或 23，但不能存储 22.5；也就是说，`int`没有提供表示您试图存储的值所需的精度。

因此，为了获得精确的温度值，需要将`roomTemp`变量声明为`double`，而不是`int`:

```
 double roomTemp = 22.5; // Room temperature
```

在您的程序中尝试这一行，您应该会看到预期的输出。

Java 提供了一些在程序中使用的数据类型。以下是技术人员的完整列表:

*   `boolean` : `true`或`false`
*   `char`:单个 16 位 Unicode 字符
*   `byte`:8 位有符号整数
*   `short`:16 位有符号整数
*   `int`:32 位有符号整数
*   `long`:64 位有符号整数
*   `float`:32 位浮点数
*   `double`:64 位浮点数

如果您想知道位数对于这些不同的数据类型意味着什么，基本上一个数据类型使用的位数越多，它可以存储的不同值就越多。因此，`long`变量可以比`int`变量存储更多的数字，而`double`变量可以比`float`变量存储更精确的值。在大多数程序中，你可以通过使用`int`、`double`和`boolean`来获得(我们马上会看到一些例子)。其他类型是为特殊情况保留的，或者您希望通过用尽可能少的位在内存中存储值来尽可能提高语言性能的情况。

有些读者可能想知道，如果没有数据类型，我们如何在变量中存储文本字符串。`char`数据类型允许你存储单个字符…这是否意味着 Java 在将字符串存储到变量中时会强迫你将它们分割成字符？肯定不是！

您可能还记得我在上一篇文章中提到过，Java 程序是通过组合软件组件(也称为**类**)来创建所需功能的。正如我将在本系列的下一篇文章中详细介绍的，类在许多方面与数据类型相似。除了让您定义自己的类，Java 语言还提供了许多内置类，其中之一就是`String`类。`String`类实际上很特殊，因为它是 Java 语言中唯一与上面显示的基本数据类型共享某些属性的类。事实上，许多 Java 程序员如此习惯于使用`String`类，好像它是一种数据类型，以至于他们忘记了它不是！

如果最后一段只是在你的脑海中掠过，不要太担心——一般的类和特殊的`String`类将在本系列的下一篇文章中更详细地讨论。与此同时，关于`String`类，您需要知道的是，它可以像我们到目前为止看到的数据类型一样使用:

```
String myName;   

myName = "Kevin Yank";   

String companyName = "SitePoint.com";   

System.out.print(myName);   

System.out.print(" works for ");   

System.out.println(companyName);
```

上面的代码将打印消息“Kevin Yank 为 SitePoint.com 工作”。唯一需要特别注意的细节是用大写字母拼写 String。虽然所有真正的数据类型都用小写字母书写，但`String`是一个类，因此它必须大写。如果输入错误，Java 编译器会显示一条错误消息。

**Go to page:** [1](https://sitepoint.comjava-language-basics) | [2](https://sitepoint.com/java-language-basics-2/) | [3](https://sitepoint.com/java-language-basics-3/) | [4](https://sitepoint.com/java-language-basics-4/) | [5](https://sitepoint.com/java-language-basics-5/) | [6](https://sitepoint.com/java-language-basics-6/) | [7](https://sitepoint.com/java-language-basics-7/)

## 分享这篇文章