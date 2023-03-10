# CSS3 中的星球大战 3D 滚动文本

> 原文：<https://www.sitepoint.com/css3-starwars-scrolling-text/>

我总是试图在 SitePoint 上提供实用的技巧。真的。这不是一个，但我无法抗拒它。事不宜迟…

[![Star Wars 3D scrolling title text](img/f03af408957ed2ee04239775da574266.png) **在 CSS3** 
(Chrome、Safari 或 Firefox)](https://blogs.sitepointstatic.com/examples/tech/css3-starwars/index.html) 中查看星球大战 3D 滚动文本

多酷啊。没有 JavaScript，没有图形——只有纯 HTML5 文本和 CSS3。它可以在 Chrome、Safari 和 Firefox 上运行。Opera 不会在当前版本中显示 3D 转换，但它会工作。互联网浏览器。嗯。抱歉，但你需要等待 IE10，因为以前的版本不支持转换和动画。

这种效果非常容易实现。HTML 需要一个外部元素(#titles)和一个将被滚动的部分(#titlecontent):

```
 <div id="titles"><div id="titlecontent">

	<p>content</p>

</div></div> 
```

外部的`DIV`位于浏览器窗口的底部中心，然后使用 3D 透视图使用变换进行旋转(注意这里省略了供应商前缀，但它们在实际代码中):

```
 #titles
{
	position: absolute;
	width: 18em;
	height: 50em;
	bottom: 0;
	left: 50%;
	margin-left: -9em;
	font-size: 350%;
	font-weight: bold;
	text-align: justify;
	overflow: hidden;
	transform-origin: 50% 100%;
	transform: perspective(300px) rotateX(25deg);
} 
```

一个伪元素被应用来覆盖外部`DIV`顶部的淡出渐变:

```
 #titles:after
{
	position: absolute;
	content: ' ';
	left: 0;
	right: 0;
	top: 0;
	bottom: 60%;
	background-image: linear-gradient(top, rgba(0,0,0,1) 0%, transparent 100%);
	pointer-events: none;
} 
```

然后我们需要做的就是使用 CSS 动画滚动内容:

```
 #titlecontent
{
	position: absolute;
	top: 100%;
	animation: scroll 100s linear 4s infinite;
}

@keyframes scroll {
	0% { top: 100%; }
	100% { top: -170%; }
} 
```

(注意你也可以使用页边距-顶部而不是绝对-定位内容，但是它看起来不平滑。)

[**再次查看演示……**](https://blogs.sitepointstatic.com/examples/tech/css3-starwars/index.html)

这就是全部了。如果你想深入研究:查看来源，卢克。

请随心所欲地使用代码，但我希望这篇文章不会导致重新引入毫无意义的受星球大战启发的飞溅页面！

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Learn CSS3](https://learnable.com/courses/learn-css3-203) 。

## 分享这篇文章