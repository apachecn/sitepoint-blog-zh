# 使用 ImageMagick 创建动态图像

> 原文：<https://www.sitepoint.com/dynamic-images-imagemagick/>

数字图像和图形可以使网站在视觉上更吸引人，浏览起来更直观。它们可以作为非常简洁的方式来说明想法，让我们用一张图片来代替众所周知的“千言万语”。但是网络上的许多图像都有一个主要的弱点:它们是静态的。它们首次加载到 web 服务器时的大小、分辨率、颜色和其他属性不会改变。

如果你的站点显示静态图像，这些图像不能响应用户与你的站点的交互而改变，不管这种能力对你的站点的用户有多有价值。更糟糕的是，只有你能改变它们，这是一个手动的过程，需要你首先在你的台式电脑上修改图像，然后再把它们上传到你的网络服务器上。

想象一下，如果您可以根据您或任何其他授权的站点访问者的输入，使您的网站改变它所显示的图像的外观，将会出现什么样的可能性。我们不仅仅是在讨论改变图像在页面上的位置，还包括图像的大小、分辨率、边框、对比度和抖动等属性。想象一下，能够自动旋转图像、翻转图像、添加色彩或将多个图形合并为一个图形。

您可以放大街道地图，而不必在服务器上存储该图像的不同版本(用户可能要求的每种可能的分辨率级别都有一个版本)；您可以为任何表明自己患有特定形式色盲的网站访问者更改图像的颜色；你可以允许人们随意旋转空中拍摄，或者允许购物者在购买之前改变艺术印刷品的色彩。你甚至可以创建一个在线图像编辑器。

##### 数字图像魔术

在你的网站上实现动态图像的关键组件是一个图像处理程序，它由生成网页的代码控制。尽管本文使用 PHP 作为脚本语言，但是其他语言——比如 Perl 和 Ruby——也可以很容易地使用，只要您考虑的图像处理程序具有针对所选语言的应用程序编程接口(API)。

我选择在本文中使用 ImageMagick 有几个原因:

*   它比其他任何软件包都更受虚拟主机服务的支持
*   它是免费的
*   它运行在 Windows 和 Linux 平台上
*   它拥有 20 年的持续改进和漏洞修复
*   它强健而强大，提供了广泛的功能

