# 开源图像存档:Exif，IPTC，XMP 等等

> 原文：<https://www.sitepoint.com/open-source-image-archiving-exif-iptc-xmp-and-all-that/>

与[相关的是，在建立数码摄影档案的背景下，这个黑客](https://www.sitepoint.com/ubuntu-add-ons-with-php/)开始认真研究向图像添加元数据的可用标准和开源工具。随着[阅读本](http://blogs.msdn.com/pix/archive/2006/08/16/702780.aspx)的进一步提示(嘿！MS 正在采用别人的标准！)，转储一些笔记…

## 照片存档:黄金法则

在图像中存储元数据*。*

实际上，这里的“元数据”一词应该更准确一些——就数字图像而言，本质上有三种重要的元数据类型，即谁创建了它以及它存储在哪里。

第一；当您拍照时，数码相机附加到图像上的内容(例如，相机品牌、拍照时间、曝光等。)，第二；您手动添加到图像中的元数据，通常在您将图像从相机下载到 PC 时添加(例如，照片拍摄地点、描述、关键字、摄影师姓名等)。)第三；“元数据分组”——图像集合如何相互关联(例如，它们都是一张照片的一部分，或者它们都是家庭照片)。出于讨论的目的，前两种类型的元数据可以被视为同一件事，您希望将这些信息存储在映像中。

第三种——“分组”信息是“相对的”,实际上，只有当它从图像外部存储时才能正常工作。这可能是“轻量级”的，就像文件系统上的目录树一样，使用基于时间戳的名称来存储图像，但是图像归档工具可能会在这方面提供额外的功能。重要的是，如果你想把一组图片放在像“家庭”这样的标题下，你也想在图片中存储一个关键字“家庭”。在这种情况下，图像存档工具可能会根据您的关键字自动建立群组。

但是您希望在映像中包含前两种类型的元数据，这样，如果您切换归档工具、将映像传输到另一个系统或者忘记备份归档数据库但记住了映像，它就不会丢失。一个像样的存档工具可能会从这些内嵌元数据中生成自己的数据库，以进行搜索等。高效，但最终的权威应该是图像本身。

### 嵌入式图像元数据标准

