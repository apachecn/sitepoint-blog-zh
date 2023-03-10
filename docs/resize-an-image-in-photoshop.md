# 如何在 Photoshop 中调整图像大小并保持清晰

> 原文：<https://www.sitepoint.com/resize-an-image-in-photoshop/>

不久前，在 SitePoint 论坛上， [Stevieg_83 询问了他在调整屏幕截图](https://www.sitepoint.com/forums/showthread?t=633521)时遇到的一个问题。他的问题是:

> 我通常使用 Firefox 的 Fireshot 插件，它可以呈现高质量的图像。然而，当我试图在 Photoshop 中调整尺寸时，质量却直线下降。有人有什么建议吗？

我认为写一些关于这方面的东西可能是有用的，因为许多人发现当他们试图调整图像大小时，他们会得到模糊的图像，不管图像是来自相机、扫描还是截图。

1.我先用 Fireshot 截图。你可以看到整个截图的一部分，在下面放大 100%。右边有一个图像，左边有一些文字。

(注意:我在这里用的是 Photoshop CS4，但这个技巧从 CS 版本开始都是一样的)。![Screengrab](img/5985a1d0d9ef7182f2c0693cf9ba0185.png "Screengrab")

2.将屏幕截图复制并粘贴到新的 Photoshop 文档中，或者在 Photoshop 中打开已保存的屏幕截图。

3.选择**图像>图像尺寸**。当您想要 ***缩小*** 图像尺寸时，点击重新取样图像复选框以确保其被选中，并从下拉菜单中选择**双三次锐化**。

![Image Size Dialog](img/034413bacad3f0d4c47227cc680dfdc9.png "Image Size Dialog")

在“宽度”字段中，输入您想要的新大小的像素数。我把这张图片的宽度从 543 像素调整到 300 像素。点按“好”,您的图像将被调整大小。

下面的结果显示文字变得有点模糊(记得它几乎是原来的一半大小)，但右边的图像看起来仍然非常清晰。


将此与我在下拉菜单中选择双线性选项进行比较。文字已经完全破碎，图像边缘看起来相当粗糙。

![screengrab-bilinear](img/11aaad4059912e0c1ffb7e3e17365ee9.png "screengrab-bilinear")

如果我选择最近邻选项，你可以看到结果更糟。

![screengrab-nearestneighbour](img/45b9f354576ca1cfecfd5895e3610ba8.png "screengrab-nearestneighbour")

因此，为了减小图像尺寸，选择双三次锐化选项。对于想把他们作品的小版本放到网上的设计师或摄影师来说，这非常有用。如果你正在做大量的图像缩小，你可以设置 Photoshop 的首选项，使双三次是默认的选择。为此，选择**首选项>常规**，你会看到**图像插值**，在那里你可以从下拉菜单中选择**双三次锐化**，然后点击确定。

![preferences-dialog](img/00f79cd5661b6ad24a4187a532757d07.png "preferences-dialog")

**调整大小的提示**

*   在你做任何事情之前，确保你的图像处于 RGB 颜色模式！如果不是，请选择“图像”>“模式”>“RGB”进行转换。转换后，如有必要，您可以再次更改模式。
*   尝试只调整一次图像的大小。你调整的越多，东西就变得越模糊。您可以通过使用智能对象来解决这个问题，但是我们改天再来看这个问题。

所以我希望这对想知道如何调整图像大小的人有用。

这篇文章写于 2009 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于 Photoshop 的知识，你可能会对这篇[关于 Photoshop](https://www.sitepoint.com/getting-started-photoshop-5/) 入门的最新文章很感兴趣。

## 分享这篇文章