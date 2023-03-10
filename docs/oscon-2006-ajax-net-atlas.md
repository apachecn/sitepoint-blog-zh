# OSCON 2006:阿贾克斯+。NET = Atlas

> 原文：<https://www.sitepoint.com/oscon-2006-ajax-net-atlas/>

本周，凯文·杨克从俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 发回报道。

在从零开始介绍 AJAX 之后，Christian Wenz 做了一个演示报告(我最喜欢的那种！)展示了微软用于在 ASP.NET 构建 AJAX 应用程序的 [Atlas toolkit](http://atlas.asp.net/) 的许多特性。

最基本的，Atlas 使得编写向 ASP.NET Web 服务发送请求的客户端 JavaScript 代码变得很简单，但是一旦你开始将 AJAX 和 ASP 结合起来，事情就变得非常有趣了。NET 的其他特性。

Atlas UpdatePanel 是“适应”AJAX 的一个很好的方式，同时减轻了普通 ASP.NET 的一个主要烦恼。通过将 ASP.NET 页面的一部分包装在 Atlas UpdatePanel 中，页面的这一部分可以通过 AJAX 执行回发(往返服务器以更新 UI 来响应用户输入)，而不必重新加载页面的其余部分。Wenz 展示了嵌入 ASP 是多么简单。NET 在 UpdatePanel 中的大量回发日历控件，可以省去那些烦人的页面重载。

然后，Wenz 继续构建了一个简单的 autocomplete 演示程序，与 Google Suggest 类似。这非常容易做到，这要感谢 Atlas 能够用 AJAX 功能扩展现有的 ASP.NET 组件(如 TextBox)。我很高兴地看到，当 JavaScript 在客户端被禁用时，它优雅地降级为普通的文本框。

其他 AJAX 工具包不能很好地处理的一个难题是跨域请求(参见[我之前关于这个主题的报告](https://www.sitepoint.com/oscon-2006-cross-site-ajax/)), Atlas 实际上有一个很好的解决方案:为从客户端调用远程服务构建服务器端代理的框架。这种解决跨域请求问题的方法并不总是可行的，但至少 Atlas 有一个解决方案，而其他框架通常让开发人员从头构建一个。

最后，温兹推出了一款广受欢迎的产品:由虚拟地球提供的 AJAX 地图控件。正如人们所预料的那样，这很容易做到。

Wenz 提供了一些其他的指示:Atlas 控制工具包，这是一个社区驱动的项目，为 Atlas 框架构建新的控制和扩展；T2 正在努力将 Atlas 的服务器端部分移植到 PHP。

## 分享这篇文章