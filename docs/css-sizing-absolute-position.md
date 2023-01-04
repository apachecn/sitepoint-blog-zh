# CSS 中调整绝对元素大小的两种方法

> 原文：<https://www.sitepoint.com/css-sizing-absolute-position/>

任何使用过 CSS 一段时间的人都会知道绝对和相对定位的优点。概括一下:

**`position: relative`** 允许元素从其原始位置水平移动(使用`left`或`right`)或垂直移动(使用`top`或`bottom`)。

**`position: absolute`** 允许使用`left`、`right`、`top`或`bottom`相对于包含块定位元素(包含块是具有相对、绝对或固定位置的最近祖先节点)。

因此，定位节点很简单，例如

**HTML:**

```
 <div id="outer">
	<div id="inner"></div>
</div> 
```

**CSS:**

```
 #outer
{
	position: relative;
	width: 200px;
	height: 200px;
	margin: 20px auto;
	border: 2px solid #c00;
}

#inner
{
	position: absolute;
	left: 50px;
	top: 50px;
	width: 96px;
	height: 96px;
	background-color: #ddc;
	border: 2px solid #00c;
} 
```

*(由于添加了边框，内部块的实际宽度和高度将为 100px)。*

每个现代浏览器(包括 IE6)都会呈现以下框:

不太为人所知的是，你可以在**相同的**时间应用所有的`left`、`right`、`top`和`bottom`属性。以下 CSS 将完全相同地呈现内部元素:

```
 #inner
{
	position: absolute;
	left: 50px;
	right: 50px;
	top: 50px;
	bottom: 50px;
	background-color: #ddc;
	border: 2px solid #00c;
} 
```

内框的宽度和高度将保持 100px，但是我们不需要显式设置尺寸。

在以下情况下，这可能很有用:

*   元素周围的间距比宽度或高度更重要。您还可以使用负的`left`、`right`、`top`和/或`bottom`属性来使内部框大于其外部父对象。
*   您有多个具有不同边框和填充的内部元素，但需要一致的外部间距。此方法允许您为所有这些元素创建一个样式。

JavaScript 动画也可以从中受益，因为如果不需要计算结果的宽度和高度，调整元素的大小会更容易、更快，例如

```
 // expands and contracts the inner box
window.onload = function() {

	var inner = document.getElementById("inner");
	var offset = 100, dir = -1;
	setInterval(function() {
		inner.style.left = inner.style.right = inner.style.top = inner.style.bottom = offset+"px";
		offset += dir;
		if (offset == 0 || offset == 100) dir = -dir;
	}, 10);

} 
```

**关于浏览器兼容性的注意事项:**这种技术适用于所有主流浏览器，*除外* IE6，它只理解明确的宽度和高度。有惊喜！

## 分享这篇文章