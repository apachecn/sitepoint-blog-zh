# 正则表达式解释

> 原文：<https://www.sitepoint.com/regexper-regular-expressions-explained/>

这段代码是做什么的？…

```
/^[0-9a-zA-Z]+@[0-9a-zA-Z]+[\.]{1}[0-9a-zA-Z]+[\.]?[0-9a-zA-Z]+$/
```

那些有几年开发经验的人会意识到这是一个正则表达式。但是，即使是最精明的专家也会花一些时间来确定它检查电子邮件地址的有效性。只有该群体中的高级子集才会理解这是相当肤浅的，不会检查所有的可能性。

正则表达式是非常强大的搜索模式，可用于字符串匹配和替换。大多数语言都支持它们，包括 JavaScript、PHP、Perl、Java、C#、Python 和 Ruby。

单个规则通常很简单，例如，`[a-z]`匹配单个小写字符，`c.t`匹配以“c”开头、以“t”结尾的三个字母的字符串，比如“cat”。然而，当规则组合在一起时，一串看似随机的难以辨认的代码开始出现。与你在野外发现的许多相比，上面的这个相对简单。

创建自己的正则表达式已经够困难了，我们中的许多人都求助于原力。但是与解析其他人的代码相比，这很容易— *这些代码通常是由不喜欢注释的人编写的！*

幸运的是，Jeff Avallone 为您的正则表达式灾难创建了一个解决方案。 [**RegExper**](http://www.regexper.com/) 将无意义的基于 JavaScript 的表达式转换成图形表示:

[![Regexper](img/19f02cb6758ab13c5ae882f5182a2a61.png)](http://www.regexper.com/#%2F^[0-9a-zA-Z]%2B%40[0-9a-zA-Z]%2B[\.]{1}[0-9a-zA-Z]%2B[\.]%3F[0-9a-zA-Z]%2B%24%2F)

不可否认，你仍然需要对模式匹配有一个合理的理解，但是更明显的是这个表达式是在分析一个电子邮件地址。

在幕后， [**RegExper**](http://www.regexper.com/) 是一个 Ruby 应用程序，它将正则表达式翻译成 SVG 图像。SVG 嵌入在页面中，但是应该可以提取或复制它以用于您自己的文档。

如果你想改进或自动化这个过程，这个项目是开源的，可以从[github.com/javallone/regexper](https://github.com/javallone/regexper)下载或下载。

[**RegExper**](http://www.regexper.com/) 聪明得不可思议。虽然有很多工具可以帮助你[设计和测试正则表达式](http://regex.larsolavtorvik.com/)，但是很少有工具可以让你解析或逆向工程现有的代码。我也没有发现任何一个做得如此漂亮。

将 [**RegExper**](http://www.regexper.com/) 添加到您的工具包中，您将会以新的热情解析正则表达式。大概吧。

## 分享这篇文章