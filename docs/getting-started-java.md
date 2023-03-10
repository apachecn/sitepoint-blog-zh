# Java 入门

> 原文：<https://www.sitepoint.com/getting-started-java/>

想学习如何构建动态网站吗？有许多选择，其中最流行的包括 ASP、PHP 和其他脚本语言。另一个选择是 Java。当然，Java 最初是作为编写基于 Web 的小应用程序和跨平台应用程序的语言出现的，但在过去几年中，它的作用已经扩展到包括 Web 页面的服务器端编程。事实上，就业市场对有编写服务器端 Java 应用程序经验的开发人员需求很大。将这些技能添加到简历中的第一步应该是对 Java 语言有一个基本的了解。

这是一系列文章中的第一篇，这些文章将教您 Java 编程语言，着眼于适用于构建动态网站的特性和技术。具体来说，在介绍了该语言的基础知识和有效使用该语言所需的面向对象概念之后，我将继续讨论 Java Servlets 和 JavaServer Pages (JSP ),这两种技术用于使用 Java 构建动态的、数据驱动的网站。同时，我将向您介绍相关的技术，比如 Java 数据库连接(JDBC)。但是在我陷入对未来事物的描述之前，让我把您的注意力转移到本文的目标:Java 语言基础的介绍。

##### Java 是什么？

Java 是一种像 C/C++一样的全功能编程语言，但是更简单，结构更紧密。像 C++一样，Java 也是一种面向对象的编程语言，这是我将在本系列的下一篇文章中详述的概念。然而，从根本上来说，Java 程序是通过创建一组结构化的软件组件来编写的，这些组件相互交互以产生预期的效果。这些组件被称为对象，Java 开发人员的基本任务是确定最佳的对象结构，以实现一个按要求尽可能高效地工作的系统，并尽可能生成可在类似应用程序中重用的组件。

如果这一切听起来有点可怕，不要担心；一旦你学会了，就都明白了。现在，我们将一步一步来。

Java 程序不是直接运行在计算机的操作系统上，而是运行在“Java 虚拟机”(JVM)上，JVM 本身就是运行在计算机操作系统上的程序。由于 Java 虚拟机的生产是高度标准化的，不同平台之间的不兼容性是最小的。这意味着，理论上，任何拥有 JVM 的操作系统(现在几乎所有的主流操作系统)都可以运行任何 Java 程序，不需要为每个平台重新编译程序。这导致了 Java 语言最吸引人的特性之一:“一次编写，随处运行”(WORA)。缺点是 Java 程序往往运行较慢，因为虚拟机必须转换 Java 程序指令并将其传递给运行它的操作系统。

大多数当前的 Web 浏览器也可以运行从 Web 下载的 Java 程序，或者使用浏览器内置的 JVM(如在 Netscape 4 和 Internet Explorer 浏览器中)，或者通过与单独安装的标准 JVM 接口(如在 Netscape 6 和 Opera 浏览器中)。为在网络浏览器中运行而设计的 Java 程序被称为 Java 小程序。当 Java 最初发布时，围绕这种语言的大量市场宣传都集中在这些运行在 Web 浏览器中的小程序上。然而，多年来，Web 上 Java 的焦点已经转移到了服务器端应用程序(包括 Servlets 和 JSP ), Java 小程序在许多情况下已经被动态 HTML 和 Flash 等更灵活的技术所取代。如今，Java 小程序的常见用途包括基于网络的聊天程序和在线游戏。

现在，您已经知道了自己将进入什么样的环境，是时候掌握开始 Java 开发工作所需的工具了。

##### 你需要什么

