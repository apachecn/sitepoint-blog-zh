# 网络字体图标:图像的替代品

> 原文：<https://www.sitepoint.com/webfont-icons/>

老实说，在一个图形包中创建几十个小图标并不好玩。即使你已经收集了一个不错的集合，你最终还是要管理几十个小文件，或者创建图像精灵，并在 CSS 中分割它们。

幸运的是，HTML5 为我们提供了另一种选择:webfonts。字体集可以包含图形图标和字符集。Wingdings 是最广为人知的，但免费字体，如图标字体可能更有用，并提供示例 HTML 和 CSS 代码。

[**查看网页字体图标演示页面……**](https://blogs.sitepointstatic.com/examples/tech/icon-fonts/index.html)

一旦找到或创建了合适的字体，就需要将其转换成多种格式，以确保良好的跨浏览器兼容性。fontsquirrel.com 的 [@Font-Face Generator 为你做了艰苦的工作，并提供了一个包含所有字体和 CSS 代码的 ZIP 文件。然后字体被导入到 CSS 文件的顶部，例如](http://www.fontsquirrel.com/fontface/generator)

```
 @font-face {
	font-family: "IconicStroke";
	src: url("iconic_stroke.eot");
	src: url("iconic_stroke.woff") format("woff"), 
	url("iconic_stroke.ttf") format("truetype"), 
	url("iconic_stroke.otf") format("opentype"), 
	url("iconic_stroke.svg#iconic") format("svg");
} 
```

可以在你的 HTML 文件中添加图标字符，比如用“r”代表 Iconic 的 RSS 图标。然而，这可能会使使用屏幕阅读器的人感到困惑，所以 CSS 伪元素是一个更安全的选择，例如

您的 HTML:

```
 <a href="rss.xml" class="rss">RSS Feed</a> 
```

您的 CSS:

```
 .rss:before
{
	font-family: "IconicStroke";
	content: "r";
} 
```

## 网络字体图标的优势

网络字体是图形的理想替代品:

*   字体可以缩放到任何大小，使用任何颜色，并应用 CSS 效果
*   网络字体提供了良好的跨浏览器兼容性，甚至可以在 IE6 中工作
*   单个字体文件可能比多个图像更有效

## 网络字体图标的缺点

web 字体可能并不总是合适的:

*   字体字符是单色的(尽管 CSS3 效果会有所帮助)
*   生成字体不像 png 或 SVG 那么容易
*   如果您只需要几个图标，图像文件会更小。

总的来说，网络字体图标没有什么不喜欢的，它们可以减少开发时间。你在你的项目中使用过它们吗？

[**查看网页字体图标演示页面……**](https://blogs.sitepointstatic.com/examples/tech/icon-fonts/index.html)

## 分享这篇文章