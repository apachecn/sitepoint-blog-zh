# CSS3 转场 101:什么是转场？

> 原文：<https://www.sitepoint.com/css3-transitions-101/>

过渡是最常用的 CSS3 动画类型。尽管有广泛的采用和相对简单的语法，但是有几个陷阱和选项，即使是最有知识的开发人员也没有意识到！

这是关于 CSS3 过渡的四篇系列文章的第一篇，但是每篇文章都可以单独阅读，也可以不按顺序阅读。让我们从基础开始…

## 动画==行为== JavaScript？

反对 CSS3 过渡的一个理由是它是行为性的，侵犯了 JavaScript 的领地。那绝对是真的…但是 CSS 已经做了很多很多年了。

当我在 20 世纪 90 年代中期开始 web 开发时，CSS 和 JavaScript 都不存在。我的第一个项目是为一些图标实现鼠标悬停效果——在预编译的 applet 中使用 Java。这是几天的努力。

不到一年，JavaScript 让悬停效果变得无比简单，每个开发人员都在 HTML 中加入了诸如`onmouseover="changeColor();" onmouseout="changeColorBack();"`这样的内嵌处理工具。

此后不久，CSS 引入了动态伪类，如:hover、:active 和:focus。我们可以将样式表从 HTML 中分离出来，并在几秒钟内实现基本效果。你真的想回到 JavaScript 时代吗？

CSS3 过渡建立在现有的 CSS 技术之上。它们不提供 JavaScript 可用的逐帧控制，但对于简单的页面小部件，如弹出窗口和菜单，您很少需要细粒度的动画。虽然我承认最初有些怀疑，但 CSS3 动画精灵已经从瓶子里出来了，你永远也不会再把它放回去。

## CSS3 的优势

虽然仍然存在 JavaScript 动画的情况，但是在可能的情况下，您通常应该使用 CSS3:

1.  CSS3 过渡由浏览器本身处理。它们总是比类似的 JavaScript 动画要快。
2.  JavaScript 动画无疑难度更大。考虑实现看起来自然的加速和减速的数学复杂性——即使你知道方程式，你也需要几行代码和一些严格的测试。在 CSS3 中，它可以用一个属性来处理。
3.  有些效果仅用 JavaScript 是不切实际的，比如在二维或三维空间中旋转元素。

## 那么什么是过渡呢？

过渡是 CSS3 动画的最简单形式；它定义了在两种状态之间触发的效果。

**状态**
一个转换需要一个起点(初始状态)和一个终点(活动状态)。例如，我们可以有一个元素，它从绝对位置 0px 开始，颜色为蓝色，到 100px 结束，颜色为红色。CSS3 过渡可以在这两种状态之间平滑地产生动画效果，而不需要定义单独的帧。

**触发器**
过渡必须以某种方式被触发，以开始动画效果，最终达到结束状态。这可以在 CSS 中单独使用伪选择器来完成，例如:hover 或:focus。您还可以通过在 JavaScript 中更改一个或多个元素的样式属性来触发动画。

## CSS2.1 过渡示例

让我们来看一个我们可以在 CSS2.1 中创建的非常简单的效果:
[**查看 CSS2.1 演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transitions/css2demo.html)

将鼠标悬停在白色块上，子元素将会移动。

我们的 HTML 有一个容器块和一个段落元素:

```
<div id="container">
	<p>I will be animated</p>
</div>
```

容器有几种样式，相对定位:

```
#container
{
	position: relative;
	width: 600px;
	height: 400px;
	background-color: #fff;
	border: 2px solid #000;
	margin: 1em auto;
}
```

段落的大小和位置位于容器的左上方:

```
#container p
{
	position: absolute;
	left: 0;
	top: 0;
	width: 100px;
	height: 70px;
	font-size: 1em;
	font-weight: bold;
	text-align: center;
	padding-top: 30px;
	background-color: #00c;
}
```

当我们将鼠标悬停在容器上时，段落会移动到右下角，其颜色和边框半径也会改变:

```
#container:hover p
{
	left: 500px;
	top: 300px;
	background-color: #c00;
	border-radius: 50px;
}
```

很简单… *很少有开发者会认为应该使用 JavaScript 来处理它！*

## CSS3 过渡示例

上面的演示工作得很好，但是状态转换非常突然。让我们运用一点 CSS3 的魔法…
[**查看 CSS3 演示页面…**](https://blogs.sitepointstatic.com/examples/tech/css3-transitions/css3demo.html)

将鼠标悬停在白色块上，您会看到相同的过渡，只是它会在两种状态之间平滑地动画化。大多数人会同意这是一个更令人愉快的效果。让我们以应用于实现动画的`#container p`选择器的 CSS3 代码为例:

```
transition: all 3s ease;
```

说真的，就是这样。二十五个字符。还要注意，您可以在动画进行到一半时将光标从容器上移开，一切都会恢复正常。用 JavaScript 编写一个类似的效果需要多长时间？

诚然，我也给:hover 状态添加了一个偷偷摸摸的`transform: rotate(360deg);`，你会在 Firefox 或 IE10 中看到——但是我们几乎没有突破项目预算！

## 跨浏览器过渡支持

Firefox、IE10 和 Opera 12 支持不带前缀的过渡。

在撰写本文时，包括 Chrome、Safari 和 Opera 15+在内的 Webkit 浏览器要求在标准属性之前添加-webkit-前缀，例如

```
-webkit-transition: all 3s ease;
transition: all 3s ease;
```

不幸的是，并不是所有的浏览器都是一样的。Opera 12 中的动画可能会有点怪异。此外，Webkit 引擎在遇到 rotate 变换时会呕吐，并放弃一些其他的过渡效果(这就是不包括`-webkit-transform: rotate(360deg);`的原因)。最后，IE9 及以下将无法显示任何动画。

然而，这并不重要。如果 CSS3 转换失败或者不像您预期的那样工作，那么最终会到达开始和结束状态。在大多数情况下，浏览器会退回到类似 CSS2.1 的即时状态切换。

在下一部分中，我们将更详细地查看过渡属性，并讨论什么可以和不可以被动画化。

## 分享这篇文章