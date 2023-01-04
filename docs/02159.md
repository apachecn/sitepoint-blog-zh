# AtoZ CSS 截屏:媒体查询

> 原文：<https://www.sitepoint.com/atoz-css-screencast-media-queries/>

这个截屏是我们 AtoZ CSS 系列的一部分。

## 副本

`@media`规则允许元素的条件样式。

这些条件可以基于介质类型或所用设备的已知特性。

将媒体查询与流畅的布局和灵活的图像结合起来，使我们能够实现响应式网页设计。

在这一集里，我们将看看如何使用`@media`查询来改变网站的样式，这是基于对设备信息的查询以及在响应式设计中处理页面布局的两种方法。

### 媒体查询

有时，我们只希望某些样式适用于某些类型的设备，或者当设备的某些特征是真实的。

例如，我们可能希望在打印网页时删除页眉和页脚。

使用查询`print`将 at-rule 的样式限制为`print`媒体类型。

```
@media print {
  .site-header, .site-footer {display: none;}
}
```

其他媒体类型包括

*   `all`
*   `braille`
*   `embossed`
*   `handheld`
*   `projection`
*   `screen`
*   `speech`
*   `tty`
*   `tv`

我使用的唯一两种媒体类型是`print`和`screen`，这对于任何基于屏幕的设备来说都有点笼统，包括移动设备、电视和投影。

### 设备查询

我们可以通过向`@media`规则传递一个查询来检查关于正在使用的设备的更细粒度的细节。要查询的一个常见属性是浏览器窗口的`min-width`

```
body {
  font-size: 0.75em;
}
@media (min-width: 600px) {
  body {
    font-size: 1em;
  }
}
```

在本例中，所有器件的初始`font-size`都是`0.75em`，但是如果器件的最小宽度为`600px`(即是`600px`或更宽)那么`font size`将增加到`1em`。

关于该设备，我们可以查询许多信息:

*   `width``min-width`
*   `height``min-height`
*   `device-width` `min-device-width` 
*   `device-height` `min-device-height` `max-device-height` 
*   `aspect-ratio` `min-aspect-ratio` 
*   `device-aspect-ratio``min-device-aspect-ratio`
*   `resolution``min-resolution`
*   `color``min-color`
*   `color-index``min-color-index`
*   `monochrome``min-monochrome`
*   `scan` `grid`

其中许多也有相应的`min`和`max`种类。

我经常使用`min-width`和`max-width`，偶尔使用`orientation`、`aspect-ratio`和`resolution`，偶尔使用`min-height`和`max height`。就我记忆所及，我从未用过其他的。

到目前为止，宽度是查询设备最常见的内容，但是由于报告的宽度和设备宽度经常不同，因此有必要在 HTML 中添加以下`meta`标记，使它们等效:

```
<meta name="viewport" content="width=device-width, initial-scale=1">
```

`initial-scale`设置为防止设备缩小以适应视口中的整个站点。可以设置`maximum-scale=1`,但是这样会让用户无法缩放页面，用户体验不好。

### 组合查询

可以使用关键字`and`将查询组合在一起:

```
@media screen and (min-width: 600px) and (max-width: 800px) { }
@media screen and (orientation: portrait) and (min-width: 800px) { }
```

也可以使用否定:

```
@media not screen { }
```

并使用`only`限制适用性:

```
@media only screen { }
```

这些`@media`块可以包含您在样式表中其他地方编写的任何 CSS，并以同样的方式层叠。这意味着你可能不需要写那么多的 CSS 来改变多种设备的设计。

### 响应式设计

当某些设备特征为真时，查询允许条件样式，我们可以使用它们来控制一个页面在一系列不同设备或设备大小上的样式。

我们可以控制细节或大图布局。对于在大屏幕上浏览的网站来说，有多列文本和图像是很常见的，但这在五分之一宽的屏幕上是不可能阅读的。

由于构建和修改复杂的布局非常耗时，所以让我们用一个简单的例子，用四个框来表示页面的四个部分。每个框包含一个图像和几行文本。

如果没有应用任何样式，图像、文本和框会相互堆叠。我们可以将它们隔开一点，并添加一些边框和背景，使它们更加突出。

随着屏幕变宽，布局看起来有点拉伸，与图像的大小相比，少量的文本看起来很奇怪。大约 500px，我们可以添加一个`@media`查询来创建两列布局，而不是一列布局。

```
@media screen and (min-width: 500px) {
  .box { 
    float: left;
    width: 50%; 
  }
}
```

随着屏幕再次变宽，我们可以放入 4 列，因此可以将每个框的`width`改为 25%。

```
@media screen and (min-width:500px) {
  .box { 
     width: 25%; 
  }
}
```

由于 CSS 样式是如何级联的，我们不需要再次指定`float:left`。

这种从小屏幕开始并添加样式以制作更复杂布局的方法被称为移动优先，这是卢克·乌鲁布莱夫斯基的同名书籍中提出的。

可以反过来，从桌面开始，从更复杂的布局开始。在这种情况下，我们从 25%宽的浮动框开始，使用`max-width`查询来覆盖较小屏幕的样式。这导致了少量的 CSS，但有时更容易理解。然而，如果可能的话，我认为最好先从手机开始，因为它从一开始就专注于设计和开发，并减少了需要将复杂的布局硬塞进小屏幕的风险。

我花了一段时间才习惯移动设备，但它绝对是我这些天的首选——尽管我很想知道你的想法……

请关注我们即将发布的快速技巧文章！

## 分享这篇文章