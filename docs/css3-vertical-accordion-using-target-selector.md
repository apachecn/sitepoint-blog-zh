# 如何使用:target 选择器创建仅支持 CSS3 的垂直折叠

> 原文：<https://www.sitepoint.com/css3-vertical-accordion-using-target-selector/>

我们最近创建了一个仅支持 CSS3 的标签控件，它使用了强大的[:目标选择器](https://www.sitepoint.com/css3-target-selector/)。CSS 的一个主要好处是我们可以随心所欲地改变 HTML 的样式；所以我们要把我们的标记转换成一个垂直的手风琴。

[**查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-target/accordionvert.html)

该解决方案适用于 IE9、Chrome、Firefox、Safari 和 Opera，不需要 JavaScript 或图像。它在 IE6、7 和 8 中失败得很惨，所以你要么使用[selectivitizr](http://selectivizr.com/)shim，要么对这些用户隐藏这个小部件，告诉他们升级。

## HTML

我们的 HTML5 代码与[选项卡控件](https://www.sitepoint.com/css3-tabs-using-target-selector)使用的代码相同。我只把`article`类改成了“accordion ”,并重命名了一些 id，这样更容易理解发生了什么。也有五个部分，因为它看起来更好一些:

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

和以前一样，可点击的部分标题包含在每个`section`中，作为初始的`h2`标签。

## CSS

首先，我们将样式化`article`容器和`section`元素。每个部分从其关闭状态开始，高度为 2em(注意溢出设置为*隐藏*):

```
 article.accordion
{
	display: block;
	width: 30em;
	padding: 0.5em 0.5em 1px 0.5em;
	margin: 0 auto;
	background-color: #666;
	border-radius: 5px;
	box-shadow: 0 3px 3px rgba(0,0,0,0.3);
}

article.accordion section
{
	display: block;
	width: 28em;
	height: 2em;
	padding: 0 1em;
	margin: 0 0 0.5em 0;
	color: #333;
	background-color: #333;
	overflow: hidden;
	border-radius: 3px;
} 
```

现在，剖面标题的样式设置为使用关闭状态下的所有可用房间:

```
 article.accordion section h2
{
	font-size: 1em;
	font-weight: bold;
	width: 100%;
	line-height: 2em;
	padding: 0;
	margin: 0;
	color: #ddd;
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

我们现在可以使用:target 选择器“打开”活动部分。我们设置了更大的高度和背景色，然后放大标题并重新着色:

```
 article.accordion section:target
{
	height: 15em;
	background-color: #fff;
}

article.accordion section:target h2
{
	font-size: 1.6em;
	color: #333;
} 
```

如有必要，您可以将`section`的高度设置为`auto`,这样它就可以使用所需的最小空间。然而，这使得它不可能添加漂亮的 CSS3 过渡平滑地调整元素的大小…

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

[**查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-target/accordionvert.html)

如果有的话，这个 CSS 比[选项卡控件](https://www.sitepoint.com/css3-tabs-using-target-selector)更简单，看起来更好。但是垂直的手风琴很容易——水平的要酷得多！…

## 分享这篇文章