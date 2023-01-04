# 如何使用:target 选择器创建一个仅支持 CSS3 的选项卡控件

> 原文：<https://www.sitepoint.com/css3-tabs-using-target-selector/>

我们需要另一个选项卡控件吗？可能不会，但是这个例子展示了 [CSS3:目标选择器](https://www.sitepoint.com/css3-target-selector/)的强大功能。我们将使用 HTML5 和 CSS3 构建一个有吸引力的动画标签控件。你不需要 JavaScript 或图片，它可以在 IE9、Chrome、Firefox、Safari 和 Opera 上运行。

## 基本特征

你会在网上发现许多只支持 CSS3 的标签控件。但是，许多都存在以下问题:

*   如果链接到没有哈希选择器的页面，不显示任何选项卡内容，即链接到 mypage.html 而不是 mypage.html#tab1。
*   不突出显示活动选项卡。

该解决方案克服了这些问题— [**查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-target/tabs.html)

## IE6，7，8 呢？

你期望现代的 CSS3 效果能在 2001 年发布的浏览器中工作吗？IE7 和 8 将只显示第一个选项卡。IE6 显示了最后一个标签，尽管很容易将其设置为第一个。

因此，您有两个选择:

1.  不要提供传统浏览器支持。IE 用户不会知道他们错过了什么，所以，如果内容对你的页面不重要，你可以选择忽略它们。
2.  但这不太好。最快的解决方案是添加[selectivitizr](http://selectivizr.com/)垫片。或者，求助于一个更好的[渐进增强的解决方案](https://www.sitepoint.com/progressive-enhancement-1-html/)，忘掉这个古怪的 CSS3 废话。

## HTML

这是我们的基本 HTML5 代码。选项卡内容包含在一个`section`中。选项卡本身是第一个子元素，被定义为一个`h2`元素，内部链接到外部的`section`:

```
 <article class="tabs">

	<section id="tab1">
		<h2><a href="#tab1">Tab 1</a></h2>
		<p>This content appears on tab 1.</p>
	</section>

	<section id="tab2">
		<h2><a href="#tab2">Tab 2</a></h2>
		<p>This content appears on tab 2.</p>
	</section>

	<section id="tab3">
		<h2><a href="#tab3">Tab 3</a></h2>
		<p>This content appears on tab 3.</p>
	</section>

</article> 
```

这不同于你以前见过的 HTML 标签代码。大多数控件将选项卡定义为一个`ul`列表，后跟每个内容部分。虽然可以使用类似的标记，但是这使得标签突出显示更加困难，因为标签本身不能使用`:target`来设计。我发现的最好的解决方案是给`section`添加一个伪元素，它被相应地着色并放置在标签文本的下面。这很快就变成了一团乱麻。

## CSS

首先，我们将样式化`article`容器。它的大小和位置被设置为相对于的*，这样我们就可以定位这些部分:*

```
 article.tabs
{
	position: relative;
	display: block;
	width: 40em;
	height: 15em;
	margin: 2em auto;
} 
```

接下来是各节。它们都绝对位于距离顶部 1.8 米的位置，以便为标签留出空间。`box-shadow`相当轻，因为每一部分都堆叠在另一部分之上:

```
 article.tabs section
{
	position: absolute;
	display: block;
	top: 1.8em;
	left: 0;
	height: 12em;
	padding: 10px 20px;
	background-color: #ddd;
	border-radius: 5px;
	box-shadow: 0 3px 3px rgba(0,0,0,0.1);
	z-index: 0;
} 
```

由于最后一个选项卡将显示在顶部，我们将通过设置更高的 z 索引将其切换到第一个选项卡:

```
 article.tabs section:first-child
{
	z-index: 1;
} 
```

我们现在可以设计标签了。它们在“关闭”状态下是彩色的，并且位置高于我们的部分。调整第二个和第三个标签的左侧位置，以确保它们不会相互重叠。

```
 article.tabs section h2
{
	position: absolute;
	font-size: 1em;
	font-weight: normal;
	width: 120px;
	height: 1.8em;
	top: -1.8em;
	left: 10px;
	padding: 0;
	margin: 0;
	color: #999;
	background-color: #ddd;
	border-radius: 5px 5px 0 0;
}

article.tabs section:nth-child(2) h2
{
	left: 132px;
}

article.tabs section:nth-child(3) h2
{
	left: 254px;
}

article.tabs section h2 a
{
	display: block;
	width: 100%;
	line-height: 1.8em;
	text-align: center;
	text-decoration: none;
	color: inherit;
	outline: 0 none;
} 
```

我们所有的选项卡和部分现在都已定义，即使 URL 中没有目标部分，选项卡 1 也会默认显示。现在，我们可以使用目标选择器来更改活动部分的颜色、背景色和 z 索引:

```
 article.tabs section:target,
article.tabs section:target h2
{
	color: #333;
	background-color: #fff;
	z-index: 2;
} 
```

作为奖励，让我们在目标选项卡改变时添加一个过渡效果:

```
 article.tabs section,
article.tabs section h2
{
	-webkit-transition: all 500ms ease;
	-moz-transition: all 500ms ease;
	-ms-transition: all 500ms ease;
	-o-transition: all 500ms ease;
	transition: all 500ms ease;
} 
```

[**查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-target/tabs.html)

与许多 JavaScript 解决方案不同，我们的 CSS3 小部件保留了选项卡视图的完整历史，因此浏览器的后退和前进按钮可以正确操作。也可以从页面的任何地方直接链接到标签——如“下一页”链接所示。

它并不完美；当您最初链接到该页面时，第一个选项卡是活动的，但显示为“关闭”状态。除非你能想出解决办法，否则很难解决那个问题？最简单的解决方法就是让“开”和“关”状态的差异变得更加微妙。

这还没有结束。因为我们使用的是 CSS，所以我们可以在不改变 HTML 标记的情况下将选项卡控件转换成其他小部件。请继续关注 SitePoint…

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Learn CSS3](https://learnable.com/courses/learn-css3-203?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。对 CSS3 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章