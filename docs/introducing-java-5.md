# Java 5 简介

> 原文：<https://www.sitepoint.com/introducing-java-5/>

Java 平台的上一个主要版本是 1.2。事实上，它是如此重要，以至于这种语言被称为 Java 2。后来的 Java 版本增加了许多新的特性和修复，但没有什么值得过分兴奋的。然而，Java 5 (JDK 1.5)是另一个大问题！

在本文中，我将向您介绍这种语言最令人激动的新特性。我将向您展示为什么 Java 比以往更加健壮，而且在许多情况下，甚至更易于使用。让我们先来看看一个叫做自动装箱的漂亮的新特性。

##### 自动装箱

自动装箱是一种语言特性，它使得程序员在处理原始包装器类型时更加容易。考虑以下代码片段:

```
int num = 23; 

Integer numObject = new Integer(num);
```

原始包装对象，比如这里使用的`Integer`类，是 Java 允许您将原始类型视为对象的方式。因此，您应该用相应的基本包装对象“包装”您的基本类型，如上所示。

忘掉这一切吧！Java 5 允许您执行以下操作:

```
int num = 23; 

Integer numObject = num;
```

这乍一看似乎有点奇怪:我们已经创建了一个 Integer 类型的对象，并为它分配了一个基本类型！这怎么可能呢？原语是原语，对象是对象，两者永远不会相遇，对吗？无论如何，不再需要包装原语的额外步骤。它已经以你的名义“自动装箱”了。

拆箱使用相反的相同过程。研究一下下面的片段:

```
Boolean canMove = new Boolean(true); 

if(canMove){ 

   System.out.println("This code is legal in Java 5!");  

}
```

这有什么大不了的？仔细看:“if”语句需要一个布尔原始值，但它被赋予了一个布尔包装对象。没问题！Java 5 会自动为您“拆箱”。

请记住，编译器仍然会创建丢失的包装器代码，因此您在性能方面并没有真正获得任何好处。考虑这个特性是为了方便程序员，而不是为了提高性能。

就我个人而言，我非常喜欢这个新的语言特性。我唯一的问题是它模糊了对象和基本类型之间的界限(我一直喜欢它们之间的区别是非黑即白的事实)。不过，我不应该抱怨。微软的 C#语言实际上把这个想法更进了一步——你甚至可以直接在原语上调用方法！

##### 哪里生气了

在 Java 中，方法规范几乎是一成不变的。例如，声明特定类型的两个参数的方法必须用这些参数调用。如果提供的参数太多、太少或输入不正确，将会出现编译时错误。当然，我们使用方法重载来处理需要声明不同数量或类型的参数的情况。

然而，有时让一个方法处理任意数量的参数是有意义的，或者更方便，就像 JavaScript 和 ColdFusion 等语言允许您做的那样。许多程序员以前在 Java 中模拟这种行为的方式是创建一个参数数组，然后将该数组作为参数传递给所需的方法。

在 Java 5 中，这种相当混乱的方法已经没有必要了。考虑这个方法规范:

```
public void myMethod(Object ... args)
```

请注意参数类型旁边的三个句点。这就是我们如何告诉这个方法它可以接受不同数量的参数。类型必须是`Object`，并且必须是方法规范中的最后一个或唯一的参数。记住这一点，下面的方法调用都是完全合法的:

```
myMethod(23, 34, 78); 

myMethod("Hello", "Goodbye"); 

myMethod(123);
```

这是有道理的，除了一点。我说过参数必须是类型`Object`，但是我清楚地在其中两个调用中传递了原语！这就是自动装箱的作用——参数作为`Object`类型传递；编译器代表我们处理原语包装。

如您所料，在方法体中，参数被作为类型为`Object`的数组处理。下面的代码示例显示了在 Java 5 之前，我们可能需要多少代码来实现同样的事情。

```
int num1 = 23; 

int num2 = 28; 

int num3 = 98; 

Integer objNum1 = new Integer(num1); 

Integer objNum2 = new Integer(num2); 

Integer objNum3 = new Integer(num3); 

Integer[] params = {objNum1, objNum1, objNum3} 

myMethod(params);
```

在这里，我们自己做所有的原始包装。然后，我们创建基于数组的参数。最后，我们进行方法调用。现在有更多的工作要做！

这是一个非常漂亮的新功能，但是我不能强调它不应该被滥用。它并不打算作为适当的面向对象设计的替代品，事实上，它的引入主要是为了使另一个新特性成为可能——`printf()`方法。接下来我们来讨论这个。

##### `printf`法

