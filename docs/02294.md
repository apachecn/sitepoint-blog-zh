# Java、Unicode 和神秘的编译错误

> 原文：<https://www.sitepoint.com/java-unicode-mysterious-compile-error/>

Unicode 是一种文本编码标准，支持多种字符和符号。虽然该标准的最新版本是 9.0，但 JDK 8 支持 [Unicode 6.2](https://docs.oracle.com/javase/8/docs/technotes/guides/intl/enhancements.8.html "Internationalization Enhancements in JDK 8") ，JDK 9 预计将发布[支持 Unicode 8.0](http://openjdk.java.net/jeps/267 "JEP 267: Unicode 8.0") 。Java 允许您使用 Unicode 转义插入任何支持的 Unicode 字符。这些基本上是一系列十六进制数字，代表一个[代码点](https://en.wikipedia.org/wiki/Code_point "Wikipedia article for code point")。在这篇文章中，我将介绍如何在 Java 中使用 Unicode 转义，以及如何避免 Unicode 转义误用导致的无法解释的编译器错误。

## 什么是 *Unicode 转义*？

让我们从头开始。Unicode 转义用于表示只有 ASCII 字符的 Unicode 符号。当您需要插入一个无法在源文件的字符集中表示的字符时，这将非常方便。根据 Java 语言规范(JLS) 的第 3.3 节[，一个 *unicode 转义符*由一个反斜杠字符(\)后跟一个或多个‘u’字符和四个十六进制数字组成。](https://docs.oracle.com/javase/specs/jls/se7/html/jls-3.html#jls-3.3 "Section 3.3 of JLS")

```
UnicodeEscape:
    \ UnicodeMarker HexDigit HexDigit HexDigit HexDigit

UnicodeMarker:
    u
    UnicodeMarker u 
```

因此，例如`\u000A`将被视为换行。

## 用法示例

下面是一段包含 Unicode 转义的 Java 代码。

```
public class HelloUnicode {
    public static void main(String[] args) {
        // \u0055 is a Unicode escape for the capital U character (U)
        System.out.println("Hello \u0055nicode".length());
    }
} 
```

花一点时间想想什么会被打印出来。如果您愿意，可以将代码复制并粘贴到一个新文件中，编译并运行它。

乍一看，这个程序打印出了`18`。双引号之间有 18 个字符，所以字符串的长度应该是 18。但是如果你运行程序，输出是`13`。正如注释所建议的，Unicode 转义将被替换为单个字符。

了解了 Unicode 转义被替换为各自的 Unicode 字符之后，我们来看下面的例子。

```
public class NewLine {
    public static void main(String[] args) {
        // \u000A is a unicode escape for the line feed (LF)
        // \u0055 is a Unicode escape for the capital U character (U)
        System.out.println("Hello \u0055nicode".length());
    }
} 
```

你能猜到现在会打印出什么吗？答案应该和之前一样吧？我相信你们中的一些人可能会怀疑这是一个陷阱问题，事实上，的确如此。这个例子根本不会编译。

```
$ javac NewLine.java
NewLine.java:3: error: ';' expected
        // \u000A is a unicode escape for the line feed (LF)
                      ^
NewLine.java:3: error: ';' expected
        // \u000A is a unicode escape for the line feed (LF)
                                     ^
NewLine.java:3: error: '(' expected
        // \u000A is a unicode escape for the line feed (LF)
                                         ^
NewLine.java:3: error: ';' expected
        // \u000A is a unicode escape for the line feed (LF)
                                                  ^
NewLine.java:3: error: ';' expected
        // \u000A is a unicode escape for the line feed (LF)
                                                            ^
NewLine.java:5: error: ')' expected
        System.out.println("Hello \u0055nicode".length());
                                                         ^
6 errors 
```

什么！？这么多错误！我的 IDE 没有显示任何弯曲的红线，我自己似乎也找不到任何语法错误。第三行出错？但那是评论。这是怎么回事？

## 是什么导致了错误？

为了更好地理解正在发生的事情，我们需要看一下 Java 语言规范的第 3.2 节——词汇翻译。我不能代表曾经存在过的所有编译器，但通常编译器的第一项工作是获取程序的源代码，将其视为一个字符序列，并产生一个符号序列。

一个*记号*是在语言的上下文中有意义的东西。例如，在 Java 中，它可以是一个[保留字](https://en.wikipedia.org/wiki/Reserved_word "Wikipedia page for reserved words") ( `public`、`class`或`interface`)、一个[运算符](https://en.wikipedia.org/wiki/Operator_(computer_programming) "Wikipedia page for operators") ( `+`、`>>`)或一个[文字](https://en.wikipedia.org/wiki/Literal_(computer_programming) "Wikipedia page for literals")(表示固定值的符号)。从字符序列生成标记的过程被称为 [*词法分析*](https://en.wikipedia.org/wiki/Lexical_analysis "Wikipedia page for lexical analysis") (或*词法翻译*，因为它在 Oracle 文档中被称为)并且执行该过程的程序被称为*词法分析器*或*标记器*。

[Java 语言规范](https://docs.oracle.com/javase/specs/jls/se8/html/jls-3.html#jls-3.2 "Section 3.2 Lexical Translations")规定词法翻译分以下 3 步执行，每一步都应用于前一步的结果:

1.  Unicode 转义的翻译。
2.  通过识别行终止符(LF、CR 或 CR LF)将输入字符流分成行。
3.  丢弃空白和注释，标记上一步的结果。

如您所见，第一步处理 Unicode 转义。这是在编译器有机会将源代码分成标记之前完成的。概括地说，这就像在源代码上应用一个*搜索和替换*函数，用它们各自的 Unicode 字符替换所有的**格式良好的** Unicode 转义，然后让编译器处理剩余的代码。

请记住，当处理 Unicode 转义时，编译器不会区分注释和实际代码。它只能看到一系列字符。这解释了你在这篇文章的介绍中看到的错误代码。让我们再看一看。

```
//This is the original source code
public class NewLine {
    public static void main(String[] args) {
        // \u000A is a unicode escape for the line feed (LF)
        // \u0055 is a Unicode escape for the capital U character (U)
        System.out.println("Hello \u0055nicode".length());
    }
}

//This is what it looks like after Unicode escapes have been processed
public class NewLine {
    public static void main(String[] args) {
        //
 is a unicode escape for the line feed (LF)
        // U is a Unicode escape for the capital U character (U)
        System.out.println("Hello Unicode".length());
    }
} 
```

表示换行符的 Unicode 转义被替换为换行符，现在注释的一部分在新的一行上。不幸的是，新行没有以*双斜线* ( `//`)开头，并且该行的其余部分不是有效的 Java 代码。因此出现了前面显示的令人困惑的编译器错误。

## 快速绕道: *native2ascii*

您可以自己尝试 Unicode 转换。在 Java 8 之前，JRE 与一个名为 *[native2ascii](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/native2ascii.html "Oracle docs about native2ascii")* 的工具捆绑在一起，该工具可以将包含任何支持的字符编码的文件转换为包含 ascii 和/或 Unicode 转义的文件，反之亦然。

```
$ native2ascii -reverse NewLine.java

public class NewLine {
    public static void main(String[] args) {
        //
 is a unicode escape for the line feed (LF)
        // U is a Unicode escape for the capital U character (U)
        System.out.println("Hello Unicode".length());
    }
} 
```

Java 9(以及更高版本)呢？在此之前，Java 属性文件[默认使用 ISO-8859-1 字符集](https://docs.oracle.com/javase/8/docs/api/java/util/Properties.html#load-java.io.InputStream-)。使用 *native2ascii* 工具将无法在 ISO-8859-1 中表示的字符转换为 Unicode 转义。但是 [JEP 226](http://openjdk.java.net/jeps/226) 改变了这一点，属性文件现在可以用 UTF-8 编码，这意味着 *native2ascii* 工具不再需要了。

在本文中， *native2ascii* 用于演示如果 Unicode 转义被替换为实际的 Unicode 字符，Java 源文件会是什么样子。对于 Java 9 用户，我推荐使用 [*uni2ascii*](http://billposer.org/Software/uni2ascii.html) ，可以达到同样的效果。

```
 # uni2ascii package consists of two programs: uni2ascii and ascii2uni.
# Commandline argument -a U specifies the format of Unicode escapes
# which matches the one used in Java
ascii2uni -a U NewLine.java 
```

## 在注释中隐藏代码

如果 Unicode 转义在其他所有事情之前被处理，那么我可以巧妙地将代码隐藏在注释中，稍后再执行吗？这个问题有点吓人的答案是肯定的。回头看前面的例子，我们看到插入了一个换行符，注释的其余部分在下一行，导致无效的 Java 代码。但是我们可以写下面的内容

```
public class HidingCode {
    public static void main(String[] args) {
        //\u000A System.out.println("This is a comment");
        System.out.println("Hello world");
    }
} 
```

如果 Unicode 转义被替换为换行符，那么应该清楚实际上执行了两个 print 语句。

```
$ native2ascii -reverse HidingCode.java
public class HidingCode {
    public static void main(String[] args) {
        //
 System.out.println("This is a comment");
        System.out.println("Hello world");
    }
}
$ javac HidingCode.java
$ java HidingCode
This is a comment
Hello world 
```

## 为什么 Java 允许这样做？

这看起来很奇怪，对吧？Java 为什么要这样设计？是不是因为会坏了别的东西而不小心引入的 bug，一直没有修复？为了找到这个问题的答案，我们需要看看 Java 语言规范(JLS)的 3.1 节(T0)和 3.3 节(T2)。

来自第 3.1 节:

> Java 编程语言使用 UTF-16 编码以 16 位代码单元的序列来表示文本。

来自第 3.3 节:

> Java 编程语言规定了一种将以 Unicode 编写的程序转换成 ASCII 的标准方法，这种方法将程序转换成可由基于 ASCII 的工具处理的形式。转换包括通过添加一个额外的 u 将程序源文本中的任何 Unicode 转义转换为 ASCII——例如，\uxxxx 变成\ uuxxxx——同时将源文本中的非 ASCII 字符转换为每个都包含一个 u 的 Unicode 转义。

Unicode 转义被设计用来确保与多种字符集兼容。想想下面的场景。您收到一段编码您的文本编辑器不理解的代码(即代码包含您使用的编码中不可用的字符)。这可以通过用 Unicode 转义替换所有未知字符来解决。由于 ASCII 是字符集的最小公分母，所以通过用 Unicode 转义替换目标编码不支持的字符，总是可以用任何编码来表示 Java 代码。今天，Unicode 相当普遍，这应该不是问题，但我想在早期这是有用的。

转换后的版本等于初始版本，编译器将它们视为相同。由于这个过程是可逆的，编译器可以通过用相应的 Unicode 字符替换 Unicode 转义来返回到初始版本。

来自第 3.3 节

> 这个转换后的版本同样可以被 Java 编译器接受，并表示完全相同的程序。通过将每个[Unicode]转义序列(其中有多个 u)转换为一个少了一个 u 的 Unicode 字符序列，同时将每个只有一个 u 的[Unicode]转义序列转换为相应的单个 Unicode 字符，可以在以后从这种 ASCII 形式恢复出准确的 Unicode 源代码。

## 首选转义序列

因为 Unicode 转义在编译过程中先于其他所有处理，所以它们会造成相当大的混乱。因此，如果可能的话，最好避免它们。相反，在字符串或字符文字中，更喜欢使用[转义序列](https://docs.oracle.com/javase/tutorial/java/data/characters.html "Java escape sequences")，例如`\n`用于换行符，或者`\”`用于双引号。不需要对 ASCII 字符使用 Unicode 转义。

## Unicode 转义必须是格式良好的

我之前提到过，在编译过程中，只有**格式良好的** Unicode 转义被替换为 Unicode 字符。如果代码中有格式错误的 Unicode 转义，您将会得到一个错误。看看下面的例子。

```
public class IllFormedUnicodeEscape {
    public static void main(String[] args) {
        // user data is read from C:\data\users\profile
        System.out.println("User data");
    }
} 
```

这看起来像是一段无辜的代码。评论试图对读者有所帮助，传达一些重要的信息。不幸的是，在这个格式不正确的代码中隐藏着一个 Unicode 转义。正如您现在所知道的，Unicode 转义以`\u`开始，编译器希望后跟四个十六进制数字。当不符合这条规则时，编译器将抛出一个错误。

Windows 路径名使用反斜杠来分隔目录名。但是如果其中一个反斜杠后面跟有`u`字符，您可能会遇到意想不到的情况。这个例子中的问题是字符序列`\users`,它实际上是一个格式错误的 Unicode 转义。

![Java Unicode Letters](img/1c90e1f50f7ab57f961e240b5e5d616c.png)

## 将它发挥到极致

我们已经看了几个 Unicode 转义会造成危害的例子。你的眼睛应该已经训练到可以认出大部分了。在下一个例子中，我将向您展示一段代码，这是我在阅读 Joshua Bloch 和 Neal Gafter 的书[Java puzzles](http://www.javapuzzlers.com/ "Java Puzzlers book")时第一次看到的。

```
\u0070\u0075\u0062\u006c\u0069\u0063\u0020\u0020\u0020\u0020
\u0063\u006c\u0061\u0073\u0073\u0020\u0055\u0067\u006c\u0079
\u007b\u0070\u0075\u0062\u006c\u0069\u0063\u0020\u0020\u0020
\u0020\u0020\u0020\u0020\u0073\u0074\u0061\u0074\u0069\u0063
\u0076\u006f\u0069\u0064\u0020\u006d\u0061\u0069\u006e\u0028
\u0053\u0074\u0072\u0069\u006e\u0067\u005b\u005d\u0020\u0020
\u0020\u0020\u0020\u0020\u0061\u0072\u0067\u0073\u0029\u007b
\u0053\u0079\u0073\u0074\u0065\u006d\u002e\u006f\u0075\u0074
\u002e\u0070\u0072\u0069\u006e\u0074\u006c\u006e\u0028\u0020
\u0022\u0048\u0065\u006c\u006c\u006f\u0020\u0077\u0022\u002b
\u0022\u006f\u0072\u006c\u0064\u0022\u0029\u003b\u007d\u007d 
```

什么？真的吗？这看起来像是一场[代码混淆竞赛](http://www.ioccc.org/ "The International Obfuscated C Code Contest")的参赛作品。但是如果你仔细想想，这看起来应该是可以编译的，假设所有的 Unicode 转义实际上代表了组成一个有效 Java 程序的字符。我们了解到编译器做的第一件事就是寻找 Unicode 转义并替换它们。此时，它对程序结构一无所知。

你可以自己试试。将文本复制到名为`Ugly.java`的文件中。然后编译运行程序。顺便说一下，试图从 IDE 中运行它是没有意义的(至少 IntelliJ IDEA 是困惑的，只能显示弯曲的红线)。请改用命令行工具。

```
$ javac Ugly.java
$ java Ugly
Hello world 
```

此外，您可以使用`native2ascii`工具查看所有 Unicode 转义被替换后的代码。

```
$ native2ascii -reverse Ugly.java
public
class Ugly
{public
    static
void main(
String[]  
    args){
System.out
.println(
"Hello w"+
"orld");}} 
```

我只有一件事要说。仅仅因为你*能*并不意味着你*应该*。

## 摘要

从专业角度来说，我从来不需要插入 Unicode 转义。现在 Unicode 相当普遍，大多数文本编辑器可以显示非 ASCII 字符。如果我发现自己需要插入一个键盘上没有的字符，[我可以使用大多数操作系统提供的方法来输入它们](https://en.wikipedia.org/wiki/Unicode_input "Unicode input")。如果可能，避免 Unicode 转义，因为它们会造成混乱。请改用转义序列。

## 分享这篇文章