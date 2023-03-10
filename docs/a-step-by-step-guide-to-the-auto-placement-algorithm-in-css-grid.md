# CSS 网格中的自动布局算法指南

> 原文：<https://www.sitepoint.com/a-step-by-step-guide-to-the-auto-placement-algorithm-in-css-grid/>

![Auto-placement algorithm in CSS Grid Layout module](img/84d1bfc0ebc49dbc78ef5565275d3e98.png)

在本教程中，我将回顾 CSS 网格布局模块的**自动放置算法在定位元素时遵循的所有步骤。这些步骤由`grid-auto-flow`属性控制。**

在[介绍 CSS 网格布局](https://www.sitepoint.com/introducing-the-css-grid-layout/)和[使用 CSS 网格布局放置元素的七种方式](https://www.sitepoint.com/seven-ways-you-can-place-elements-using-css-grid-layout/)中，我给出了 CSS 网格规范的概述，并解释了网格让你在 web 上排列元素的所有不同方式。然而，在我以前的文章中，我只明确指定了网格中一个元素的位置。至于其余的物品，它们根据一种算法得到了正确的放置。

在这里，我将向你展示这个算法是如何工作的。这样，下一次一个元素出现在一个意想不到的位置时，您就不会感到困惑了。

## 更好地掌握自动布局算法的基本概念

在深入研究算法的工作原理之前，让我们先回顾一些基本概念。

*   **匿名网格项目**–如果你将一些文本直接放入一个网格容器中，而没有用任何标签包装，它将形成自己的匿名网格项目。您不能对匿名网格项目进行样式化，因为没有可供您进行样式化的元素，但是它仍然从其父容器中继承样式规则。另一方面，请注意，网格容器中的空白不会创建自己的匿名网格项
*   **网格跨度值**–与网格位置不同，该算法没有特殊规则来解析网格跨度值。如果没有明确指定，它们的值将被设置为 1(该项只占用自己的单元格)
*   **隐式网格**——基于`grid-template-rows`、`grid-template-columns`、`grid-template-areas`等属性值构建的网格称为**显式网格**。现在，如果您以位于显式网格边界之外的方式指定网格项的位置，浏览器将生成额外的网格线来容纳该项。这些线条与显式网格一起构成了隐式网格。你可以在 CSS 网格布局工作草案的[中读到更多。自动放置算法还会导致在隐式网格中创建额外的行或列。](https://www.sitepoint.com/where-things-are-at-in-the-css-grid-layout-working-draft/)

最后，我想提出以下初步观点。控制算法的属性`grid-auto-flow`的默认值是`row`。这也是我在下面的自动放置算法的解释中将要假设的值。另一方面，如果您显式地将上述属性设置为`column`，请记住在我对算法的解释中用术语*列*替换术语*行*的实例。例如，步骤“设置了行位置但没有设置列位置的元素的放置”将变成“设置了列位置但没有设置行位置的元素的放置”。

现在，在你最喜欢的现代浏览器中启用[实验特征标志](http://caniuse.com/#search=css%20grid)跟随，让我们来看一下算法构建布局所遵循的所有步骤的细节。

## 步骤 1:匿名网格项目的生成

当算法试图将所有项目放入一个网格中时，首先要做的是创建匿名的网格项目。正如我前面提到的，您不能设置这些元素的样式，因为没有项目可以应用样式。

下面的标记从元素间文本生成一个匿名网格项:

```
<div class="container">
  <span class="nonan">1</span>
    Anonymous Item
  <div class="nonan floating">2</div>
  <div class="nonan">3</div>
  <div class="nonan floating">4</div>
  <div class="nonan">5</div>
</div>
```

除了匿名项的生成，下面的演示中还有一点需要注意，网格放置算法忽略了应用于 *div 2* 和 *div 4* 的 CSS 浮动。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔[匿名网格项](http://codepen.io/SitePoint/pen/ygXeBr/)。