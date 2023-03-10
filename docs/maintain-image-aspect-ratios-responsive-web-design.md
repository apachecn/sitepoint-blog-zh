# 如何在响应式网页设计中保持图像的长宽比

> 原文：<https://www.sitepoint.com/maintain-image-aspect-ratios-responsive-web-design/>

考虑一组典型的图片库缩略图:

```
<ul>
  <li><a href="#"><img src="https://lorempixel.com/320/180/sports" /></a></li>
  <li><a href="#"><img src="https://lorempixel.com/320/180/city" /></a></li>
  <li><a href="#"><img src="https://lorempixel.com/352/198/technics" /></a></li>
</ul>
```

我们可以使用 CSS 在响应页面模板中以任意大小显示此图库(显示了基本属性):

```
ul
{
  width: 100%;
  padding: 0;
  margin: 0 0 2em 0;
  list-style-type: none;
}

li
{
  float: left;
  width: 33.3%;
  padding: 0;
  margin: 0;
  background-color: #000;
  border: 10px solid #fff;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  overflow: hidden;
}

li a
{
  display: block;
  width: 100%;
}

img
{
  display: block;
  max-width: 100%;
}
```

这样做很好，因为我们所有的图像都有相同的 16:9 宽高比。图像的高度正好是宽度的 56.25%(用百分比表示的 9 除以 16)。

![Aspect ratio example images in a row](img/10527ffade4cd9513828951613199cce.png)

然而，我们网页设计师是偏执的:人们密谋反对我们，提供无限大小和长宽比的照片。

```
<ul>
  <li><a href="#"><img src="https://lorempixel.com/320/180/sports" /></a></li>
  <li><a href="#"><img src="https://lorempixel.com/320/320/city" /></a></li>
  <li><a href="#"><img src="https://lorempixel.com/200/150/technics" /></a></li>
</ul>
```

![Responsive aspect ratio examples - part 2](img/295f855c60836fcc2aeaa47012343ed7.png)

这个问题有多种解决方案:

1.  我们可以手动调整每个图像的大小。那是费时又繁琐的。
2.  我们可以实现一个聪明的基于服务器的自动图像大小调整解决方案。这可能需要一段时间，最终的图像可能不会像我们希望的那样完美或优化。
3.  我们可以像天后一样大发脾气，拒绝在这种条件下工作。当然，这是不专业的，我们中没有人会诉诸这种策略(太频繁)。

或者我们可以使用 CSS 来解决这个问题吗？

我们可以，但并不像你想象的那么简单。在旧的固定宽度设计时代，我们已经知道图像占位符的宽度。如果是 160 像素，我们可以把高度设为 90 像素，然后早点离开去喝啤酒。在这个例子中，我们的宽度是容器的 33.3%减去左边和右边的 20px。它可以是任何大小，所以设置一个固定的高度会妨碍我们所需的长宽比。

## 百分比填充策略

填充的一个鲜为人知的怪癖是，设置顶部或底部百分比基于包含块的**宽度**。如果你的块宽为 100 像素，`padding-top: 30%;`将等于 30 像素。我怀疑这样做是为了使渲染计算更容易，因为元素高度通常是由其内容决定的。此外，如果你有一个 300 像素的固定高度的父节点，并在一个子节点上设置`padding-top: 200%;`,那么父节点至少会变成 600 像素——从而导致一个破坏 web 的递归级联。

不管是什么原因，它都非常有用，因为它允许你设置一个内在比率，例如

```
#element
{
	position: relative;
	height: 0;
	padding: 56.25% 0 0 0;
}
```

基于容器的宽度，该元素将保持 16:9 的比例。高度被设置为 0px，但是由于我们已经设置了`position: relative;`，我们可以绝对地定位任何子元素。

据我所知，填充技巧最初是由 Thierry Koblentz 为了创建响应视频而提出的，但同样的概念也可以应用于图像或任何其他内容。让我们更新我们的缩略图 CSS:

```
li a
{
  display: block;
  width: 100%;
  position: relative;
  height: 0;
  padding: 56.25% 0 0 0;
  overflow: hidden;
}

img
{
  position: absolute;
  display: block;
  max-width: 100%;
  max-height: 100%;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto;
}
```

如果没有匹配的 16:9 尺寸，结果将显示带有黑色边框的图像:

![Responsive aspect ratio examples - example 3](img/ef096c5fcb376d233def0300bd5e5943.png)

[**查看演示代码……**](http://cssdeck.com/labs/rwd-image-aspect-ratio)

摆弄 CSS。移除图像`max-width`或`max-height`可以应用裁剪效果，而不是调整大小。希望你觉得有用。

如果你有兴趣了解更多关于响应式网页设计的知识，可以看看我们的新书《快速启动响应式网页设计》。这篇文章的作者 Craig Buckler 编辑了这篇文章。

## 分享这篇文章