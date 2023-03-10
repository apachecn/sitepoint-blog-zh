# Internet Explorer 9 中的真实世界 HTML5 和 CSS3

> 原文：<https://www.sitepoint.com/real-world-html5-and-css3-in-internet-explorer-9/>

在我们周围的世界里，人们想要解决他们的问题。他们希望使用有用的东西，希望快速高效地完成工作。但更重要的是，他们希望能够专注于真正重要的事情。

作为在现实世界中构建应用程序和网站的 web 开发人员，我们希望我们使用的代码是相同的——也就是说，我们希望我们的代码高效、易于实现、易于维护并且没有错误。

随着 Internet Explorer 9 Beta 的发布及其对 HTML5 和 CSS3 中许多最新标准的支持，IE9 开发团队朝着帮助开发人员实现上述目标迈出了重要的一步。

在本文中，我们将向您介绍 IE9 Beta 中包含的许多新的 HTML5 和 CSS3 特性，这样您就可以看到为 IE9 Beta 创建网站和应用程序将如何使事情比以前更具可伸缩性、更高效和更易维护。

## 对网络标准的承诺

在仔细审查了由万维网联盟(W3C)起草的官方规范的进展后，已经将 HTML5 和 CSS3 特性添加到 IE9 Beta 中。

考虑到这一点，我们可以看到微软坚持最新标准的承诺，同时认识到标准通常仍处于早期阶段，随着规范的发展，可能会出现多种变化。

