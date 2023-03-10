# 引导网格:掌握最有用的 Flexbox 属性

> 原文：<https://www.sitepoint.com/bootstrap-grid-mastering-flexbox/>

在本文中，我将向您介绍使用引导网格系统构建布局的关键引导 CSS 类。

Bootstrap 4 使用 Flexbox 作为其网格系统的基础。我将解释作为新网格功能基础的 Flexbox CSS 属性，并定义 Bootstrap flex 实用程序类如何帮助您快速、轻松地构建出色的布局。

## 什么是 Flexbox？

先介绍一下 [Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox) 。它代表 *flex* ible *box* ，它是一个尖端的 CSS 布局系统，可以轻松地为动态或未知的屏幕尺寸创建布局。(flex 容器能够调整和控制其子元素的大小，以适应不同的视口。)

您可以使用为此任务设计的一组 CSS 属性轻松创建 Flexbox 布局。

Bootstrap 通过在 Flexbox 属性的基础上提供一组包装类，使创建基于 Flexbox 的布局变得更加容易，您可以简单地将这些包装类应用到您的标记中，以获得您想要的结果。

## 自举网格系统简介

网格系统是 CSS 框架的一个重要元素，因为如果没有强大灵活的网格系统，构建复杂的布局将是一项艰巨的任务。

在最新的[引导网格系统](http://getbootstrap.com/docs/4.0/layout/grid/)的新功能中，你会发现`xl`(超大)网格断点，对应于超大屏幕尺寸，以及使用 Flexbox 而不是 floats 作为底层布局机制。

### 引导网格系统的关键类

通过应用一堆 Bootstrap 类:`.container`、`.row`和`.col-*-*`，你可以使用 Bootstrap 的网格系统构建一个布局。(`.col-*-*`中的第一个*需要替换为断点说明符——比如`xs, sm, md, lg, xl`——第二个*需要填充列跨度大小。所有列的总和必须等于 12。)

现在让我们看看引导网格的核心组件。

#### 容器

容器是网格布局的外部包装。它是一个`div`，要么具有固定宽度的类别`.container`，要么具有 100% 全宽度的类别`.container-fluid`。

#### 排

行充当列的逻辑容器。

#### 圆柱

列是网格中的一个块。它应该包含在一行中。

引导网格系统提供了这些附加的列类:

*   `.col-xs-*`:为小于 *576px* 宽度的超小屏幕设计
*   `.col-sm-*`:为宽度等于或大于 *576px* 的小屏幕设计
*   `.col-md-*`:为宽度> = *768px* 的中型屏幕设计
*   `.col-lg-*`:为宽度> = *992px* 的大屏幕设计
*   `.col-xl-*`:专为宽度等于或大于 *1200px* 的超大屏幕设计。

如果要为不同的屏幕尺寸指定相同的宽度，不需要添加多个类；只需添加断点最小的类。所以，比如不用`.col-sm-6`和`.col-md-6`，只需要应用`.col-sm-6`。

## 具有 Flexbox 和 Floats 的引导网格布局

多亏了 Flexbox，你可以很容易地实现相同高度的列或相同宽度的列，这在以前只能用 CSS 实现。

构建布局的 CSS *float* 和 *clearfix* 技术就是其中之一，这使得构建和调试复杂布局变得困难。

例如，考虑两列布局。如果您使用 Bootstrap 3 构建此布局，它将如下所示:

参见 [CodePen](https://codepen.io) 上 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )的 Pen [Bootstrap 3 2 列布局](https://codepen.io/SitePoint/pen/paQwyy/)。