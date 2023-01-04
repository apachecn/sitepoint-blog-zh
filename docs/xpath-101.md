# XPath 101

> 原文：<https://www.sitepoint.com/xpath-101/>

作为对我最近一篇文章的补充，这篇文章将通过示例向您展示如何利用 XPath 的强大功能。

XPath 是 XML 的一种查询语言，类似于关系数据库的 SQL(好吧，有点类似！)用于从 XML 文件中提取节点。

让我们来看一些例子:

下面是我们要解析的 XML 文件:

 `<catalog><cd><title>Be Here Now</title>
<artist>Oasis</artist> <price>$9.99</price></cd>
 <cd><title>Heathen Chemistry</title>
<artist>Oasis</artist> <price>$13.99</price></cd>
 <cd><title>Let It Be</title>
<artist>The Beatles</artist> <price>$12.99</price></cd></catalog>` 

我们可以通过 XmlDocument 使用 XPath 查询。SelectNodes 方法返回一组与我们的查询匹配的节点。让我们来设置一下:

 `string fileName = Server.MapPath("catalog.xml");
XmlDocument doc = new XmlDocument();
doc.Load(fileName);`

我们的 XmlDocument 现在可以查询了。与其解释查询语言的细节，我认为用例子来展示更好。XPath 的完整细节可以在这里找到。

好，让我们选择目录中的所有 CD:

 `XmlNodeList cdNodes = doc.SelectNodes("catalog/cd");`

简单吧。注意，我们只是写出了我们的节点在 XML 文件中的“路径”,使用/来表示层次结构的级别。

让我们变得复杂一点。以下 XPath 表达式将选择艺术家 Oasis 的所有 CD:

 `XmlNodeList cdNodes = doc.SelectNodes("//cd[artist='Oasis']");`

注意这个表达式开头的双斜线。双斜线告诉 XPath 查看它遇到的任何 CD 元素，不管它在层次结构中的确切位置。实际上，双斜杠让我们不必写出整个层次结构路径，从而节省了我们的时间(如果你写了，它将是“catalog/cd[artist='Oasis']”)

这个表达式的第二个不同之处是，我们要求所有节点的 artist 子元素等于 Oasis。方括号用于表示任何类型的查询。我们可以使用常规的“and”和“or”来组合这些查询。

最后，我将展示如何从元素中获取特定的节点。以下查询将返回我们目录中所有 Beatles CDs 的价格:

 `XmlNodeList cdNodes = doc.SelectNodes("//cd[artist='The Beatles']/price");`

XPath 快速指南到此结束:)

## 分享这篇文章