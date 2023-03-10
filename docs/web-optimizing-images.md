# 优化网页图像(快速加载技巧)

> 原文：<https://www.sitepoint.com/web-optimizing-images/>

有相当多的方法可以优化你的图片以加快加载速度。最好的方法包括**创建一个包含你所有图像的 sprite** ，然后使用 CSS 调整 sprite 上与你的图像对齐的偏移位置。其他技术包括 **[无损压缩](http://en.wikipedia.org/wiki/Lossless_compression)，根据需要使用正确的尺寸/格式**为图像指定尺寸和**。**

## 本帖涵盖的主题:

*   使用无损压缩减小图像尺寸
*   使用精灵优化您的图像
*   为什么优化图像很重要
*   使用 Photoshop 优化图像
*   优化图像的其他技巧和诀窍

## 使用压缩技术优化您的图像

你可以使用各种在线工具检查你的网站，比如 [GTMetrix](http://gtmetrix.com/) 报告图像优化建议和**无损压缩**缩减。使用 GTMetrix，您可以单击“查看优化版本”,如果您希望替换当前图像，您可以从中进行保存。这是一个压缩图像缩小的例子。

**在 GTMetrix 中检测到图像，作为压缩的候选对象。**

![](img/51638603b3e7c2fdd3fbf096c0f5d6e3.png "22-11-2012 2-01-25 PM")

这些图像看起来非常相似，因此质量没有损失(当你将鼠标悬停在图像的白色版本上时，白色区域是图像的白色版本)。

![](img/d29c881da2516a166768b790ff1a3221.png "22-11-2012 12-19-48 PM")

更快装货的证明！:)

![](img/a7b68f4f3cf445aec5a8cec12f781c60.png "22-11-2012 1-59-23 PM")

## 使用精灵优化您的图像

使用[在线精灵工具](http://www.jquery4u.com/animation/image-sprite-plugins-tutorials/)创建一个精灵——其中最好的精灵工具叫做[针](http://draeton.github.com/stitches/)。非常简单的使用你简单的添加你的图片，你就可以得到精灵和匹配的 CSS。

[![](img/623e2a25d3c857ce12a14085623234c1.png)](http://draeton.github.com/stitches/)

## 为什么优化图像很重要

这有很多原因，在这里我会给你一个优化你的图片的理由，这应该足以说服你。

1.  **页面加载速度**–图片在网页大小和加载速度方面起着至关重要的作用(平均占总大小的 45%——基于前 10 大网站)。谷歌喜欢快网站——慢 500 毫秒可能会导致 20%的流量下降！不太好。此外，如果你使用精灵，它可以减少你的 http 请求，并提供更少的图像闪烁，特别是动画。很明显，如果你的图片越小，你的带宽就越少，你的主机费用就越低！

## 使用 photoshop 优化图像

你也可以使用 Photoshop 来压缩你的图片。从我的经验来看，JPG 格式在大小比例上达到了最好的质量，所以这是我倾向于使用的格式，除非我需要一些透明度，然后我选择 PNG。让我们快速看一下不同格式的同一张图片——看看大小和质量。我不会深入探讨如何做到这一点，但这里有一些职位将有所帮助:

*   [优化网页图像](http://inobscuro.com/tutorials/optimizing-images-for-web-35/)
*   [在 Photoshop 中查看 JPEG 压缩的差异](http://www.photoshopessentials.com/essentials/jpeg-compression/)
*   [通过 Photoshop 中更好的图像优化加速您的网站](http://www.uxbooth.com/articles/speed-up-your-website-with-better-image-optimization-in-photoshop/)

## 优化图像的其他技巧和诀窍

这是一个学习不同格式的好帖子:[何时以及如何使用互联网图像格式](http://www.r1ch.net/img-formats/)。

雅虎有个工具叫 [Smush.it](http://www.smushit.com/ysmush.it/) 可以批量上传和转换你的图片。


我见过的另一种技术是剥离保存在一些 JPEGS 上的元数据。

用 PNGCRUSH 粉碎 PNGS 我很确定这是雅虎的 Smush.it 正在使用的工具/算法之一。

养成习惯，调整、优化和重命名你放在网上的每张图片。

## 分享这篇文章