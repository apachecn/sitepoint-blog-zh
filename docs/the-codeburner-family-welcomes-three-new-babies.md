# CodeBurner 一家迎来了三个新宝宝！

> 原文：<https://www.sitepoint.com/the-codeburner-family-welcomes-three-new-babies/>

![codeburner](img/00e12322e159cb770bbf40d614b9746f.png)

我非常高兴地宣布[**code burner 1.5 版本**](https://getfirebug.com/) **的推出。**不止一次更新，是三次(数数，*三次！*)新版本——扩展 CodeBurner 家族，包括 Opera 小部件、Mac OS X 仪表板小部件和基于 Adobe AIR 平台构建的独立应用程序。您可以在此下载:

*   [Mac OS X 仪表盘的 CodeBurner】](https://getfirebug.com/)
*   [歌剧刻字员](https://getfirebug.com/)
*   【Adobe AIR 的 CodeBurner】

也许最重要的新特性是添加了一个新的 CSS 视图。它与 DOM 视图协同工作，提供 DOM 视图页面上使用的所有样式表的完整分类。您可以深入每个样式表，并获得有关其规则、属性和选择器的信息。此外，当您执行“DOM 选择搜索”时，结果现在还包括关于属性和选择器的信息。

将这一功能添加到 Firefox 和 Firebug 版本相对简单，如果说相当复杂的话。DOM utilities 类提供的数据提供了关于 CSS DOM 的信息，这是 DOM Inspector 和 Firebug 中 CSS 检查的动力。

然而，Firefox 之外没有这样的功能——将这种功能扩展到其他版本意味着*编写一个全新的实用程序类*,以便从文档的样式表集合中分析和提取必要的信息！我可以告诉你，这可不是件容易的事。它提供了一些功能，比如返回适用于任意元素的属性列表的能力，新版本使用这些属性进行选择搜索。

CodeBurner 现在在其所有搜索中包括 **CSS 选择器和 at-rules** ，此外还有以前拥有的 CSS 属性、HTML 元素和属性信息。对于所有的代码输出和代码示例，现在已经有了**完整的语法高亮显示**(在 Firefox 版本中有用户可配置的颜色)。正如你所料，还有一系列更小的错误修复和稳定性增强，包括一种新的 Firefox 和 Firebug 版本的按需加载技术；后一个特性有助于减少扩展在不活动时的内存占用。

为了保持版本号的统一，所有的新版本都是 1.5 版本；但是没有必要担心——您仍然可以跟上速度！版本号只是反映了这样一个事实，所有这些新版本都有一个与 Firefox 和 Firebug 主要版本相当的特性集，这两个版本自然都达到了 1.5。

这样你就有了——比以往更多的选择，以最方便的格式为你提供你需要的 HTML 和 CSS 参考数据，无论是基于[浏览器的](https://getfirebug.com/) [开发工具](https://getfirebug.com/)、[浏览器小部件](https://getfirebug.com/)、[仪表板小部件](https://getfirebug.com/)，还是独立的[桌面应用程序](https://getfirebug.com/)。

如果您还没有被打动，那么您应该看看第 2 版中将会出现什么！(抱歉，你只能等了。)

**相关:**

*   CodeBurner:一个免费的 HTML 和 CSS 参考工具
*   构建你自己的 Firefox 扩展 (PDF)
*   在火狐扩展学校他们永远不会告诉你的 10 件事
*   [用一点 HTML 给你的 Firefox 扩展增添趣味](https://www.sitepoint.com/article/firefox-extensions-with-html)

## 分享这篇文章