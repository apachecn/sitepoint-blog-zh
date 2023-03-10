# AtoZ CSS 截屏:对齐文本

> 原文：<https://www.sitepoint.com/atoz-css-screencast-justify-text/>

本文是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

Justify 是`text-align`属性的一个可用值。

它允许以报纸设计中常用的样式格式化文本。

在这一集里，我们将看看`text-align`的 12 个可能值，其中一个是`justify`。接下来，我们将看看如何在 CSS 列中使用两端对齐的文本，最后看看如何使用`justify`只用几行代码创建一个无浮动响应的网格系统。

### 文本对齐

属性执行一个非常熟悉的对齐文本的动作。任何阅读过页面上的文字或使用过文字处理器的人都会熟悉文本左对齐、居中对齐或右对齐的概念。

这是与`text-align`一起使用的 3 个最常见的值，但还有其他值。

*   `left`
*   `right`
*   `center`
*   `justify`
*   `start`
*   `end`
*   `start end`
*   `match-parent`
*   `<string>`
*   `start <string>`
*   `<string> end`
*   `inherit`

`start`和`end`关键字类似于`left`和`right`，但是考虑了文本的方向。如果语言是从左到右，`start`和`left`是等价的。如果文本方向是`rtl`，那么`start`和`right`是等效的。

`match-parent`值与`inherit`相似，但`start`和`end`是根据父节点的`direction`计算的。这个值不支持浏览器，但是在 Chrome 中可以使用。

`<string>`值允许由任意文本字符串确定对齐。目前任何浏览器都不支持这个值，所以我无法测试它。MDN 上的例子听起来非常有用，虽然要在“.”上对齐十进制值句号字符串。

### CSS 列

抛开浏览器兼容性的泥潭和`text-align`的一些小众用途，让我们来看看`text-align: justify`变得非常有用的地方。

有时，将大块文本分成多列对设计有益。这曾经是一些相当复杂的 Javascript 的领域，但现在可以在 CSS 中使用`column-count`属性简单地完成。所有现代浏览器都支持这个属性，但是除了 IE 之外，所有浏览器都需要前缀。

```
.box {
	-webkit-column-count: 2;
	-moz-column-count: 2;
	column-count: 2;
}
```

随着可用空间的变化，内联内容会自动重排。很可爱。

为了使列的视觉冲击力更强，看起来更像一篇报纸文章，我们可以使用`text-align: justify`。

### 网格

我们已经看到了如何操作文本对齐来调整复制并创建定义整齐的列。也有可能利用`justify`来创建一个没有浮动、没有明确修正、没有戏剧的流动的、响应迅速的网格系统。要了解更多关于`float`的信息和一些棘手问题的解决方案，请查看*《第六集:漂浮》*。

两端对齐的文本将其左右边缘与其块容器的左右边缘对齐。我们可以创建一系列的`inline-block`元素，并通过调整其容器元素的内容将它们放在一个网格中。我在 Patrick Kunka 于 2013 年 3 月发表的一篇文章中读到了这种技术，它非常令人惊叹。

我将用一个“grid-container”类创建一个无序列表，用六个列表项作为“grid-items”。

我将添加一个宽度，一个背景颜色和一些填充到列表项中，使它们间隔开一点。我还会将它们设置为`display: inline-block`，这是它们被调整所必需的。为了删除由`inline-block`引入的间距，我将把网格容器的`font-size`设置为零，把网格项目的`font-size`设置为`1rem`。

现在我们在网格容器上设置`text-align: justify`,但是在网格项目被等间距放置之前，我们需要创建一个全角的“隐藏”元素，它将决定项目被展开的空间。

使用一个`:after`伪元素，有点像 clearfix 中使用的方法，我们可以强制网格容器的宽度与其父容器一样宽。现在，网格项可以正确分隔，并随着浏览器窗口大小的变化而重新排列。

```
* {
	-moz-box-sizing: border-box;
	box-sizing: border-box;
}
ul { 
	margin: 0;
	padding: 0;
	text-align: justify; 
	font-size: 0; 
}
ul:after { 
	content: "";
	display: inline-block; 
	width: 100%; 
}
li { 
	display: inline-block; 
	width: 50%; 
	margin: 0 0 1em; 
	padding: 1em; 
	background: #eee; 
	font-size: 1rem; 
	text-align: left; 
}
```

通过更改列表项的宽度，您可以轻松创建不同数量的列，而无需对边距和装订线宽度进行大量计算。我认为这是一个非常简单和优雅的技术，可能会在未来的项目中派上用场。

请关注我们即将发布的快速技巧文章！

## 分享这篇文章