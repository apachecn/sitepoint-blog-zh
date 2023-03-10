# 从头开始理解 CSS 网格系统

> 原文：<https://www.sitepoint.com/understanding-css-grid-systems/>

在过去的几年里，CSS 网格系统越来越受欢迎，很快成为快速布局搭建的最佳实践。因此，出现了很多提供自己的网格系统的框架，试图获得青睐。

如果你是好奇型的，比如我，那么你可能会问自己网格系统到底带来了什么？它们是如何工作的？你会如何着手创造你自己的呢？这些只是我将尝试回答的一些问题，我将探索各种概念，同时从头开始拼接一个基本的网格系统。

## 什么是网格系统？

如果您不熟悉 CSS 网格系统，我们将从一个快速定义开始。基本上，网格系统是一种允许内容以一致且易于管理的方式垂直和水平堆叠的结构。此外，网格系统代码是与项目无关的，这赋予了它高度的可移植性，因此它可以被新项目采用。

## 好处

*   他们通过为 HTML 设计提供简单且可预测的布局框架来提高生产力。页面的结构可以很快形成，而不需要再次猜测它的精度或跨浏览器兼容性。
*   在如何构建布局方面，它们是多才多艺的，可以适应不同的行和列组合。对于更复杂的用例，它们甚至支持嵌套网格。不管您的布局需求是什么，网格系统几乎肯定非常适合。
*   它们是响应式布局的理想选择。这就是网格系统至高无上的地方。它们使得创建适应不同尺寸视窗的移动友好界面变得非常容易。

## 主要组件

网格系统包括两个关键组件:*行*和*列*。行用于容纳列。列构成了最终的结构，包含了实际的内容。一些网格系统会额外包含*容器*，作为布局的包装器。

## 重置箱式模型

首先，对任何网格系统来说，重置盒子模型都是很重要的。默认情况下，浏览器不在元素声明的宽度和高度内包含填充和边框。这对响应能力来说不是好兆头。幸运的是，这可以通过将行和列的`box-sizing`属性都设置为`border-box`来解决:

```
.row, 
.column {
    box-sizing: border-box;
}
```

现在，我们可以利用列宽的百分比。这允许列在不同的视口中上下缩放，同时保持结构不变。

## 结算浮动

