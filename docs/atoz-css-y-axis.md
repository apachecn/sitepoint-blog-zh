# AtoZ CSS 快速提示:使用 Y 轴变换和矩阵

> 原文：<https://www.sitepoint.com/atoz-css-y-axis/>

本文是我们 AtoZ CSS 系列的一部分。你可以在这里找到这个系列[的其他条目。
你可以在`rotateY`](https://www.sitepoint.com/blog/) [点击](https://www.sitepoint.com/atoz-css-screencast-rotatey)查看完整的文字稿和截屏。

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),每个值都以字母表中不同的字母开始。我们知道有时仅仅截屏是不够的，所以在本文中，我们添加了一个关于 Y 轴变换和矩阵的快速技巧。

![y3-01](img/32acde47716dd8e57625c68bea269838.png)

## Y 代表 Y 轴变换和矩阵

我们可以沿着不同的轴对元素进行各种有趣的变换。在这篇技巧文章中，我们将提供所有常见选项的概要，然后查看一个不太为人知的值`transform`，它允许我们通过`matrix()`值设置多个`transform`值。

### `transform`沿 y 轴

由于最初的截屏聚焦在`rotateY()`周围，我认为沿着垂直 y 轴观察所有其他可用的变换将是有用的。

我们可以应用以下变换:

*   `transform: translateY(100px)`
*   `transform: rotateY(45deg)`
*   `transform: scaleY(1.5)`
*   `transform: skewY(30deg)`

这些值都被限制在 y 轴上，并且可以用来传达这样的意图，即沿着该单个轴的唯一变换正在被执行。

`rotateY()`是这些值中唯一一个执行 3D 变换的值，其中元素绕 y 轴旋转。如果执行任何类型的 3D `transform`，请确保在父元素上设置`transform-style:preserve-3d`。

### 多重`transform`

如果您想将多个`transform`值组合在一起，可以通过空间分隔不同的转换函数来实现。例如，下面的代码片段将一个翻译与一个`skew`组合在一起:

```
.element {
  transform: translateY(50px) skew(30deg);
}
```

这一切都很好，但如果你像这样设置多个`transform`作为动画或过渡的一部分，这可能会非常复杂，因为`transform`的每个部分都需要在每个状态或关键帧中处理。

通过`matrix()`值设置`transform`有一个稍微复杂一点但是更强大的方法。

### 用`matrix()`设置复合体`transform`

`matrix()`是一个单一值，允许您通过传递 6 个数字参数来一次设置`scale`、`skew`和`translate`。

```
.element {
  transform: matrix(a, b, c, d, e, f);
}
```

这项技术的核心是一些非常复杂的数学问题，我总是绞尽脑汁去尝试解决。因此，我不想解释数学矩阵、点积和向量，让我们看看`matrix()`中的每个参数指的是什么。

如果你真的想深入了解数学，可以读一读这篇由用户代理人撰写的文章。

在上面的代码片段中，我用从`a`到`f`的不同字母标记了矩阵函数的每个参数。

`a`和`d`用于设置水平和垂直刻度。

`b`和`c`用于设置水平和垂直倾斜量。

最后，`e`和`f`用于设置水平和垂直平移。

有一点让整个事情变得有点难以理解，那就是每个参数都是以数值而不是长度的形式提供的。长度是像`100px`或`30deg`这样的值，但是这些对于矩阵来说不是有效值，我们需要使用数字来代替。

为了演示这一点，下面的代码片段产生了两个相同的视觉结果；一个使用多个`transform`和一个矩阵:

```
.one {
  transform: scale(1.5, 1.5) skew(10deg, 10deg) translate(100px, -50px);
}
.two {
  transform: matrix(1.5, 0.26449, 0.26449, 1.5, 136.775, -48.551);
}
```

下面是上述代码片段的代码笔，您可以看到相同的结果:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [qqEjMz](http://codepen.io/SitePoint/pen/qqEjMz/) 。