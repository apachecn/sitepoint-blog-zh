# 渐进增强与优雅退化:做出选择

> 原文：<https://www.sitepoint.com/progressive-enhancement-graceful-degradation-choice/>

在我的[上一篇文章](https://www.sitepoint.com/progressive-enhancement-graceful-degradation-basics/)中，我们讨论了优雅的退化和渐进的增强——这些概念可以帮助富 web 应用程序支持更多的浏览器并具有更广的覆盖范围。概括一下:

*   **适度降级**是指为现代浏览器构建一个应用程序，同时确保它在旧浏览器中保持功能。
*   **渐进式增强**是指为基本用户体验构建应用程序，但在浏览器支持时增加功能增强。

好的可访问的 web 应用程序可能会结合使用这两种方法。无论您采用哪种实践，一个特性都可能在同一点结束。

## 评估应用程序的可访问性要求

为所有浏览器创建一个网站或应用程序显然是一种理想的情况。但是，您的时间表、预算和功能集会带来一些限制。您还应该了解无障碍立法带来的法律问题，例如美国[第 508 条](http://en.wikipedia.org/wiki/Section_508)和英国[残疾歧视法案](http://en.wikipedia.org/wiki/Disability_Discrimination_Act_1995)。建议从一开始就计划您的可访问性需求。

有几个例外；您的应用程序可能如此依赖脚本或插件，以至于迎合旧的或不兼容的系统没有什么意义。例如，在线视频编辑器几乎肯定需要 Flash、Silverlight 或类似的技术；纯 HTML 版本是不可行的。

许多开发人员认为他们的网站或应用程序是特定于浏览器的，只需要支持该应用程序。例如， [Mozilla 插件](http://addons.mozilla.org/)可能无法被非 Firefox 用户访问。然而，托管 Mozilla 附加目录的网站在大多数浏览器中运行良好:

1.  Mozilla 支持网络标准；他们没有理由创建一个专门针对 Firefox 的网站
2.  使用其他浏览器的用户可能会访问附加组件页面，了解更多关于 Firefox 的信息。阻止用户或提供糟糕的体验不会让 Mozilla 喜欢他们。

另一个极端是 Amazon.com 等电子商务网站。如果他们限制任何用户的访问，公司可能会损失数千美元。

## 选择您的发展方法

在我看来，优雅的退化从错误的方向攻击了问题。渐进式增强通常提供了一种更符合逻辑的方法:

*   它使您能够构建一个具有坚实基础的稳定的应用程序，可以在任何设备上工作。
*   随着新技术和浏览器的引入，您可以在不改变核心功能的情况下添加进一步的增强。相反，优雅降级需要一个[浏览器支持列表](https://www.sitepoint.com/5-reasons-to-avoid-supported-browser-lists/)；随着新浏览器的引入，您可能需要更新应用程序。
*   增强功能允许用户更快或更容易地实现目标，但它们不是先决条件。例如，一个 JavaScript 滑块可能允许用户快速选择一个数字，但是这个值会被输入到一个标准的、可访问的 HTML 输入框中。

有几个例外情况适合适度降级:

*   如果系统仍然可用，表面问题通常是可以接受的。老版本的浏览器对 CSS 的支持更加有限——如果核心功能仍然可用，布局可能会不同或者难看。
*   像谷歌地图这样复杂的可视化应用程序如此依赖 JavaScript，以至于为没有 JavaScript 的用户开发了一个完全不同的系统。这并不是说谷歌地图不能通过渐进式改进来实现，但我怀疑创建两个独立的应用程序会更简单、更划算。

一般来说，渐进式改进应该提供更好的稳定性和浏览器支持。初始开发成本较高，但应用程序需要的长期维护较少。

**即将推出:**展示如何使用渐进式增强技术构建简单小部件的系列文章。

视图部分 1: [**渐进增强和优雅退化:概述**](https://www.sitepoint.com/progressive-enhancement-graceful-degradation-basics/)

**相关阅读:** 

*   [不应发布受支持浏览器列表的 5 个理由](https://www.sitepoint.com/5-reasons-to-avoid-supported-browser-lists/)
*   [微软 Office Online:针对受支持浏览器列表的案例](https://www.sitepoint.com/case-against-official-browser-support/)
*   谁在使用 ARIA？

## 分享这篇文章