正如在 [IE9 测试版开发者指南](https://msdn.microsoft.com/en-us/ie/ff468705.aspx)中所述，IE9 已经“在标准支持和互操作性方面进行了重大投资”，包括对许多 CSS3 功能的更好支持和增加的 HTML5 支持。这是继 IE8 的改进之后，IE8 提供了“CSS 2.1 的高度互操作的实现”

介绍到此为止。让我们深入研究 IE9 Beta 中新的 HTML5 和 CSS3 特性。

## 帆布

IE9 Beta 现在支持 HTML5 Canvas 元素，它允许脚本访问依赖于分辨率的位图 Canvas，用于动态渲染图形、游戏图形或其他可视图像。

HTML5 中的画布支持包括对画布 2D 上下文、绘图 API 和硬件加速的支持，以实现最佳性能。

IE9 beta 中的画布绘制功能包括对线条、矩形、填充、弧线、阴影、贝塞尔曲线、图像、视频等的支持。

下面的屏幕截图显示了来自 IE9 试驾网站的[“Canvas Pinball”](https://ie.microsoft.com/testdrive/Graphics/CanvasPinball/?o=1)——这只是 IE9 中的 Canvas 2D 绘图 API 为开发人员提供的无限可能性的一个例子。

![Canvas Pinball](img/d189499764a29abfe8ee37af8015fb7a.png)

这种动态动画和交互以前只能通过第三方解决方案或过于复杂的脚本来实现。IE9 Beta 对 canvas 和 2D 绘图 API 的支持允许开发人员使用本地浏览器元素和专门为此设计的 API 来创建真实世界的交互。

## 视频和音频元素

直到最近，在网页中包含视频或音频的唯一可靠的跨浏览器方法是使用标记，这需要像 Adobe 的 Flash Player 这样的第三方软件。这不是一个理想的解决方案。

HTML5 现在增加了对`<video>`和`<audio>`标签的支持，允许开发者使用简单、有意义且易于维护的标记来包含多媒体内容。下面是一个 HTML5 代码示例，它使用内置视频控件将视频嵌入到网页中:

```
<video width="400" height="300" poster="example.png" controls> 

  <source src="example.mp4" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"' /><!-- for WebKit -->
  <source src="example.ogv" type='video/ogg; codecs="theora, vorbis"' /><!-- for Firefox and Opera --> 

</video>
```

上面的代码将在页面上放置一个视频，用户可以通过可视控件来播放、暂停或“拖动”视频。

下面是 IE9 试驾网站上 [IMDb 视频全景](https://ie.microsoft.com/testdrive/mobile/Graphics/VideoPanorama/Default.html)页面的截图，显示在 IE9 Beta 中。

![IMDb Video Panorama](img/9b2ec7a8235f9ef7a72672d554e8cee1.png)

使用视频标签中的“控制”属性，根据视频的大小，一个全功能的控制栏会出现在视频的底部。这不需要 JavaScript 或任何额外的图形；上面看到的控件是 IE9 Beta 中显示的默认控件。

有了这些用于视频和音频的新 HTML5 特性，开发人员可以避免浪费时间创建自定义视频工具或使用第三方解决方案。相反，内容创建者和开发人员可以专注于更重要的问题:创建视频和音频内容，编写可以与视频和音频控件交互的脚本。

当然，HTML5 视频并非在所有浏览器中都可用。但是代码允许为不支持的浏览器包含回退内容。因此，一个几乎防弹的 HTML5 视频元素将编码如下:

```
<video width="400" height="300" poster="example.png" controls>

  <source src="example.mp4" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"' /><!-- for IE9 and WebKit -->
  <source src="example.ogv" type='video/ogg; codecs="theora, vorbis"' /><!-- for Firefox and Opera -->

  <object width="400" height="300" type="application/x-shockwave-flash" data="example.swf">

    <param name="movie" value="example.swf" />

    <param name="flashvars" value="controlbar=over&amp;image=example.png&amp;file=example.mp4" />
    <img src="example.png" width="400" height="300" alt="example" title="example" />

  </object>

</video>
```

开头的`<video>`标签中可选的“poster”属性允许图像显示为视频的预览。如果浏览器不支持`<source>`标签中包含的任何编解码器，则视频标签之间包含的非源元素的所有内容都将作为后备内容。

面向开发者的 IE9 测试版指南有更多关于使用 HTML5 视频和音频元素的信息；你可以在[【人人视频】](https://camendesign.com/code/video_for_everybody)网页上阅读防弹 HTML5 视频的详细文章。

IE9 Beta 中的 HTML5 音频与视频的工作方式类似，只是没有使用宽度、高度和海报属性。下面是 IE9 试驾网站上[“html 5 音频播放器+ XML 播放列表”](https://ie.microsoft.com/TEStdrive/HTML5/HTML5AudioXMLPlaylist/Default.html)的截图。

![HTML5 Audio Player](img/668a7ba9e093ced407aed43ae03a7819.png)

上面的嵌入式音频示例包括用 SVG 创建的自定义控件，SVG 是 IE9 Beta 中的另一项新技术。

## 新的语义元素

HTML5 规范为开发者的工具箱增加了许多新的语义元素。这允许标记有更多的含义，并有助于避免文档中充斥着无意义的元素的问题。

IE9 现在支持以下新元素:

*   部分
*   航行
*   文章
*   在旁边
*   h 组
*   页眉
*   页脚
*   数字
*   图片标题
*   标记

使用这些新元素代替通用的`<div>`和`<span>`标签(仍然可以在 HTML5 中使用)增加了许多好处。首先，从现实世界开发的角度来看，它使事情更容易维护。例如，用`<figure>`标签包装一些东西，会让你的代码更容易识别。

另一个好处是，许多这些新元素有助于创建一个合适的 HTML5 文档大纲。在 HTML4 中，文档大纲依赖于使用`<h1>`到`<h6>`标签，而在 HTML5 中，文档大纲由`<article>`元素定义。虽然 HTML4 和 XHTML 风格的文档结构并不是无效的 HTML5，但是这种更新的方法允许内容易于移植(使用`<section>`)和易于整合(使用`<article>`)。

最新版本的 Internet Explorer 平台预览版(Build 7)现在支持新语义元素的样式，并将它们识别为真正的 HTML 元素，而不是将其视为未知元素，从而避免了使用基于 JavaScript 的解决方案。这些特性虽然还没有在 IE9 Beta 版中推出，但将会包含在 IE9 的未来版本中。

能够将内容和标记的整个部分移动到新的位置对于代码的可维护性和内容的共享是一个巨大的好处。在现实世界中，我们希望我们的内容易于移动、易于维护、易于识别和易于整合。IE9 对这些新特性的支持允许开发者解决这些现实世界的问题。

## CSS3 颜色和 Alpha 通道透明度

IE9 Beta 引入了对 CSS3 颜色模块的支持，包括对 RGBA、HSL、HSLA 和不透明度属性的支持。这些新的 CSS3 特性允许开发人员直接在标记和 CSS 中创建半透明元素，而不需要额外的图像。

不透明度属性让开发人员可以轻松地为几乎任何元素添加不同级别的透明度。不幸的是，该属性并不能解决现实世界中的所有透明度问题，因为在任何单个元素上设置不透明度值也会在该元素的子元素上设置相同级别的不透明度，没有简单的方法来反转子元素上的设置。

这个问题用 RGBA 颜料解决了。这个新的颜色模块允许任何接受颜色值的 CSS 属性也包括透明度值，使用 RGBA，而不影响子元素。下面是一个代码示例:

```
#element {
  background: rgba(98, 135, 167, .5);
}
```

上面的背景属性被赋予了 RGBA 定义的颜色，而不是习惯的十六进制代码。RGB 值对 CSS 来说并不陌生——它们在 CSS 2.1 中是允许的，但是 CSS3 现在允许添加第四个通道:“alpha”该通道的工作方式与 opacity 属性相同，接受 0 到 1 之间的十进制值，“0”表示完全透明，“1”表示完全不透明。

与 RGBA 类似，HSL 通过 alpha 通道支持透明性。下面是一些使用 HSLA 定义元素背景颜色和背景不透明度的 CSS 示例:

```
#element {
  background: hsla(207, 38%, 47%, .8);
}
```

## 多重背景

IE9 Beta 现在支持对背景图像属性使用多个值，允许一个元素拥有多个背景图像，每个背景图像都有自己的背景位置设置。这个新的 CSS3 特性有助于解决现实世界中通常需要额外标记的问题。

下面的代码演示了如何在 IE9 中实现多个背景:

```
#element {
  background: url(images/bg-example-1.png) top center no-repeat,
              url(images/bg-example-2.png) bottom center no-repeat;
}
```

对开发人员的 CSS 工具箱的这一补充提供了很大的好处，使我们的标记和样式更容易维护，也更灵活。

## 圆角(边框半径)

另一个困扰开发人员多年的常见布局挑战是在元素上轻松放置圆角的能力。在过去，这只能通过使用图像或复杂的脚本解决方案来实现。在 IE9 Beta 中，只需一行代码就可以在几乎任何元素上设置圆角:

```
#element {
  border-radius: 10px;
}
```

这个新特性消除了对复杂脚本解决方案的需求，或者在大多数情况下，消除了使用图像结合 CSS 边框来创建圆角的需求。通过新添加的 border-radius 属性，IE9 Beta 允许开发人员使用很少的代码快速创建和更改圆角的半径级别。IE9 还支持边界半径的手写符号，因此每个角可以有不同的半径设置，如下面的代码所示:

```
#element {
  border-top-left-radius: 20px;
  border-top-right-radius: 10px;
  border-bottom-right-radius: 15px;
  border-bottom-left-radius: 7px;
}
```

## 媒体查询

在现实世界中，人们不会在一台设备上以一种屏幕分辨率访问我们的网络应用和网站。一段时间以来，创建能够在不同屏幕和设备上正确显示的页面一直是 web 开发中的一个挑战。过去，JavaScript 解决方案和服务器端重定向为这个问题提供了平庸的解决方案。

不幸的是，这些解决方案要么会对性能产生负面影响，要么需要使用并不总是可靠的检测方法。

IE9 Beta 版现在支持 CSS3 媒体查询，允许开发人员根据许多因素来设计页面样式，包括宽度、高度、方向和分辨率。下面是一个 CSS 示例，它根据查看设备的大小来改变元素的宽度:

```
@media (min-width:950px){
  #element {
    width: 600px;
  }
}

@media (min-width:450px) and (max-width:950px){
  #element {
    width: 400px;
  }
}

@media (max-width:450px){
  #element {
    width: 200px;
  }
}
```

使用 CSS3 媒体查询，无需脚本或服务器端解决方案，即可轻松定位不同的设备及其各自的功能。关于 IE9 测试版中媒体查询的更多信息可以在 [IE9 测试版开发者指南](https://msdn.microsoft.com/en-us/ie/ff468705.aspx)中找到。

## CSS3 选择器

在 CSS2.1 中选择和定位元素的能力相当广泛，但是在某些领域受到限制。CSS3 增加了可供开发人员使用的 CSS 选择器的数量，IE9 Beta 提供了对其中许多选择器的支持。

IE9 Beta 中的 CSS3 选择器支持包括对结构伪类、目标伪类、UI 元素状态伪类和否定伪类的支持。

CSS3 中提供的、IE9 Beta 支持的一些实用选择器的例子如下所示:

```
li:nth-child(4) {
  /* targets the 4th list item in every unordered list */
}

li:last-child {
  /* targets the last list item in every unordered list */
}

li:only-child {
  /* targets a list item if it is the only child */
}

input:disabled {
  /* targets a form input that is disabled */
}

input:checked {
  /* targets a form element that is selected */
}
```

关于 IE9 Beta 版支持的 CSS3 选择器的完整列表，请参见 [IE9 Beta 版开发者指南](https://ie.microsoft.com/testdrive/Graphics/CanvasPinball/?o=1)的 CSS3 选择器部分。

## 渐进增强

自然，在现实世界中，CSS3 的许多新特性不会被所有用户看到。那么这些特性在不支持它们的浏览器中会发生什么呢？在那些浏览器中，他们不理解的 CSS 代码行将被忽略。

这允许开发人员为旧浏览器创建一个更简单、不太吸引人(但可以接受)的体验，然后添加 CSS3 增强功能，为可以看到新功能的用户美化体验。还可以为选择器支持和其他旧浏览器不支持的 CSS3 特性提供 JavaScript 解决方案。

## 为现实世界的开发铺平道路

微软最近宣布 IE9 是他们有史以来采用速度最快的测试版，在前六周就有超过 1000 万次下载。随着现代浏览器的使用份额统计数据的增加，开发人员将能够更轻松地实现这些以及其他前瞻性的 HTML5 和 CSS3 解决方案来解决他们的现实问题。最终，他们将能够创建可维护的网站和应用程序，这些网站和应用程序是可用的，并针对性能进行了优化。

IE9 Beta 的发布极大地促进了现实世界中 web 应用程序设计和开发的未来。要开始使用这些有效的解决方案开发项目，请立即下载 IE9 测试版。

你认为你已经了解 IE9 是如何实现 HTML5 和 CCS3 的了吗？通过参加我们的简短[测验](https://www.sitepoint.com/quiz/microsoft/real-world-html5-and-css3-in-internet-explorer-9/)来测试自己。

**note:**SitePoint Content Partner

本教程是在微软的支持下完成的。我们与微软合作，由 SitePoint 独立编写，努力共同开发对您(我们的读者)最有用、最相关的内容。

![](img/8253e52907a368a35cf5c9934e90f669.png)

## 分享这篇文章