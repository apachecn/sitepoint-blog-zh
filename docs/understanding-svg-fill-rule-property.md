# 了解 SVG 填充规则属性

> 原文：<https://www.sitepoint.com/understanding-svg-fill-rule-property/>

SVG `fill`属性用纯色、渐变或图案绘制图形的内部。[使用 SVG inline](http://css-tricks.com/using-svg/) 可以完全控制 HTML 内整个 SVG 文档片段中元素的属性。

在大多数情况下，图形的“内部”是很简单的。但是，当图形涉及更复杂的复合路径时，例如那些相交或封闭的形状，定义形状内部的内容就变得不太清楚了。

然后可以包含[填充规则](https://www.w3.org/TR/SVG/painting.html#FillRuleProperty)属性，以进一步定义我们的意图，即我们的完整形状的内的*。*

本文将回顾一下`fill`属性，然后描述与`fill-rule`相关的算法规则。理解这些规则背后的原因有助于消除在浏览器中呈现图形时的任何意外。

## `fill`属性

在进入`fill-rule`之前，理解`fill`属性的功能很重要。

`fill`绘制特定图形元素的内部，并包含在要填充的元素的代码中。一个形状的内部是通过检查所有子路径和在`fill-rule`中列出的规格来确定的，然后这个空间被涂上颜色。[绘制](https://www.w3.org/TR/SVG/painting.html#Introduction)是指通过`fill`和/或`stroke`将颜色、渐变或图案应用于图形的动作。

填充形状或路径时，`fill`将绘制开放路径，就像路径的最后一个点与第一个点相连一样，即使`stroke`颜色不会在路径的这一部分上呈现。

为了演示`fill`在开放路径上的效果，让我们来看看两条基本的 SVG 折线:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的钢笔 [SVG 折线填充示例](http://codepen.io/SitePoint/pen/BagGE/)。