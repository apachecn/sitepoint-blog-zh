# Ruby on Medicine:从 DICOM 到 JPG

> 原文：<https://www.sitepoint.com/ruby-on-medicine-converting-dicom-to-jpg/>

![first aid long shadow icon](img/0f87c324c1d467defe975de32f86096a.png)

本文将是 Ruby on Medicine (RoM)系列的第一篇文章，主要讨论如何将 Ruby 应用于医学领域。

在本教程中，我将向您展示如何将 DICOM 文件转换为 JPG 图像，这允许我们对该图像执行不同的操作，这将在本系列的后续文章中看到。

本教程我用的操作系统是 *Ubuntu 14.10* 。如果你正在使用另一个操作系统，不要担心。我会在下面的适当部分向您推荐可能有用的链接。

## 什么是 DICOM？

你们中的许多人可能不太熟悉医学术语。那完全没问题。为了帮助您解决这个问题，让我从介绍 DICOM 开始，这种文件格式是本文的出发点。

我非常喜欢 Roni 的这篇文章，他介绍了 DICOM(医学中的数字成像和通信)。我在这里用 Roni 的帖子简单描述一下 DICOM。

你肯定听说过像 x 光、超声波、CT 和 MRI 这样的术语。这样的医学图像支持 DICOM 并广泛使用。DICOM 文件规范的核心既是一种*文件格式*又是一种*网络协议*。

所有医学图像都以 DICOM 格式(*即* dcm)保存。这种格式不仅包括图像，还包括患者信息(即姓名、性别、出生日期)、设备数据(含义、使用的设备)和其他信息。

DICOM *网络协议*被连接到医院网络的医疗应用程序用来交换信息。它还用于搜索和恢复存档中的病历报告。

当然，DICOM 还有更多的内容，但这超出了本文的范围。

## 为什么要将 DICOM 转换成 JPG？

为什么要将 DICOM 图像转换成 JPG 这样的图像格式？这是几个潜在的原因，其中一些是:

*   DICOM 文件以其巨大的尺寸而闻名。因为我们的目标是只处理文件的图像部分，转换成 JPG 既能给出图像又能减小文件大小。显然，这使得在线分享变得更加容易。

*   如前所述，DICOM 文件包含关于患者的重要数据。因此，当在线传输此类文件时，这些敏感信息就会暴露。这可能侵犯了病人的隐私。当我们将 DICOM 文件转换为图像时，我们使图像匿名，因为患者的信息不再存在。

*   这个系列的目的之一是在医学图像上执行不同的图像处理和计算机视觉任务，例如检测肿瘤。在这些情况下，DICOM 文件的图像部分就可以满足要求。几乎任何东西都可以阅读或操纵 JPG。

### 设置

让我们从确保环境具备所需的一切开始。

*红宝石*

如果您没有安装 Ruby，请在终端中运行以下命令来安装 Ruby:

```
sudo-apt get install ruby-full
```

以下命令将显示安装的 Ruby 版本的版本:

```
ruby -v
```

它应该是这样的:

```
ruby 2.1.2p95 (2014-05-08) [x86_64-linux-gnu]
```

如果你没有安装 Ubuntu，请通过下面的链接为你的操作系统安装 Ruby:

