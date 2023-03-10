# 使用 png 和 Canvas 压缩 CSS 和 JavaScript

> 原文：<https://www.sitepoint.com/compress-css-javascript-png-canvas/>

这是一个卑鄙的伎俩。它是由 Nihilogic.dk 网站的 Jacob Seidelin 设计的，并被一些参加 [10K 独立活动竞赛](https://www.sitepoint.com/10k-apart-web-application-competition/)的人使用，尽管对于它是否符合活动精神还有一些争议。

说了这么多，看看怎么做的挺有意思的，那就来看看吧。以下是步骤:

**1。最小化你的应用**
第一步是使用任何合适的压缩工具将你最终的 CSS 和 JavaScript 压缩到尽可能小的尺寸。

**2。将你的 CSS 和 JavaScript 编码成图形**
这是棘手的部分。您需要将 ASCII 编码的 CSS 和 JavaScript 文件转换成一个二进制图像。PNG 格式是最好的，因为它是无损的(您的代码被保留)，使用 zlib 压缩，并且被所有浏览器支持。

Web 开发人员 Cal Henderson 已经发布了他的研究成果和 [PHP/Perl 代码](http://github.com/iamcal/PNGStore)，这些代码使用各种格式将代码转换成图像，比如 1 位、8 位、24 位等等。[您可以在这里将 jQuery 库视为 1 位正方形](http://github.com/iamcal/PNGStore/raw/master/perl_images_pngout/jquery_ascii_t0_1b_square.png)。

使用 [Smush.it](http://developer.yahoo.com/yslow/smushit/) 和 [PNGOUT](http://advsys.net/ken/utils.htm) 等工具可以进一步缩小 PNG 的大小。

**3。从图像中提取代码**
我们现在需要加载图像并将其转换回可执行代码。这可以通过使用`drawImage()`方法将图像加载到`canvas`元素中来实现。然后使用`getImageData()`读取单个像素，并转换成可以传递给`eval()`或嵌入 DOM 的字符串。

结果各不相同，但可以节省 75%的文件大小。在少数情况下，图像格式可以击败用 Gzip 压缩的文件。所以你可以用 40Kb 的代码参加 10K 竞赛！

## 这在现实世界中有用吗？

那么，除了在比赛中作弊，这种技术在现实世界中有什么应用吗？这是一个有趣的技巧，但是在服务器上 gzip 源文件通常会产生更好的压缩效果，并且更容易实现。

然而，我可以看到这项技术被用于[隐写术](http://en.wikipedia.org/wiki/Steganography)，或者加密来自黑客新手的敏感代码。非常酷，但是你会使用它吗？

## 分享这篇文章