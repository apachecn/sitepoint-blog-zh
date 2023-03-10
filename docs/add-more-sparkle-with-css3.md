# 使用 CSS3 添加更多亮点

> 原文：<https://www.sitepoint.com/add-more-sparkle-with-css3/>

![css3sparkle](img/00990a5844979e8685151cd6e5e9fc9a.png "css3sparkle")

你有没有注意到，当 EA Games 发布一款新游戏时，他们通常会同时发布从 PS3 到任天堂 DS 的所有游戏平台的游戏？

这些设备的功能有很大的差异，但它们能让游戏适应每一种设备。它们改变了视觉设计和游戏性，同时保持了游戏的核心体验、主题和品牌。他们专注于为每位客户提供他们所选游戏设备的最佳体验。

我可以把这和 CSS 开发联系起来。抛开 IE6 支持的问题不谈，我们似乎处于一个奇妙的境地，我们有一个强大的浏览器集合，所有的浏览器都支持 CSS 2.1:Safari，Firefox，Opera，Chrome 和 Internet Explorer。事实上，这是我第一次记得有 5 个浏览器可用，都有成熟和兼容的 CSS 支持。

在过去的几年里，最佳实践 CSS 开发已经从优雅的退化转变为渐进的增强。在现代浏览器的进步和旧浏览器的消亡的鼓舞下，人们对现在可能发生的事情越来越兴奋。越来越多的设计者正在考虑如何在不影响兼容性的情况下为每种浏览器提供最佳的用户体验。

以下是我最近遇到的几个例子:

[媒体查询](https://reference.sitepoint.com/css/mediaqueries)允许根据网页显示设备的特性，对如何应用 CSS 规则进行精细控制。设备宽度、颜色、分辨率等等都可以查询。Reinhold Weber 创建了一个演示，展示媒体查询和 CSS3 列的结合如何使您能够为 Firefox、Safari 和 Chrome 用户提供优化的体验。该技术根据屏幕宽度增加文本列的数量。

Jonathan Snook 在 Safari、Chrome 和最新版本的 Firefox 中使用 CSS3 transform 扩展演示了一个文本旋转技巧。此外，他还演示了通过使用专有的 Microsoft filterproperty，您也可以支持 Internet Explorer。

Tim Brown 在 Safari 中实现了纯 CSS 文本渐变，而 Markus Stange 在他的帖子“[用方框阴影取乐](http://markusstange.wordpress.com/2009/02/15/fun-with-box-shadows/)”中用方框阴影取乐 Markus 在 Firefox 3.5 中为按钮产生了一些令人印象深刻的效果，在 Safari 和 Chrome 中也有一些对框阴影的支持。

我在我的 Adobe AIR 应用程序中使用了 [-webkit-box-sizing 属性，以便强制元素采用 100%宽度，同时排除其边框和填充的宽度。通常，边框和填充会将元素的宽度扩展到 100%以上——这是经常遇到的麻烦。正如](https://www.sitepoint.com/article/css-desktop-adobe-air/) [James Hopkins](http://idreamincode.co.uk/css/css3/the-css3-box-sizing-concept-a-solution-to-a-longstanding-problem) 解释的那样，现在盒子大小属性已经得到了广泛的浏览器支持。

Chrome 一直支持的 CSS 文本阴影(Safari[从 1.1 版本开始]支持的时间更长)，现在最新版本的 Firefox 和 Opera 也支持。Westciv 有一个优秀的 [CSS 文本阴影生成工具](http://westciv.com/tools/shadows/index.html)可以玩。你也可以用目前由 Safari 和 Chrome 支持的 [CSS 渐变生成工具](http://westciv.com/tools/gradients/index.html)来找乐子。

这样的例子不胜枚举:[圆角](http://www.the-art-of-web.com/css/border-radius/)、[自定义字体](https://reference.sitepoint.com/css/at-fontface)、 [RGBA 颜色](http://www.zurb.com/article/266/super-awesome-buttons-with-css3-and-rgba)。这的确令人兴奋！

但是，尽管这东西令人兴奋，但它仍然必须具有成本效益，才值得努力。所以我很想知道是否有人在日常工作中以任何方式使用渐进式 CSS 增强？如果你在客户工作中这样做，你如何向客户推销这个想法？如果客户可以在一个浏览器中看到增强效果，他们会要求在所有浏览器中使用吗？如果你有任何活生生的例子，让我们知道你是如何做到的。

## 分享这篇文章