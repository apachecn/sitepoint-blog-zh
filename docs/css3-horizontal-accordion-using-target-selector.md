# 如何使用:target 选择器创建一个仅支持 CSS3 的水平折叠面板

> 原文：<https://www.sitepoint.com/css3-horizontal-accordion-using-target-selector/>

CSS3:目标选择器非常强大，允许我们创建有吸引力的小部件，这在几年前需要忍者般的 JavaScript 技能。我们已经构建了一个[选项卡控件](https://www.sitepoint.com/css3-tabs-using-target-selector)和一个[垂直折叠控件](https://www.sitepoint.com/css3-vertical-accordion-using-target-selector)。在这篇文章中，我们将使用相同的 HTML5 标记创建一个水平折叠。

[**查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-target/accordionhorz.html)

该解决方案适用于 IE9、Chrome、Firefox、Safari 和 Opera，不需要 JavaScript 或图像。同样，它在 IE6、7 和 8 中失败得很惨，所以你要么需要一个 JavaScript shim，比如[selectivitizr](http://selectivizr.com/)或者一些其他的后备。

## HTML

这里有一个 HTML5 代码的提示。这与我们的垂直手风琴一样:在最初的`h2`标签中包含了许多带有可点击标题的`section`元素:

```
 <article class="accordion">

	<section id="acc1">
		<h2><a href="#acc1">Title One</a></h2>
		<p>This content appears on page 1.</p>
	</section>

	<section id="acc2">
		<h2><a href="#acc2">Title Two</a></h2>
		<p>This content appears on page 2.</p>
	</section>

	<section id="acc3">
		<h2><a href="#acc3">Title Three</a></h2>
		<p>This content appears on page 3.</p>
	</section>

	<section id="acc4">
		<h2><a href="#acc4">Title Four</a></h2>
		<p>This content appears on page 4.</p>
	</section>

	<section id="acc5">
		<h2><a href="#acc5">Title Five</a></h2>
		<p>This content appears on page 5.</p>
	</section>

</article> 
```

## CSS

CSS 稍微复杂一点，但是它是值得的。首先，我们设计外部`article`容器的样式:

```
 article.accordion
{
	display: block;
	width: 43em;
	margin: 0 auto;
	background-color: #666;
	overflow: auto;
	border-radius: 5px;
	box-shadow: 0 3px 3px rgba(0,0,0,0.3);
} 
```

每个部分从其关闭状态开始；它们浮动在左边，宽度为 2 米。溢出设置为*隐藏*，我们也使文本颜色与背景相同，这样它们看起来就像没有内容的实体块:

```
 article.accordion section
{
	position: relative;
	display: block;
	float: left;
	width: 2em;
	height: 12em;
	margin: 0.5em 0 0.5em 0.5em;
	color: #333;
	background-color: #333;
	overflow: hidden;
	border-radius: 3px;
} 
```

现在为一些讨厌的前缀 CSS3！使用变换将每个`h2`标题逆时针旋转 90 °,并绝对定位在关闭的`section`上:

```
 article.accordion section h2
{
	position: absolute;
	font-size: 1em;
	font-weight: bold;
	width: 12em;
	height: 2em;
	top: 12em;
	left: 0;
	text-indent: 1em;
	padding: 0;
	margin: 0;
	color: #ddd;
	-webkit-transform-origin: 0 0;
	-moz-transform-origin: 0 0;
	-ms-transform-origin: 0 0;
	-o-transform-origin: 0 0;
	transform-origin: 0 0;
	-webkit-transform: rotate(-90deg);
	-moz-transform: rotate(-90deg);
	-ms-transform: rotate(-90deg);
	-o-transform: rotate(-90deg);
	transform: rotate(-90deg);
}

article.accordion section h2 a
{
	display: block;
	width: 100%;
	line-height: 2em;
	text-decoration: none;
	color: inherit;
	outline: 0 none;
} 
```

我们现在可以使用:target 选择器“打开”活动部分。`section`宽度和颜色改变，标题移回顶部:

```
 article.accordion section:target
{
	width: 30em;
	padding: 0 1em;
	color: #333;
	background-color: #fff;
}

article.accordion section:target h2
{
	position: static;
	font-size: 1.3em;
	text-indent: 0;
	color: #333;
	-webkit-transform: rotate(0deg);
	-moz-transform: rotate(0deg);
	-ms-transform: rotate(0deg);
	-o-transform: rotate(0deg);
	transform: rotate(0deg);
} 
```

这很好，但是 CSS3 的过渡让它看起来很棒:

```
 article.accordion section,
article.accordion section h2
{
	-webkit-transition: all 1s ease;
	-moz-transition: all 1s ease;
	-ms-transition: all 1s ease;
	-o-transition: all 1s ease;
	transition: all 1s ease;
} 
```

[**查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-target/accordionhorz.html)

很遗憾 IE6，7 和 8 用户不能使用这个小工具。IE9 用户也不会看到动画。但是您需要大量的时间和耐心来使用 JavaScript 达到同样的效果！好好享受吧。

## 分享这篇文章