这里是对`java.io.PrintStream`和`java.io.PrintWriter`类的一个方便的补充——C like`printf()`方法。`printf()`的第一个参数是一个字符串，称为格式字符串。剩下的参数称为“格式说明符”。由于 var-args 特性，您可以拥有任意多的格式说明符。这更容易通过一个简单的例子来解释:

```
Calendar c = Calendar.getInstance(); 

System.out.printf("Hi %2s, the current month is:  %1tB", cal, "Andy");
```

让我们把它分解一下，因为这里发生了很多事情。让我们考虑一下格式字符串中的第一个格式说明符——它是读为`%2s`的位。

`%`符号表示我们正在使用格式说明符。后面的数字是参数索引。在本例中，它是`2`，因此引用第二个参数，也就是字符串`"Andy"`。

下一个格式说明符是`%1tB`。从上面的解释中，您会知道这是另一个格式说明符，这次指的是第一个参数(`%1`部分告诉了我们这么多)。在这种情况下，我们使用一个`t`来表示日期转换。`t`后面的`B`表示应该输出月份。结果字符串将是，“嗨，安迪，当前月份是十月”。

对于 C 程序员来说，好消息是 Sun 决定让这背后的迷你语言变得易于识别(尽管不是 100%兼容)。如果您想知道应该如何弄清楚所有这些花哨的格式说明符是做什么的——有很多这样的说明符——`java.util.Formatter`类的`javadcoc`有所有血淋淋的细节。

正如您可能看到的，如果没有 var-args 特性，`printf()`几乎是不可能的。这是一个以正确方式使用 var-args 的罕见例子。

##### 扫描设备

过去，许多初学编程的人不必要地对 Java 产生了错误的印象。这部分是由于使用系统控制台的困难——这是许多 Java 教育的起点。

要读取标准输入，首先需要编写异常处理代码。一旦完成，你就可以用一个`InputStreamReader`和一个`BufferedReader`包裹`System.in`。最后，您可以通过`Integer.parseInt()`将输入转换成您的程序可以使用的类型。这个过程绝对不适合心脏虚弱的人！

向我的新朋友问好。这个类极大地简化了从各种字符源(实际上是实现了`java.lang.Readable`的任何东西)读取输入的过程，并使使用键盘输入变得轻而易举。以下是你如何得到一台扫描仪的方法:

```
Scanner keyboard = Scanner.create(System.in);
```

输入作为一组“标记”读入。在控制台上，一旦按下 enter 键，您就可以使用`nextSomething()`方法来获取这些令牌。`Scanner`类有一个`nextSomething()`方法，用于每个原始类型和 3 个对象:`String`、`BigInteger`和`BigDecimal`。考虑以下代码:

```
Scanner keyboard = Scanner.create(System.in); 

System.out.println("Please enter your name:"); 

String name = keyboard.nextString(); 

System.out.println("Welcome " + name + " Please enter your age:"); 

int age = keyboard.nextInt();
```

在这里，我只使用我期望提供的单个令牌，但是您应该知道有一系列相应的布尔返回`hasNextSomething()`方法，您可以使用它们来循环整个令牌集。

在您认为可能对错误类型的令牌调用了`nextSomething()`方法的情况下，您可以选择性地选择捕获异常`InputMismatchException`——这是一个未检查的异常，因此，您不必处理它。

扫描仪的功能远不止于此，比如对地区和正则表达式的支持。看看 javaDocs，和这个方便的小类交朋友。

##### 静态进口

新的“import static”语句是该语言的另一个重要补充。作为 Java 程序员，您可能不止一次地编写过如下代码:

```
PrintStream o = System.out; 

o.print("stuff"); 

o.print("more stuff");
```

参考变量`o`只不过是`System.out`的便捷快捷方式。在 Java 5 中，您可以导入一个类的静态成员，并在没有通常的类名前缀的情况下引用它们。因此，您现在可以这样做:

```
import static java.lang.System.out; 

// other code here. 

out.print("stuff"); 

out.print("more stuff");
```

一旦静态成员被导入，您就可以在没有`System`类前缀的情况下在代码中使用它。这里有一个来自 Swing GUI 世界的更好的例子。

```
// without static import. 

setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE); 

// with static import. 

import static javax.swing.WindowConstants.*; 

// other code 

setDefaultCloseOperation(EXIT_ON_CLOSE);
```

在第二个示例中，静态导入的添加极大地增强了代码的可读性，并减少了所需的代码量。在本例中，我使用了`*`来导入所有的 WindowConstants 静态成员。

##### 枚举类型

让我们先来看看我们用来模拟枚举类型的方法:

```
class FizzyDrink { 

  static final int PEPSI = 1; 

  static final int COKE = 2; 

static final int SCHWEPPES = 3 

}
```

