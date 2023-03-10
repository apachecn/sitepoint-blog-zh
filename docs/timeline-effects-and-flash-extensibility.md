# 时间轴效果和 Flash 可扩展性

> 原文：<https://www.sitepoint.com/timeline-effects-and-flash-extensibility/>

Flash MX 2004 带来了几个新的功能，其中一个虽然被称为动画和富媒体的补充，但并没有像最初那样受到媒体的好评，但现在正在 Flash 社区中聚集动力。

新增加的内容统称为时间轴效果，主要针对 Flash 初学者，以提高他们在普通程序(如简单效果和补间)中的工作效率。

如果您认为自己是 Flash 的中级或高级拥护者，会发生什么情况？他们在你的军火库中还有位置吗？嗯…是和不是。有两个主要的时间轴效果非常方便，它们是复制到网格和分发副本，这两个都是节省时间的快速复制实例到网格状结构和控制许多因素的渐变复制。

但是爆炸、扩展和模糊时间轴效果呢？当然，它们对初学者来说有点方便，但在我看来，过一段时间后不会有太大的用处，特别是在你开始熟悉 ActionScript 之后，因为与脚本控制机制相比，你可以用这些效果产生的迭代和效果类型是有限的。

通过重写最新版本的 Flash，集成的 JavaScript API (JSAPI)使您能够创建自己的时间轴效果、命令和面板，以便在 Flash 中使用。

虽然产品附带的第一批时间线效果并不令人惊讶，但有几个第三方发布的扩展确实非常有趣；PixelFX、DistortFX 和 TextFX。MX Studio 2004 短期捆绑了这些商业时间轴效果，现在可以通过 Macromedia 商店([http://www.macromedia.com/software/flash/extensions/](http://www.macromedia.com/software/flash/extensions/))下载。

这些时间轴效果确实向初学者和高级用户展示了 JSAPI 以及如何利用它在 Flash 中创建诱人的效果。

在仔细阅读 JSAPI 文档(见下文)和命令面板后，您可以创建自己的时间轴效果，以提高您在 Flash 和 I 中的工作效率

## 分享这篇文章