# 正则表达式的替代:apg-exp

> 原文：<https://www.sitepoint.com/alternative-to-regular-expressions/>

*这篇文章由[塞巴斯蒂安·塞茨](https://www.sitepoint.com/community/users/m3g4p0p/activity)和[阿尔米尔·比耶迪克](https://www.sitepoint.com/author/abijedic/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

几乎没有任何程序员不需要不时地以某种形式使用[正则表达式](https://www.sitepoint.com/learn-regex/)。对许多人来说，模式语法看起来晦涩难懂。本教程将介绍一个新的模式匹配引擎，[**APG-exp**](https://github.com/ldthomas/apg-js2-exp)——这是对 [RegExp](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp) 的一个功能丰富的替代，带有一个 [ABNF](https://tools.ietf.org/html/rfc5234) 模式语法，看起来稍微容易一些。

## 快速比较

您是否曾经需要验证电子邮件地址并遇到过类似的情况？

```
^[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[A-Z0-9-]+\.)+[A-Z]{2,6}$ 
```

模式匹配引擎是这项工作的合适工具。这是一个设计良好、写得很好的正则表达式。效果很好。那么有什么不喜欢的呢？

嗯，如果你是正则表达式的专家，什么都不是。但是对于我们其他人来说，他们可能是

*   难以阅读
*   更难写了
*   难以维护

正则表达式语法有着悠久的历史,并且深深地融入到我们作为程序员每天使用的许多工具和语言中。

然而，有一种替代语法已经存在了几乎同样长的时间，非常受互联网技术规范的作者和用户的欢迎，具有正则表达式的所有功能，但在 JavaScript 编程领域很少使用。即 **A** 增强的**B**ackus-**N**aur**F**ORM，或 ABNF，由 [IETF](https://www.ietf.org/) 在 [RFC 5234](https://tools.ietf.org/html/rfc5234) 和 [RFC 7405](https://tools.ietf.org/html/rfc7405) 中正式定义。

让我们看看同样的电子邮件地址在 ABNF 会是什么样子。

```
email-address   = local "@" domain
local           = local-word *("." local-word)
domain          = 1*(sub-domain ".") top-domain
local-word      = 1*local-char
sub-domain      = 1*sub-domain-char
top-domain      = 2*6top-domain-char
local-char      = alpha / num / special
sub-domain-char = alpha / num / "-"
top-domain-char = alpha
alpha           = %d65-90 / %d97-122
num             = %d48-57
special         = %d33 / %d35 / %d36-39 / %d42-43 / %d45 / %d47 
                / %d61 / %d63 / %d94-96 / %d123-126 
```

当然，它没有 HTML 和 XML 紧凑，但是它被设计成既能被机器也能被人类阅读。我猜想，只要对通配符搜索模式有一点点了解，你就可以用“简单英语”来理解这里发生的事情。

*   电子邮件地址被定义为由`@`分隔的本地部分和域
*   本地部分是一个单词，后跟可选的点分隔单词
*   该域是一个或多个点分隔的子域，后跟单个顶级域
*   在这里，您可能不知道但可能猜到的唯一事情是:
    *   正如通配符`*`表示“零次或多次”一样，`1*`表示“一次或多次”，而`2*6`表示最小 2 次和最大 6 次重复
    *   `/`分隔备选选项
    *   `%d`定义十进制字符代码和字符代码范围
    *   例如，`%d35`代表`#`，ASCII 十进制 35
    *   `%d65-90`代表范围`A-Z`内的任意字符，ASCII 小数 65-90

在[示例 1](http://codepen.io/SitePoint/pen/gMLgZQ) 中，RegExp 和 **apg-exp** 会针对该电子邮件地址进行比较。

[**apg-exp**](https://github.com/ldthomas/apg-js2-exp) 是一个模式匹配引擎，设计为具有 RegExp 的外观，但使用 ABNF 语法进行模式定义。在接下来的几节中，我将带您了解:

*   如何让 **apg-exp** 进入你的应用程序
*   ABNF 语法简明指南
*   使用**APG-exp**—几个例子
*   下一步何去何从—更多详细信息和高级示例

## 启动并运行—如何实现

### npm

如果您在一个 [Node.js](https://nodejs.org/en/) 环境中工作，从您的项目目录运行:

```
npm install apg-exp --save 
```

然后，您可以用`require()`在代码中访问它。

例如:

```
var ApgExp = require("apg-exp");
var exp = new ApgExp(pattern, flags);
var result = exp.exec(stringToMatch); 
```

### 开源代码库

要从 GitHub 获得代码的副本，您可以将存储库克隆到您的项目目录中:

```
git clone https://github.com/ldthomas/apg-js2-exp.git apg-exp 
```

或者[下载成 zip 文件](https://github.com/ldthomas/apg-js2-exp/archive/master.zip)。

然后在`page.html`中:

```
<!-- optional stylesheet used in tutorial examples -->
<link rel="stylesheet" href="./apg-exp/apgexp.css">
<script src="./apg-exp/apgexp-min.js"></script>

<script> var useApgExp = function(){
      var exp = new ApgExp(pattern, flags); 
      var result = exp.exec(stringToMatch);
      /* do something with the result */
  } </script> 
```

### 加拿大

您还可以使用 [RawGit](https://rawgit.com/) 直接从 GitHub 源代码创建一个 CDN 版本。然而，请务必阅读[无正常运行时间或支持保证](https://github.com/rgrove/rawgit/wiki/Frequently-Asked-Questions#i-need-guaranteed-100-uptime-should-i-use-cdnrawgitcom)(事实上，请务必阅读整个[常见问题](https://github.com/rgrove/rawgit/wiki/Frequently-Asked-Questions))。

本教程中的所有示例都使用了以下内容。

```
<link rel="stylesheet"
 href="https://cdn.rawgit.com/ldthomas/apg-js2-exp/89c6681798ba9e47583b685c87b244406b18a26d/apgexp.css">
<script
 src="https://cdn.rawgit.com/ldthomas/apg-js2-exp/c0fc3adac954a6f6ad6f265fd2f8f06f68001e10/apgexp-min.js"
 charset="utf-8"></script> 
```

这些文件缓存在 [MaxCDN](https://www.maxcdn.com/) 服务器上，只要它们仍然可用，你就可以免费使用它们进行测试。然而，对于生产，您应该将`apgexp-min.js`和`apgexp.css`的副本放在您自己的服务器上，以保证访问
，并将它们包含在您的页面中，以最适合您的应用程序。

## ABNF 简明指南

ABNF 是一种描述短语的语法，短语可以是任何字符串。正如你在上面的电子邮件例子中看到的，它允许你将复杂的短语分解成一组简单的短语。短语定义具有以下形式:

```
name = elements LF 
```

其中`LF`是换行符(新行`\n`)字符。

下表是元素的简短指南(完整指南见 [SABNF](https://github.com/ldthomas/apg-js2/blob/master/SABNF.md) )。

| 元素 | 定义 | 评论/示例 |
| --- | --- | --- |
| 名字 | 规则名称 | alphanum +连字符(*见下面的注释 1*) |
| %d32 | 单字符代码 | 字符代码的十进制值 |
| %d97.98 | 字符代码串 | 字母表 |
| %d48-57 | 字符代码范围 | 任何 ASCII 数字 0-9 |
| “abc” | 不区分大小写的文字字符串 | abc 或 ABC 等。 |
| %s"aBc " | 区分大小写的文字字符串 | 仅 aBc(= % d 97 . 66 . 99) |
| 空间 | 连接两个元素 | %d97 %d98 (= ab) |
| / | 分隔两个交替元素 | %d97 / %d98 (= a 或 b) |
| *元素 | 元素的零次或多次重复 | (*参见下面的注释 2*) |
| (元素) | 分组，被视为单个元素 | (*参见下面的注释 3*) |
| [元素] | 可选分组 | [%d97] %d98 (ab 或 b) |
| %^ | 输入字符串的开头 | 仅将位置匹配为空短语 |
| %$ | 输入字符串结束 | 仅将位置匹配为空短语 |
| &元素 | 向前看元素 | 元素必须跟在当前字符串位置之后 |
| ！元素 | 向前看没有元素 | 元素不能跟在当前字符串位置之后 |
| & &元素 | 向后寻找元素 | 元素必须在当前字符串位置之前 |
| ！！元素 | 向后看没有元素 | 元素不能在当前字符串位置之前 |
| \名称 | 对规则“名称”的反向引用 | 匹配先前找到的“名称”短语 |
| ；评论 | 评论 | 评论来自；到行尾 |

**注 1** :规则名称必须以字母字符开头，位于该行的第一列。延续行必须以空格或制表符开始。第一个规则定义了要匹配的整个短语或模式。以下规则定义了整个短语中的命名子短语(或命名捕获)。

**注 2** :重复的一般形式是`n*m`，定义最小`n`和最大`m`重复。简写符号可以是从零到`m`的`*m`，从`n`到无穷大的`n*`，或者仅仅是`n`到`n*n`。

**注 3** :分组对于保持交替和连接的预期行为非常重要。串联比交替具有更紧密的绑定。如果

```
phrase1 = elem (foo / bar) blat LF
phrase2 = (elem foo) / (bar blat) LF
phrase3 = elem foo / bar blat LF 
```

然后，`phrase1`匹配`elem foo blat`或`elem bar blat`，`phrase2`和`phrase3`都匹配`elem foo`或`bar blat`。要小心谨慎，不要随意使用分组。

## 使用**APG-exp**—几个例子

现在你的应用程序中已经包含了 **apg-exp** ，并且知道了编写模式语法的基本知识，让我们直接进入有趣的部分，看看如何使用它的一些例子。

我已经把构造对象的无聊细节放在下面的下一节了。你可以根据需要参考它来理解例子。我也将跳过错误处理，但是您应该知道，如果有模式错误，构造函数将抛出一个`ApgExpError`异常对象，该对象有几个方便的函数用于格式化显示模式错误。您的`try/catch`块可能看起来像这样:

```
try {
  var exp = new ApgExp(pattern, flags);
  var result = exp.exec(stringToMatch);
  if (result) {
    // do something with results
  } else {
    // handle failure
  }
} catch(e) {
  if (e.name === "ApgExpError") {
    // display pattern errors to console
    console.log(e.toText());
    // display pattern errors to HTML page
    $("#errors").html(e.toHtml());
  } else {
    // handle other exceptions
  }
} 
```

### 电话号码

电话号码是一个常见的表单验证问题，也是介绍一些基础知识的好机会。通常你只想知道在一个看起来像电话号码的表格中输入了 10 个数字，而不用担心北美计划或 T2 国际号码的细节。

我们只需要它以一个括号、`(`或一个数字开始，并包含由 0 到 3 个非数字分隔的 3，3 & 4 个数字块。这足以接受最常见的格式，也足以将地区、办公室和用户代码作为任何重新格式化的捕获组。

这是它在 ABNF 的样子:

```
phone-number = ["("] area-code sep office-code sep subscriber
area-code    = 3digit                       ; 3 digits
office-code  = 3digit                       ; 3 digits
subscriber   = 4digit                       ; 4 digits
sep          = *3(%d32-47 / %d58-126 / %d9) ; 0-3 ASCII non-digits
digit        = %d48-57                      ; 0-9 
```

[示例 2](http://codepen.io/SitePoint/pen/QEGdYL) 演示了这一点，并为您提供了改变电话号码格式的机会:

[示例 3](http://codepen.io/SitePoint/pen/JKbExy) 让我们来玩一下模式语法，看看您可能会看到的错误消息:

RegExp 语法，

```
\(?(\d{3})\D{0,3}(\d{3})\D{0,3}(\d{4}) 
```

也不难和[例 4](http://codepen.io/SitePoint/pen/vKygbr) 并列对比。

### 日期

现在让我们稍微放大一下，看看 **apg-exp** 和 RegExp 在匹配日期时是如何比较的。

我们的日期格式要求是:

```
mm/dd/yy or mm/dd/yyyy
dd/mm/yy or dd/mm/yyyy
mm, 1-12 or 01-12, i.e. with or without leading zero
dd, 1-31 or 01-31, i.e. with or without leading zero
yy, 00-99
yyyy, 1900-1999 or 2000-2099 
```

`mm/dd/yyyy`格式本身并不太难，但是限制数值范围会使它变得更复杂。和 ABNF 一起到达那里看起来像这样:

```
date     = %^ (mm-first / dd-first) %$
mm-first = mm "/" dd "/" yyyy   ; month before day
dd-first = dd "/" mm "/" yyyy   ; day before month
dd       = "0" digit1           ;    01-09
         / ("1"/"2") digit      ; or 10-29
         / "3" %d48-49          ; or 30-31
         / digit1               ; or 1-9
mm       = "0" digit1           ;    01-09
         / "1" %d48-50          ; or 10-12
         / digit1               ; or 1-9
yyyy     = ("19" / "20") 2digit ; 1900-1999 or 2000-2099
         / 2digit               ; or 00-99
digit    = %d48-57              ; 0-9
digit1   = %d49-57              ; 1-9 
```

这些评论使它变得不言自明。请注意，`dd`、`mm`和`yyyy`具有最短的最后选择。这一点非常重要，因为 **apg-exp** 总是采取“第一场比赛获胜”的方法来解决替代方案。从左到右尝试替换，一旦找到匹配，任何剩余的替换都将被忽略。在这里，这意味着当一个模式可以匹配一位或两位数时，两位数的模式需要优先。

按照与上面完全相同的方法，将日期分解成`dd`、`mm`和`[yy]yy`的交替模式，然后将它们组合成完整的日期，得到下面的 RegExp 语法:

```
^(?:((?:0[1-9])|(?:1[0-2])|(?:[1-9]))/((?:0[1-9])|(?:(?:1|2)[0-9])|(?:3[0-1])|(?:[1-9]))|((?:0[1-9])|(?:(?:1|2)[0-9])|(?:3[0-1])|(?:[1-9]))/((?:0[1-9])|(?:1[0-2])|(?:[1-9])))/((?:19|20)?[0-9][0-9])$ 
```

我不是正则表达式专家，所以可能有一些方法可以缩短它，但是这个方法是有效的。你可以在[例 5](http://codepen.io/SitePoint/pen/beBgzy) 中自己对比一下。跳到那边去解决它。

### 使用递归匹配嵌套对(())

最后，我想展示如何匹配嵌套的圆括号、方括号等。虽然这是一个非常重要的模式匹配问题，但您不能用 RegExp 来解决。考虑以下匹配括号对的 ABNF:

```
P = L P R / L R
L = "("
R = ")" 
```

注意，规则`P`出现在它自己的定义中。这叫做递归。尽管一些[风格的正则表达式引擎](http://www.regular-expressions.info/recurse.html)确实支持递归，一些 [RegExp 工具](http://xregexp.com/api/#matchRecursive)确实提供了一些递归功能，但是 JavaScript 的 RegExp 根本不支持递归。上面的`L`和`R`已经被选择来匹配括号，但是它们可以是几乎任何东西，只要`L`不能与`R`匹配相同的东西。

看一下[例 6](http://codepen.io/SitePoint/pen/JKEdoe) ，我们将从递归中得到一些乐趣。

在结束匹配、嵌套对的主题之前，我想展示几个真实世界的例子，apg-exp 可以帮助你。

在[示例 6](http://codepen.io/SitePoint/pen/JKEdoe) 中，您看到了如何在配对中配对，以及如何在括号中包含文本。假设你的作业是写一个程序，这样:如果光标在一个左花括号`{`上，高亮显示匹配的右花括号`}`。

[例 7](http://codepen.io/SitePoint/pen/LZxVEq) 向你展示了这个问题的解决方案。你需要了解`sticky`模式。

最后一个嵌套对的例子。你是否曾经想注释掉一大块 HTML，却发现块中已经有注释了？令人沮丧？只要在互联网上搜索“HTML 嵌套注释问题”就能感受到这种挫败感。[例 8](http://codepen.io/SitePoint/pen/gMgppp) 向你展示了这个问题的一个可能的解决方案。*警告*——这件可能会让你有点捉襟见肘。你需要理解`result.rules`对象和`global`模式。

### 将这一切结合在一起

最后，让我们来看看一个完整的表单验证示例是什么样子的。

通常，创建新帐户时，会要求您输入用户名、电子邮件地址、密码和密码确认。让我们要求用户名由 3-32 个 ASCII 字母、连字符和句点组成。密码必须是 8-16 个大写字母、小写字母或数字，并且每种字母必须至少有一个。

在继续之前，该表单将在任何无效条目上方显示一条描述性错误消息。[例 9](http://codepen.io/SitePoint/pen/Nrdqqb/) 把这一切集合在一起。

参见 Pen[APG-exp:A RegExp Alternative:Example 9](http://codepen.io/SitePoint/pen/Nrdqqb/)by site point([@ site point](http://codepen.io/SitePoint))on[CodePen](http://codepen.io)。