# 为什么数据:URI 计划可以帮助拯救你的慢网站

> 原文：<https://www.sitepoint.com/why-the-data-uri-scheme-could-help-save-your-slow-site/>

每当你加载一个网页，你的浏览器就会启动一系列的网络连接。如果这些连接中的任何一个超时，你将会在很长一段时间内看到浏览器的加载进度指示器。这就是为什么减少浏览器完成页面加载所需的 HTTP 请求数量是优化网站速度的基本步骤。一种(或许未被充分利用的)方法是通过使用`data:` URI 方案嵌入二进制数据，例如常用图标的图像与其他资产内嵌在一起。

## 直接发送

在普通的网页上，最常见的外部资源是图像文件。通过使用像 [CSS sprites](/blogs/2004/05/04/css-sprites/) 这样的技术，将许多图像合并成一个物理文件，然后使用 CSS 一次只显示图像的一部分，你可以大大减少网页调用外部资源的数量。然而，这些技术仍然需要至少一个额外的 HTTP `GET`请求，以及它的开销和可能的新 TCP 连接。

为了最终实现性能优化，我们可以通过使用`data:` URI 方案将图像(或其他外部资源)直接嵌入到 HTML 或 CSS(或其他类型)文件中，从而消除 HTTP 请求。[自 1998 年以来，`data:` URI 方案已经由 <acronym title="Internet Engineering Task Force">IETF</acronym> 的 <acronym title="Request For Comment">RFC</acronym> 2397](http://www.ietf.org/rfc/rfc2397.txt) 定义，尽管迄今为止只有一些主流浏览器支持它。具体来说，基于 Gecko 和 WebKit 的浏览器，如 Firefox、Safari 和 Chrome，以及 Opera 支持它的使用，但[不支持 Internet Explorer 5–7](http://blogs.msdn.com/ie/archive/2005/08/15/452006.aspx)。随着“ [CSS 表格](/blogs/2008/10/22/everything-you-know-about-css-is-wrong/)”的出现，然而，据说[最终为它增加了一些支持](http://www.websiteoptimization.com/speed/tweak/inline-images/#more)。

当`data:` URI 方案的实现最终无处不在时，web 开发人员——更有可能是框架作者——可以开始使用它来增强他们前端代码的性能。下面是怎么做的。

## `data:`肮脏的细节

`data:`方案的语法如下所示:

`data:[<`MIME-type`>][;`encoding`],<`data`>`

换句话说，内联二进制资源以字符串文字`data:`开始。紧随其后的是可选的 MIME 类型和类似的可选编码。如果指定了编码，它前面会有一个分号(`;`)。省略这些标记会使它们默认为`text/plain;charset=US-ASCII`的值。然后，最后，逗号(`,`)将这些属性与实际编码的二进制数据分开，并放在最后。

因为这只是一个很好的老式 URI，你可以在文档中任何可以放置 URI 引用的地方使用 inline-ed `data:`。这意味着您可以在`<img>`元素的`src`属性中、CSS `url()`值中使用它们，等等。除了传闻中的 IE8，你甚至可以嵌入任何其他类型的二进制数据，而不仅仅是图像。(出于安全考虑，IE 可能会限制它在`data:` URI 中支持的二进制数据的种类。)

下面摘录了在 CSS 样式表中嵌入 PNG 图像的样子。在本例中，我们设置了列表项的`list-style-image`属性，以在列表项的标记框中显示自定义图像。(当然，为了提供完整的图像，下面代码中以`iVBORw…`开始的编码数据会更长。)

```
 ul li { list-style-image: url(data:image/png;base64,iVBORw…); } 
```

## 从您的图像中获取`data:`

有许多免费的工具可供你使用，它们将为你构建部分或全部的`data:` URI。

*   [dataurl maker](http://www.sveinbjorn.org/dataurlmaker)是一个 web 表单，它将一个图像文件作为输入，并生成一个 HTML `<img>`元素，其`src`属性就是您想要的`data:` URI。
*   伊恩·希克森也有一个叫做[的工具:URI 厨房](http://software.hixie.ch/utilities/cgi/data/data)也做同样的事情。

当然，如果您已经掌握了服务器端编程语言，您也可以自己动态生成这些 URIs。[维基百科有一个例子](http://en.wikipedia.org/wiki/Data_URI_scheme#Inclusion_in_HTML_or_CSS_using_PHP)通过 [PHP `base64_encode()`函数](//php.net/base64_encode)运行一个图像文件产生一个`data:` URI。其他语言也有类似的功能。

## 利弊:去`data:`还是不去`data:`

大多数网站可能不需要这种优化。当然，将资源作为二进制数据硬编码到您的 web 页面中也存在可维护性问题，因此需要权衡。然而，我可以预见在许多情况下这仍然可以派上用场。

其中一个例子可能是创建 CSS 框架。这种客户端框架旨在可缓存，并且可以包括标准化的图形词汇表。如今，这样的视觉词汇在网络上已经很常见了，比如文件系统上的文件夹、[知识共享许可](//creativecommons.org/)或 [RSS 订阅符号](http://www.feedicons.com/)等图标。在这些变化不太可能或很慢的情况下，可维护性可能不是一个大问题。因此，从性能角度来看，将像这些图像这样的二进制资产直接编码到某些样式表中可能很有意义。

另一个缓解因素是对源文件进行后处理的能力，比如在缩小 JavaScript 时经常做的。这些额外的构建步骤看起来好像前期的努力不值得，但它们正是有远见的开发人员应该做的事情，以帮助他们未来的自己。

当然，由于 Internet Explorer 仍然不能很好地与`data:` URI 方案兼容，我们不能充分利用今天使用它的潜在好处。然而，即使您现在不能做到这一点，如果您将来需要性能提升，了解这一能力也是很好的。

## 分享这篇文章