*   *Windows*:[ruby installer](http://rubyinstaller.org/)
*   *Mac OS X* : [如何在 Mac 上安装 Ruby](http://code.tutsplus.com/tutorials/how-to-install-ruby-on-a-mac--net-21664)

### Ruby DICOM

Ruby DICOM 是我们将用来将 DICOM 转换成 JPG 的库。

从 Ruby DICOM 的 GitHub 页面:

> Ruby DICOM 是一个小而简单的库，用于处理 Ruby 中的 DICOM。DICOM(医学数字成像和通信)是医学成像中处理、存储、打印和传输信息的标准。它包括文件格式定义和网络通信协议。Ruby DICOM 支持读取、编辑和写入这种文件格式。它还支持选择网络通信方式，如查询、移动、发送和接收文件。

要安装 Ruby DICOM，请在终端中运行以下命令:

*   *Ubuntu* : `sudo gem install dicom`
*   *视窗* : `gem install dicom`
*   麦克·OS X:`gem install dicom`

### RMagick

使用 Ruby 查看转换后的图像，意味着是时候安装 [RMagick](http://www.imagemagick.org/RMagick/doc/) 了。

但是，什么是 RMagick？基于 RMagick 的用户指南:

> RMagick 是从 Ruby 到 ImageMagick <sup>TM</sup> 图像操作库的绑定。ImageMagick <sup>TM</sup> 是一个创建、编辑和合成位图图像的免费软件套件。它可以读取、转换和写入各种格式的图像。可以裁剪图像、改变颜色、应用各种效果、旋转和组合图像，还可以将文本、线条、多边形、椭圆和贝塞尔曲线添加到图像中并进行拉伸和旋转。

> RMagick 是 ImageMagick 的完整接口。版本 1.0.0 于 2003 年 2 月发布。在其 4 个主要类和 18 个次要类中，RMagick 定义了超过 650 个方法和 350 个常量。RMagick 利用了 Ruby 的习惯用法，比如块和迭代器、结构类、符号？-还有！-带后缀的方法和异常。

要安装 RMagick，请在终端中运行以下命令:

Ubuntu:

```
sudo apt-get install imagemagick
sudo apt-get install libmagickwand-dev
sudo gem install rmagick
```

其他操作系统的链接:

*   *Windows* : [在 Windows 上安装 RMagick](http://codeofalice.com/code/installing-rmagick-on-windows-7/)
*   *Mac OS X* : [在 Mac OS X 上安装 RMagick](http://blog.paulopoiati.com/2013/01/28/installing-rmagick-in-mac-os-x-mountain-lion-with-homebrew/)

## 查看 DICOM

随着环境准备就绪，我们需要一个 DICOM 文件来调用我们自己的。我在这里使用了[的 *DIASTOLIX* 数据集(别名)，特别是文件: *IM-0004-0043.dcm*](http://www.osirix-viewer.com/datasets/)

使用像 [RadiAnt](http://www.radiantviewer.com/) 这样的 DICOM 查看器查看这个 DICOM 文件，我们看到如下内容:

![imageDicomViewer](img/d53ba8d23bdc3ad58b98b7241773fd54.png)

以下脚本将把 DICOM 文件转换成 JPG 图像:

```
require 'dicom'
include DICOM

dcm = DObject.read("IM-0004-0044.dcm")
dcm_image = dcm.image
dcm_image.normalize.write("IM-0004-0044.jpg")

exit
```

使用以下语法运行该文件:

```
ruby file_name.rb
```

它将输出如下内容:

```
I, [2014-12-24T16:55:59.974029 #4056]  INFO -- DICOM: DICOM file successfully read: IM-0004-0044.dcm
```

并且，应该创建一个新的 JPG 文件。使用 RMagick 进场时显示创建的 JPG 图像。

```
require 'RMagick'
include Magick

img = ImageList.new("IM-0004-0044.jpg")
img.display
```

综上所述，以下脚本将把 DICOM 转换成 JPG 并显示结果图像:

```
require 'RMagick'
require 'dicom'
include Magick
include DICOM

dcm = DObject.read("IM-0004-0044.dcm")
dcm_image = dcm.image
dcm_image.normalize.write("IM-0004-0044.jpg")

img = ImageList.new("IM-0004-0044.jpg")
img.display

exit
```

当您运行该程序时，您将看到以下输出:

![result](img/87a93347e13f02befd4a32ddd2add2f0.png)

现在，你有一个 JPG 图像，可以很容易地用于不同的图像处理操作。在接下来的文章中，我将向您展示其中的一些操作。

在我结束本教程之前，我想提一下，您可以将 DICOM 文件转换成您喜欢的任何图像类型(*即* PNG、BMP)。只需用您选择的扩展名替换图像文件中的`.jpg`部分。

在这个系列中，你希望看到什么特定的主题吗？如果是这样，请在下面的评论中留下它们。

## 分享这篇文章