# AtoZ CSS 截屏:Auto 的 CSS 值

> 原文：<https://www.sitepoint.com/atoz-css-screencast-auto/>

这个截屏是我们 AtoZ CSS 系列的一部分。点击查看整个系列[。](https://www.sitepoint.com/blog/)

## 副本

**Auto** 是一个有多种用途的 CSS 值。

这是许多盒模型属性的默认值，如`width`、`height`、`margin`和`background-size`。

```
.box {
  width: auto;
  height: auto;
  margin: auto;
  background-size: auto auto;
}
```

也是`top`、`bottom`、`left`、`right`、`z-index`等职位属性的默认值。

```
.box {
  position: relative|absolute|fixed;
  top: auto;
  right: auto;
  bottom: auto;
  left: auto;
  z-index: auto;
}
```

auto 还可以用在其他更不显眼的地方，比如 CSS 列的`overflow`、`cursor`、`table-layout`和`column-width`属性。

```
.box {
  overflow: auto;
  cursor: auto;
  table-layout: auto;
  column-width: auto;
}
```

一般来说，auto 的值是由用户代理自动计算的，具体效果取决于它所使用的内容和上下文。

让我们看一个例子…

```
.box {
  width: auto;
  height: auto;
}
```

### 宽度/高度

当默认值 auto 应用于包含一些虚拟文本的`<div>`的宽度和高度时，该框的尺寸被自动计算，并且该框填充页面的宽度，其高度与包含所有内容所需的高度一样。

值得注意的是，width 和 height `auto`不需要声明，但在这里已经被添加来说明这一点。

在这种情况下，当调整窗口大小时，`<div>`的可用宽度会减少，其尺寸会自动重新计算以容纳内容。

这是任何块级元素的默认行为，我们将在*“第 4 集:`display`”*中详细了解这一点。

### 边缘

虽然`auto`在很多情况下是默认值，但有时我们希望专门使用它来达到某种效果。

web 上一个非常常见的设计模式是将一块内容居中，通常是整个网站在一定宽度之上。这可以通过在包装器元素上设置一个`max-width`并在左侧和右侧设置`margin:auto`来实现。

当我们使用`margin-left:auto`时，浏览器会在左侧添加尽可能多的空白区域——并且框会向右移动，直到它的最右边与其包含的元素对齐。当我们在右手边做同样的事情时，同样的事情发生在相反的方向，盒子尽可能地向左移动。

```
.box {
  max-width: 50%;
  margin: 0 auto;
}
```

因为现在两边的边距相等，所以该框在页面上保持居中。

很遗憾这个技巧不能和垂直居中一起使用，但是我们会在后面的视频中解决这个问题。

### 宽度 100%

带有`width:auto`的`<div>`扩展到其父对象的宽度，但是在`width:auto`和`width:100%`之间有一个重要的区别。

如果我们有一个具有宽度和高度的`<div>`和一些内部填充，宽度会自动计算并考虑填充。

如果我们把`width:auto`改成`width:100%`，盒子就会从容器中脱离出来，所有东西看起来都碎了。这是因为`width:100%`将我们的`<div>`的宽度设置为其父元素宽度的 100%——然后填充是额外添加的。

这使得我们的`<div>`的宽度为 100% + 50px + 50px，比视窗宽。

一种解决方法是使用`auto`或`box-sizing: border-box`模型，在该模型中，填充和边框被添加到框的宽度内。下一集是关于 CSS 盒子模型的，所以一定要查看更多信息。有时使用`box-sizing`不是一个选项，所以这是一个方便的工具。

### 位置

auto 可以提供帮助的另一个地方是使用 CSS 定位。

假设一个元素位于一个盒子的左上角。假设在某个宽度上，我们希望相同的元素在右下角。

我已经更改了背景颜色，以显示媒体查询确实被触发，但似乎设置`bottom`或`right:0`不会有任何效果。

这是因为`top`和`left`优先于`bottom`和`right`。

要做到这一点，我们首先必须通过将`top`和`left`设置为`auto`来消除它们的影响。

```
.box {
  position: absolute;
  left: auto;
  right: 0;
}
```

现在一切都按预期运行。

请关注我们即将发布的快速技巧文章！

## 分享这篇文章