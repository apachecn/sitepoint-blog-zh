# CSS3 过渡属性基础

> 原文：<https://www.sitepoint.com/css3-transition-properties/>

在本系列关于 CSS3 转换的第二部分中，我们将详细了解这些属性。但首先，我们需要知道什么可以和不可以动画…

## 哪些 CSS 属性可以制作动画？

一般来说，转换可以应用于任何具有在开始和结束状态之间变化的离散值的 CSS 属性。通常，这包括布局属性(宽度、高度、填充、边距、边框)、位置属性(左、右、上、下)、变换、字体大小、颜色、背景颜色和不透明度…

*   `width`例如，10em 至 200px
*   `padding`，例如 0px 到 10px
*   `color`，例如#F00 至#00F
*   `top`，例如 0px 到 300px
*   `border-radius`，例如 10px 到 3px
*   `transform` , e.g. rotate(0deg) to rotate(90deg)

您还可以将过渡应用到颜色关键词，如栗色、紫红色和蓝绿色，因为这些颜色关键词会有效地转换为 RGB 值。

## 哪些 CSS 属性不能制作动画？

这就是事情变得有点棘手的地方。如果你像我一样，你尝试的第一个转变是这样的:

```
#container p
{
	display: none;
	transition: all 3s ease;
}

#container:hover p
{
	display: block;
}
```

您将看到一个像 CSS2.1 中那样的突然过渡，而不是很好地淡入元素。原因:*浏览器不可能确定中间状态*。

从页面上删除一个块，就好像它从来不存在一样。块不能部分显示；要么在那里，要么不在。对于`visibility`也是如此；你不能指望一个街区是半个`hidden`，根据定义，应该是`visible`！幸运的是，你可以用`opacity`来代替淡入淡出效果。

下一个问题:你不能依赖“自动”维度，例如

```
#container p
{
	height: 0px;
	transition: all 3s ease;
}

#container:hover p
{
	height: auto;
}
```

“自动”不是离散尺寸，因此浏览器无法计算零和不确定值之间的点。然而，有一个巧妙的解决办法；使用`max-height`并将其设置为大于盒子实际高度的值。

同样，您不能将属性切换到“自动”来处理移动。此示例也将失败:

```
#container p
{
	left: 0px;
	right: auto;
	transition: all 3s ease;
}

#container:hover p
{
	left: auto;
	right: 0px;
}
```

最后，不要指望背景图片会神奇地从一张照片变成另一张。它有可能在未来实现，一些供应商在 CSS3 渐变方面取得了有限的成功，但现在最好避免。

## 过渡属性

属性声明哪些 CSS 属性将被应用一个过渡，例如

```
#container p.one
{
	transition-property: color;
}

#container p.two
{
	transition-property: width;
}

#container p.three
{
	transition-property: color, width;
}
```

如果你乐意一切都改变，使用值`all`。您也可以使用值`none`关闭过渡。

## 过渡持续时间

`transition-duration`以秒(s)或毫秒(ms)为单位指定动画周期——千分之一秒。以下持续时间是相同的:

```
#container p.one
{
	transition-duration: 3s;
}

#container p.two
{
	transition-duration: 3000ms;
}
```

## 过渡时间函数

`transition-timing-function`决定动画在整个持续时间内的速度变化。有许多可能的值:

*   **缓和** —默认；动画开始缓慢，然后快速加速。在中点，它减速并减速到停止。
*   **缓入缓出** —类似于缓出，但加速和减速更微妙。
*   **缓入** —缓慢启动，但突然加速并停止。
*   **减速** —快速启动，但减速至停止。
*   **线性** —动画全程匀速；通常最适合用于颜色或不透明度的变化。

最后，我们有`cubic-bezier`可以让你定义你自己的怪异和美妙的计时功能。这有点复杂，所以我们将在本系列的下一部分回到这个问题。

## 过渡延迟

最后，`transition-delay`以秒或毫秒为单位指定过渡开始前的时间。以下是相同的:

```
#container p.one
{
	transition-delay: 0.5s;
}

#container p.two
{
	transition-delay: 500ms;
}
```

## CSS 速记符号

让我们看一个完整的转换声明:

```
#container p
{
	transition-property: all;
	transition-duration: 3s;
	transition-timing-function: ease-in-out;
	transition-delay: 0.5s;
}
```

不要忘记，您还需要-webkit 前缀的等价物。

幸运的是，您可以使用一个单独的`transition`声明来保存您的手指和理智，该声明按顺序接受属性、持续时间、计时功能和延迟，例如

```
#element
{
	/* starting state styles */
	color: #F00;
	-webkit-transition: all 3s ease-in-out 0.5s;
	transition: all 3s ease-in-out 0.5s;
}

#element:hover
{
	/* ending state styles */
	color: #00F;
}
```

[**使用此过渡查看示例页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transitions/properties.html)

## 选择选择器

在所有这些例子中，我已经在标准(非悬停)CSS 选择器中定义了 CSS `transition`。然而，这将级联到`:hover`选择器。换句话说，它等同于:

```
#element, 
#element:hover
{
	/* starting and ending state styles */
	-webkit-transition: all 3s ease-in-out 0.5s;
	transition: all 3s ease-in-out 0.5s;
}
```

因此，相同的转换将在两个方向上发生:从开始到结束状态和从结束到开始状态。

在本系列的下一部分中，我们将更详细地研究`cubic-bezier`跃迁计时函数。

## 分享这篇文章