# Java 入门:数据类型、变量和数组

> 原文：<https://www.sitepoint.com/beginning-java-data-types-variables-and-arrays/>

Java 是一种简单、面向对象的高性能语言。它是分布式的、可移植的、多线程的、解释型的——主要用于开发面向对象的、基于网络的互联网应用软件。“互联网”意味着不同的系统、不同的网络特性、不同的视窗库和不同的操作系统。Java 保证了不同平台上相同的程序行为。

## Java 简史

1990 年，太阳微系统公司开始了一个名为“绿色”的项目，为消费电子产品开发软件。开发人员高斯林开始用 C++编写嵌入到烤面包机、录像机和个人数字助理(PDA 的)中的软件。嵌入式软件使许多设备更加智能。高斯林解决 C++问题的方法是一种叫做 Oak 的新语言。终于在 1995 年，Oak 更名为 Java。从那以后，Java 越来越受欢迎。

## 字节码

允许 Java 解决安全性和可移植性问题的关键是 Java 编译器的输出不是可执行代码，而是字节码。

字节码是一组高度优化的指令，旨在由 Java 运行时系统执行，该系统称为 Java 虚拟机(JVM)。它是字节码的解释器。

在 Java 编程语言中，所有的源代码首先都是用纯文本文件编写的，以。java 扩展。这些源文件然后被编译成。javac 编译器生成的类文件。. class 文件不包含处理器的本机代码；相反，它包含字节码 JVM 的语言。

然后，Java 启动器工具使用 Java 虚拟机的实例运行您的应用程序。

Java VM 可以在许多不同的操作系统上使用，因此也是一样的。类文件能够在 Microsoft Windows、Solaris 操作系统(Solaris OS)、Linux 或 Mac OS 上运行。一些虚拟机，如 Java HotSpot 虚拟机，在运行时执行额外的步骤，如查找性能瓶颈和重新编译(到本机代码)经常使用的代码部分。

## Java 与 C++

Java 是真正的面向对象语言，而 C++基本上是带有面向对象扩展的 C 语言。下面列出了 Java 有意省略或大幅修改的一些主要 C++特性:

*   Java 不支持运算符重载。
*   Java 没有模板类。
*   Java 不支持类的多重继承。
*   Java 不支持全局变量。
*   Java 不使用指针。
*   Java 用`finalize()`函数代替了析构函数。
*   Java 中没有头文件。

## Java 的应用

Java 被用于:

*   实时系统
*   模拟和建模
*   面向对象的数据库
*   人工智能和专家系统
*   计算机集成制造/计算机辅助设计/计算机辅助制造系统
*   神经网络和并行编程
*   决策支持系统

现在我们已经对 Java 语言有了一个大致的了解，让我们从语言的技术细节开始。

## 数据类型

