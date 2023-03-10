# 现在每个人都应该知道的关于使用 SVG 的事情

> 原文：<https://www.sitepoint.com/what-everybody-ought-to-know-about-using-svg-right-now/>

通常，真正好的东西最大的问题就是没有足够多的人知道如何利用它。当然，这是一个 Web 开发人员非常熟悉的鸡和蛋的问题。在一项技术被使用之前，是由制造商来支持它，还是由开发人员来实现使用一项技术的东西，以便让制造商来支持它？

SVG 就是这种情况，它是一种非常强大的图形技术，尽管不完全跨浏览器兼容。我自己不是一个浏览器制造商，我现在的观点是，如果有一个好东西，我也可以开始使用它。也许如果每个人都以这种方式倡导好的技术，那么很少有人会关心这个行业应该先有鸡还是先有蛋。

不管怎样，当我开始对 SVG 及其潜力感兴趣的时候，我希望有人能给我一个我需要知道的事情的整体概述。谢天谢地，几年后，那个人出现了，他就是德米特里·巴拉诺夫斯基。他告诉我(以及 Web Directions South 的其他观众)的最重要的事情是*我可以[立即在制作项目中使用 SVG](http://south08.webdirections.org/?page_id=7#post-39)。*

## What is SVG？

对于那些还不知道什么是 SVG 的人，请允许我简单地回顾一下，解释一下[为什么 SVG 如此之好](https://www.sitepoint.com/the-future-is-svg-so-very-good/)。一句话，[据土坯](http://www.adobe.com/svg/)，<ins datetime="2010-01-25T06:54:39+00:00">谁是技术创始人</ins>的早期拥护者:

> 可缩放矢量图形(SVG)是一种基于文本的图形语言，它用矢量形状、文本和嵌入式光栅图形来描述图像。

好吧，但那是什么意思？

*   *可扩展*意味着该技术是[天生独立于分辨率的](http://en.wikipedia.org/wiki/File:Bitmap_VS_SVG.svg)。
*   *Vector* means that image data is described using relative mathematical coordinates. To borrow [Leigh Dodds’ words from this early XML.com article](http://www.xml.com/lpt/a/65):

    > `<path>`元素使用类似于笔式绘图仪的指令定义对象的形状，例如，移动到此处、绘制、移动到此处等。这里有一个取自规范的[示例](https://www.w3.org/TR/SVG/paths.html#PathData):
    > 
    > ```
    > <path d="M 100 100 L 140 100 L 120 140 z"/>
    > ```

    填充了字母和数字的属性是 SVG 画布上的坐标，类似于浏览器窗口的视口中的坐标。在`<path>`元素的`d`属性中的`M`命令是“移动到”命令，`L`是“行到”，最后的`z`是“关闭”这里有一个简短而甜蜜的 SVG 路径符号视觉指南。

*   最后， *Graphics* 意味着它用于创建图像，但也意味着它本身支持使用其`[<foreignObject>](https://www.w3.org/TR/SVG/extend.html#ForeignObjectElement) element`导入和显示位图图形和视频。你可以把这个特性想象成 HTML 中更熟悉的`<object>`或`<embed>`元素。

## 嘿，美国 SVG？

好了，现在我们知道了什么是 SVG，为什么所有这些缩放、向量 y 的东西让 SVG 如此酷？原因有很多，但我认为最有说服力的三个原因是:

*   **SVG 的可搜索性非常高**因为 SVG 图像实际上只是解释过的 XML 文件(就像网页是解释过的 HTML 文件一样)，而 XML 只是简单的纯文本。如果你还记得“Flash 网站”的全盛时期，你无疑会回忆起 Flash 的专有二进制文件格式对一直偏好文本的[搜索引擎来说是一场噩梦。随着时间的推移，](https://www.sitepoint.com/for-the-best-seo-google-says-think-text/) [Flash 为搜索引擎蜘蛛和人类用户开发了各种各样的特殊功能来增加可访问性](https://www.sitepoint.com/article/accessible-flash-parts-1-2)，但是如果 SVG 一直占据主导地位，这个特殊的问题就没有实际意义了。
*   从很早开始，SVG 就以模块化的方式设计，模仿其“母”技术 XML 的设计理念。实际上，这意味着 **SVG 提供了相对[强大的移动支持](https://vimeo.com/1987967)** ，因为它有几种不同的风格，可以相互操作。它通过定义配置文件来支持具有合理质量的低端设备，同时仍然提供高保真的内容。你知道，就像网络一直想做的那样。
*   如果你关心这些事情(我也是)，SVG 早在 Flash 之前就已经是一个开放标准了。即使你不关心开放标准，仍然有[许多其他原因 SVG 对 Web 很重要](https://www.sitepoint.com/9-reasons-why-svgs-are-important-for-the-web/)。

正如任何优秀的开发人员都知道的那样，当您在坚实的基础之上构建更高级别的功能时，随着新功能的引入，迭代地引入增强功能会变得更加容易。如果您允许，SVG 可以成为“闪光”效果基础。

## 浏览器对 SVG 的支持

SVG 支持的故事很大程度上反映了浏览器大战的令人沮丧的故事。然而，令人欣慰的是，浏览器对 SVG 的支持已经增加了很长时间，在过去的几年里，它已经有了很大的发展。简而言之，以下是支持情况:

*   Opera 几乎完全支持 SVG,从 Opera 8 开始就有了一些支持。
*   自 2005 年以来，Gecko 获得了越来越多的支持。今天， [Firefox 支持绝大多数 SVG 特性](https://developer.mozilla.org/en/SVG_in_Firefox)。
*   自 2006 年以来，WebKit 获得了越来越多的支持。今天，任何基于 WebKit 的浏览器，如 [Safari，都有完全可用的实现](http://webkit.org/projects/svg/status.xml)。
*   Internet Explorer(仍然)没有本地支持。也就是说，[微软最近刚刚加入了 <acronym title="World Wide Web Consortium">W3C</acronym> 的 <acronym title="Scalable Vector Graphics">SVG</acronym> 工作组](https://www.sitepoint.com/internet-explorer-svg/)，所以可能还有一些希望。

如果你不必(也不想)关心 Internet Explorer，那么你就没什么好担心的了。只需[拿起 SVG 入门](https://www.sitepoint.com/scalable-vector-graphics/)并开始在你最喜欢的图形应用程序中使用“另存为 SVG”选项。

然而，大多数时候你需要做一些事情来帮助你使用 Internet Explorer。那么今天你能对 IE 做些什么呢？这里有一个解决 IE 令人沮丧的缺乏支持的选项的鸟瞰图:

*   **依靠 HTTP 内容协商**向支持浏览器提供 SVG，向 Internet Explorer 提供光栅图像。虽然这很可靠，但感觉又像是 1995 年，因为您仍然需要两个独立的内容，除非您从另一个中动态构建一个。这是[今天维基百科做的事情](http://en.wikipedia.org/wiki/Scalable_Vector_Graphics#Support_for_SVG_in_web_browsers)。
*   **让用户安装插件**。Adobe 一直有一个叫做[的 Adobe SVG 浏览器](http://www.adobe.com/svg/viewer/install/)。它相当不错，但是在 2007 年被判生命终结，而且，嗯，它是一个插件。(我不确定 Flash 是如何摆脱插件的，但这不是重点。)
*   放松并在客户端使用 Flash，但是陶醉于 SVG 对源文件的纯净。有一些制作免费软件的尝试，[使用 SVG 路径数据，并将其传递给 Flash 来渲染](http://flash-creations.com/notes/sample_svgtoflash.php)，但这些仍然难以使用。
*   最后，也可能是最令人信服的是，**使用跨浏览器 API** 。碰巧的是，其中有相当多的是免费的、开源的、非常巧妙的:
    *   Dojo 工具包中的 SVG 看起来很可靠，对于已经使用 Dojo 的人来说，这可能是最好的选择。
    *   [svgweb](http://code.google.com/p/svgweb/) 是谷歌的实验图书馆。(由于显而易见的原因，它现在正享受着最大的炒作。)
    *   由 Dmitry Baranovskiy 编写的 raphaljs 是我最喜欢的跨浏览器 SVG 工具包，也是我们之前在 SitePoint 上写过的[。因为它是一个独立的库，所以您实际上可以将它与您喜欢的 JavaScript 框架混合使用。换句话说，您可以选择 jQuery、YUI、Prototype 或另一个 JavaScript 框架，并在其上放置 raphaljs 调用来完成所有与 SVG 相关的工作。](https://www.sitepoint.com/easy-vector-graphics-with-the-raphael-javascript-library/)

## 编写 SVG 的 JS 框架是如何工作的？

为了支持 Internet Explorer 并为 SVG 能给你的网站带来的类似 Flash 的效果增加交互性，你几乎肯定会想要使用一个框架。但是它们是如何工作的呢？这实际上非常简单，这都要归功于微软开发的一种早期的矢量图形语言，这种语言今天仍在使用，开放 SVG 标准从它那里获得了许多启示:VML，或矢量标记语言。

因此，编写 SVG 的 JavaScript 框架都遵循相同的范式。他们每个人基本上都是这样做的:

1.  提供一个跨浏览器的 JavaScript API 来编写 SVG 命令(就像 jQuery 这样的库对 DOM 操作一样)。
2.  根据您的命令为 DOM 节点编写合适的矢量图形语言(SVG 用于兼容浏览器，VML 用于 Internet Explorer)。
3.  给你煮咖啡。(好吧，嗯，也不尽然。)

## 大肆宣传

正如您所看到的，现在有大量的信息和工具可以帮助您使用 SVG。希望我们已经越过了临界点。此外，能够并愿意帮助您实现 SVG 解决方案的知识渊博的人的数量每天都在增长，许多人是 [SitePoint 自己的论坛](https://www.sitepoint.com/forums/)的成员。所以，有了这些资源，走出去，开始享受各种可能性吧！

## 分享这篇文章