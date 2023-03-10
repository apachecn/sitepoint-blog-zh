# 最新引导实用程序类的初学者指南

> 原文：<https://www.sitepoint.com/bootstrap-utility-classes/>

**[Bootstrap 4](https://getbootstrap.com/) 发布稳定版(终于！)并为我们带来了很多很酷的新功能。其中之一是一组升级的 Bootstrap [实用程序类](https://getbootstrap.com/docs/4.0/utilities)，我们将在本文中重点讨论。**

## 引导实用程序类简介

Bootstrap 实用程序类旨在应用于页面上的各种元素，以某种方式快速设置它们的样式，而无需编写定制的 CSS 规则。在许多情况下，您只需为您的元素提供一个类，并立即观察结果。如果您以前从未使用过实用程序类，不要担心:在大多数情况下，它们非常简单，但是功能强大。它们非常方便，因为在创建 web 应用程序时，您不需要重新发明轮子，可以专注于更复杂的任务。

有很多 Bootstrap 实用程序类可以设置元素的位置，调整其显示和浮动属性，调整边距和填充，给文本和背景着色，等等。这些类的完整列表可以在[getbootstrap.com](https://getbootstrap.com/docs/4.0)官方网站上找到，在本文中，我将介绍一些有趣的引导实用程序类，它们可能会极大地简化您作为开发人员的生活。我们可以开始了吗？

## 弯曲

[Flex](https://getbootstrap.com/docs/4.0/utilities/flex/) 是 Bootstrap 4 备受期待的新特性。它允许我们轻松地管理一个元素的布局和与一堆类的对齐。然而，请注意，[这个特性依赖于`display: flex`属性上的](https://www.sitepoint.com/flexbox-css-flexible-box-layout/)，这可能不被老版本的浏览器所支持。有趣的是， [Bootstrap 的网格系统](https://getbootstrap.com/docs/4.0/layout/grid/)现在也依赖于 flexbox。

Flex 本身是一个相当大的主题，我们不会在本文中彻底讨论它。让我快速展示一下使用这个特性可以获得的一些结果。

首先，可以很容易地控制元素的方向(即，它们是从左到右显示还是从右到左显示)。例如:

```
<div class="d-flex flex-row-reverse">
  <div class="p-2">Flex item 1</div>
  <div class="p-2">Flex item 2</div>
  <div class="p-2">Flex item 3</div>
</div> 
```

这将使三个元素向右对齐，第*个*元素位于*最右边的*位置:

![Bootstrap utility classes: flex-row-reverse](img/45a0e9d4c036c8ee41f737a4193ed1d3.png)

元素的[顺序](https://getbootstrap.com/docs/4.0/utilities/flex/#order)可以用类似`order-N`的类进一步控制，其中`N`是位置的编号:

```
<div class="d-flex flex-nowrap">
  <div class="order-3 p-2">First flex item</div>
  <div class="order-2 p-2">Second flex item</div>
  <div class="order-1 p-2">Third flex item</div>
</div> 
```

注意，这个特性支持像`order-sm-1`这样的类的响应。

元件也可以在 X 轴或 Y 轴上以特定方式[对齐](https://getbootstrap.com/docs/4.0/utilities/flex/#align-items)。例如:

```
<div class="d-flex align-items-center bd-highlight mb-3">
  <div>Flex item</div>
</div> 
```

这将使项目在 Y 轴上居中:

![Bootstrap utility classes: flex-align-items](img/7836c9366474d01957554b587be8e354.png)

## 浮动

[Float](https://getbootstrap.com/docs/4.0/utilities/float/) 是一个新的实用程序，它取代了[好的老“拉”](https://getbootstrap.com/docs/3.3/css/#helper-classes-floats)。在 Bootstrap 3 中，您应该写:

```
<div class="pull-left">Pull to the left!</div> 
```

在引导程序 4 中:

```
<div class="float-left">Float to the left!</div> 
```

一个很酷的增强是，浮动也变得有响应性了，这意味着你不需要精心设计自己的风格来处理各种断点。例如，以下元素将仅在中等(或更大)屏幕上向右浮动:

```
<div class="float-md-right"></div> 
```

## 显示

在 Bootstrap 4 中,[显示属性](https://getbootstrap.com/docs/4.0/utilities/display/)也被完全修改了。它现在支持所有显示值(从`none`到`flex`)并完全响应。对应的类看起来像`.d-{breakpoint}-{value}`:

*   `breakpoint`可以具有值`sm`、`md`、`lg`或`xl`。如果跳过该值，则默认使用`xs`，这实际上意味着在所有视窗上显示将具有相同的值。

*   `{value}`是由 [`display`属性](https://developer.mozilla.org/en-US/docs/Web/CSS/display)支持的任意一个。

这意味着您可以隐藏所选视口的元素，如下所示:

```
<div class="d-lg-none">Hide on screens wider than lg</div> 
```

此外，该实用程序还可以与[打印布局](https://getbootstrap.com/docs/4.0/utilities/display/#display-in-print)配合使用。例如，可以隐藏某个元素进行打印:

```
<div class="d-print-none">Screen Only (Hide on print only)</div> 
```

## 胶料

使用 Bootstrap 4，你还可以轻松控制元素的[大小](https://getbootstrap.com/docs/4.0/utilities/sizing/)。假设我们想要显示一个占据父元素宽度 25%的`div`:

```
<div class="w-25">Width 25%</div> 
```

身高呢？这也是可能的:

```
<div class="h-25">Height 25%</div> 
```

想提供`max-height`还是`max-width`？利用`mh-100`或`mw-100`类。如你所见，没什么复杂的！

## 间隔

[Spacing](https://getbootstrap.com/docs/4.0/utilities/spacing/) 特性提供了一系列小而方便的工具，可以用来快速设置任何元素的边距和填充。像我这样懒惰的开发人员肯定会喜欢这个附加功能！

对应的类看起来像`{property}{sides}-{size}`:

*   `{property}`要么是`m`(边距)，要么是`p`(填充)。
*   `{sides}`可以有像`t`(上)或`l`(左)这样的值。如果它有一个空值，大小将应用于元素的所有四个边。
*   `{size}`是从`0`到`5`的整数，或`auto`。这个整数指定了一个乘数，该乘数被传递给计算结果大小的公式`($spacer * MULTIPLIER)`。`$spacer`的默认值为`1 rem`。例如`1`对应`0.25`乘数，`2`对应`0.5`乘数，`3`单独对应`$spacer`的值，`4`对应`1.5`乘数，最后`5`对应`3`乘数。

下面的代码意味着该元素将具有值为`1rem` ( `x`表示“X 轴”)的左右边距:

```
<div class="mx-3"></div> 
```

![Bootstrap utility classes: spacing](img/d2f93877dbb3e4b3d508dc6db804d01e.png)

## 文本

文本工具在 Bootstrap 3 中可用，但在新版本中它们变得更加强大和方便。

### 对齐

如前所述，您可以指定文本的[对齐方式](https://getbootstrap.com/docs/4.0/utilities/text/#text-alignment)，例如，使其居中:

```
<p class="text-center">Text is centered!</p> 
```

但是，更重要的是，这些实用程序类现在也可以响应，这意味着我们可以使文本仅在超大视窗中居中:

```
<p class="text-xl-center">Text is centered only on very large screens!</p> 
```

### 改变

[Transform](https://getbootstrap.com/docs/4.0/utilities/text/#text-transform) 是一个新的小功能，可以将文本转换成小写、大写或大写。例如:

```
<p class="text-capitalize">capiTaliZed</p> 
```

这会将我们的文本显示为“大写”(只有第一个“c”字母是大写的):

![Bootstrap utility classes: text-transform](img/6ada7642d93334025985ac04fba557af.png)

### 字体粗细

改变[字体的粗细](https://getbootstrap.com/docs/4.0/utilities/text/#font-weight-and-italics)现在非常简单:

```
<p class="font-weight-bold">Bold text.</p>
<p class="font-weight-light">Light weight text.</p> 
```

![Bootstrap utility classes: font-weight](img/8fd69dc5218f263b3ba8e45d10724fd2.png)

## 颜色；色彩；色调

现在，您可以使用一组用于 [color](https://getbootstrap.com/docs/4.0/utilities/colors/) 的引导实用程序类来使您的网站更加明亮！这个特性[在 Bootstrap 的前一版本中已经可用](https://getbootstrap.com/docs/3.3/css/#helper-classes-colors)，但是现在它提供了一些新的类和选项。例如，我们现在可以用以下方式组合颜色和背景:

```
<p class="text-danger bg-dark">Text</p> 
```

这将在黑色背景上显示红色文本:

![Bootstrap utility classes for text and background colors](img/ab35822af5caa91f17c5946977222c85.png)

总之，改变文本颜色的类看起来像`text-{color}`。背景可以用`bg-{color}`这样的类来改变。

也支持[背景渐变](https://getbootstrap.com/docs/4.0/utilities/colors/#background-gradient)，但是必须通过将`$enable-gradients` Sass 变量显式设置为`true`来启用。

## 边界

这是 Bootstrap 4 的一个非常酷的新特性，它允许你快速的设计任何元素的边框(甚至是单独的边框！).

例如，以下代码将为元素添加灰色边框:

```
<div class="border"></div> 
```

![Bootstrap utility classes: border](img/1456540a32696770b4d50b1572aa8621.png)

如果出于某种原因，你只对顶部边框感兴趣，那么就这样写:

```
<div class="border-top"></div> 
```

![Bootstrap utility classes: border-top](img/575a64dcf9b783f545b902e4f4f1f494.png)

这些类类似于`border-{side}`，其中`side`可以有以下值:

*   `left`
*   `right`
*   `bottom`
*   `top`
*   无值(在这种情况下，将为所有边设置边框)

通过利用所谓的[减法边界类](https://getbootstrap.com/docs/4.0/utilities/borders/#subtractive)，我们可以更加具体。这些类看起来与上面列出的非常相似，但是必须以`-0`后缀:`border-{side}-0`结尾。所以，如果我们说:

```
<div class="border-top-0"></div> 
```

除了顶部的边框，我们的元素将拥有所有的边框。您可以进一步组合这些类，例如，仅显示左边和底部边框:

```
<div class="border-top-0 border-right-0"></div> 
```

![Bootstrap utility classes to hide specific borders](img/29ba163c8c8215aa24eb99dc7dceed47.png)

很整洁，是吧？

### 把它们都涂上颜色！

此外，[边框颜色](https://getbootstrap.com/docs/4.0/utilities/borders/#border-color)也可以在 Bootstrap 实用程序类的帮助下更改。可用的颜色与[颜色页面](https://getbootstrap.com/docs/4.0/utilities/colors/)上列出的颜色相对应。

例如，我们可以说下面的话来使元素的文本显示为红色:

```
<div class="border border-danger"></div> 
```

![Bootstrap utility classes to color borders](img/a8dbfe0cebf5f37d1bce78a729d36d83.png)

### 调整半径

最后，任何元素的[半径](https://getbootstrap.com/docs/4.0/utilities/borders/#border-radius)也可以在以下类的帮助下修改:

*   `rounded`
*   `rounded-top`
*   `rounded-left`
*   `rounded-right`
*   `rounded-bottom`
*   `rounded-circle`
*   `rounded-0`

`rounded-circle`将一个元素的`border-radius`设置为`50%`，而`rounded-0`取消所有舍入。其他类通过将值设置为`0.25rem`使相应的边框稍微变圆:

![Bootstrap utility classes for the border-radius CSS property](img/1d8db59dc8abdd38678a607ce0dc4d54.png)

## 使...嵌入

[Embeds](https://getbootstrap.com/docs/4.0/utilities/embed/) 是另一个新的 Bootstrap 实用程序类特性，可能会大大简化您的工作。顾名思义，它允许您创建响应性嵌入，在各种设备上具有特定的*比率*缩放。

那是什么意思？假设你想在你的网站上显示一个 YouTube 视频。你可以这样做:

```
<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="SOME_URL_HERE"></iframe>
</div> 
```

这将确保视频的比例始终是 16:9，它将适当缩放！还有其他可用的比率:

*   `embed-responsive-21by9`
*   `embed-responsive-16by9`
*   `embed-responsive-4by3`
*   `embed-responsive-1by1`

## 关闭图标

最后但并非最不重要的是这个小小的实用程序做到了它所说的:显示一个[关闭图标](https://getbootstrap.com/docs/4.0/utilities/close-icon/)。要记住的一件事是用下面的方式为屏幕阅读器指定一些文本:

```
<button type="button" class="close" aria-label="Close">
  <span aria-hidden="true">&times;</span>
</button> 
```

看起来是这样的:

![Bootstrap utility classes for the close icon](img/ee08af97671a80a58c7eb559156790d8.png)

## 结论

在本文中，我们讨论了简化网页上各种元素样式的引导实用程序类。我们已经介绍了 flex、display、border 等实用工具，并看到了它们的一些用法示例。当然，这些类还有更多，所以一定要浏览官方文档，在那里你会找到更多有用的例子。

你喜欢 Bootstrap 4 的新功能吗？有没有你觉得特别有用或有趣的特定引导实用程序类？请在评论中分享你的观点，也不要犹豫发表你的问题！

**如果你听说过 Bootstrap，但因为它看起来太复杂而推迟了学习，那么请浏览我们的[Bootstrap 简介 4](https://www.sitepoint.com/premium/courses/introduction-to-bootstrap-4-2984) 课程，快速而有趣地了解 Bootstrap 的强大功能。**

## 分享这篇文章