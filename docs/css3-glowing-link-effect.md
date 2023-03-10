# 如何在 CSS3 中创建发光链接

> 原文：<https://www.sitepoint.com/css3-glowing-link-effect/>

在我之前的 CSS3 文章中，我们使用阴影和透明文本颜色创建了模糊文本。今天，我们将使用类似的技术来创建动画发光链接。

Text-shadow 是一个多功能的 CSS3 属性，所有浏览器都支持它，没有供应商前缀。除了一个。对不起 IE9 用户——你需要再等几个月 IE10。不过，它不仅仅对阴影有用。在较暗的背景上，白色“阴影”会使文本发光:

![CSS3 Glowing Text](img/aa903aded686331264e1879fbe9d88a8.png)

这可以在用户悬停/聚焦在链接上时应用。通过一点 CSS3 过渡魔法，我们可以创建一个动画发光效果。让我们写一些代码。从我们的 HTML 开始，我们将对链接应用一个“glow”类:

```
 <a href="#" class="glow">Glowing Link</a> 
```

我们的第一个 CSS3 声明定义了初始状态和厂商前缀转换属性。过渡立即开始，持续半秒钟。我发现“线性”定时产生了最自然的效果，但是你可以用其他的(缓进、缓出、缓进、缓出、三次贝塞尔曲线)进行实验:

```
 a.glow, a.glow:hover, a.glow:focus
{
	text-decoration: none;
	color: #aaf;
	text-shadow: none;
	-webkit-transition: 500ms linear 0s;
	-moz-transition: 500ms linear 0s;
	-o-transition: 500ms linear 0s;
	transition: 500ms linear 0s;
	outline: 0 none;
} 
```

我们现在可以定义发光文本的属性。我发现一个单一的文字阴影，如 0 0 8px #fff 有点太微妙。两个阴影产生了更好的结果-一个白色和一个亮黄色，偏移略有不同:

```
 a.glow:hover, a.glow:focus
{
	color: #fff;
	text-shadow: -1px 1px 8px #ffc, 1px -1px 8px #fff;
} 
```

[**查看发光链接演示页面**](https://blogs.sitepointstatic.com/examples/tech/link-glow/index.html) 。源代码包含了所有的代码，我建议你尝试不同的动画和文本阴影属性。

**warning:** More blurry behavior in Opera

这种动画效果在 Firefox、Chrome 和 Safari 中运行良好。IE9 不支持文字阴影，所以看不到效果。Opera 支持 CSS3 过渡，但它只影响某些属性。颜色效果很好，但它不适用于文本阴影，这会导致更突兀的动画。这应该在未来的版本中得到解决。

[演示页面上的第二组链接](https://blogs.sitepointstatic.com/examples/tech/link-glow/index.html)显示了通过将文本颜色改为与背景相同而创建的背光效果。但是，这使得文本在 IE9 及以下版本上不可见。为了解决这个问题，我们可以使用 [Modernizr](http://www.modernizr.com/) 或者[编写我们自己的文本阴影检测代码](https://www.sitepoint.com/detect-css3-property-browser-support/)，例如

```
 if (document.createElement("detect").style.textShadow === "") {
	document.getElementsByTagName("html")[0].className += " textshadow";
} 
```

享受技术带来的乐趣。如果你在你的网站上创造了一个好的效果，请留下你的评论和网址。

## 分享这篇文章