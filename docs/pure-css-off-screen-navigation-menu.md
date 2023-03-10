# 纯 CSS 离屏导航菜单

> 原文：<https://www.sitepoint.com/pure-css-off-screen-navigation-menu/>

汉堡菜单、抽屉菜单、画布外菜单:不管你怎么称呼它，将网站的主要导航隐藏在屏幕之外正在成为响应式网站设计中无处不在的模式。越来越多的网站都有一个固定位置的图标，当点击它时，会将整个网站推到一边，显示一个隐藏的导航菜单。

虽然有很多 jQuery 插件可以为您创建这种效果，但实际上不需要使用任何 JavaScript 就可以轻松实现。

这篇文章将向你展示如何只用 CSS 制作一个简单的画布外菜单和滑动效果。

在我们开始之前，我想说明的是，使用这种方法创建抽屉菜单意味着你的站点的导航将总是在一个抽屉中，不管视窗大小如何。如果你只想在较小的屏幕上显示抽屉菜单，你必须使用一些 JavaScript 来操作 DOM 顺序，或者使用一些时髦的 CSS 来使菜单在较大的屏幕上看起来合适。换句话说:它很漂亮也很简单，但它并不总是适合所有情况的正确解决方案。

这是一个展示最终结果的 CodePen 演示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[纯 CSS 离屏导航菜单](http://codepen.io/SitePoint/pen/uIemr/)。