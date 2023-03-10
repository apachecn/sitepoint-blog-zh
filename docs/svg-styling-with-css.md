# 如何使用 CSS 设计可缩放矢量图形的样式

> 原文：<https://www.sitepoint.com/svg-styling-with-css/>

如果你一直在关注这个系列，你会知道什么是 SVG、[为什么你应该考虑它们](https://www.sitepoint.com/7-reasons-to-consider-svgs-instead-of-canvas/)、[如何创建基本绘图](https://www.sitepoint.com/svg-drawing-basics/)和[在你的网页中嵌入 SVG 的六种方法](https://www.sitepoint.com/add-svg-to-web-page/)。

虽然创建 SVG XML 可能很容易，但是代码可能有点冗长，例如

```
 <circle cx="100" cy="300" r="80"
stroke="#909" stroke-width="10" fill="#f6f" /> 
```

如果我们可以将 CSS 样式规则应用于 SVG 元素，那不是很好吗？嗯，那正是你能做的！您可以使用具有相同属性名称的内联样式来代替属性:

```
 <circle cx="100" cy="100" r="80"
style="stroke: #909; stroke-width: 10; fill: #f6f;" /> 
```

内联样式仍然很笨重，所以您可以嵌入样式表代码。标准的 CSS 选择器用于将样式应用于元素类型或那些具有特定 id 或类名的元素类型，例如

```
 <?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "https://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg  viewBox="0 0 200 200" preserveAspectRatio="xMidYMid meet">
	<defs>
		<style type="text/css"><![CDATA[
			circle {
				stroke: #909;
				stroke-width: 10;
				fill: #f6f;
			}
		]]></style>
	</defs>
	<circle cx="100" cy="100" r="80" />
</svg> 
```

注意， … 块是必需的，因为 CSS 可以包含>字符，这会使 XML 解析器混淆。我建议你无论如何都要使用它们。

这很好，但是所有优秀的开发人员都知道，最好将样式表分开，这样它们更容易维护，并且可以在其他地方重用。我们可以通过在 xml 声明后立即添加一个新的 xml-stylesheet 标记来做到这一点:

```
 <?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet href="styles.css" type="text/css"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "https://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg... 
```

## 将 SitePoint 徽标创建为 CSS 样式的 SVG

SVG 是徽标的理想选择，因为它们可以缩放到任何大小并用于响应模板中。我们首先定义一个指向名为 sp-styles.css 的样式表的 sitepoint.svg 文件:

```
 <?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet href="sp-styles.css" type="text/css"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "https://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg  viewBox="95 40 550 180" preserveAspectRatio="xMidYMid meet">
	<title>SitePoint</title>
	<desc>The SitePoint Logo</desc>
	<g id="main"> 
```

我们将在蓝色斜面上使用折线元素，但现在还不用担心样式:

```
 <polyline points="100,100 150,100 170,150 120,150" />
		<polyline points="170,100 220,100 240,150 190,150" /> 
```

橙色斜面是用类似的折线定义的，只是我们给它们一个名为“orange”的类:

```
 <polyline class="orange" points="120,45 170,45 150,95 100,95" />
		<polyline class="orange" points="190,155 240,155 220,205 170,205" /> 
```

我们以徽标文本和结束 SVG 标记结束:

```
 <text id="sp" x="240" y="150">sitepoint</text>
	</g>
</svg> 
```

可以在浏览器中查看未样式化的 SVG:

![unstyled SVG](img/88f7fc291eba2af486e7c1957656a72c.png)

我们现在将一些简单的样式添加到 sp-styles.css 文件中。第一个将对所有折线应用蓝色填充:

```
 polyline
{
	stroke: 0;
	stroke-linejoin: butt;
	fill: #003565;
} 
```

类别名称为“橙色”的多段线将应用橙色填充:

```
 polyline.orange
{
	fill: #ff6400;
} 
```

最后，我们可以应用简单的文本样式。我们可以使用 webfonts 或任何标准的 CSS 字体/文本属性:

```
 text#sp
{
	font-family: verdana, sans-serif;
	font-size: 570%;
	fill: #003565;
} 
```

我们的 SVG 现在已经完成了:

![CSS-styled SVG](img/ad5eea6189abf059d3dae4966424059a.png)

## 在响应式设计中使用 SVG

可以开发一个简单的 HTML5 模板，在一个`object`标签中显示 SVG:

```
 <!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>SVG logo</title>
</head>
<style>
html, body
{
	width: 100%;
	height: 100%;
	overflow: hidden;
}

#sp
{
	display: block;
	width: 100%;
	height: 100%;
	margin: auto;
}
</style>
<body>

<object id="sp" type="image/svg+xml" data="sitepoint.svg">SitePoint</object>

</body>
</html> 
```

不管浏览器的高度或宽度如何变化，徽标总是以最大可能的尺寸出现在页面的中央。不管怎样，质量仍然很好。

[**查看 SVG 标志演示页面……**](https://blogs.sitepointstatic.com/examples/tech/svg-basics/svg-logo.html)

在压缩和 gzipping 之前，SVG 和 CSS 文件的总大小为 931 字节**。上面显示的 PNG 在 500 像素以上的宽度不会很好看，并且在**压缩
… *后文件大小为 3.4kB **(尽管它在 IE6/7/8 中也能工作！)***。

请继续关注 SitePoint，更多 SVG 文章即将发布…

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对这篇文章的评论已经关闭。对 CSS 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章