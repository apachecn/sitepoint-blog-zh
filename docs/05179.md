# 位置(CSS 属性)

> 原文：<https://www.sitepoint.com/position-css-property/>

## 描述

position 属性和 float 属性一起控制元素生成的框的位置的计算方式。关于元件定位的详细信息，请参见[定位](https://reference.sitepoint.com/css/positioning)。

位置值不是静态的盒子被称为已定位。它们在堆叠上下文中的垂直位置由 [z-index](https://reference.sitepoint.com/css/z-index) 属性决定。

## 价值

`absolute`
值`absolute`生成一个绝对定位的盒子，相对于它的包含块定位。可以使用一个或多个属性[顶部](https://reference.sitepoint.com/css/top)、[右侧](https://reference.sitepoint.com/css/right)、[底部](https://reference.sitepoint.com/css/bottom)和[左侧](https://reference.sitepoint.com/css/left)来指定位置。绝对定位的盒子从流程中移除，对后面的兄弟没有影响。绝对定位框上的边距永远不会与其他框上的边距一起折叠。

`fixed`
值`fixed`生成一个相对于初始包含块(通常是视口)定位的绝对定位框。可以使用一个或多个属性[顶部](https://reference.sitepoint.com/css/top)、[右侧](https://reference.sitepoint.com/css/right)、[底部](https://reference.sitepoint.com/css/bottom)和[左侧](https://reference.sitepoint.com/css/left)来指定位置。在印刷媒体类型中，元素呈现在每一页上。

`relative`
值`relative`生成一个定位框，其位置首先按照正常流程计算。然后根据属性[顶部](https://reference.sitepoint.com/css/top)或[底部](https://reference.sitepoint.com/css/bottom)和/或[左侧](https://reference.sitepoint.com/css/left)或[右侧](https://reference.sitepoint.com/css/right)将生成的框从该位置偏移。计算下一个框的位置时，就好像相对定位的框占据了该框偏移之前计算的位置。该值不能用于表格单元格、列、列组、行、行组或标题。

`static`
值`static`生成一个没有定位的盒子，但是发生在正常流程中。对于静态框，属性[顶部](https://reference.sitepoint.com/css/top)、[右侧](https://reference.sitepoint.com/css/right)、[底部](https://reference.sitepoint.com/css/bottom)、[左侧](https://reference.sitepoint.com/css/left)和 [z-index](https://reference.sitepoint.com/css/z-index) 被忽略。

`inherit`
值`inherit`使元素取与它的父元素相同的计算值(更多信息见[CSS 属性值继承](https://reference.sitepoint.com/css/inheritvalue))。

## 分享这篇文章