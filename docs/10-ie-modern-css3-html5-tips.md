# 10 IE CSS3 和 HTML5 现代浏览器模仿

> 原文：<https://www.sitepoint.com/10-ie-modern-css3-html5-tips/>

让我们面对现实吧，有些人坚持使用 Internet Explorer 作为他们的网络浏览器。这可能是工作锁定了他们的机器，他们不能安装一个像样的浏览器。在 HTML5 和 CSS3 等新的强大技术出现的时候，IE 不能正确处理它们并不奇怪。幸运的是，有一些技巧可以让你的生活更轻松。看看这个帖子。

## 1.IE 兼容字体嵌入

几年前，网络一直由几种字体主导(Arial、Verdana、Courier 和最著名的 Times New Roman)。几乎所有的电脑都安装了这些字体，因为它们被标为“网络安全”。(没有安装在 GNU/Linux 上，因为它们不是免费的)。

但是不久前，字体嵌入已经成为一种非常有趣和受欢迎的技术，因为它允许你在你的设计中嵌入特定的字体，在其他技术中，@font-face 方法可能是最干净的，信不信由你，IE 从版本 4 开始就支持字体嵌入。

```
@font-face {  
	  font-family: yourFontName ;  
	  src: url( /location/of/font/FontFileName.ttf ) format("truetype");  
	}  

	/* Then use it like you would any other font */  
	.yourFontName { font-family: yourFontName , verdana, helvetica, sans-serif;  
	}  
}
```

