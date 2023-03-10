# Flex 2 不是“全有或全无”

> 原文：<https://www.sitepoint.com/flex-2-is-not-all-or-nothing/>

自从 Macromedia [宣布 Flex 2](https://www.sitepoint.com/flex-20-announced-with-more-affordable-pricing/) 和[发布预发布版本](https://www.sitepoint.com/flex-2-and-flash-player-85-alphas-available/)以来，我一直在咬牙切齿，因为越来越多关于 Flex 2 将能够做什么的酷消息浮出水面。

因为 ~~Macromedia~~ Adobe 不会像对 Flex 1 那样为 Flex 2 [的非商业用户提供免费许可证，所以我一直避免向人们推荐这项技术，尽管连续的预发布版本(](https://www.sitepoint.com/free-flex-for-all-well-almost/)[现在是 Beta 2](http://labs.macromedia.com/flexproductline/) )带来了源源不断的令人兴奋的消息。

最新的令人兴奋的消息——这迫使我不情愿地重新审视这个平台——是 Adobe 已经开发了 Flex-AJAX bridge。这个名字有点误导，因为它实际上更像是 Flex-JavaScript 的桥梁，但它确实帮助 Adobe 避开了 AJAX 是 Flex 杀手的看法。

此桥是 Flex 的扩展库，当加载时，允许页面中的 JavaScript 代码与 Flex 应用程序通信，获取/设置属性值，操纵用户界面元素，并为 Flex 应用程序内的元素注册 JavaScript 事件侦听器，否则这些元素只能由应用程序内的 ActionScript 代码访问。同样，桥允许 Flex 应用程序进入包含它的 HTML 文档，获取/设置表单字段值，操作 CSS 属性，并注册事件侦听器，这将允许 Flex 应用程序响应 JavaScript 事件。

以前，Flex 被视为 web 应用程序的全有或全无平台，您的 Flex 应用程序只是用包含用户界面的 Flash 电影来填充浏览器窗口，而 Flex-AJAX bridge 允许您构建混合应用程序，使用更小的组件可以受益于 Flex 的灵活性，而用户体验的其余部分可以继续使用 HTML 和 CSS 来呈现，并带来所有附加的兼容性和可访问性。

要试用 Flex-AJAX bridge，您需要最新的 Flash Player 8.5 测试版。然后你可以看看 Flex 开发者 [Christophe Coenraets](http://coenraets.com/) 在最近的[博客文章](http://coenraets.com/viewarticle.jsp?articleId=99)中描述的简单的[混合商店例子](http://coenraets.com/apps/hybridstore/hybridstore.html)。该应用程序利用了来自 Yahoo！的滑块组件。用户界面库提供一些 JavaScript 驱动的控件，允许您过滤显示在相邻 Flex 应用程序中的一组结果。

[在 Adobe 实验室网站上可以找到一个更详细的例子](http://flexapps.macromedia.com/flex2beta2/fabridge/samples/FABridgeSample.html)，它展示了使用桥与 Flex 通信所需的非常简单的 JavaScript 代码。

我还没有跳上灵活的潮流，但是如果合适的项目出现了，投资是合理的…嗯，你永远不知道。

## 分享这篇文章