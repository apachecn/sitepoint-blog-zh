# 用 JavaScript 处理 XML

> 原文：<https://www.sitepoint.com/processing-xml-with-javascript/>

我今天在工作中遇到了一个情况，我需要从一个文本区域获取一个 XML 字符串，并使用 JavaScript 对其执行 DOM 操作。经过一番挖掘，我想起了几天前我查看过的一个名为 Sarissa 的开源库。Sarissa 是一段非常有用的代码:它在 Internet Explorer 和 Mozilla 中都提供了一个统一的接口，用于处理 HTTP 请求、XML 文档和片段以及执行 XSLT 转换。IE 函数主要是使用 ActiveX 对象提供的，而 Mozilla 函数利用了 Mozilla 的 [XML Extras](http://www.mozilla.org/xmlextras/) 包。

使用 Sarissa，可以通过以下方式将包含 XML 的字符串转换为常规 DOM 节点:

 `var dom = Sarissa.getDomDocument();
var xml = '<example>This is XML!</example>';
dom.loadXML(xml);`

然后，可以将“dom”视为一个 DOM 节点，并使用常规的 DOM API 函数(appendChild、childNodes 等)进行操作。要将节点转换回 xml，只需访问它的“XML”属性:

 `var xml_again = dom.xml;`

Sarissa 有一个缺点:库本身有 24 KB。由于我正在开发的应用程序只需要与 Mozilla 一起工作，所以我决定深入研究 Sarissa 源代码，看看在没有兼容层的情况下如何实现上述功能。下面是等效的 Mozilla 特定代码，直接调用 Mozilla 的 XML extras 提供的其他类:

 `var xml = '<example>This is XML!</example>';
var dom = (new DOMParser()).parseFromString(xml, "text/xml");`

要转换回字符串:

 `var xml_again = (new XMLSerializer()).serializeToString(dom);`

不幸的是，据我所知，XML extras 包的唯一官方文档采用了测试套件的形式。

## 分享这篇文章