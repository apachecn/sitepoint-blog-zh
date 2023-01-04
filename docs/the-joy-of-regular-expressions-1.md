# 正则表达式的乐趣[1]

> 原文：<https://www.sitepoint.com/the-joy-of-regular-expressions-1/>

最近有人问我是否知道任何好的[正则表达式](https://www.sitepoint.com/learn-regex/)教程(最好是 PHP 版本)。这个问题来自于某个足够聪明的人，他可以“得到”正则表达式，但是他们无法找到可获得的帮助。

我见过的大多数正则表达式教程都是围绕着语法的增量式教学来组织的，这很快就会导致精神负担过重。例子通常围绕着类似‘aaabbbababa’…”的字符串——如果你正在为[瑞典流行音乐](http://en.wikipedia.org/wiki/ABBA)编写网络爬虫，这很好，但是对于其他人来说很混乱。虽然网上有[复制粘贴](http://regexlib.com/)正则表达式，但如果你不知道自己在做什么，使用它们可能比根本不使用还要糟糕。它们符合你的需求吗？哎呦！小心安全漏洞…

因此，我们将尝试另一个正则表达式教程，重点是*做*(在 PHP 中)，同时慢慢向您介绍 regexp(正则表达式的简写)语法。这将跨越几个博客帖子(将保持下面的内容更新)，并逐渐变得“更有趣”——不全是对初学者，但如果你坚持下去，希望你能掌握它。虽然是“regex 和 PHP”，但我将使用的 regex 语法在很大程度上可以移植到其他编程语言。

## 内容

### 第一部分

*   [资源](#Resources)
*   [背景](#PHP_PCRE_Background_Info)
*   为什么我需要正则表达式？
*   [掌握概念](#Grasping_the_Concept)
*   [边做边学](#Practice_makes_perfect)
*   [正匹配](#Positive_matching)
*   [表达式分隔符和模式修饰符](#Expression_Delimiters_and_Pattern_Modifiers)
    *   [preg_match()返回值](#preg_match_returns)
    *   [preg_match_all()](#preg_match_all)
*   [完全匹配](#Exact_Match)
    *   [一个童话故事](#Fairy_Tale)
    *   [战术](#Tactics)
*   [验证用户名](#Validating_a_username)
    *   [量化长度](#Quantifying_Length)

[第二部分在这里](https://www.sitepoint.com/the-joy-of-regular-expressions-2/)

## 资源

在我开始之前，有一两个选择链接。最好的 PHP 专用正则表达式教程之一是[here](http://www.tote-taste.de/X-Project/regex/printable.html)——它采用“增量语法”方法，但设法做到相当友好。也挺全面的。你在这里找不到任何新的正则表达式语法:只是故事的复述和一些有趣的例子。

维基百科当然有大量的信息和更多信息的链接，但可能比你想一次消化的还要多。

如果你真的感觉很勇敢，你也可以尝试一下 [Perl 正则表达式教程](http://perldoc.perl.org/perlretut.html)。尽管 Perl 的用于*执行*正则表达式的 API 与 PHP 的有很大不同，但正则表达式语法本身几乎完全相同，并且本教程有丰富的深入见解。

最后，[Regex 蔻驰](http://www.weitz.de/regex-coach/)(感谢 [Maarten](https://www.sitepoint.com/blog/) 的提示，我没有尝试就把它写下来了)是一个优秀的工具，不仅用于学习，还用于调试正则表达式和感受性能(专业人士可能也会发现它很有帮助)。

## 一些背景

PHP 附带了两套正则表达式函数和语法——[POSIX 扩展的](http://www.php.net/regex)正则表达式和 [Perl 兼容的正则表达式](http://www.php.net/pcre)扩展。

曾几何时，这些扩展的底层代码是不同的，但现在它们都使用相同的东西 [PCRE 引擎](http://sourceforge.net/projects/pcre)——它被捆绑到你下载的 PHP 发行版中。这里的讨论将纯粹集中在 Perl 兼容语法上——它更强大，并且或多或少已经成为一种标准——一旦你了解了它，你会发现它在很大程度上被大多数流行的编程语言所支持，从 [Java](http://java.sun.com/docs/books/tutorial/essential/regex/intro.html) 到 [Javascript](http://www.visibone.com/regular-expressions/) 。

注意 PHP 并不是唯一使用 PCRE 库的项目。虽然有些语言已经从头开始构建了自己的实现，但您会发现 PCRE 也用于 Apache、Ruby 和许多其他开源项目，这些项目只需很少的努力就需要强大的正则表达式支持。

## 为什么我需要正则表达式？

…因为除了最琐碎的文本处理之外，它们对任何事情都非常重要。我所说的“文本处理”指的是分析或修改字符串的任何事情，例如用<和>替换像< and >这样的字符，将包含分号的字符串拆分成一系列更小的字符串，计算特定单词出现的次数等等。对于这类简单的问题，你也许可以用基本的字符串功能来解决，但是任务越复杂，没有正则表达式就越难解决*。例如，考虑验证用户提交的 URL 是否符合 [RFC 2396 语法](http://www.zvon.org/tmRFC/RFC2396/Output/chapter11.html)——仅使用基本的字符串函数，非常困难。对于正则表达式，这是[可行的](http://pear.php.net/package/Validate/docs/0.6.4/Validate/Validate.html#methoduri)。*

深信不疑？大概不会。那么，对于“为什么是正则表达式？”的恐惧和厌恶呢？:**没有正则表达式，就写不出安全的 web 应用**。虽然 PHP 提供了[其他](http://www.php.net/ctype) [工具](http://www.php.net/strstr)可以用于简单的输入测试，但是很快你就会遇到一个只有正则表达式才有意义的问题。

否则——信不信由你——它们会让你的生活更轻松。例如，如果您考虑编写一个 [BBCode](http://en.wikipedia.org/wiki/BBCode) 解析器或从 HTML 文档中提取所有链接的任务，正则表达式可以使它变得轻而易举(另一个时间的例子)。

## 把握概念

也许正则表达式的最高障碍是概念上的——它们是什么？

一个古怪的答案是，它们是一种[领域特定语言](http://en.wikipedia.org/wiki/Domain-specific_language)——一种专为描述和匹配文本而设计的“迷你”编程语言。对于初学者来说，这可能不是一个有用的描述…

另一种思考正则表达式的方法是类比。大多数开发过基本数据库驱动的 web 应用程序的人都熟悉 SQL，它是一种从 RDBMS(如 MySQL)中检索(选择)数据的语言。正则表达式可以被认为是与 SQL 相同的东西，但是不是从数据库中提取数据，而是使用它们从文本块中提取数据。就像你将 SQL 语句嵌入到你的代码中一样(除非你在做某种 [ORM](http://blogs.tedneward.com/2006/06/26/The+Vietnam+Of+Computer+Science.aspx) ，你对正则表达式做同样的事情——你可以调用 [mysqli_query()](http://www.php.net/mysqli_query) 来执行你的 SQL 语句，你可以调用 [preg_match()](http://www.php.net/preg_match) 之类的函数来执行你的正则表达式。

当然，你可以用类比的方式走得太远，所以我就此打住。要点是正则表达式是你的[正则引擎](http://www.pcre.org/)的指令，告诉它如何从给定的文本块中找到你想要的字符。

## 边做边学…

像任何语言一样，学习正则表达式的最好方法是通过练习和耐心。当你已经记住了大部分的[语法](http://www.php.net/manual/en/reference.pcre.pattern.syntax.php)，并且能够阅读正则表达式而不必查阅文档时，你就开始变得自信了。

为此，将开始使用与 web 相关的示例来探索语法(您可能会重用这些示例)。还会有其他方法(包括避免正则表达式的解决方案)，但目的是说明正则表达式，所以请耐心等待。

## 正匹配

最容易的地方是从一些正则表达式开始，这些表达式完全匹配您正在寻找的文本，不需要任何额外的正则表达式语法。

这个例子有点做作，但不管怎样……你有一个表单询问用户是否阅读了“注册的条款和条件”，你将他们的答案存储在变量`$answer`中。你现在想测试他们是否对这个问题回答“是”——其他任何问题都被认为是“否”。使用 [preg_match()](http://www.php.net/preg_match) 函数你*可以*这样做…

```
 if ( preg_match('/yes/', $answer) ) {

    print "Say YES!!!n";

} else {

    print "what do you mean no?!?n";

} 

```

现在请允许我用一些细节来淹没你。这段代码问的是“我能在字符串`$answer`内的任何地方找到字符串‘yes’【T1 吗？”。

正则表达式是`preg_match()`—`'/yes/'`的第一个参数。在 PHP 中，正则表达式*总是*放在 PHP 字符串变量中(就像 SQL 一样)。这不同于其他一些语言，如 Javascript 和 Perl，正则表达式也可以是“文字”，例如(Javascript)；

```
 if ( /yes/.exec(answer) ) {
    alert("Say YES!!!");
} 

```

在 PHP 中，这意味着当涉及到反斜杠时你需要小心一点，并且要知道 PHP [如何解析字符串](http://www.php.net/manual/en/language.types.string.php#language.types.string.parsing)。

### 表达式分隔符和模式修饰符

那么这两个正斜杠在这里做什么呢？

```
 if ( preg_match('/yes/', $answer) ) { 

```

它们是标记正则表达式开始和结束的表达式分隔符。在这个例子中，不清楚为什么需要它们，但是目的是允许在表达式的末尾包含[模式修饰符](http://www.php.net/manual/en/reference.pcre.pattern.modifiers.php)。模式修饰符是 regex 引擎的“全局指令”,告诉它改变它的默认行为。我将很快看到模式修饰符，但一个例子是`/i`修饰符，它告诉引擎执行不区分大小写的*匹配，例如*

```
 if ( preg_match('/yes/i', $answer) ) {
    // etc. 

```

通过将`/i`模式修饰符放在表达式的末尾，我现在可以匹配字符串‘YES’和‘YES’(以及‘YES’或其他大小写的组合)。

注意，表达式分隔符不一定是正斜杠——除了之外，您还可以使用几乎任何东西*、反斜杠或字母数字字符。你只需要确保在模式的两端使用相同的*分隔符*。比如说；*

```
 if ( preg_match('%yes%i', $answer) ) {
    // etc. 

```

我使用了“%”字符而不是正斜杠来分隔表达式。当您想要搜索的模式包含分隔符(如果您匹配类似文件系统路径的 URL 的内容，这是很常见的)时，这可能很有用——只需更改分隔符，而不必对表达式中的字符进行转义(下一次将详细介绍转义)。

## preg_match()返回值

根据 PHP 手册， [preg_match()](http://www.php.net/preg_match) 返回*的次数*，它能够将你给出的模式(第一个参数'/yes/')与你正在搜索的字符串(第二个参数`$answer`)进行匹配。因此，如果它不能进行任何匹配，它将返回一个整数 0，这将使 PHP `if`条件失败。`preg_match()`也会在第一次匹配成功时*停止搜索*，因此*最多只会返回 1。现在你可能会想，如果结果不是 0 就是 1，为什么手册上不直接写 0 或者 1 呢？它试图表达的意思是,`preg_match()`一找到匹配就停止——当您在大型文档中运行正则表达式时，这一点可能很重要，在这种情况下，性能可能很重要:如果您想检查一个文档是否包含一个单词，而这个单词恰好在第一段中，您不希望正则表达式引擎在已经找到匹配时扫描整个文档*

注意，0 和 1 不是唯一的返回值——如果出错(比如模式不是有效的正则表达式语法),它将返回`FALSE`(另外你会得到一个粗鲁的错误警告)——确保你的[仔细检查](http://www.php.net/manual/en/types.comparisons.php)(如果你正在生成模式)。

更多关于从`preg_match()`中获取实际匹配的信息，请另找时间

## preg_match_all()

相比之下， [preg_match_all()](http://www.php.net/preg_match_all) 会继续搜索，直到它检查完您正在搜索的整个文本。这可以用下面的例子来说明:

```
 <?php

$answer1 = "no";
$answer2 = "yes";
$answer3 = "yes yes";

print preg_match('/yes/', $answer1)."n";           // Displays '0'
print preg_match('/yes/', $answer2)."n";           // Displays '1'
print preg_match('/yes/', $answer3)."n";           // Displays '1'

print preg_match_all('/yes/', $answer1, $m)."n";   // Displays '0'
print preg_match_all('/yes/', $answer2, $m)."n";   // Displays '1'
print preg_match_all('/yes/', $answer3, $m)."n";   // Displays '2' 

```

关于`preg_match()`和`preg_match_all()`的更多内容(比如如何从它们中获取匹配的文本)。

## 精确匹配

现在到目前为止，我只能确认`$answer`包含‘yes’*在它里面的某个地方*。也就是说，如果用户提供一个类似“巴<u>是</u>伊恩垃圾邮件过滤器”的答案，它将通过我的测试。我真的想 100%确定用户对条款和条件说了*确切的*‘是’。所以我需要更多一点的模式语法，即两个元字符…

```
 if ( preg_match('/^yes$/', $answer) ) {
    // etc. 

```

`^`元字符表示“断言我们从`$answer`的开始匹配”，而`$`元字符表示“断言我们匹配到`$answer`的*结束*”。因此,
表达式现在的意思是:

> 匹配单词“yes ”,但不匹配其他任何内容

最好不要纠结于术语“元字符”的哲学含义——只需记住这两个——`^`断言字符串的开始,`$`断言结束——它们结合起来帮助你使*与一个完整的字符串精确匹配。*

## 童话故事

您也可以单独使用它们。另一个人为的例子(我保证这将是最后一个):你有一个用户可以张贴童话故事的网站，你想确保每个故事*都以*“从前”开始；

```
 if ( !preg_match('/^Once upon a time/', $story) ) {
    die("This is not how a real fairy starts!n");
} 

```

然后，为了确保他们以“从此幸福地生活在一起”结束，你要加上…

```
 if ( !preg_match('/happily ever after$/', $story) ) {
    die("Don't give me sob stories!n");
} 

```

一会儿会有更多的元字符。

## 关于战术的一个注记

现在一些正则表达式大师可以手工构建巨型表达式作为单一模式。对于我们其余的人来说，更聪明的方法是保持小表达式，只做一个单一的任务。一旦正则表达式开始增长，当它们不再按预期运行时，调试会变得非常困难。

正如前面的例子所说明的，你可以从重复的小模式中获得很多好处，缺点是*潜在的*性能开销，这取决于你在做什么，以及额外的代码行。

如果您发现您的正则表达式在增长，您可以使用`/x` pattern 修饰符使它们更具可读性，它允许您将一个正则表达式拆分成多行并包含注释——我将在下一次举例说明，以及可以帮助您分阶段使用正则表达式处理文本的方法。

## 验证用户名

让我们来看一个离家更近的例子，一个典型的初学者错误是，在 web 应用程序中添加用户认证系统时，允许用户在注册时随意选择用户名。很快，一些聪明的家伙出现了，他们将自己注册为类似“admin”(注意开头的空格字符)的东西，然后在你的网站上到处张贴令人困惑的帖子，在最坏的情况下，他们会利用构造糟糕的代码。

一般来说，*对用户名等关键标识符非常严格是个好主意，所以这是引入一种特殊的正则表达式元字符的好机会: *[字符
类](http://www.php.net/manual/en/reference.pcre.pattern.syntax.php#regexp.reference.squarebrackets)* 。除了“内置”的元字符，比如你见过的`^`和`$`字符，你也可以通过使用一个*字符类*来定义你自己的元字符，它用来表示一个*单个*字符。直接跳到一个例子…*

```
 if ( !preg_match('/^[a-zA-Z0-9_]+$/', $username) ) {
    die("Invalid username: only alpha numeric characters allowed.");
} 

```

我这里的字符类是`[a-zA-Z0-9_]`——它匹配任何符合以下条件之一的字符；

*   它是介于 a 和 z 之间的小写字母
*   …或者是介于“A”和“Z”之间的大写字符
*   …或者是一个介于“0”和“9”之间的数字
*   …或者它只是一个“_”下划线字符。

出现在字符类中的减号'-'指定了一个*范围*，你会注意到，在 ASCII 字符的[表中的‘a’和‘z’之间，你有字母表中所有的小写字母，排序很好。](http://www.asciitable.co.uk/)

## 量化长度

你可能也注意到了我在最后一个例子中加入了另一个元字符——量词。

`+`元字符是指模式中字符(或元字符)前面的*，并将其含义修改为“这个字符的一个或多个”——它*量化了*的长度。所以我的例子…*

```
 if ( !preg_match('/^[a-zA-Z0-9_]+$/', $username) ) {
    // etc. 

```

…要求用户名*至少有*个字符长，但对最大长度没有限制。现在，这实际上不是一个聪明的想法——用户名可能需要至少 5 个字符长才可读，并且，考虑到 VARCHAR 列的空间限制和屏幕分辨率，设置一个最大长度可能是明智的；说 20 个字。

不使用`+`量词，我可以使用我自己定义的最小/最大量词，使用花括号`{ }`；

```
 if ( !preg_match('/^[a-zA-Z0-9_]{5,20}$/', $username) ) {
    // etc. 

```

就像`+`量词一样，最小/最大量词适用于模式中位于字符(或元字符)之前的*。*

在这里，您开始看到正则表达式相对于其他方法的一些威力。我的用户名检查现在不仅查看用户名的内容(它包含哪些字符),还查看*和*它的长度，只需一条语句。

如你所知，最小/最大量词允许你做其他的长度检查，这取决于你是否省略了最小或最大量词。

```
 # Username must be _at least_ 5 characters long but no max limit...
if ( !preg_match('/^[a-zA-Z0-9_]{5,}$/', $username) ) {
    // etc. 

```

…还有…

```
 # Username must be _exactly_ 10 characters long...
if ( !preg_match('/^[a-zA-Z0-9_]{10}$/', $username) ) {
    // etc. 

```

好了——第一部分到此为止。到目前为止还没有太激烈的事情——下次会有更多的正则表达式操作…

## 分享这篇文章