这里有什么问题？我们现在有三种`FizzyDrink`类型:`FizzyDrink.PEPSI`、`FizzyDrink.COKE`和`FizzyDrink.SCHWEPPES`，对吗？不完全是。我们实际上只有 3 个`int`值。看一下下面的代码:

```
int todaysFizzyDrink = 237; // ouch, this shouldn't happen!
```

这种方法是类型不安全的，这意味着编译器不能强迫我们类的用户使用我们定义的一个可接受的值。此外，因为常数被编译到使用它们的每个类中，所以这种方法有时会导致代码中的细微错误——每次需要更改包含常数的类时，您都必须记住跟踪并重新编译所有其他相关类！

这是新的方法:

```
enum FizzyDrink{ pepsi, coke, schweppes }
```

注意到用`enum`关键字代替了`class`关键字吗？一个`enum`实际上只是一种特殊的职业。这里有一个使用中的`enum`的例子:

```
FizzyDrink todaysFizzyDrink = FizzyDrink.pepsi;
```

现在，我们真的有一种`FizzyDrink`类型。事实上，如果我们打印出`todaysFizzyDrink`的值，我们会得到字符串`pepsi`，而不是一些无意义的整数值。这是真正酷的部分:不需要依赖类的重新编译，当您更改枚举类型但仍然有代码在其他地方使用旧的枚举值时，Java 甚至会警告您！

这个新增加的功能是我个人最喜欢的，因为它极大地提高了 Java 软件的可靠性，而且使用起来非常简单。当然，枚举的内容比我在本文中介绍的要多，但是它们确实非常简单。

##### 无商标消费品

类型泛型是对核心 Java 语言最激进的增强。简而言之，泛型允许 java 程序员将类型作为参数传递给类，就像将值传递给方法一样。

泛型主要用于集合框架，所以我将把对它们的简短讨论集中在这个领域。看看下面这个访问`LinkedList`中元素的前后示例:

```
String custName = (String) myList.getFirst();   // without generics 

String custName = myList.getFirst();     // with generics
```

在第一个例子中，我们必须执行通常的强制转换，因为当前的集合类处理对象。由程序员来决定类型并进行适当的转换。

在第二个示例中，没有强制转换。那么`getFirst()`方法是如何知道返回一个`String`对象的呢？简单的回答是，当`LinkedList`被实例化时，它被明确告知使用字符串。让我们来看看这是如何做到的:

```
LinkedList<String> myList = new LinkedList<String>();
```

你会看到两组尖括号包含了我们在实例化时传递给类的“类型”。这个列表现在只适用于字符串——如果试图添加错误类型的对象，它将无法编译。让我们看一个示例`LinkedList`实现来了解一下正在发生的事情:

```
public class LinkedList <Element>{ 

  boolean add(Element o) { 

    // code omitted 

  } 

  Element getFirst(){ 

    // code omitted 

  } 

}
```

这一切的关键是`<Element>`声明。这种看起来相当奇怪的语法实际上与圆括号为方法参数服务的目的是一样的，当然，除了我们传递的是类型，而不是值。顺便说一下，真正的`LinkedList`类使用`<E>`作为类型参数——`<Element>`没有什么特别之处，就像您选择的任何方法参数名称也没有什么特别之处一样。

起初，很容易认为泛型只是让我们不必执行强制转换——这确实是一个很好的特性，但这不是重点。在泛型出现之前，一些错误的代码可能会向应该保存字符串的集合中添加一个整数。不幸的是，编译器不会注意到这一点，相反，它会在几个月或几年后以几乎不可能发现的形式出现！

集合类现在确切地知道它们要处理的类型，所以错误检测被移到开发生命周期的早期:编译时。

我对泛型特性唯一的担心是，它给初学编程的人增加了另一层复杂性(那些尖括号简直太难看了！)，但是 Java 对他们来说更好更强。

##### JVM 改进

虽然没有我到目前为止提到的那些特性那么明显，但是 Java 5 中发生的一些最好的事情是增强了 JVM 的工作方式。

类数据共享将提供一个主要的性能提升。本质上，大多数运行时库现在作为内存映像映射到内存中，而不是从一系列类文件中加载。

此外，很大一部分运行时库现在将在多个 JVM 实例之间共享——当多个 Java 程序同时运行时，这将非常有帮助。

##### 摘要

Java 5 确实是向前迈进了一大步，因此有很多东西需要学习。一些新功能可能有点棘手，直到你看到它们的实际应用，所以不要害羞——漫步到[java.sun.com](http://java.sun.com)，给自己复制一份。这当然不是所有新特性的最终列表，所以一定要探索，最重要的是，要玩得开心！

## 分享这篇文章