[来源](http://randsco.com/index.php/2009/07/04/p680)

## 2.RGBa 支持

RGBa 中的“a”代表阿尔法。这个新特性允许开发者指定一种颜色的不透明度值，这在网站编码时非常有用。

```
.color-block {
   width: 50%;
   background-color: rgba(0, 0, 255, 0.2); /* Modern Browsers */
}
```

和往常一样，Internet Explorer 显示出了它的创新不足，以及它相对于其他完全没有 RGBa 支持的浏览器的劣势。幸运是，过滤器可以实现与 RGBa 非常相似的效果:

[来源](http://css-tricks.com/2151-rgba-browser-support/)

## 3.旋转 HTML 元素

使用转换属性，CSS3 可以旋转元素。

```
transform: rotate(240deg);
-webkit-tranform: rotate(240deg);
-moz-transform: rotate(240deg);
```

Internet Explorer 将简单地忽略上述所有 3 个声明。但是嘿，IE 用户得到了过滤器，不是吗？当然，这个属性不是 W3C 有效的，但是因为它是 Internet Explorer，你不应该要求太多。以下代码将在 IE 的所有版本上模仿 transform:

```
 SOME TEXT

	    SOME TEXT

	    SOME TEXT

	    SOME TEXT 
```

[来源](http://msdn.microsoft.com/en-us/library/ms533014%28VS.85%29.aspx)

## 4.不透明

不透明是 IE 无法渲染的另一个 CSS3。这很遗憾，因为从网页设计的角度来看，允许在特定元素的不透明度上进行交互是非常有趣的。

同样，蹩脚的过滤器属性可以帮助我们在 IE 上获得满意的结果。下面的例子显示了如何使用过滤器使一个元素透明。

```
.element{
    opacity:.7; /* Standard CSS */
    filter:alpha(opacity=70); /* IE patch */
}
```

[来源](http://www.catswhocode.com/blog/10-ways-to-make-internet-explorer-act-like-a-modern-browser)

## 5.CSS3 伪选择器仿真

CSS3 引入了许多非常有用的选择器。其中:nth-child()伪类的目标是在文档树中自身之前有一定数量的兄弟元素，如下所示:

```
p:nth-child(3) {
	color:#069;
}
```

正如你所猜测的，这些东西对 IE 来说太高级了。为了克服这个问题，Keith Clark 创建了一个非常有用的脚本，名为 ie-css3.js。(使用它很容易:下载 Robert Nyman 的域名助手，Keith 的 ie-css3.js，并将它们链接到 HTML 文档头中。)

[来源](http://www.keithclark.co.uk/labs/ie-css3/)

## 6.多栏布局

CSS3 允许你自动显示一些分栏的内容。这是一件很棒的事情，因为它给了设计师更多的可能性来创造令人敬畏的布局。

下面的 CSS 可以在 Firefox 和 Safari 上运行。它会自动向 div 元素添加列。

```
.column {
	-moz-column-width: 13em,
	-webkit-column-width: 13em,
	-moz-column-gap: 1em,
	-webkit-column-gap: 1em
}
```

不幸的是，在 Internet Explorer 上没有办法做类似的事情。但是 jQuery 和它的 columnize 插件是来帮忙的！以下示例显示了使用 jQuery 和 columnize 创建列是多么容易:

```
$('#mydiv').columnize();
$('#mydiv').columnize({ width:200 });
$('#mydiv').columnize({ columns:2 });
```

[来源](http://welcome.totheinter.net/2008/07/22/multi-column-layout-with-css-and-jquery/)

## 7.圆角！

他们如此受欢迎是因为他们的“Web 2.0？外观和感觉。CSS3 规范理解了这一点，并创建了一个名为 border-radius 的属性，该属性旨在轻松创建圆角，而无需使用单个图像。对于那些不知道的人，以下是如何使用 border-radius:

```
.round {
	border-radius: 5px;
	-moz-border-radius: 5px;
	-webkit-border-radius: 5px;
}
```

幸运的是，有几种方法可以在不使用图像的情况下创建符合 IE 的圆角。我最喜欢的是 DD roundies，它是一小段 JavaScript，可以对任何类型的 HTML 元素进行舍入。下面的例子将使用 roundify 类在任何 HTML 元素上创建圆角。

```
<script src="js/scripts.js" type="text/javascript"></script>
<script type="text/javascript"> DD_roundies_addRule('.roundify', '10px') </script>
```

## 8.CSS 框-IE 上的阴影

这是 CSS3 最酷的新特性之一，因为它允许你在任何类型的 html 元素上轻松地创建美丽的阴影，而不需要使用任何图像。但是，不要问 Internet Explorer 是否可以处理 box-shadow。它不能。
同样，为了模仿 box-shadow CSS 属性，我们必须使用 filter 专有属性，如下例所示:

```
.shadowed {
-moz-box-shadow: 2px 2px 3px #969696;
-webkit-box-shadow: 2px 2px 3px #969696;
}

.shadowed {
	background-color: #fff;
	zoom: 1;
	filter: progid:DXImageTransform.Microsoft.Shadow(color='#969696', Direction=135, Strength=3);
}
```

[来源](http://ole-laursen.blogspot.com/2009/08/using-css3-box-shadow-with-ie.html)

## 9.在 IE 上使用文本阴影 CSS 属性

今天，大多数现代浏览器可以很好地呈现这一属性，但 IE 又一次忽略了这一点。令人高兴的是，专有的 IE 专用过滤器属性可以很好地模仿文本阴影。

```
p.shadowed {
	text-shadow: #0000ff 0px 0px 30px; /* Modern Browsers */
	filter: glow(color=#0000ff,strength=3); /* IE */
}
```

[来源](http://www.howtocreate.co.uk/textshadow.html)

## 10.在 IE 上启用 HTML5

大多数现代浏览器已经可以处理，至少是部分处理新的 HTML5 推荐标准。但由于 Internet Explorer 并不以其创新意识而闻名，它会简单地忽略标记。

js 是一个非常有趣的项目，旨在使 Internet Explorer HTML5 兼容。您唯一需要做的就是将 html5.js 脚本嵌入到您的 html 文档头中。您可以热链接脚本，如下例所示:

[来源](http://remysharp.com/2009/01/07/html5-enabling-script/)

## 分享这篇文章