为了水平对齐列，网格系统将使列浮动。这意味着您需要清除行上的浮动元素，以保持布局的结构。这就是[clear fix](https://www.sitepoint.com/clearing-floats-overview-different-clearfix-methods/)的用武之地:

```
.row:before,
.row:after {
    content: " ";
    display: table;
}

.row:after {
    clear: both;
}
```

通过将 clearfix 应用到 CSS 中的行，它将导致行拉伸以容纳它所包含的列，而不会添加到标记中。

## 定义列

对于列，样式需要分为两部分定义:公共样式和宽度。首先是共同点:

```
.column {
    position: relative;
    float: left;
}
```

这里，给列一个相对位置，以允许列中的任何绝对位置内容相对于该列进行定位。然后，该列向左浮动以进行水平对齐，这将导致元素变成`display: block`，即使它不是这样开始的。

## 创建排水沟

檐槽有助于在各列之间形成分隔，以提高可读性和美观性。接近排水沟时有两种想法；在每列中定义填充，或者为每列使用基于百分比的左边距。

我更喜欢后一种方法，因为它有助于在不同的屏幕尺寸下保持相对于列和视口的响应性空白。它还允许您为列定义额外的填充，以获得更大的灵活性。基于填充的装订线的最大优点是它们简化了列宽的计算，这将在下一节中变得明显。

使用基于百分比的边距方法，我们可以将前一列的相邻兄弟列作为目标列。这将为除第一列之外的每一列创建一个左边距，我们将使用`margin-left`属性将其定义为 1.6%:

```
.column + .column {
    margin-left: 1.6%;
}
```

## 计算列宽

在开始计算之前，我们需要确定每行的最大列数。一个流行的选择是 12，因为它可以被 1、2、3、4 和 6 整除，具有很大的灵活性。这允许各种不同的组合，仍然允许相同大小的均匀分布的列。

理解每行最多 12 列是很重要的，无论您想要多少列，您都需要满足每行的数量。例如，如果您只需要一行 3 个相等的列，您将使用 3 个元素，每个元素跨越 4 列(4×3=12)。超过 12 的总和将导致额外的列换行。

现在我们知道了最大列数，接下来我们需要使用下面的公式来确定单个(1/12)列的宽度:

*SCW =(100-(m *(MC–1))/MC*

其中:

*   *scw* =单列宽度
*   *m* =保证金(1.6%)
*   *mc* =最大列数(12)

当我们插入数字时，我们会得到 6.86666666667%的单列宽度。从这里，我们可以使用这个数字来计算其余的列宽。这个公式是:

*CW =(SCW * cs)+(m *(cs–1))*

其中:

*   *cw* =列宽
*   *scw* =单列宽度(6.866666666667%)
*   *cs* =柱距(1-12)
*   *m* =保证金(1.6%)

对 12 列中的每一列都应用这个公式会产生下面的 CSS。

```
.column-1 {
    width: 6.86666666667%;
}

.column-2 {
    width: 15.3333333333%;
}

.column-3 {
    width: 23.8%;
}

.column-4 {
    width: 32.2666666667%;
}

.column-5 {
    width: 40.7333333333%;
}

.column-6 {
    width: 49.2%;
}

.column-7 {
    width: 57.6666666667%;
}

.column-8 {
    width: 66.1333333333%;
}

.column-9 {
    width: 74.6%;
}

.column-10 {
    width: 83.0666666667%;
}

.column-11 {
    width: 91.5333333333%;
}

.column-12 {
    width: 100%;
}
```

## 针对移动设备进行优化

尽管电网系统反应迅速，但也只能到此为止。对于具有小视窗的设备，如智能手机，需要调整列的宽度，以使它们包含的内容仍然清晰易读，具有视觉吸引力。媒体查询对此有所帮助:

```
@media only screen and (max-width: 550px) {
    .column-1, 
    .column-2, 
    .column-3, 
    .column-4, 
    .column-5, 
    .column-6, 
    .column-7, 
    .column-8, 
    .column-9, 
    .column-10, 
    .column-11, 
    .column-12 {
        width: auto;
        float: none;
    }

    .column + .column {
        margin-left: 0;
    }
}
```

这里，我们告诉网格，对于视窗宽度小于 550 像素的设备，允许每一列占据其容器的整个宽度。既然这里不再需要水槽，我们也把它们移走。

或者，你可以选择 [mobile first](https://www.sitepoint.com/making-case-mobile-first-designs/) 策略，采取相反的方法，向上扩展到 12 列布局。在这种情况下，列以全角开始，然后我们建立列宽和浮动，以允许它们在屏幕分辨率达到指定阈值时水平对齐。这是 [Bootstrap 的网格系统](https://www.sitepoint.com/understanding-bootstrap-grid-system/)的首选方法，该系统直到视窗达到 992 像素的最小宽度时才设置列宽。对于您的用例来说，这可能是一种更好的方法，并且在评估网格系统时应该注意这一点。

## 齐心协力

当我们结合所有的概念和 CSS 时，我们可以像这样编写 HTML 布局支架:

```
<div class="row">
    <div class="column column-4"></div>
    <div class="column column-4"></div>
    <div class="column column-4"></div>
</div>

<div class="row">
    <div class="column column-2"></div>
    <div class="column column-4"></div>
    <div class="column column-4"></div>
    <div class="column column-2"></div>
</div>
```

查看下面的 CodePen 演示，了解整个网格系统的运行情况，包括嵌套网格:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[了解 CSS 网格系统](http://codepen.io/SitePoint/pen/dPqqvN/)。