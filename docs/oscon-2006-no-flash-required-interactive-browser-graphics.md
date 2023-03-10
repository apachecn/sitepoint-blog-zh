# OSCON 2006:不需要闪光灯:交互式浏览器图形

> 原文：<https://www.sitepoint.com/oscon-2006-no-flash-required-interactive-browser-graphics/>

本周，凯文·杨克从俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 发回报道。

Gavin Doughtie 曾为索尼从事游戏开发，为 idealab 从事网页开发，为 Picasa 从事软件开发，但现在他在梦工厂动画公司工作(是的，就像在《怪物史莱克》中一样),业余时间涉足令人兴奋的浏览器内交互图形世界。他的好友乔恩·斯图尔特(抱歉乔恩，我来晚了，找不到你的简历)来为 IE 浏览器提供了许多 IE 不支持的解决方案。

这个演讲实际上是一个大杂烩，包含了大部分在这里或那里都有效的解决方案，一些即将出现的实验性的东西，以及一些今天实际上可以使用的突出的东西。幻灯片可以在网上找到。

对于矢量图形，显而易见的选择是 W3C 标准:[可伸缩矢量图形](https://www.w3.org/Graphics/SVG/) (SVG)。尽管 SVG 很灵活而且功能强大，但它还不能在 Internet Explorer 中运行，但该浏览器有微软的对等物: [VML](http://msdn.microsoft.com/workshop/author/vml/default.asp) 。 [Safari 将很快支持 SVG](http://webkit.opendarwin.org/blog/?p=35) ，但不是现在。

对于光栅(基于像素)图形，有[画布](http://www.whatwg.org/specs/web-apps/current-work/#scs-dynamic)，它也在除了 Internet Explorer 之外的任何地方都可以工作。再说一次，Internet Explorer 有自己专有的替代方案， [DirectAnimation](http://www.sworks.com/keng/da.html) ，尽管它在 Internet Explorer 7 中被删除了。IE7 的解决方案有望成为 [Windows 演示基础](http://msdn.microsoft.com/winfx/technologies/presentation/) (WPF)。

当然，在现实世界的应用程序中编写这些各种各样的 API 并不实际，这就是为什么我们现在在 Web 上很少看到动态图形，但是有一些解决方案正在酝酿中，以几个跨浏览器绘图 API 的形式出现。

谷歌的 ExplorerCanvas 项目试图通过将绘图指令转换为 VML 来支持 Internet Explorer 中 Canvas 的大部分功能。 [IESVG](http://starkravingfinkle.org/blog/2006/03/svg-in-ie-update/) 在 ie 浏览器中为 SVG 做了类似的事情。

非常基本的 SVG 支持也可以使用 [CanvaSVG](http://fuchsia-design.com/CanvaSVG/) 项目添加到 Safari 中，直到在该浏览器的最终版本中出现对 SVG 的完全支持。

最有希望的，尽管还没有准备好，是 Doughtie 正在为 Dojo Toolkit 的绘图 API 所做的工作。这个名为 dojo.gfx 的库将把上述项目的许多功能合并到一个 API 中。在这次演讲中展示的简短演示看起来确实很有希望！

## 分享这篇文章