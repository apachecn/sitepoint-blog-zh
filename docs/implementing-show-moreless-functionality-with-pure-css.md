# 用纯 CSS 实现“显示更多/更少”功能

> 原文：<https://www.sitepoint.com/implementing-show-moreless-functionality-with-pure-css/>

如今，开发人员利用不同的 CSS 技术来创建滑块、模态、工具提示和更多基于 Javascript 的组件。

在本文中，我们将使用其中的一些技术来实现我们称之为“显示更多/更少”的功能，并且不需要编写任何 JavaScript 就可以实现。我们还将创建一个全功能的[手风琴风格的显示/隐藏演示](http://codepen.io/SitePoint/pen/YXRxqE)，您可以将它作为您项目的起点。

我们有很多内容要介绍(技术、陷阱和挑战)，所以让我们开始吧！

注意:本文不会讨论如何使这个组件具有可访问性，但这肯定是一个有效的改进，也许是另一篇文章的主题。

## CSS 要求

为了更好地理解创建此功能的过程，您必须熟悉以下关键 CSS 概念:

*   [通用同胞选择器](http://dev.w3.org/csswg/selectors-3/#general-sibling-combinators) ( `~`)
*   [相邻同胞选择器](http://dev.w3.org/csswg/selectors-3/#adjacent-sibling-combinators) ( `+`)
*   [复选框破解](http://www.thecssninja.com/talks/abuse_checkboxes/#/)

为了帮助解决这个问题，我创建了一个演示，您可以在下面看到，它为您提供了阅读本文所需的知识。

参见 Pen [课，为](http://codepen.io/SitePoint/pen/QbJMGR/) [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 checkbox hack 做准备。