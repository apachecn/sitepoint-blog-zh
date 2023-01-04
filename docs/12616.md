# Adobe 发布 Flash 10 播放器

> 原文：<https://www.sitepoint.com/adobe-releases-flash-10-player/>

Adobe 今天正式发布了其在全球无处不在的 Flash 播放器的第十个版本。Flash 10 之前以代号“Astro”为人所知，它为 Flash player 添加了大量新功能，但最炫的(请原谅这个可怕的双关语)当然是 3D 支持、新的文本引擎和 Pixel Bender 图像处理器，它以前被称为 Hydra。

我与 Adobe 的高级产品营销经理 Tom Barclay 和 Adobe 的高级产品经理 Justin Everett-Church 进行了交谈，他们向我概述了这些功能。

Flash 10 中最有可能被谈论的新特性是对 3D 的原生支持。开发人员现在将能够非常容易地获取 2D 对象，并在三维空间中对其进行转换和动画制作——例如，在三维空间中以圆形旋转一组 2D 图像。通过 Flash CS4 中的简单 API 和新工具可以访问 Flash 中的 3D 效果。以前，开发人员必须使用 ActionScript 或依赖第三方工具来实现类似的 3D 效果。“对我们其他人来说，这真的是 3D，”巴克利说。

由于原生 3D 转换和动画利用了 GPU 硬件加速，第三方 3D 动画工具也将在 Flash 10 中获得速度提升。

新版本的 Flash 还将支持一个全新的文本渲染引擎，允许开发者创建自己的文本布局组件。新的文本渲染引擎将于今年晚些时候在 Adobe 实验室首次亮相。届时，开发人员将能够在其基于 Flash 的应用程序中支持更高级的文本布局，如多列可流动文本、内嵌图像、双向文本，以及将多个自定义文本组件链接在一起的能力。这将使支持非拉丁字母变得更加容易。

新的 Flash 文本渲染引擎是与 Adobe InDesign 团队合作开发的。

然而，Flash 10 中最引人注目的新功能可能是集成了新的 Pixel Bender 图像处理技术，该技术基于 Adobe After Effect CS4 视频后期制作软件中的视觉效果技术。Pixel Bender 允许开发人员创建可在运行时应用于视频、图像和位图的自定义滤镜和效果。去年 5 月，当 Flash 10 进入测试版时，Adobe 建立了一个 [Pixel Bender Exchange](http://www.adobe.com/go/pixelbender/) ，所以你可以了解我们正在谈论的是什么样的效果。

根据 Everett-Church 的说法，因为 Pixel Bender 支持多核 CPU(而 ActionScript 不支持)，而且因为它是异步的，并在自己的线程中运行进程，所以当你进行大量计算时，它不会降低你的应用程序的速度。基本上，Pixel Bender 是一个数学计算速度非常快的引擎。

让它如此酷的是，它的实时编译器不仅仅可以用于图形。例如，Everett-Church 和 Barclay 告诉我，有一位客户正在使用 Pixel Bender 实时渲染多通道、全方位的音频(在后面的帖子中会详细介绍)。

Flash Player 10 在[www.adobe.com/go/getflashplayer](http://www.adobe.com/go/getflashplayer)有售。它可以立即在 Windows、Mac 和 Linux 上使用，随后还有 Solaris 版本。Adobe 告诉我，Flash 10 的功能应该会在今年年底的下一个 AIR 版本中实现。

## 分享这篇文章