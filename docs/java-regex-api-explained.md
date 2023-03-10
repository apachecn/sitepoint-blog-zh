# Java Regex API 解释道

> 原文：<https://www.sitepoint.com/java-regex-api-explained/>

这是一个漫长的过程，但是 java.util.regex 包是对 Java 1.4 的一个重要且非常有用的补充。对于经常处理基于文本的内容的 Web 开发人员来说，这代表着生产力和效率的大幅提升。Java 正则表达式可以用在客户端 Java 小程序中，也可以用在服务器端 J2EE 和 JSP 代码中。

使用[正则表达式](https://www.sitepoint.com/learn-regex/)和 regex 包，您可以轻松地描述、定位和操作复杂的文本模式。相信我，这绝对是一个“没有它我怎么过的？”类似的事情。

在本文中，我将解释正则表达式背后的一般思想，解释 java.util.regex 包是如何工作的，然后快速地看一下 String 类是如何改进以利用正则表达式的。

在我们进入 Java regex API 本身的细节之前，让我们快速地看一下正则表达式，或者对于业内人士来说，一个“regex”，实际上是什么。如果您已经知道什么是正则表达式，请随意浏览下一节。

##### 什么是正则表达式？

正则表达式是一系列元字符和文字，允许您使用模式描述文本中的子字符串。这些元字符本身实际上形成了一种微型语言。事实上，在许多方面，您可以将正则表达式视为一种针对自由流动文本的 SQL 查询。考虑下面的句子:

```
My name is Will and I live in williamstown.
```

我们如何找到文本“Will”的所有出现，而不管是否使用了大写或小写的“w ”?使用正则表达式，您可以通过由一系列元字符和文字组成的模式来描述这一需求。这里有这样一个模式:

```
[Ww]ill
```

这个很简单。有趣的部分是[Ww]分组——它表示括号内的任何一个字母(在本例中，大写的“W”或小写的“W”)都是可接受的。因此，这个正则表达式将匹配以大写或小写字母`w`开头，后跟文字`i`，然后是`l`，然后是另一个`l`的文本。

让我们更进一步。上面的正则表达式实际上将匹配两次出现的`will`——名称`Will`和文本`williamstown`中的前 4 个字符。我们可能只想搜索`will`和`Will`，而不想搜索只包含这 4 个字符的单词。这是一个改进的版本:

```
b[Ww]illb
```

`b`是我们描述单词边界的方式。单词边界将匹配空格、制表符以及一行的起点和终点。这有效地排除了匹配的`williamstown`,因为`williamtown`中的第二个`l`后面没有单词边界——后面是一个`i`。

我可以用一整篇文章来讲述制作正则表达式的艺术，但是我在这里的重点是 Java 正则表达式包本身。因此，让我们再检查一个正则表达式——在本文的其余部分，我们将坚持使用这个表达式。

```
 (w+)@(w+.)(w+)(.w+)?
```

让我们采用分而治之的方法来分析这种模式。`(w+)`分组(它出现两次——检查开始的那个)查找单词字符，如`w`所示。`+`表示必须出现一个或多个单词字符(不一定是同一个)。这后面必须跟一个文字`@`字符。这里实际上并不需要括号，但是它们确实将表达式分成了组，您很快就会看到以这种方式形成逻辑组是非常有用的。

基于我们示例正则表达式的第一部分，即`(w+)@`部分，这里有一些到目前为止满足要求的示例:

```
 billy@ 

  joe@ 

  francisfordcoppola@
```

让我们继续进行下一部分。`(w+.)`分组与此类似，但是需要一个句点来进行匹配。使用反斜杠对句点进行了转义，因为句点字符本身就是一个 regex 元字符(匹配任何字符的通配符)。如果您想匹配元字符的字面意义，您必须始终以这种方式对元字符进行转义。
让我们来看看目前为止符合要求的几个例子:

```
 billy@webworld. 

  joe@optus. 

  francisfordcoppola@myisp.
```

`(w+)`分组与第一个分组相同，它查找一个或多个单词字符。所以，你肯定已经意识到了，我们的正则表达式是用来匹配电子邮件地址的。

目前满足要求的几个例子:

```
 billy@webworld.com 

  joe@optus.net 

  francisfordcoppola@myisp.com
```

我们快到了。在这一点上,`(.w+)*`分组应该是最有意义的——我们正在寻找一个句点，后跟一个或多个单词字符。但是右括号后面的`*`是怎么回事？在正则表达式中，我们使用`*`来表示前面的元字符、文字或组可以出现零次或多次。例如，`wd*`将匹配一个单词字符，后跟零个或多个数字。在我们的例子中，我们使用括号将一系列元字符组合在一起，因此`*`适用于整个组。因此，您可以将`(.w+)*`解释为“匹配一个句点后跟一个或多个单词字符，并匹配该组合零次或多次”。

满足完整正则表达式要求的几个示例:

```
 fred@vianet.com 

  barney@comcorp.net.au 

  wilma@mjinteractive.iinet.net.au
```

随着我们的正则表达式的完成，是时候转向 Java 方面了。您需要知道的第一件事是如何解决 Java 字符串和正则表达式之间相当不幸的语法冲突。这是一个你，开发者，必须处理的冲突。

##### Java 安全正则表达式

这有点烦人，但事实是，您需要使您的正则表达式在 Java 代码中安全使用。这意味着任何反斜杠分隔的元字符都需要转义。这是因为反斜杠字符在 Java 中有其特殊的含义。因此，我们的示例电子邮件地址正则表达式必须重写如下:

```
 String emailRegEx = "(\w+)@(\w+\.)(\w+)(\.\w+)*";
```

请记住，如果您实际上需要匹配一个字面反斜杠，您必须再次加倍。阅读 Java 安全正则表达式可能会更加困难，因此您可能希望首先创建一个“正则”正则表达式(也许是一个 regregregex？)并将副本放在手边——也许放在代码注释中。

那么，我们如何利用这一切来实现一些有用的东西呢？在某些情况下，你可以简单地直接在`String`类上调用`replace()`和`replaceAll()`这样的方法——我们稍后会快速地看一下这种方法。然而，对于更复杂的正则表达式操作，采用更面向对象的方法会更好。

##### 模式类

这里有一些令人耳目一新的东西:java.util.regex 包只包含三个类——其中一个是例外！如您所料，这是一个非常容易学习的 API。下面是使用 regex 包通常要遵循的 3 个步骤:

1.  使用 Pattern 类编译 regex 字符串。

3.  使用 Pattern 类获取 Matcher 对象。

5.  调用 Matcher 上的方法来获取任何匹配。

接下来我们将看一下 Matcher 类，但是让我们深入看一下 Pattern 类。这个类允许您编译您的正则表达式——这有效地优化了它的效率和多个目标字符串(您想要测试编译的正则表达式的字符串)的使用。考虑下面的例子:

```
 String emailRegEx = "(\w+)@(\w+\.)(\w+)(\.\w+)*"; 

      // Compile and get a reference to a Pattern object. 

      Pattern pattern = Pattern.compile(emailRegEx); 

      // Get a matcher object - we cover this next. 

      Matcher matcher = pattern.matcher(emailRegEx);
```

注意，模式对象是通过模式类的静态编译方法获取的——不能使用`new`实例化模式对象。一旦有了模式对象，就可以用它来获取对匹配器对象的引用。接下来我们看看 Matcher。

##### Matcher 类

前面，我提到正则表达式是一种针对自由流动文本的 SQL 查询。这个类比并不完全完美，但是当使用 regex API 时，沿着这些思路思考会有所帮助。如果您认为`Pattern.compile(myRegEx)`是一种 JDBC PreparedStatement，那么您可以认为模式类`matcher(targetString)`方法是一种 SQL SELECT 语句。研究以下代码:

```
 // Compile the regex. 

    String regex = "(\w+)@(\w+\.)(\w+)(\.\w+)*"; 

    Pattern pattern = Pattern.compile(regex); 

    // Create the 'target' string we wish to interrogate. 

    String targetString = "You can email me at g_andy@example.com or andy@example.net to get more info"; 

    // Get a Matcher based on the target string. 

    Matcher matcher = pattern.matcher(targetString); 

    // Find all the matches. 

    while (matcher.find()) { 

      System.out.println("Found a match: " + matcher.group()); 

      System.out.println("Start position: " + matcher.start()); 

      System.out.println("End position: " + matcher.end()); 

    }
```

这里发生了一些有趣的事情。首先，注意我们使用了 Pattern 类的`matcher()`方法来获得一个 Matcher 对象。这个对象仍然使用我们的 SQL 类比，是保存结果匹配的地方——想想 JDBC 结果集。当然，记录是匹配正则表达式的文本部分。

`while`循环根据 Matcher 类的`find()`方法的结果有条件地运行。这个方法将解析足够的目标字符串来进行匹配，此时它将返回 true。小心:任何在调用`find()`之前使用匹配器的尝试都将导致未检查的`IllegalStateException`在运行时被抛出。

在 while 循环体中，我们使用 Matcher 类的`group()`方法检索匹配的子串。我们的 while 循环执行两次:对目标字符串中的每个电子邮件地址执行一次。每次，它打印由`group()`方法返回的匹配的电子邮件地址和子串位置信息。看一下输出:

```
Found a match: g_andy@example.com 

Start position: 20 

End position: 38 

Found a match: andy@example.net 

Start position: 42 

End position: 58
```

如您所见，这只是使用匹配器的`start()`和`end()`方法来找出匹配的子字符串在目标字符串中出现的位置。接下来，让我们仔细看看`group()`方法。

##### 了解组

如您所知，`Matcher.group()`将从目标字符串中检索一个完全匹配。但是如果你也对匹配文本的子部分或“子群”感兴趣呢？在我们的电子邮件示例中，可能需要提取电子邮件地址的主机名部分和用户名部分。看看我们的匹配器驱动 while 循环的修订版:

```
 while (matcher.find()) { 

      System.out.println("Found a match: " + matcher.group(0) + 

                         ". The Username is " + 

                         matcher.group(1) + " and the ISP is " + 

                         matcher.group(2)); 

    }
```

您可能还记得，组被表示为模式的一个子部分周围的一组括号。第一组使用`Matcher.group()`定位，或者，如示例中，使用更具体的`Matcher.group(0)`定位，代表整个匹配。使用同样的`group(int index)`方法可以找到更多的组。以上示例的输出如下:

```
Found a match: g_andy@example.com.. The Username is g_andy and the ISP is example. 

Found a match: andy@example.net.. The Username is andy and the ISP is example.
```

如您所见，`group(1)`检索电子邮件地址的用户名部分，`group(2)`检索 ISP 部分。当然，在创建自己的正则表达式时，如何对模式进行逻辑分组取决于你自己。这个例子中的一个小疏忽是周期本身被捕获为由`group(2)`返回的子组的一部分！

请记住，子组是根据其左括号的顺序从左到右进行索引的。当您处理嵌套在其他组中的组时，这一点尤其重要。

##### 关于模式和匹配器类的更多信息

这是这个非常小但非常强大的 Java API 的核心。然而，一旦你有机会尝试这些基础知识，你还应该了解一些其他的细节。Pattern 类有许多标志，可以用作其`compile()`方法的第二个参数。例如，您可以使用`Pattern.CASE_INSENSITIVE`告诉 regex 引擎匹配 ASCII 字符，不考虑大小写。

```
Pattern.MULTILINE is another useful one. You will sometimes want to tell the regex engine that your target string is not a single line of code; rather, it contains several lines that have their own termination characters.

If you need to, you can combine multiple flags by using the java | (vertical bar) operator. For instance, if you wanted to compile a regex with multiline and case insensitivity support, you could do the following:

```

```
Pattern.compile(myRegEx, Pattern.CASE_INSENSITIVE | Pattern.MULTILINE );
```

Matcher 类也有许多有趣的方法:`String replaceAll(String replacementString)`和`String replaceFirst(String replacementString)`尤其值得一提。

`replaceAll()`方法获取一个替换字符串并用它替换所有匹配。`replaceFirst()`方法非常相似，但是将——你猜对了——只替换第一次出现的匹配。看看下面的代码:

```
 // Matches 'BBC' words that end with a digit. 

    String thePattern = "bbc\d"; 

    // Compile regex and switch off case sensitivity. 

    Pattern pattern = Pattern.compile(thePattern, Pattern.CASE_INSENSITIVE); 

    // The target string. 

    String target = "I like to watch bBC1 and BbC2 - I suppose ITV is okay too"; 

    // Get the Matcher for the target string. 

    Matcher matcher = pattern.matcher(target); 

    // Blot out all references to the BBC. 

    System.out.println(matcher.replaceAll("xxxx") );
```

这是输出结果:

```
I like to watch xxxx and xxxx - I suppose ITV is okay too
```

##### 反向引用

有必要快速浏览一下另一个重要的正则表达式主题:反向引用。反向引用允许您在 regex 引擎执行时访问捕获的子组。基本上，这意味着您可以在稍后的模式中引用匹配的早期部分的子组。假设您需要检查一个 3 个字母的单词的目标字符串，这些单词以相同的字母开始和结束——哇，sos，mum，诸如此类。这里有一个模式可以完成这项工作:

```
(w)(w)(1)
```

在这种情况下，`(1)`组包含对模式中第一个匹配的反向引用。基本上，第三个带括号的组只有在该位置的字符与第一个带括号的组中的字符相同时才匹配。当然，如果你想反向引用第二组，你可以简单地用`2`代替`1`。这很简单，但是在很多情况下，非常有用。

Matcher 对象的替换方法(以及 String 类的对应方法)也支持在替换字符串中进行反向引用的符号。它以同样的方式工作，但是使用美元符号而不是反斜杠。因此，`matcher.replaceAll("$2")`将用正则表达式的第二个子组匹配的值替换目标字符串中的所有匹配。

##### 字符串类正则表达式方法

正如我前面提到的，Java String 类已经更新，可以利用正则表达式。在简单的情况下，您可以通过直接在 String 类上调用支持 regex 的方法来完全绕过使用 regex API。有 5 种这样的方法可用。

您可以使用`boolean matches(String regex)`方法快速确定一个字符串是否完全匹配特定的模式。恰当命名的`String replaceFirst(String regex, String replacement)`和`String replaceAll(String regex, String replacement)`方法允许你快速替换脏文本。最后，`String[] split(String regEx)`和`String[] split(String regEx, int limit)`方法允许您基于正则表达式将一个字符串分割成子字符串。这最后两种方法在概念上类似于`java.util.StringTokenizer`，只是更加强大。

请记住，在许多情况下，使用 regex API 和更面向对象的方法更有意义。原因之一是这种方法允许您预编译正则表达式，然后在多个目标字符串中使用它。另一个原因是它的能力更强。您将很快掌握何时选择一种方法而不是另一种方法的诀窍。

希望我已经让您对 regex API 有了一个良好的开端，并让那些还没有发现这个强大工具的人认真考虑一下。一个小提示:不要浪费几个小时的宝贵开发时间去尝试制作一个复杂的正则表达式——它可能已经存在了。有很多地方，比如 www.regexlib.com，免费提供一大堆。

## 分享这篇文章