# 什么是 WebP 图像格式(为什么它很重要)？

> 原文：<https://www.sitepoint.com/webp-image-format/>

[WebP](https://developers.google.com/speed/webp/) 格式是谷歌的一种新的图像格式，它采用了有损(质量和大小之间的平衡)和无损(在不影响质量的情况下减小大小)压缩技术。

谷歌在 [BSD 许可](http://en.wikipedia.org/wiki/BSD_licenses)下开发格式和 WebP 相关软件。它最初于 2010 年 9 月 30 日发布，是一种开放的格式。它使用的文件扩展名是`.webp`。

![WebP: A new image format for the web](img/f4f344c4dae65c526f3a1c581d15f757.png)

### WebP 压缩的基本机制

有损 WebP 算法使用预测编码来预测其相邻像素的颜色值。然后*只对实际值和预测值*之间的差异进行编码。在这些编码值或残差值中，许多是空值或零，这种现象有助于进一步减小 WebP 图像的大小。

因此，像 JPEG 一样，WEBP 有损压缩是基于块预测的。

### 支持和使用

谷歌 Chrome 和 Opera 都原生支持 WebP。在图形软件中， [Picasa](https://picasa.google.com/) 、 [PhotoLine](http://www.pl32.com/) 、 [Pixelmator](http://www.pixelmator.com/) 、 [ImageMagick](http://www.imagemagick.org/) 、 [Konvertor](http://www.konvertor.net/) 、XnView、 [IrfanView](http://www.irfanview.com/) 和 GDAL 都是原生支持 WebP 的。

脸书已经开始使用 WebP 来降低网络成本和提高网站速度。在脸书的规模下，即使相对较小的效率提升也开始累积。当人们上传 JPEG/JPG 图像时，脸书会自动以 WebP 格式制作副本。他们现在显然已经开始向使用 Chrome 和 Opera 的人提供这些图像。

Telegram 也开始使用 WebP 作为它的流行贴纸。谷歌搜索的即时预览功能在内部使用 WebP 来减少预览使用的磁盘空间。

当一个人想在网络浏览器之外的软件中查看图像或对其进行编辑时，主要问题就出现了，因为大多数最流行的图形应用程序——包括 Window 或 OS X 的图像查看器和 Photoshop——都不能本地处理 WebP。

2013 年 10 月，Mozilla Research 的 Josh Aas 发表了[一份关于当前有损编码技术的综合研究](https://blog.mozilla.org/research/2013/10/17/studying-lossy-image-compression-efficiency/)，但未能得出 WebP 的表现明显优于 JPEG 的结论。

不过，Telegraphics 已经发布了一个免费插件，可以在 Adobe Photoshop 中支持 WebP，GIMP 和 Paint.NET 通过插件支持 WebP。谷歌还发布了一个 Windows 插件，可以在 Windows Photo Viewer、Microsoft Office 2010 和任何其他使用 Windows 图像组件的应用程序中支持 WebP。

### 福利和统计数据

谷歌的 WebP 文档声称:

> 与 png 相比，WebP 无损图像的大小要小 26%。与同等 SSIM 指数的 JPEG 图像相比，WebP 有损图像的尺寸要小 25-34%。WebP 支持无损透明(也称为 alpha 通道),仅增加 22%的字节。有损压缩也支持透明，当红色/绿色/蓝色通道接受有损压缩时，透明文件通常比 PNG 文件小 3 倍。

### 转换到 WebP

WebP 的文档提供了将 PNG 和 JPEG 图像转换为 WebP 图像的预编译软件。你可以从[这里](https://developers.google.com/speed/webp/docs/precompiled)阅读和下载更多信息。这里是[链接](http://image.online-convert.com/convert-to-webp)到一个在线转换器，它不需要下载任何软件就能完成任务。

### 为电报制作网络贴纸

关于 WebP 我们已经谈得够多了。让我们将我们的 WebP 知识用于一些建设性和创造性的用途。我们将看看如何从 JPEG 图像制作电报贴纸，并将其转换为`.webp`文件。我们将根据下图制作我们的贴纸。

![Original JPEG Image](img/aeeaac9334764f68a10b76eaa19fdeec.png)

我们将利用 Photoshop 使图像背景透明。我们可以选择“背景橡皮擦工具”。我们也将裁剪图像，因为它相当大！

![Cropped and Transparent Image](img/60252ad62bcac7820ee3fa33f95be219.png)

接下来，我们将选择当前层和应用混合选项(中风)并选择白色。此外，ew 需要选择投影来创建一个微妙的阴影效果，我把它保存为 PNG 格式。

![Stroked with White Outline and Drop Shadow](img/6f96b32260b282f758f68ebfdbd12c28.png)

最后，我们将使用在线转换器将图像转换成`.webp`格式，我们的贴纸就做好了！

![Sticker in Telegram](img/5af237d5da94a722603a10a82d066f96.png)

你可以用类似的方式制作许多其他贴纸，在 Telegram 上分发给你的朋友。

### 结论

WebP 是一种新兴的图像压缩格式，得到了谷歌、脸书、Opera 和 Telegram 的支持，这无疑提高了它的受欢迎程度。这种形式已经拥有了大量的粉丝和批评者。

然而，能够将 GIF 和 PNG 的优点打包成一种图像尺寸更小的格式，将继续吸引 web 开发人员创建更小、更丰富的图像。

如果这有助于使整个网络更快，我们都赢了。

## 分享这篇文章