[数据类型](https://www.sitepoint.com/typing-versus-dynamic-typing/)指定可以存储在标识符中的值的大小和类型。Java 语言有丰富的数据类型。不同的数据类型允许您选择适合应用程序需求的类型。

Java 中的数据类型分为两种:

1.  原语—包括整数、字符、[布尔](https://www.sitepoint.com/boolean-data-type/)和浮点。
2.  非原语—包括类、接口和数组。

### 原始数据类型

#### 1.整数

整数类型可以保存整数，如 123 和 96。可以存储的值的大小取决于我们选择的整数类型。

| 类型 | 大小 | 可以存储的值的范围 |
| --- | --- | --- |
| 字节 | 1 字节 | 128 至 127 |
| 短的 | 2 字节 | 32768 至 32767 |
| （同 Internationalorganizations）国际组织 | 4 字节 | 2147483648 至 2147483647 |
| 长的 | 8 字节 | 9，223，372，036，854，775，808 至
 |

数值范围计算为(2<sup>n1</sup>)到(2<sup>n1</sup>)1；其中 n 是所需的位数。例如，字节数据类型需要 1 字节= 8 位。因此，可以存储在字节数据类型中的值的范围是(2<sup>81</sup>)到(2<sup>81</sup>)-1
= 2<sup>7</sup>到(2<sup>7</sup>)-1
= 128 到 127

#### 2.浮点

浮点数据类型用于表示带小数部分的数字。单精度浮点数占用 4 个字节，双精度浮点数占用 8 个字节。有两种亚型:

| 类型 | 大小 | 可以存储的值的范围 |
| --- | --- | --- |
| 漂浮物 | 4 字节 | 3.4e 038 至 3.4e+038 |
| 两倍 | 8 字节 | 1.7e 308 至 1.7e+038 |

#### 3.性格；角色；字母

它在内存中存储字符常量。它假定大小为 2 个字节，但基本上它只能容纳一个字符，因为 char 存储 unicode 字符集。它的最小值为“u0000”(或 0)，最大值为“uffff”(或 65，535，含 65，535)。

#### 4.布尔代数学体系的

布尔数据类型用于存储两种状态的值:真或假。

## Java 令牌

令牌是程序中对编译器有意义的最小元素。这些标记定义了语言的结构。Java 标记集可以分为五类:标识符、关键字、文字、运算符和分隔符。

### 1.标识符

标识符是您提供的名称。这些可以分配给变量、方法、函数、类等。向编译器唯一标识它们。

### 2.关键词

关键字是保留字，对编译器有特定的意义。它们不能用作标识符。Java 有一组丰富的关键字。一些例子是:布尔，字符，如果，受保护的，新的，这个，尝试，捕捉，空，线程安全等。

### 3.文字

文字是在整个程序中其值保持不变的变量。它们也被称为常数。文字可以有四种类型。它们是:

#### a.字符串文字

字符串总是用双引号括起来，并使用 java.lang.String 类实现。用双引号将字符串括起来会自动创建一个新的 string 对象。比如`String s = "this is a string";`。String 对象是不可变的，这意味着一旦创建，它们的值就不能更改。

#### b.字符文字

这些字符用单引号括起来，并且只包含一个字符。

#### c.布尔文字

它们只能有值`true`或`false`。这些值与 C 或 C++中的 1 或 0 不同。

#### d.数字文字

数字文本可以包含整数或浮点值。

#### 4.经营者

运算符是对一个或多个操作数进行运算以产生结果的符号。

它们将在下一篇文章中详细讨论。

#### 5.分离器

分隔符是指示代码组的划分和排列的符号。代码的结构和功能通常由分隔符定义。Java 中使用的分隔符如下:

parentheses ( )

Used to define precedence in expressions, to enclose parameters in method definitions, and enclosing cast types.

braces { }

Used to define a block of code and to hold the values of arrays.

brackets [ ]

Used to declare array types.

semicolon ;

Used to separate statements.

comma ,

Used to separate identifiers in a variable declaration and in the `for` statement.

period .

Used to separate package names from classes and subclasses and to separate a variable or a method from a reference variable.

## 变量

Java 中有不同类型的变量。它们如下:

### 1.实例变量(非静态字段)

对象将它们各自的状态存储在“非静态字段”中，即没有使用`static`关键字声明的字段。

非静态字段也称为实例变量，因为它们的值对于类的每个实例都是唯一的。例如，一辆自行车的`currentSpeed`独立于另一辆自行车的`currentSpeed`。

### 2.类变量(静态字段)

类变量是用 static 修饰符声明的任何字段；这告诉编译器，无论类被实例化了多少次，这个变量都只有一个副本。定义特定种类自行车的齿轮数量的字段可以被标记为静态，因为从概念上讲，相同的齿轮数量将适用于所有实例。代码`static int numGears = 6;`将创建这样一个静态字段。

### 3.局部变量

方法将其临时状态存储在局部变量中。声明局部变量的语法类似于声明字段(例如，`int count = 0;`)。没有特殊的关键字将变量指定为局部变量；这个决定完全来自于声明变量的位置——在方法的左括号和右括号之间。因此，局部变量仅对声明它们的方法可见；它们不能从类的其他部分访问。

### 4.因素

它们是传递给类的方法的变量。

## 变量声明

标识符是变量的名称。它们只能由字母、数字、下划线和美元符号($)组成。它们不能包含空格。标识符只能以字母、下划线或美元符号开头。变量不能以数字开头。所有变量名都区分大小写。

### 变量声明的语法

数据类型 1 变量 1，数据类型 2 变量 2，…数据类型 n 变量 n；

例如:

```
int a, char ch;
```

### 初始化

变量可以通过以下方式赋值:`Variablename = value;`

比如说；

```
ch='a';
a=0;
```

## 数组

数组是一组共享相同数据类型的变量，由一个公共名称引用。可以创建任何类型的数组，并且可以有一个或多个维度。

数组中的特定元素通过其索引来访问。数组索引范围从 0 到 n-1；因此，在大小为 10 的数组中，第一个元素存储在索引 0 处，最后一个或第 10 个元素存储在索引 9 处。

下面的程序`Printarr`创建一个整数数组，将一些值放入其中，并将每个值打印到标准输出。

```
class Printarr {
  public static void main(String[] args) {
    // declares an array of integers
    int[ ] A;

    // allocates memory for 5 integers
    A = new int[5];

    // initialize elements
    A[0] = 15;//first element

    A[1] = 20;//second element

    A[2] = 25;//third element

    A[3] = 30;//fourth element

    A[4] = 50;//fifth element

    System.out.println("Element at index 0: "
                       + A[0]);
    System.out.println("Element at index 1: "
                       + A[1]);
    System.out.println("Element at index 2: "
                       + A[2]);
    System.out.println("Element at index 3: "
                       + A[3]);
    System.out.println("Element at index 4: "
                       + A[4]);
  }
}
```

这个程序的输出是:

```
Element at index 0: 15
Element at index 1: 20
Element at index 2: 25
Element at index 3: 30
Element at index 4: 50
```

## 复制数组

通过使用 System 类的`arraycopy`方法，可以将一个数组中的数据复制到另一个数组中:

```
public static void arraycopy(Object src,
                       int srcPos,
                       Object dest,
                       int destPos,
                       int length)
```

这两个对象参数指定了要从中复制的数组和要复制到的数组。三个 int 参数指定源数组中的起始位置、目标数组中的起始位置以及要复制的数组元素的数量。

下面的程序`Copyarr`声明了一个 char 元素的数组，拼写单词“array”。它使用`arraycopy`将第一个数组的三个元素复制到第二个数组中:

```
class Copyarr {
  public static void main(String[] args) {
    char[] source = { 'a', 'r', 'r', 'a', 'y' };
    char[] target = new char[3];

    System.arraycopy(source, 0, target, 0, 3);
    System.out.println(new String(target));
  }
}
```

这个程序的输出是:

```
arr
```

## 摘要

*   Java 是由美国 Sun Microsystems 公司推出的一种纯面向对象的语言，它具有许多适合因特网编程的特性。
*   Java 的平台无关特性是通过字节码实现的。
*   八种基本数据类型是:字节、短整型、长整型、浮点型、双精度型、布尔型和字符型。java.lang.String 类表示字符串。
*   实例变量(非静态字段)对于一个类的每个实例都是唯一的。
*   类变量(静态字段)是用`static`修饰符声明的字段；不管类被实例化了多少次，类变量只有一个副本。
*   局部变量在方法内部存储临时状态。

下一个教程将讨论操作符、表达式语句和块。运算符可用于构建计算值的表达式；表达式是语句的核心组成部分；语句可以分组为代码块。

*通过我们的教程[在 SitePoint 上发现块、进程和 Lambdas](https://www.sitepoint.com/premium/screencasts/discovering-the-differences-between-blocks-procs-and-lambdas) 之间的差异，了解更多关于 Java 的知识。*

## 分享这篇文章