开发和运行 Java 程序所需的一切都可以从 Sun Java 网站 www.javasoft.com 的[免费下载。你需要的主要软件包是](http://www.javasoft.com/) [Java 2 软件开发工具包](http://www.javasoft.com/j2se/) (SDK)，标准版。SDK 包含创建 Java 程序的开发工具和运行 Java 程序的 Java 运行时环境(JRE)。JRE 包括 JVM 和各种支持文件。除了使用一些浏览器的内部 JVM 运行的 Java 小程序，任何想要运行 Java 程序的人都需要安装 JRE，JRE 也可以从 SDK 单独下载。然而，要编写自己的 Java 程序，您需要完整的 SDK。您还会想要一份 Java 2 平台文档包，其中包含 Java 为您提供的所有内置功能的综合参考。一旦你熟悉了这门语言的基础，并准备好编写真实世界的程序，这些文档将会是你最好的朋友。

每个软件包都提供了详细的安装说明，我强烈建议您严格遵守这些说明。许多安装 Windows 版本的人只是下载并运行安装程序，而忽略了完成设置关键环境变量以使 Java 正确运行的额外任务。所提供的安装说明中唯一遗漏的是在 Windows ME 和 Windows 2000 中添加/编辑环境变量的方法。说明中提供的方法适用于 Windows 95/98/NT，但在 Windows ME 中，您需要单击**开始**、**运行……**，然后键入 **msconfig** 来启动 Windows ME 的系统配置实用程序。可以在**环境**选项卡上调整系统环境变量。在 Windows 2000 中，他们提供的过程几乎是正确的。运行**系统**控制面板图标后，进入**高级**选项卡，点击**环境变量**按钮，然后按照说明进行操作。

要仔细检查 Java SDK 是否正确安装在您的计算机上，请打开命令提示符(即 Windows 中的命令提示符或 MS-DOS 命令提示符，或者 Linux 中的 XTerm)。尝试下面显示的两个命令。如果您得到类似的输出，您就可以开始了。如果您的操作系统不能识别这些命令，您可能需要重新启动以检测对环境变量的更改。

```
C:> **java â€“version**  

java version "1.3.0"  

Java(TM) 2 Runtime Environment, Standard Edition (build 1.3.0-C)  

Java HotSpot(TM) Client VM (build 1.3.0-C, mixed mode)  

C:> **javac**  

Usage: javac <options> <source files>  

where possible options include:  

  ...list of options...
```

一旦安装了 SDK(以及可选的文档),就可以编写第一个 Java 程序了。在您继续之前，让我就集成开发环境(IDE)说几句话。您可能已经看到过为编写 Java 程序而设计的奇特开发环境的广告，包括商业的和免费的。虽然这些对有经验的 Java 开发人员很有用(我自己在自己的项目中大量使用 IBM VisualAge for Java)，但我强烈建议，作为初学者，您应该开始使用 SDK 中提供的简单工具。

我在这个问题上的理由是，集成开发环境(如 JBuilder 等)提供的许多特性旨在提供快捷方式来更快地完成某些任务。问题是，如果你不理解提前完成这些任务的“漫长道路”,你将很难理解使用这些工具提供的自动化方法的含义和陷阱。这个问题类似于在像 HotDog Pro 这样的文本编辑器和像 Dreamweaver 这样的 WYSIWYG 编辑器之间进行选择来构建一个网站。虽然 Dreamweaver 可以帮助您快速制作一些高级效果，但如果您不知道如何事先手工编写这些效果，那么您将很难制作出超出 Dreamweaver 自动化工具所能提供的范围的任何高级效果。

##### 你的第一个 Java 程序

本节的目标是让您对编写、编译和运行 Java 程序的过程有所了解。您还将看到 Java 程序——即使只是一个非常基础的程序——是什么样子的。我会用最基本的术语解释一切，但是如果有些东西对你来说没有意义，不要太担心。有些概念只有有经验的程序员才熟悉，如果那不是你，不用担心！在这个阶段，最重要的是你要习惯**需要做什么**，而不是理解**为什么要做**。

首先在一个方便的地方创建一个新目录；在大多数情况下，您会希望将每个 Java 程序的文件存储在一个单独的目录中。我假设您正在使用的目录是`D:MyFirstJava`，但是您可以随意命名该目录。例如，在 Linux 下，您可能会将该目录创建为您个人主目录的子目录(例如`/home/kyank/MyFirstJava`)。

使用您喜欢的文本编辑器(例如，Windows 下的记事本也可以)，在该目录中创建一个名为`Hello.java`的文件。注意文件扩展名`.java`。所有包含 Java 源代码的文件都应该被赋予`.java`扩展名，以便将它们识别为 Java 源文件。还要注意文件名是大写的。这很重要，所以一定要给文件起一个准确的名字。要在记事本中保存不带`.txt`扩展名的文件，请务必在**另存为**对话框的**另存为类型**下拉菜单中选择**所有文件**；否则，记事本会在文件名末尾加上`.txt`(如`Hello.java.txt`)。

键入以下内容作为`Hello.java`的内容，然后保存文件。请注意，提供行号只是为了您的方便；不要在文件中输入行号！

```
**1**  /**   

**2**   * Hello.java   

**3**   * A simple Java program   

**4**   */   

**5**   

**6**  class Hello {   

**7**    public static void main(String[] args) {   

**8**      // Print a couple of one-line messages   

**9**      System.out.println("Hello, World!");   

**10**     System.out.println("This is a test.");   

**11**   }   

**12** }
```

您刚刚编写了您的第一个 Java 程序！在我向您展示如何编译和运行它之前，我将一行一行地解释代码，用一般的术语来说，每件事都做什么。

*   ****Lines 1-4:**** The file starts out with a **comment**. This is a section of the code that is intended for people to read to help them better understand what the code does. Java actually ignores comments entirely, so the program would work exactly the same if these four lines were removed from the file (try it later if you like!), but it is good practice to always describe the contents of a file with a comment at the top. In Java, there are two ways to include comments in your files. This first method starts a comment with `/*` (pronounced ‘slash-star’) and ends it with `*/` (‘star-slash’). Everything between these two markers is ignored by Java. As you can see, a comment of this type can span multiple lines.

    我已经按照 Java 文档样式指南推荐的标准样式格式化了这个注释。注释以斜杠-星号开始，后跟另一个星号和一个换行符，然后注释的每一行都以一个空格开始，后跟一个星号和另一个空格。注释以空格结尾，后跟单独出现在一行中的终止星斜线。虽然这是推荐的评论风格，也将是我在本系列文章中使用的风格，但您或您的公司可以开发一种替代风格。以下是几个替代注释样式的示例:

    | `/*                           /*-----------------------*  
     Hello.java                  &#124; Hello.java            &#124;  
     A simple Java Program       &#124; A simple Java program &#124;  
    */                           *-----------------------*/` |

    虽然它们看起来非常不同，但请注意，它们都以`/*`开头，以`*/`结尾。至于程序如何工作，介于这两个标记之间的内容并不重要——使用您喜欢的任何样式。

*   ****第 5 行:**** 空行被忽略，就像注释一样。
*   ****Line 6:**** I said earlier on that Java programs consist of a set of software **components** that work together to achieve the desired effect. This very simple program requires only one such component, and this line marks it’s beginning. The word `class` indicates that I am starting a new component (for now, you can take a **class** to mean a component), and the word `Hello` is the name of that component (or class). The opening brace (`{`) marks the beginning of the code that will define the new class.

    这个类(`Hello`)的名字和定义它的文件(`Hello.java`)的名字一样，这并不是巧合。其实这是要求。您创建的每个类都必须在它自己的文件中定义，并与在其中定义的类同名。这两个名字必须完全匹配，精确到大小写(你不能在名为`myclass.java`的文件中定义名为`MyClass`的类；相反，该文件必须命名为`MyClass.java`。

    Java 中的类名按照惯例是大写的。也就是说，如果你真的真的想命名一个类`myclass`，但是其他 Java 程序员习惯于看到大写的类名，所以如果你改为调用类`MyClass`，你的代码将更容易阅读和维护。

*   ****Line 7:**** This line looks pretty tricky, and indeed it is the most complicated line in the program, as far as how much you need to know to understand it completely. Just as the previous line marked the beginning of a class, this line marks the beginning of a **method**. A method may be loosely defined as something that a class can do. If you’ve worked with other programming languages, methods are the Java equivalent of functions or subroutines.

    行首的单词`public`、`static`和`void`提供了关于该方法的某些信息。每一个都有一个相当长的故事来解释为什么这里需要它；所以暂时不用担心这些。单词`main`是方法的名称。注意，与类名不同，方法名不是大写的。再说一次，这只是一个约定，但是为了那些可能不得不阅读你的代码的人，你真的应该遵守这个约定。

    取决于你有多少编程经验，这一行的括号部分要么你已经很熟悉了，要么看起来很吓人。基本上，它定义了需要什么信息来使这个方法完成它的工作，但是现在你不需要担心细节。

    这个`main`方法其实比较特别。当它第一次开始运行一个程序时，Java 在类中寻找一个名为`main`的方法，这个方法前面有`public static void`几个字，并把它作为程序的起点。所以如果你愿意，你可以把这一整行想象成说“程序从这里开始”的“神奇公式”。

*   ****第 8 行:**** 这是另一个注释，是为了方便需要阅读代码的人而包含的代码的一部分。同样，如果删除这一行，程序的功能将完全相同。注意，这个注释不是以`/*`和`*/`开始和结束的。这是 Java 允许你使用的第二种类型的注释的例子。当遇到代码中的`//`(斜杠)时，Java 会忽略该行的其余部分。这种类型的注释对于在代码中快速编写单行注释非常有用，或者对于在特别复杂的代码行的末尾添加简短的解释非常有用。
*   ****Line 9:**** This line is the first example of a Java **statement** in this program. A statement tells the program to do something; it is a command, instruction, or order if you prefer. Java statements take many different forms, but they all end with a semicolon (`;`). One of the most common mistakes that beginners make when writing Java programs is to forget the semicolons on the ends of their statements. Fortunately, Java is pretty good about spotting these mistakes and letting you know about them.

    这条特殊的语句指示你的程序在屏幕上打印文本`Hello, World!`。`System.out.println`表示“在屏幕上打印下一行文本”，而这一行的其余部分表示应该打印的内容。

*   ****第 10 行:**** 与前一行大同小异，这条语句告诉程序将文本`This is a test.`打印在屏幕上。因为语句是一个接一个执行的，所以这条消息应该紧接在前面的消息之后显示在屏幕上。
*   ****第 11 行:**** 这一行的右括号标志着`main`方法的结束。当程序运行到这一步时，它将停止运行。
*   ****第 12 行:**** 这一行的右括号标志着`Hello`类的结束。

在我继续之前，先说明一下编码风格。你可能注意到这个程序的一些行前面有空格，而其他的没有。这种被称为**缩进**的编码风格意在展示代码的结构。也就是说，通过将`main`方法(以及第 11 行的右括号)缩进两个空格，可以清楚地表明该方法在`Hello`类内部。同样，通过将第 8-10 行(`main`方法的内容)再缩进两个空格，很明显这些行属于`main`方法。程序的代码本来可以不用缩进(每一行都从左边开始)来编写，它也可以工作得很好，但是程序的结构看起来并不明显。当你进入编写复杂程序的阶段时，你代码的某些段落缩进五级甚至十级是很常见的。通过现在养成这个好习惯，它将确保你的代码在现在和将来都易于阅读。

##### 编译和运行您的程序

当你写完第一个 Java 程序时，你可能很想看到它运行。在运行程序之前，必须先将其转换成 Java 虚拟机(JVM)可以理解的格式。这个过程被称为**编译**，包括运行一个名为 **Java 编译器**的程序，该程序检查你的 Java 代码是否有错误(遗漏分号等等)，然后将其转换成 JVM 能够理解的二进制格式 **Java 字节码**。Java 编译器是从命令行运行的，方法是键入`javac`，后跟您想要编译的 Java 代码文件的名称。

要编译您的`Hello.java`文件，首先打开一个命令提示符并导航到包含要编译的文件的目录。例如，在 Windows 上，要导航到`D:MyFirstJava`，请键入以下命令(每次键入后按 Enter 键):

```
C:> **d:**    

D:> **cd MyFirstJava**    

D:MyFirstJava>
```

要编译`Hello.java`，请键入以下命令:

```
D:MyFirstJava> javac Hello.java
```

如果一切按计划进行，应该会有一个短暂的延迟，然后是另一个提示(没有消息就是好消息！).但是，如果编译器发现您的代码有任何问题，将会显示一个或多个错误或警告。在大多数情况下，会给出准确的行号和一些关于错误性质的线索。将您的代码与上面提供的代码进行比较，确保它们完全匹配(记住，行号不是代码的一部分)。

一旦你成功地编译了你的程序，一个名为`Hello.class`的文件将会在你的`Hello.java`文件旁边的目录中被创建。这是 Java 代码的编译版本，包含您在文件中定义的`Hello`类的描述。出于显而易见的原因，这样的文件被称为**类文件**。这种描述是二进制格式的(特别是 Java 字节码)，可以由 JVM 运行。

运行一个 Java 程序与编译它类似，除了不使用 Java 编译器(`javac`)，而是使用 Java 运行时环境(JRE)，可以使用`java`命令调用它，后跟包含特殊的`main`方法的类名。在这种情况下，您的类被称为`Hello`，因此要运行您的程序，您可以键入以下命令:

```
D:MyFirstJava> **java Hello**

Hello, World!

This is a test.
```

正如所料，两条消息`Hello, World!`和`This is a test.`被打印出来。你的程序起作用了！一个重要的注意事项:当运行一个程序时，你需要指定类名(`Hello`)，而不是类文件名(`Hello.class`)。Java 使用类名自动找到类文件。如果你让它运行`Hello.class`，它会寻找一个包含一个名为`Hello.class`的类的定义的文件，你会得到如下的错误:

```
D:MyFirstJava> java Hello.class

Exception in thread "main" java.lang.NoClassDefFoundError: Hello/class
```

这个`NoClassDefFoundError`表示 Java 找不到它所寻找的类的定义，在这里是`Hello.class`(它显示为`Hello/class`，原因我将在以后的文章中解释)。

##### 总结和进一步阅读

在本文中，我从头开始介绍了 Java 编程语言。我讲述了 Java 是什么以及为什么它是一个有用的工具，我解释了在您的计算机上编写、编译和运行 Java 程序需要什么工具以及在哪里可以获得它们，最后我带您完成了创建和运行一个非常简单的 Java 程序的过程。

正如我所说的，这只是旨在带您进入 Web Java 编程世界的系列文章中的第一篇。在第 2 部分中， [Java 语言基础](http://www.webmasterbase.com/article.php/381)，我们将更详细地探索这种语言，包括数据类型、操作符、变量和控制流。与此同时，如果你的最终目标是学习使用 Java Servlets 和 JavaServer Pages (JSP)创建动态网站，你可以阅读我的 [JSP 快速入门指南](http://www.webmasterbase.com/article.php/305)来设置你需要的软件，以便为 Web 生成服务器端 Java 程序。

如果您急于继续学习 Java 语言，在等待本系列的后续文章时，您可以参考一些其他资源。Java 教程是学习 Java 大部分内容的官方在线指南，可以免费获得。然而，更好的学习语言的方法是拿起一本关于这个主题的好书。我的最佳推荐是 WROX 出版社的《Java 2-JDK 1.3 版入门》，完整的评论可以在[这里](http://www.webmasterbase.com/article.php/300)找到。

## 分享这篇文章