下一个问题是数据如何存储在图像中。数字摄影/处理中常用的文件格式，如 [JPEG](http://en.wikipedia.org/wiki/JPEG) 、 [RAW](http://en.wikipedia.org/wiki/RAW_image_format) 和 [TIFF](http://en.wikipedia.org/wiki/TIFF) ，允许在图像文件中存储额外的元数据。

这里有一个“避免过多细节”的观点:这里有三种重要的内联元数据格式——[Exif](http://en.wikipedia.org/wiki/Exif)、 [IPTC](http://en.wikipedia.org/wiki/International_Press_Telecommunications_Council) 和 [XMP](http://en.wikipedia.org/wiki/Extensible_Metadata_Platform) 。大多数数码相机使用 Exif 来附加信息，如拍摄照片时的曝光，还有一些工具可以手动(或自动)添加信息，如[地理编码](http://www.chebucto.ns.ca/~rakerman/geocode-photos.html)。IPTC“扩展”了 Exif，为元数据提供了一整套更具名称的“标签”。从技术的角度来看，这两者都很脆弱(容易破坏/损坏)，从扩展的角度来看，这两者都有问题(如果您想要存储的元数据没有命名的“标签”，那么您就不走运了)。同时，Adobe 的作品 XMP 是“圣杯”，它为存储元数据提供了 XML / RDF / [Dublin core](http://en.wikipedia.org/wiki/Dublin_Core) 的优点。它只是还没有得到很好的支持，但是已经开始被广泛接受。当然，我们的朋友对 XMP 也有自己的看法

在实践中，您需要能够使用所有三种格式进行有效的照片归档。

## 图像归档应用

有很多图像存档工具，适用于任何和所有操作系统。一个好的将允许你手动提供关于图像的额外元数据(例如，像“家庭”、“狗”等关键字。)但是，根据一项民意测验，大多数人将这些数据与图像分开存储在某种数据库中。就 Ubuntu 而言， [GThumb](http://en.wikipedia.org/wiki/GThumb) 就是这样做的——从映像外部存储任何用户提供的元数据(如果我没记错，它在`~/.gnome2/gthumb`下面)。

我认为这是根本错误的原因有很多，当图像离开您的文件系统(或者甚至更改目录/文件名)时，这些原因就变得很明显，再加上在图像归档领域没有“明显的赢家”,您很可能会多次更换工具。一句话:将元数据与它所引用的图像分开存储并不是这样做的。

在开源/ Linux 领域，有几个竞争者正在“做对”，即 [F-spot](http://en.wikipedia.org/wiki/F-spot) (C# / Mono)和 [jbrout](http://jbrout.python-hosting.com/) (python / wxpython)。两者都能够在图像中存储元数据，但都有局限性，例如 jbrout 不支持 XMP。一些值得一读的博客(和评论)是[如何管理 EXIF，linux gnome kde 上的 XMP 元数据](http://assente.altervista.org/?q=it/how_to_manage_exif_xmp_metadata_on_linux_gnome_kde)，[管理照片](http://norman.walsh.name/2006/07/16/managingPhotographs)和[Linux 中的照片组织](http://idea.zanestate.edu/archives/2005/11/photo-organizing-in-linux/)。

### 图像存档做得好

相比之下，IMO 的模型应用程序是 [PixVue](http://pixvue.com/) ，它可以正确地使用。

首先，它不是一个单一的应用程序，而是添加了一系列 Windows Shell 命名空间扩展，这意味着您可以在 Windows 资源管理器中处理您的图像-例如，右键单击并用您的元数据“注释”。

在我看来,“数字工作流程”,总的来说，都是关于程序的——你需要“面向流程”的应用程序来适应你的程序，而不是你的图像的单一窗口视图，它决定你如何工作。PixVue 的做法是正确的，因此我希望[扩展 Nautilus](https://www.sitepoint.com/ubuntu-add-ons-with-php/) 。

PixVue 还存储图像中几乎所有的元数据，理解 EXIF、IPTC *和 XMP*。例外是“分组”元数据，它提供了一种称为“图库”的东西，这是一种虚拟的目录层次结构——在 Explorer 中，您可以将图像拖放到一个图库(或多个图库)中进行组织，但这些只是指向文件系统上原始文件的“符号链接”。您还可以将图库结构导出为 XML，这样至少有机会实现可移植性。

此外，它提供的“模板”功能有助于尽可能快速、轻松地将元数据附加到大量图像中。

最后，它有出色的搜索功能(通过图库视图),而且它“集成”了 Windows 搜索和谷歌桌面(填充索引)。

PixVue 的唯一缺点是源代码不可用，而且*看起来不像是一个 API(它确实注册了一些 COM 组件，但是，从快速浏览来看，这些组件不允许你用 Python 脚本来驱动它)，所以如果你想扩展它来与一些地理编码工具集成，你就不走运了。*

## 开源图像元数据工具

那么，考虑到开源图像存档*应用*的不太理想的情况，有什么库的形式可用，所以你可以自己开发？

总之，更多的是坏消息。有一大堆库只能做一点点，也许只能(部分地)理解一种格式(通常是 Exif)，只读——不能写或者缺乏对制造商 Exif 扩展的支持。

对于 Python 来说，这种情况尤其严重，因为它非常缺乏——有一些“读者”,偶尔也有作者(虽然没有 XMP 作家),但没有什么是真正完整的。这对 jbrout 来说是个坏消息，对 60 系列手机上的 [Python 来说更是个坏消息，当你坐在火车上时，能够标记你的照片，将是一个杀手级应用。](http://wiki.opensource.nokia.com/projects/Python_for_S60)

不过也有例外，排名第一的是菲尔·哈维的 exiftool。ExifTool 不仅好，而且非常出色。它基本上什么都有了——EXIF、IPTC 和 XMP 支持(读/写),支持[制造商扩展](http://cpan.uwinnipeg.ca/module/Image::ExifTool::Sanyo),以及一个通用的标签名字典，在某种程度上，它是独立于元数据格式的。唯一的缺点是*看起来不像*(如果错了请纠正我)一个暴露 Exiftool 所有功能的 Linux GUI 前端——你不是在说命令行就是在写你自己的 Perl 脚本。

另一个值得注意的例外[伊万·亨特的](http://www.ozhiker.com/) [PHP JPEG 元数据工具包](http://www.ozhiker.com/electronics/pjmt/)，它拥有 EXIF、IPTC 和 XMP 的支持，并了解一些制造商的怪癖。API 也非常简单。

无论如何——希望这能节省一些研究时间。

## 分享这篇文章