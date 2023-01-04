# 如何制作 CSS 盒子阴影的动画并优化性能

> 原文：<https://www.sitepoint.com/css-box-shadow-animation-performance/>

在这篇文章中，你将学习如何在不降低浏览器性能的情况下制作 CSS 盒子阴影的动画。

在 CSS 中，`box-shadow`属性用于给 web 元素添加阴影，并且这些阴影可以被动画化。但是，阴影动画会影响浏览器的性能，导致呈现页面时出现延迟。

本指南面向具备 HTML 和 CSS 动画工作知识的前端开发人员。

## 为什么这很重要

一个网页必须有一个非常短的加载时间，理想情况下低于五秒钟。研究表明，快速的页面加载大大提高了转化率。[进一步的研究](https://unbounce.com/page-speed-report/)表明，70%的用户表示网站的速度会影响他们从网上商店购物的意愿。基本上，快速的网站等于快乐的用户。

在我们继续之前，这里有一个演示，演示了动画如何在网页上运行。您可以滚动浏览 web 元素并与之交互。

在 [CodePen](https://codepen.io) 上通过 site point([@ site point](https://codepen.io/SitePoint))
看到带阴影动画的钢笔 [Web 元素。](https://codepen.io/SitePoint/pen/GRdwqWe)