# 使用纯 CSS 构建样式切换器:选中

> 原文：<https://www.sitepoint.com/building-style-switcher-with-pure-css-using-checked/>

几年前，web 开发人员无法仅使用 CSS 而不依赖 JavaScript 来实现和构建如此多的东西。但是今天，CSS 已经足够成熟，能够在不编写任何 JavaScript 代码的情况下完成强大的功能。你可能也读过一些关于“只有 CSS 的方法”的文章，这些文章展示了 CSS 的力量。

当谈到仅使用 CSS 的方法时，我们不能忽略`:checked`伪类选择器，我将在本文中使用它。当然，我不是第一个写这种技术的人，也有其他更广泛的关于使用表单元素替代 JavaScript 的讨论。例如[路易斯·拉扎里斯](http://www.adobe.com/devnet/archive/html5/articles/using-form-elements-and-css3-to-replace-javascript.html)的这篇 Adobe 文章和[瑞安·塞登](http://www.thecssninja.com/talks/abuse_checkboxes/#/)的这个很棒的幻灯片。

## 使用`:checked`

简而言之，`:checked`伪类选择器表示(选择)任何被选中或选中的单选或复选框元素。用户可以通过选中复选框或在一组单选按钮中选择不同的值来更改这些元素的状态。

在我们深入学习之前，先看一下最终的演示，了解一下我们将在本教程中构建什么:

看笔[一个纯 CSS 的风格切换器使用:在](http://codepen.io/SitePoint/pen/oXRReL/) [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )检查。