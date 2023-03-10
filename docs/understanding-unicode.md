# 理解 Unicode

> 原文：<https://www.sitepoint.com/understanding-unicode/>

现代网络上最棘手的问题之一是国际化，通常缩写为 i18n(“I”，再加 18 个字母，然后是“n”)。万维网名副其实，即使你的网站有本地用户，你仍然会发现自己在处理外国信件，不管是带有德语标点的名字还是外语引用。

外来字符问题的最终解决方案是 Unicode，这是一个真正庞大的标准，它试图为人类已知的每种语言中的几乎每个字符提供文档和编码，并为未来的语言发展留出空间。理解 Unicode 的一个很好的起点是蒂姆·布雷的文章，[论 Unicode](http://tbray.org/ongoing/When/200x/2003/04/06/Unicode) 。如果这让你想知道更多，乔尔·斯波尔斯基的[绝对是每个软件开发人员绝对必须知道的关于 Unicode 和字符集的最低要求(没有借口！也有助于阅读。](http://www.joelonsoftware.com/articles/Unicode.html)

现在你已经了解了 i18n 问题的基础，那么在你自己的网络工作中，你如何着手解决它呢？幸运的是，W3C 有两个关于这个主题的有用文档，作为他们 XHTML 和 HTML 国际化系列的*创作技术的一部分:[字符和编码 1.0](https://www.w3.org/TR/i18n-html-tech-char/#IDAPNFO) 和[指定内容的语言](https://www.w3.org/TR/i18n-html-tech-lang/#ri20030510.102829377)。不要被冗长的标题或冗长的序言所影响；这些文件的核心是一套易于遵循的指导原则。Jukka Korpela 的关于字符代码问题的教程是关于这个主题的另一个极好的资源。*

最后一点，如果你想在 PHP 中使用 Unicode，你可能会发现 Keith Devens 关于主题的[笔记非常有用。如果您想测试您的 web 应用程序的字符编码意识，请尝试粘贴 Sam Ruby 的](http://keithdevens.com/weblog/archive/2004/May/15/i18n.xhtml)[生存指南到 i18n](http://intertwingly.net/stories/2004/04/14/i18n.html) 中的测试。

## 分享这篇文章