# 如何在普通 JavaScript 中实现平滑滚动

> 原文：<https://www.sitepoint.com/smooth-scrolling-vanilla-javascript/>

*这篇文章由[阿德里安·桑杜](https://www.sitepoint.com/author/asandu/)、[克里斯·佩里](https://www.sitepoint.com/author/cperry/)、[杰瑞米·海琳](https://www.sitepoint.com/author/jheleine/)和马洛里·范·阿赫特博格进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

平滑滚动是一种用户界面模式，它逐渐增强了默认的页面内导航体验，使滚动框(视口或可滚动元素)内的位置从激活链接的位置变化到链接 URL 的散列片段中指示的目标元素的位置。

这不是什么新鲜事，这是一个多年前就知道的模式，看看这篇 2003 年的文章！顺便说一下，这篇文章具有历史价值，因为它展示了客户端 JavaScript 编程，尤其是 DOM，这些年来是如何变化和发展的，从而允许开发不那么繁琐的普通 JavaScript 解决方案。

在 jQuery 生态系统中有许多这种模式的实现，或者直接使用 jQuery，或者用插件实现，但是在本文中，我们感兴趣的是一个纯 JavaScript 解决方案。具体来说，我们将探索并利用 [Jump.js](http://callmecavs.com/jump.js/) 库。

在介绍完这个库，概述了它的特性和特征之后，我们将对原始代码进行一些修改，使之适应我们的需要。在这样做的时候，我们将更新一些核心的 JavaScript 语言技能，比如函数和闭包。然后，我们将创建一个 HTML 页面来测试平滑滚动行为，然后它将被实现为一个自定义脚本。当可用时，将增加对 CSS 本地平滑滚动的支持，最后我们将总结一些关于浏览器导航历史的观察。

这是我们将要制作的最后一个演示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[平滑滚动](http://codepen.io/SitePoint/pen/YqewzZ/)。