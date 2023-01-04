# 文档，文档，文档

> 原文：<https://www.sitepoint.com/documentation-documentation-documentation/>

这是真的说，如果你不写下来，它没有发生。考虑到这一点，我将讨论一个基于 JavaScript 的地图服务，它不是 Google Maps。 [Community Mapbuilder](http://mapbuilder.sourceforge.net/index.php) 是“帮助组织开始基于标准的在线制图的一系列资源”。他们提供的主要部分是 *mapbuilder-lib* ，一组客户端(和少量服务器端)脚本，可以使用 OpenGIS 标准与地图服务器对话，然后使用 JavaScript 和 XSL 显示结果地图。

[社区地图构建器演示](http://mapbuilder.sourceforge.net/mapbuilder/demo/complete.html)可能会使这一点更加清晰；它提供了许多可缩放的地图，包括一个简单的世界国家地图和另一个火星地图。).包含的 JavaScript 将可缩放地图本身不引人注目地插入到页面中。

这都是标准的东西。这是一个很好的技术应用，但技术本身是相当众所周知的。关于 mapbuilder-lib 有趣的一点是，它有大量的文档。除了一些[概述技术文档](http://mapbuilder.sourceforge.net/docs/design/index.html)之外，MapBuilder 团队还为整个库提供了[完整的 API 文档](http://mapbuilder.sourceforge.net/docs/jsdoc/index.html)，这对于希望使用 MapBuilder 实现地图应用的人来说非常有帮助。API 文档是使用 [JSDoc](http://jsdoc.sourceforge.net/) 创建的，它解析 JavaScript 文件并从注释中提取文档，就像 JavaDoc 处理 Java 文件一样。本质上，在编写代码时，作者添加了特殊格式的注释，如下所示:

`/**
* Get a random colour, really inefficiently
* @returns a random colour name as a string
*/
function randomColour() {
var colours = ['red','blue','green','yellow','heliotrope'];
return colours[parseInt(Math.random()*colours.length)];
}`

然后 JSDoc 提取函数描述、每个函数返回的细节(用@returns 表示)等，并构建一组可浏览的 HTML 文档。JSDoc 和类似的工具大大减轻了为 API 或库编写文档的负担，随着更多的 web 应用程序和类似的程序使用 DOM 脚本构建，更多的共享库将会涌现。跨浏览器 XML 处理 JavaScript 库 Sarissa 也使用 JSDoc 进行文档处理，当然还有其他的。让我们希望更多为重用而设计的库继续为黑客提供好的文档。

## 分享这篇文章