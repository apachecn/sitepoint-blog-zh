# AtoZ CSS:平移和相对位置之间的差异

> 原文：<https://www.sitepoint.com/atoz-css-translate-vs-position/>

本文是我们 AtoZ CSS 系列的一部分。你可以在这里找到这个系列[的其他条目。
你可以在这里](https://www.sitepoint.com/blog/)查看关于`translateX` [的相应视频的完整文字稿和截屏。](https://www.sitepoint.com/atoz-css-screencast-translatex)

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),每个值都以字母表中不同的字母开始。我们知道有时仅仅截屏是不够的，所以在本文中，我们添加了关于`translate`和`position`之间差异的快速提示。

![x1-01](img/fa9d669aee04728933b96af81c901364.png)

## x 代表`translate`和`position`

有许多 CSS 属性用于在页面上放置元素。这些包括大图片布局属性，如`float`、`margin`和`padding`，以及更细粒度的工具，如`position`和`translate()`。

从表面上看，`position:relative`和`transform:translate()`似乎以非常相似的方式工作，但是有一些微妙的区别，这一点很重要，因此我们要为工作选择正确的工具。

### `translate`和`position:relative`有什么区别？

在本帖中，我们将看看这些元素放置方法之间的一些差异，但首先，让我澄清一下这些不同的属性是做什么的。

如果我们在一个元素上设置了`position:relative`,我们可以用它来创建一个边界，用于*绝对*定位元素。这可能是相对定位更常见的用法，但它不是我们在这里讨论的*而不是*的用法。

如果我们将`position:relative`与偏移属性`top`、`bottom`、`left`或`right`中的一个结合起来，元素将从其在布局中的原始位置移动，同时保留其在文档中曾经占据的空间。该元素将被移动到一个新层上，其“层顺序”或堆叠顺序可以用`z-index`属性控制。

```
.thing {
  position: relative;
  top: 100px;
  left: 50px;
}
```

在上面的例子中，元素将从顶部移动`100px`，从原始位置的左侧移动`50px`。

当使用`transform:translate(x,y)`时，我们得到了与使用相对位置非常相似的视觉结果。使用下面的代码片段可以获得与上面相同的结果:

```
.thing {
  transform: translate(50px, 100px);
}
```

在这种情况下，我们通过沿 x 轴的`50px`和沿 y 轴的`100px`来转换元素的坐标。最终结果在视觉上与前面的`position`示例相同。

那么，为什么我们有两种方法来做同样的事情呢？嗯，这些方法之间有一些差异…

### 浏览器支持

`position`是 CSS2 属性，而`transform`是 CSS3 属性。结果是浏览器支持有所不同，尽管真正唯一不支持 2D 变换的浏览器是 IE8 及以下版本。

如果你需要支持旧版本的 IE，那么`transform`不是你的选择。

### GPU 加速

在可能的情况下，`transform`属性将使用硬件加速，因此如果元素上也使用了任何动画或过渡，使用`translate()`而不是`position`将会看到性能优势。

如果你想移动一个元素作为过渡或关键帧动画的一部分，最好使用`translate`而不是`position`(这适用于绝对和相对定位)。关于这方面的更多深度，包括解释和性能分析，请查看保罗·爱尔兰的视频[。](http://www.paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/)

### 基于百分比的值表现不同

这两种放置元素的方法之间的一个主要区别是它们对基于百分比的值的响应方式。

以下列标记和样式为例:

```
<div class="box position"></div> 
<div class="box transform"></div>
```

```
.box {
  width: 200px;
  height: 200px;
}
.position {
  position: relative;
  left: 50%;
  background: red;
}
.transform {
  transform: translateX(50%);
  background: blue;
}
```

两个元素都被赋予了从其左边缘`50%`的偏移量。

红框的左边缘将距离其父容器边缘 50%。

蓝框的左边缘将`100px`远离其父容器的左边缘。这个距离是因为`200px`的 50%是`100px`。

当使用`translate`设置百分比值时，百分比是以元素计算宽度或高度的百分比来测量的。

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[vyxgj](http://codepen.io/SitePoint/pen/vyYxgJ/)。