下载和安装 ImageMagick 的说明可以在 [ImageMagick 网站](http://www.imagemagick.org/script/index.php)上找到。

ImageMagick 提供了大量强大的功能，但所有这些功能都必须从命令行运行。为了从 PHP 支持的网站上访问该功能，您需要使用一个 API(以 PHP 扩展的形式)，例如[magick and](http://www.magickwand.org/download/php/)或 [imagick](http://pecl.php.net/package/imagick/) 。下面的例子利用了 MagickWand API，所以您需要在 web 服务器上安装它(以及 ImageMagick)。

##### 除“胡言乱语”以外的命令

不管您想对图像执行什么类型的操作，首先，您需要将该图像加载到计算机内存中，并将其分配给 MagickWand 资源。以下 PHP 脚本从当前目录加载一个示例图像文件，然后在 web 浏览器中显示它:

`<?php // script_1.php`

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . jpg ')；

标头(' Content-Type:image/JPEG ')；
magickechimageblob($ resource)；

？>

![1541_fig1](img/236257f7acde3771d50aa834613dcc43.png)
图 1:原始图像，显示在浏览器中

一旦您创建了 MagickWand 资源并加载了图像，您就可以找到关于图像的信息，例如它的尺寸:

`<?php // script_2.php`

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . jpg ')；

$ width = MagickGetImageWidth($ resource)；
$ height = MagickGetImageHeight($ resource)；
echo“图像大小，以像素为单位，为:width $ width x height $ height”；

？>

![1541_fig2](img/a9d297b2961ea1aaa1a94b777dbbc767.png)
图 2:确定图像的尺寸

也许你想限制网站访问者上传的任何图片的物理尺寸？这可以通过修剪图像中位于矩形子图像之外的所有点，或者通过缩放原始图像以适合指定的边界尺寸来实现。

在第一种情况下，假设您的应用程序需要丢弃图像左上角 200×50 像素部分以外的所有内容。左上角被认为是偏移量(0，0)。以下脚本将在显示示例图像之前对其进行裁剪:

`<?php // script_3.php`

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . jpg ')；

MagickCropImage( $resource，200，50，0，0)；

标头(' Content-Type:image/JPEG ')；
magickechimageblob($ resource)；

？>

![1541_fig3](img/aa964a2635e5de7a7c7ddbce38dd59ca.png)
图 3:裁剪后的图像

在第二种情况下，假设您的应用程序要求将图像作为一个整体保存，但是需要将图像缩小到一个更小的尺寸(比如最大 200×200 像素)。以下脚本将调整图像的大小:

`<?php // script_4.php`

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . jpg ')；

$ resource = MagickTransformImage($ resource，' 0x0 '，' 200×200 ')；

标头(' Content-Type:image/JPEG ')；
magickechimageblob($ resource)；

？>

![1541_fig4](img/f95135570d7bb63de8963dddd998016e.png)
图 4:缩放后的图像

上图展示了`MagickTransformImage`功能如何在缩放时自动保持图像的宽高比。

还要注意的是，`MagickTransformImage`将转换后的图像作为一个新的资源返回，为了保存它，必须将它赋给一个变量。相比之下，我们之前考虑的函数——`MagickCropImage`——作用于传递给它的资源变量；它返回一个布尔值(真或假)。当编写自己的 MagickWand 代码时，一定要确认每个函数的返回类型，因为任何不正确的假设都会导致调试花费大量时间。

也许一幅图像的左右两边有 20 个像素的边框，您希望消除这些边框，但是您不希望从图像的顶部或底部移除任何像素。以下脚本将完成这一任务:

`<?php // script_5.php`

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . jpg ')；

MagickShaveImage( $resource，20，0)；

标头(' Content-Type:image/JPEG ')；
magickechimageblob($ resource)；

？>

![1541_fig5](img/5df169beaac301867b5a1a610f9dec23.png)
图 5:修剪后的图像

接下来，让我们假设您有一个 PNG 格式的图像，您想将其重新格式化为 JPEG 图像。以下是执行转换的脚本:

`<?php // script_6.php`

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . png ')；

MagickSetImageFormat( $resource，' JPEG ')；

标头(' Content-Type:image/JPEG ')；
magickechimageblob($ resource)；

？>

![1541_fig6](img/b1277846af8dabbb7530a407da9a3623.png)
图 PNG 格式转换的 JPEG 图片

图像也可以旋转！也许你想把上面的图像顺时针旋转 45 度？轻松点。

`<?php // script_7.php`

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . jpg ')；

MagickRotateImage( $resource，null，45)；

标头(' Content-Type:image/JPEG ')；
magickechimageblob($ resource)；

？>

![1541_fig7](img/bf55674e5e1ddc6a8d699e37b200fcbc.png)
图 7:旋转后的图像

在上面的脚本中，我们没有为任何由于旋转而新暴露的区域指定颜色。但是我们肯定能做到:

`<?php // script_8.php`

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . jpg ')；

MagickRotateImage( $resource，NewPixelWand( 'blue ')，45)；

标头(' Content-Type:image/JPEG ')；
magickechimageblob($ resource)；

？>

![1541_fig8](img/49d95ce3b0b3dbee536f20ae14360bd5.png)
图 8:旋转后的图像，蓝色背景

##### 从帽子里抓出兔子

在对图像进行了所有需要的更改之后，或者您的 PHP 脚本允许您的站点访问者进行同样的操作之后，您可以在访问者的 web 浏览器中显示完成的图像(就像我们上面所做的那样)，或者您可以将新图像保存回磁盘。让我们修改前面的示例脚本，将图像保存到磁盘:

`<?php // script_9.php`

set _ time _ limit(60 * 5)；

$ resource = NewMagickWand()；
MagickReadImage( $resource，' image _ 1 . jpg ')；

MagickRotateImage( $resource，NewPixelWand( 'blue ')，45)；

MagickWriteImage( $resource，' new _ image . jpg ')；

？>

您会注意到这里有一个额外的变化:set_time_limit 以秒为单位设置脚本超时(在本例中，设置为五分钟)。如果您的脚本可能处理的图像文件大到超过了 web 服务器的默认脚本超时，这是一个明智的预防措施。

##### 学习技巧

如果你想了解更多关于 ImageMagick 的知识，最有希望的资源是迈克尔·斯蒂尔的书《ImageMagick 权威指南》( ISBN 1590595904 ),该书于 2005 年 12 月由[出版社](http://www.apress.com)出版。它解释了如何为几个 Linux 发行版以及 Microsoft Windows 安装和配置 ImageMagick。这些章节涵盖了基本的图像处理、压缩、其他元数据、ImageMagick 工具、艺术转换、其他图像转换和绘图命令。本书最后给出了在用 Perl、C、Ruby 和 PHP 编写的程序中使用 ImageMagick 的例子。

ImageMagick 网站提供了一些文档。对于 ImageMagick 的十个命令行工具中的每一个，该网站都有一个页面，简要解释了该工具的用途，提供了其用法示例，并列出了所有命令行选项。每个选项都链接到另一页上更详细的报道。此外，还有一个页面专门介绍 11 种不同编程语言的可用 ImageMagick APIs，包括 PHP、Perl 和 Java。

要了解更多关于 MagickWand 和它的命令，你可以下载在线的[手册](http://www.magickwand.org/download/php/windows/manual/)，它是 HTML 帮助编译的文件格式。如果您选择使用 imagick 作为您的 API，您会发现可用的资源很少，主要包括下载包中 examples 目录下的示例代码。

程序员和 web 开发人员经常发现查看工作代码是学习新技术的最有效方式，ImageMagick 也不例外。上面的代码片段演示了所有基本的图像操作。要了解如何执行更复杂的操作，请看一下 Anthony Thyssen 的 [ImageMagick 用法](http://http://www.cit.gu.edu.au/~anthony/graphics/imagick6/)示例。

所有网站所有者和 web 开发人员应该已经知道，使用 PHP 和其他脚本语言，他们的网站页面可以真正实现动态化——现在他们也可以对他们网站的图形进行同样的描述了！

## 分享这篇文章