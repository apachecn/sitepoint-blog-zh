# 如何创建没有图像的 CSS3 功能区

> 原文：<https://www.sitepoint.com/pure-css3-ribbons/>

在我的上一篇文章[没有图像的纯 CSS3 语音气泡](https://www.sitepoint.com/pure-css3-speech-bubbles/)中，我们看到了:before 和:after 伪元素如何被用来创建不同的效果。在这篇文章中，我们将使用类似的技术来创建各种丝带。

缎带效果正在流行。大多数设计师使用定位图像，但是我们将创建它们而不使用边框效果和单个`h2`标签:

```
 <h2>My Heading</h2> 
```

让我们给它一点风格，让它覆盖包含元素:

```
 /* containing element */
#page
{
	width: 500px;
	padding: 50px;
	margin: 0 auto;
	background-color: #fff;
	border: 1px solid #333;
}

h2
{
	position: relative;
	width: 50%;
	font-size: 1.5em;
	font-weight: bold;
	padding: 6px 20px 6px 70px;
	margin: 30px 10px 10px -70px;
	color: #555;
	background-color: #999;
	text-shadow: 0px 1px 2px #bbb;
	-webkit-box-shadow: 0px 2px 4px #888;
	-moz-box-shadow: 0px 2px 4px #888;
	box-shadow: 0px 2px 4px #888;
} 
```

在这个例子中，我们的#页面有 50px 的填充和 1px 的边框。标题的左边距为-71px，因此它与边缘重叠了 20px。请注意，我们还使用了`position: relative`,因此其他带状元素可以根据需要进行定位。

现在我们需要创建丝带的折叠部分，放在页面的“后面”。正如我们在[上一篇文章](https://www.sitepoint.com/pure-css3-speech-bubbles/)中看到的，我们可以使用边框效果在宽度和高度为零的伪元素 an :after 上创建任何类型的三角形:

```
 h2:after
{
	content: ' ';
	position: absolute;
	width: 0;
	height: 0;
	left: 0px;
	top: 100%;
	border-width: 5px 10px;
	border-style: solid;
	border-color: #666 #666 transparent transparent;
} 
```

它看起来很棒，这可能就是你所需要的。但是客户喜欢过度设计的页面，所以让我们更进一步。首先，我们可以通过叠加应用于伪元素:before:的白色三角形，将旗帜形状应用于右边:

```
 h2:before
{
	content: ' ';
	position: absolute;
	width: 0;
	height: 0;
	right: -2px;
	top: 0px;
	border-color: transparent #fff transparent transparent;
} 
```

那很好，但是在左手边有折回的缎带效果呢？没问题:

```
 h2:before
{
	content: ' ';
	position: absolute;
	width: 30px;
	height: 0;
	left: -30px;
	top: 12px;
	border-width: 20px 10px;
	border-style: solid;
	border-color: #999 #999 #999 transparent;
} 
```

![CSS3 ribbons](img/0f9197b4dd181c34bb9714f293261abb.png)伪元素的边框颜色与 h2 的背景颜色非常匹配，因为它实际上出现在标题上方。如果定位了父元素，则更改 z 索引对伪元素不起作用。

[**请参见演示页面**](https://blogs.sitepointstatic.com/examples/tech/css3-ribbons/index.html) 中显示所有三种丝带样式的示例。它在 IE8、IE9、Firefox、Chrome、Safari 和 Opera 中都能正常工作。IE6 和 IE7 优雅降级，显示主重叠标题。所有的 CSS 代码都包含在 HTML 源代码中。

谁需要 Photoshop？！

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。对 CSS3 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章