# Ajax 和 Web 服务数据格式第 1 部分:XML、SOAP 和 HTML

> 原文：<https://www.sitepoint.com/ajax-data-formats-xml-soap-html/>

当 Jesse James Garrett 发明 AJAX 这个缩写词时，它的原意是“异步 JavaScript 和 XML”本质上，您遵循以下流程:

1.  创建一个 web 服务，例如一个 PHP“页面”,它被传递 HTTP GET/POST 参数并以 [XML](https://www.sitepoint.com/really-good-introduction-xml/) 返回一个响应。
2.  编写客户端 JavaScript 代码来使用 web 服务，即传递参数和检索 XML 响应。调用是异步处理的，所以浏览器在等待数据到达时不会被锁定。
3.  解析 XML 并相应地更新 HTML 文档。

AJAX 这个名字被保留了下来，这个术语被开发人员和营销人员使用和滥用。今天，这个大写的首字母缩写词已经演变成了术语“Ajax”，这是一个在浏览器和服务器之间发送数据而不需要重新加载整个页面的任何技术的名称。原因是:

1.  使用异步方法并不是必要的(尽管它通常是可取的)。
2.  你不一定需要 JavaScript。
3.  你当然不需要 XML。

最终，无论您使用何种技术，您仍然必须在两个设备之间传递数据。这是讨论各种格式选项及其优缺点的三篇文章系列的第一部分。

## 可扩展标记语言

一开始，XML 是合乎逻辑的选择。很少有其他数据交换格式被正式化，大多数语言都提供了创建、验证和解析 XML 数据的库。即使您的语言不直接支持 XML，它本质上也是纯文本:

```
 <?xml version="1.0"?>
<products>
	<book>
		<title>The Principles of Beautiful Web Design, 2nd Edition</title>
		<url>https://www.sitepoint.com/books/design2/</url>
		<author>Jason Beaird</author>
		<publisher>SitePoint</publisher>
		<price currency="USD">39.95</price>
	</book>
	<book>
		<title>jQuery: Novice to Ninja</title>
		<url>https://www.sitepoint.com/books/jquery1/</url>
		<author>Earle Castledine &amp; Craig Sharkie</author>
		<publisher>SitePoint</publisher>
		<price currency="USD">29.95</price>
	</book>
	<book>
		<title>Build Your Own Database Driven Website</title>
		<url>https://www.sitepoint.com/books/phpmysql4/</url>
		<author>Kevin Yank</author>
		<publisher>SitePoint</publisher>
		<price currency="USD">39.95</price>
	</book>
</products> 
```

XML 的好处包括:

*   XML 可以被人类阅读，并且比其他一些格式更容易理解(假设你使用可理解的标签)。在我以前的系列文章[如何用 PHP 创建自己的 Twitter 小部件](https://www.sitepoint.com/create-your-own-twitter-widget-1/)中，我使用了 XML 提要作为参考，尽管应用程序并没有使用它。
*   大多数语言都为 XML 提供了出色的支持，其中最重要的是 JavaScript。
*   XML 提供了合理的安全性。数据必须被提取和解析，所以不容易发送恶意的有效载荷。

不幸的是，使用 XML 有几个缺点:

*   您发布的数据并不总是有行业认可的 XML 格式(模式)。您也许可以采用诸如 RSS 之类的格式，但是，即使这样，JavaScript 客户机也必须被编程为能够理解它。
*   XML 可能很冗长，数据与结构的比率很低。理想情况下，Ajax 响应应该很小，以便最小化带宽并减轻浏览器的负担。
*   XML 可能有点含糊不清。一个数据项应该是一个新元素还是一个已有元素的属性？您可以通过选择属性来减小 XML 文档的大小，但这不一定是采用它们的好理由。
*   JavaScript 中的 XML 解析非常繁琐。XPath 支持最多也只是零零碎碎的，所以在使用之前，必须提取数据并将字符串转换成实数值，例如

```
 // grab value in first <data> element
var xml = xhr.responseXML;
var nodes = xml.getElementsByTagName("data");
var data = (nodes.length > 0 ? nodes[0].firstChild.nodeValue : null); 
```

许多开发人员认为 XML 已经奄奄一息。我不同意。它可能不是 Ajax 客户端的最佳选择，但是您并不总是知道 web 服务将如何被使用。XML 的普遍性使它成为一个很好的选择——不要忽视它。

## 肥皂

SOAP 是 web 服务数据交换的标准化格式。完整的技术细节长达数百页，但是最终，SOAP 依赖于定义良好的 XML 模式。

很少有开发人员直接使用 SOAP】(这种气味会泄露他们的身份！SOAP 的美妙之处在于客户端库自动将 XML 响应解析为本机对象。例如，。NET 开发人员可以毫不费力地创建基于 SOAP 的 web 服务和客户端。就开发人员而言，他们只是实例化了一个 C#对象——它是在远程机器上创建的并不重要。

不幸的是，SOAP 的 XML 根显示:

*   SOAP 甚至比典型的 XML 响应更加冗长。
*   用 JavaScript 解析 SOAP 消息仍然很困难。这是可能的，而且 [SOAP 库可以帮助](http://plugins.jquery.com/project/jqSOAPClient)，但是对于编码者和浏览器来说这是很大的努力。

SOAP 仍然是服务器到服务器通信的可行选择——特别是如果它们在同一个网络中。然而，如果大部分调用来自 Ajax 请求，这就太笨拙了。

## 超文本标记语言

如果您想将 Ajax 响应直接插入页面，而不需要进一步分析，HTML 是一种很容易使用的格式。例如，假设您有一个出现在每个页面上的购物车小部件。您已经有了创建 HTML 的服务器端代码——当购买一件商品时，它可以作为 Ajax 响应返回相同的 HTML。

好处包括:

*   重用现有代码和创建 web 服务很容易。
*   客户端不需要复杂的数据解析。
*   可以使用 innerHTML 将 HTML 快速添加到页面中。

但是也有缺点:

*   可能很难提取有用的数据。例如，如果您想在其他地方显示购物车总额，那么在 HTML 中识别该值可能并不容易。
*   该消息过于冗长，可能比等效的 XML 消息还要大。
*   在页面中插入 HTML 有破坏当前布局的风险。
*   安全性可能是一个问题—响应可能包含恶意脚本。

在我的下一篇文章中，我们将讨论几种对 Ajax 使用更有效的数据格式: [JSON 和 JSONP](https://www.sitepoint.com/ajax-data-formats-json-jsonp/) 。

## 分享这篇文章