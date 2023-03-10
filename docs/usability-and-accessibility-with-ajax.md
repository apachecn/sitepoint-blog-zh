# Ajax 的可用性和可访问性

> 原文：<https://www.sitepoint.com/usability-and-accessibility-with-ajax/>

阿贾克斯特快列车隆隆前行，威胁着要碾碎它所经过的任何东西。最近的讨论已经转向良好的网站开发的关键要素，可用性和可访问性。可访问性是 Ajax 的一个主要问题，主要是因为任何依赖 JavaScript 运行的东西在默认情况下都是不可访问的。有两种解决方案:要么提供一个后备系统，使网站在没有 Ajax 增强的情况下仍然有用，要么提供一个完全独立的界面，无需脚本即可工作。

谷歌最近采用了 GMail 的第二种选择。来自他们的[最新消息页面](http://gmail.google.com/gmail/help/about_whatsnew.html):

> 基本 HTML 视图允许您从运行几乎任何网络浏览器的几乎任何计算机上访问您的 Gmail 邮件，甚至是旧的浏览器(不仅仅是 IE5.5+，Mozilla 和 Safari)。尤其适合旅行，因为你永远不知道西伯利亚的网吧会有什么样的浏览器。

标准的可访问性良好实践是[像瘟疫一样避免页面的纯文本版本](http://www.webcredible.co.uk/user-friendly-resources/web-accessibility/text-only.shtml)，但是在我看来，web 内容和 web 应用程序之间的区别使得简化版本的 Ajax 风格应用程序成为一种可以接受的折衷。

更多关于 AJAX 和可访问性的信息可以在 Standards-Schmandards.com 上找到。

Ajax 的可用性方面也很有趣，主要是因为 Ajax 技巧会严重改变浏览器的预期行为。几周前，Thomas Baekdal 发布了一套建议的 XMLHttpRequest 可用性指南，最近又发表了一篇文章[将他的想法应用到一个简洁的名片设计示例中。](http://www.baekdal.com/articles/usability/usable-XMLHttpRequest/)

最后，对于那些真的不能得到足够 Ajax 的人，Mike Papageorge 已经收集了一个全面的工作示例列表。

## 分享这篇文章