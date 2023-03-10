# Ruby on Medicine:使用医学图像

> 原文：<https://www.sitepoint.com/ruby-on-medicine-working-with-medical-images/>

![first aid long shadow icon, vector symbol](img/4722e476e7dd99ee9b46ac2a29867918.png)急救长影图标，矢量符号

作为医学图像分析的研究者，我怎么强调图像的重要性都不为过。这些图像揭示的关于身体的信息，无论是内部的(*即* MRI)还是外部的(*即*皮肤镜检查)，都是非常有价值的。当我注意到图像中清晰显示的肿瘤时，我感到很惊讶。有时，图像需要一些处理，但最终医学图像是该信息的来源。

显然，肿瘤不是医学图像显示的唯一项目。其他问题，如骨折，使用图像进行诊断。医学图像分析是医学的一个重要组成部分，许多会议和杂志都是专门针对这一领域的。

在本系列的[中，我重点介绍了 Ruby 编程语言在医学领域的应用。在本文中，我将向您展示如何使用 Ruby 来查看和编码医学图像。](https://www.sitepoint.com/series/ruby-on-medicine/)

事不宜迟，我们开始吧。

## 软件

### 坦克

在深入探讨之前，简要概述一下 [Tk](https://en.wikipedia.org/wiki/Tk_%28software%29) 是很重要的，这是我们在本文中工作的一个基本概念。

Tk 是一个免费的、开源的、跨平台的小部件工具包，用于以不同的编程语言(包括 Ruby)构建图形用户界面(GUI)。换句话说，它为我们提供了 GUI 部件，如*文本*、*按钮*、*标签*等。，这是我们构建桌面应用程序时需要的。Tk 是 Ruby 标准库的一部分。

### 图像魔术

ImageMagick 是一个库和一组命令行工具，提供操作各种图像格式的能力。

### RMagick

RMagick 是 Ruby 和 ImageMagick 之间的接口。RMagick 抽象了 ImageMagick 提供的各种操作，因此它们可以在 Ruby 程序中使用

## 环境设置

现在我们已经了解了基本概念，让我们继续设置环境。我们需要安装上面介绍的工具: *Tk* 、 *ImageMagick* 和 *RMagick* 。

### 安装 Tk

我目前在 Mac OS X 上工作，所以指令将基于这个操作系统。别担心，如果你运行的是不同的 OS，你可以在这里找到安装 Tk [的说明。](http://ruby.about.com/od/tk/a/Tk.htm)

在 Mac OS X 上， [ActiveTcl](http://www.activestate.com/activetcl) 是获取 Tk 最简单的方法。ActiveTcl 包括 Tcl、Tk 和其他库。你可以直接从[这里](http://www.activestate.com/activetcl/downloads/thank-you?dl=http://downloads.activestate.com/ActiveTcl/releases/8.6.4.1/ActiveTcl8.6.4.1.299124-macosx10.5-i386-x86_64-threaded.dmg)下载。按照安装向导，你都设置好了！

#### 安装 ImageMagick

安装 ImageMagick 很简单。不同操作系统的不同发行版可以在[这里](http://www.imagemagick.org/script/binary-releases.php)找到。我会用[自制](http://brew.sh/)来安装 ImageMigick。您可以通过在终端中键入以下命令(不带`$`符号)在您的机器上安装 Homebrew:

```
 $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

现在，为了安装 ImageMagick，只需在您的终端中键入以下命令(不带`$`符号):

```
 $ brew install imagemagick 
```

#### 安装 RMagick

为了安装 RMagick，您只需要在终端中键入以下命令(假设您已经安装了 Ruby):

```
 $ gem install rmagick 
```

## 查看医学图像

我们将尝试使用 Ruby 查看的图像如下:

![brain](img/a77b51c8f9622e3b55395022512b16c7.png)

将它保存到您的硬盘上，命名为`brain.jpg`。

我们需要做的第一件事是读取图像。 [`TkPhotoImage`](http://ruby-doc.org/stdlib-2.0.0/libdoc/tk/rdoc/TkPhotoImage.html) 类用于此:

```
 require 'tk'
img = TkPhotoImage.new
img.file = 'brain.jpg' 
```

在 Tk 中，遵循小部件层次结构。换句话说，小部件可以包含在其他小部件中。Tk 中的主要小部件是`root`小部件，可以使用 [`TkRoot`](http://ruby-doc.org/stdlib-2.0.0/libdoc/tk/rdoc/Tk/Root.html) 类来创建。把`TkRoot`当成主窗口。要在 Ruby 中创建这个主窗口，请执行以下操作:

```
 root = TkRoot.new(title 'view image') 
```

创建主窗口后，我们现在需要一个合适的小部件来显示图像。 [`TkLabel`](http://www.rubydoc.info/stdlib/tk/Tk/Label) 将适合这项任务，因为它将使我们能够创建一个新的标签，将用于显示我们的形象。为了将标签包含在根中，我们执行以下操作:

```
 label = TkLabel.new(root) 
```

现在，为了在标签中显示图像，我们编写:

```
 label.image = img 
```

在处理对`Tk`的控制之前，我们需要指定标签的`height`和`width`。让它等于我们的图像的高度和宽度。所以，我们可以这样做:

```
 label.place(height: img.height, width: img.width) 
```

事情终于准备就绪。为了让 Tk 显示小部件，我们需要调用`Tk.mainloop`。这是最终的脚本:

```
 require 'tk'
img = TkPhotoImage.new
img.file = 'brain.jpg'
root = TkRoot.new {title 'view image'}
label = TkLabel.new(root) 
label.image = img
label.place(height: img.height, width: img.width)
Tk.mainloop 
```

## 问题和陷阱

在我向您展示输出之前，我想回顾一下您在处理 Tk 时可能会遇到的一些问题。

### Gif 问题

你可能会问为什么我们要安装`ImageMagick`和`RMagick`，特别是我们没有在上面的代码中使用它们。

正如你在这里看到的，标准 Tk 只支持`gif`图像格式，正如你注意到的，我们正在使用`jpg`图像。在这种情况下，ImageMagick 和 RMagick 进场。使用 RMagick，我们可以将一个`jpg`图像转换成一个`gif`图像，然后在我们的程序中使用后一种格式。

RMagick 在`Magick`模块中实现，因此，我们需要包含 Magick，如下所示:

```
 require 'RMagick'
include Magick 
```

要执行从`jpg`到`gif`的转换，请执行以下操作:

```
 img = ImageList.new('brain.jpg')
img.write('brain.gif') 
```

所以，你将会读到`brain.gif`而不是`brain.jpg`。值得注意的是，您也可以使用 ImageMagick 命令行工具来完成此任务。

### 无法加载这样的文件— tk (LoadError)

运行该程序时，您可能会看到这个错误，它是从第`require 'tk'`条语句中弹出的:

```
 cannot load such file -- tk (LoadError) 
which only seems to happen if you're running RVM. Based on this post, the issue can be solved as follows:

```

*   确保你已经安装了 Tcl(见上面的`Install Tk`)
*   在您的终端中运行以下命令(这是对当前版本 2.2.3 的 ruby 升级，在 Mac OS X 上支持 Tk):

```
 rvm reinstall 2.2.3 --enable-shared --enable-pthread --with-tk --with-tcl 
```

### 无法加载此类文件— RMagick (LoadError)

作为`require 'RMagick'`语句的结果，您可能会遇到的另一个错误是:

```
 cannot load such file -- RMagick (LoadError) 
```

为了解决这个问题，只需运行:

```
 $ gem install rmagick 
```

解决了这些问题后，程序的输出如下所示(我先显示了一个小窗口，所以我放大了窗口，得到了下图):

![output](img/c8ffea04408f04ce3406154c3e9c3837.png)

## Ruby on Medicine: Base 64 编码和解码

假设您的脚本必须通过网络传输该图像，比如在电子邮件中。当你意识到你的文件的另一面有奇怪的字符时，你可能会大吃一惊！对此的一种解释可能是，您试图以原始位和字节格式发送文件，而所使用的媒体是为流式文本设计的。

但是不要担心，实际上有一个工具可以避免这样的问题。是 *Base64 编码*。我将向您展示使用 Ruby 对医学图像进行 Base64 编码和解码是多么容易。

## 什么是 Base64？

Base64 是一种将 8 位二进制数据编码成可以用 7 位表示的格式的方式。这仅使用字符`A-Z`、`a-z`、`0-9`、`+`和`/`来完成，其中`=`用于填充数据。使用这种编码，三个 8 位字节被转换成四个 7 位字节。

## 为什么使用 Base64？

Base64 对于二进制数据表示非常重要，它允许二进制数据以看起来和行为上都像纯文本的方式来表示。这使得数据更可靠地存储在数据库中，通过电子邮件发送，或以基于文本的格式使用，如 XML。Base64 基本上用于以 [ASCII](https://en.wikipedia.org/wiki/ASCII) 字符串格式表示数据。

## Base64 编码

Base64 编码是将二进制数据转换为 64 个字符的有限字符集的过程。如第一节所示，那些人物分别是`A-Z`、`a-z`、`0-9`、`+`、`/`(真的是 64 个人物吗？数一数。)这个字符集被称为 MIME 的 Base64，被认为是最常见的字符集。前 62 个值使用`A-Z`、`a-z`、`0-9`、`+`和`/`，后两个值使用`+`和`/`。

Base64 编码数据最终比原始数据长，因此对于每 3 个字节的二进制数据，至少有 4 个字节的 Base64 编码数据。有时，您可能会看到类似于下图所示的奇怪消息，这很可能是在您查看带附件的原始电子邮件时产生的。您实际上看到的是 Base64 编码！(如果您注意到“=”，您可以断定这是一种 Base64 编码，因为等号用于编码过程中的填充):

```
 Content-Type: text/plain; charset=UTF-8
Content-Transfer-Encoding: base64

2 kfzhniz 2 yt type 9 mfini 52 ytzimd2 yug 2 yjysdit 2 yxyqsdyp 9 me 2 ytzhzinio 2 lhzg 9 in 2 krz H9 imdonctij 2 yjyrydzdmc 2 lcg 2 kfzhnin 2 lpyqtmb 2 lpyqtmb 9 ixini 52 yyg 2 kfzhnmf 2 ylysdix 2 kfyqidyp 9 me 2k/ysdin 2 lpzitinin 2 yqtmkiniq 2 ybytdit 2 yjzhihzhicharset=UTF-8 内容传输编码:base64 
```

Base64 通过以下步骤执行:

*   要编码的文本被转换成它的 ASCII 等价物(*即* a:97、b:98、*等*)。看这里的 ASCII 表。
*   上一步中获得的十进制值被转换成它们的二进制等效值(*即* 97: 01100001)。
*   所有等价的二进制数被连接起来，得到一大组二进制数。
*   大量的二进制数被分成相等的部分，每个部分只包含 6 位。
*   相等的 6 位组被转换成它们的十进制等效值。
*   十进制等效值最终被转换成它们的 Base64 值(*即* 4: E)。十进制数值和它们的 Base64 字母表可以在[这里](http://www.garykessler.net/library/base64.html)找到。

## Base64 解码

Base64 解码是通过颠倒上一节中描述的步骤来执行的:

*   字符串中的每个字符都被更改为其 Base64 十进制值。
*   获得的十进制值被转换成它们的等效二进制值。
*   从获得的每个二进制数中截取二进制数的前两位，并将 6 位组组合起来，形成一个大的二进制数字串。
*   在前一步骤中获得的大串二进制数字被分成 8 位组。
*   8 位二进制数被转换成十进制数。
*   获得的十进制值最终被转换成等价的 ASCII 码。

## 编码图像

足够的理论，让我们开始一些 Ruby 编码。我将使用这篇文章第一部分的大脑图像。要在 Ruby 中使用 Base64，我们必须做的第一件事是包含 [base64 模块](http://ruby-doc.org/stdlib-2.2.2/libdoc/base64/rdoc/Base64.html)，它是标准库的一部分:

```
require base64
```

为了对图像进行编码，只需使用 [`encode64(bin)`](http://ruby-doc.org/stdlib-2.2.2/libdoc/base64/rdoc/Base64.html#method-i-encode64) 功能。这个函数的 Ruby 文档说:

>>返回 bin 的 Base64 编码版本。该方法符合 RFC 2045。每 60 个编码字符添加一个换行符。

因此，为了对我们的图像进行 Base64 编码，我们可以执行以下操作:

```
 require 'base64'

#open the image file and treat it as a binary file

image = File.open('brain.jpg').binmode
image_read = image.read
image_64_encode = Base64.encode64(image_read) 
```

如果您想查看编码过程的输出，请键入以下内容:

```
puts image_64_encode 
```

## 解码图像

要使用 Ruby 解码图像，只需使用`decode64(str)`函数。这个函数的 Ruby 文档:

>>返回 str 的 Base64 解码版本。该方法符合 RFC 2045。基本字母表之外的字符将被忽略。

为了对上一节中编码的图像进行解码，请执行以下操作:

```
Base64.decode64(image_64_encode)
```

## 放在一起

让我们把 Base64 编码和解码一个图像的 Ruby 脚本放在一起。执行此操作的 Ruby 脚本如下所示:

```
 require 'base64'
image = File.open('brain.jpg').binmode
image_read = image.read
image_64_encode = Base64.encode64(image_read)
image_64_decode = Base64.decode64(image_64_encode)

#create a writable image and write the decoding result

image_result = File.open('brain_decode.jpg', 'w')
image_result.write(image_64_decode) 
```

打开`brain_decode.jpg`图像，注意这是我们在第一步中编码的原始图像`brain.jpg`。

## 结论

Ruby 是一种旨在让程序员开心的语言。它提供了许多工具来帮助我们的日常工作。这篇文章和系列文章的目标是教授一些 Ruby 的基础知识，同时关注一个在讨论 Ruby 时不太常见的领域。我希望您今天学到了一些关于如何使用 Tk 以及如何在 Ruby 中编码和解码图像的知识。希望你大脑的图像变大了一点。:)

## 分享这篇文章