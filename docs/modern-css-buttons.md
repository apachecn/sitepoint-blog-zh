# 现代 CSS 按钮基础介绍

> 原文：<https://www.sitepoint.com/modern-css-buttons/>

***更新(2016 年 7 月 9 日):**本文已更新，包含`<button>`标签而非锚点标签，以满足现代可访问性最佳实践。如果你用按钮工作，总是坚持使用`<button>`标签。*

按钮是任何网页中最重要的组件之一，它们有许多不同的状态和功能，都需要正确匹配以前的设计决策。在这篇文章中，我们将讨论三种按钮设计思想，以及 CSS 代码和工具来帮助新开发者创建他们自己的按钮。

在我们深入各种按钮思维模式之前，我们需要加强 CSS 按钮的一些基础知识。如果你不知道哪些 CSS 组件发生了变化，那么知道平面 UI 和材质设计之间的意识形态差异并不重要。

让我们快速回顾一下 CSS 按钮的基础知识。

## CSS 按钮的基础知识

好按钮的定义对每个网站来说都是主观的，但是有一些非技术标准存在:

1.  **可访问性**-这是最重要的。对于残障人士和老版本的浏览器来说，按钮应该是容易访问的。web 的开放性是美好的，不要用懒惰的 CSS 破坏它。
2.  **简单文本**–保持按钮内的文本简短。用户应该能够立即理解按钮的用途以及它将把他们带到哪里。

几乎所有你在网上看到的按钮都会使用一些颜色变化，翻译时间，边框和阴影变化。可以使用各种 CSS 伪类来利用这些。我们将关注其中的两个— `:hover`和`:active`。`:hover`伪类定义了当鼠标悬停在对象上时 CSS 应该如何变化。`:active`通常在用户按下鼠标按钮和松开鼠标按钮之间执行。

可以用伪类改变按钮的整个显示，但这不是一种用户友好的方法。对于初学者来说，一个好的策略是在熟悉按钮的同时，对按钮的基本原理做一些小的或简单的改变。按钮的三个主要基础是**颜色**、**阴影**和**平移时间**。

### 基础 1 —颜色

这是最常见的变化。我们可以改变各种属性的颜色，其中最简单的是`color`、`background-color`和`border`属性。在我们开始举例之前，让我们先来关注如何选择按钮颜色:

1.  **颜色组合**–使用相互补充的颜色。 [Colorhexa](http://www.colorhexa.com/) 是一个很好的工具，可以找到哪些颜色可以搭配。如果你还在寻找颜色，看看[平板用户界面颜色选择器](http://www.flatuicolorpicker.com/)。
2.  **搭配你的调色板**–搭配你正在使用的调色板通常是个好主意。如果你还在寻找调色板，看看 [lolcolors](http://www.lolcolors.com/) 。

### 基础 2 —阴影

`box-shadow`让您在对象周围添加阴影。可以给每一面添加独特的阴影，这种想法被平面用户界面和材质设计所利用。要了解更多关于`box-shadow`的信息，请查看[MDN box-shadow docs](https://developer.mozilla.org/en-US/docs/Web/CSS/box-shadow)。

### 基础 3 —过渡持续时间

`transition-duration`允许您为 CSS 更改添加时间刻度。一个没有转换时间的按钮会立即变成它的`:hover` CSS，这可能会让用户感到不快。本指南中的许多按钮利用翻译时间来感受自然。

以下示例在`:hover`上逐渐过渡按钮样式(超过 0.5 秒):

```
.color-change {
  border-radius: 5px;
  font-size: 20px;
  padding: 14px 80px;
  cursor: pointer;
  color: #fff;
  background-color: #00A6FF;
  font-size: 1.5rem;
  font-family: 'Roboto';
  font-weight: 100;
  border: 1px solid #fff;
  box-shadow: 2px 2px 5px #AFE9FF;
  transition-duration: 0.5s;
  -webkit-transition-duration: 0.5s;
  -moz-transition-duration: 0.5s;
}

.color-change:hover {
  color: #006398;
  border: 1px solid #006398;
  box-shadow: 2px 2px 20px #AFE9FF;
}
```

看起来是这样的:

在 [CodePen](http://codepen.io) 上看到带有 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )转场的笔[按钮。](http://codepen.io/SitePoint/pen/LNKQNp/)