# 云存储:在 Drobpox、Drive、S3 和其他之间选择

> 原文：<https://www.sitepoint.com/cloud-storage-for-you-and-your-business/>

![Cloud Storage for You and Your Business](img/0ff171d9ffd789288fdcc432ca799ded.png)

无论是为您非常成功的应用程序提供高吞吐量存储，还是为您的业务和项目文件提供归档，甚至是为共享数据提供轻松集成的桌面服务，总有一种服务可以满足您处理 100GB、100TB 或 100PB 的需求。

在我们上一期关于云服务的文章中，[AWS、Google Cloud 和 Azure](https://www.sitepoint.com/a-side-by-side-comparison-of-aws-google-cloud-and-azure/) 的并排比较，我们概述了在云中可以做什么，涵盖了计算、分析和网络等主题。在本文中，我们将更深入地探讨[云存储](https://en.wikipedia.org/wiki/Cloud_storage)，从家庭用户非常实际的小需求(桌面服务)开始，并使用相同的技术扩展到业务需求。

## 什么是“云”？

### “老”的方式

不到十年前，当你需要上传一些东西到互联网上时，你会雇佣一家托管公司来租用一定数量的在线空间。如果你想的话，你可以做一个额外的文件备份，以防服务器出了什么问题。当然,*和*在服务器上发生的事情:硬件故障(甚至看起来[硬盘驱动器](https://en.wikipedia.org/wiki/Hard_disk_drive) (HDD)注定要坏掉)，黑客(如果你曾经有一个共享主机的帐户，你就会知道每隔几个月就会有一些外国国旗出现在你的网站上)，以及总是无意但笨拙的文件覆盖或删除(拜托，我们都这样做过)。

现在，如果你真的*喜欢，你会立刻将你的文件部署到不同的位置，以确保冗余和可用性，并最小化全球范围内的[延迟](https://en.wikipedia.org/wiki/Latency_(engineering))(即，你请求一个文件和你最终访问它之间的时间量)。然而，*以正确的方式做事情不仅昂贵且耗时，而且需要相当多的技术知识*(我们甚至没有讨论如何进行负载平衡，以便在高流量情况下您的文件仍然可用)。*

### 新的方式

今天，当你上传一个文件到一个服务，比如 Google Drive，iCloud，亚马逊 S3 或者一个[托管网格](https://en.wikipedia.org/wiki/Grid_computing)，这个文件*会在多个服务器上自动复制，并且经常复制到多个位置*——这样当你在日本或者美国东海岸试图访问你的文件时，你的请求会自动路由到最近的可用数据池。*那个*是云。

在这种新架构下，如果服务器或数据中心中断，您可能永远也不会发现，因为流量将透明地重定向到下一个可用的数据池。即使服务器被破解，攻击者也不会像在传统托管服务中那样接触到常规文件，因为在许多情况下，数据以某种方式被隐藏，甚至被加密，实际上是您的请求构建了文件。

请注意，这项如今几乎无处不在的技术仅在十年前取得了突破:

*   亚马逊 S3，于 2006 年推出
*   Dropbox，成立于 2007 年
*   iCloud 于 2011 年推出
*   谷歌驱动，2012 年推出
*   …等等。

### 您已经在使用它了

有些人(好吧，我的前女友)对使用云犹豫不决。“云？”他们说。“不用了谢谢！”他们不喜欢让他们的文件“无处不在”的想法，当你这样说时，听起来确实很尴尬。但是正如我们提到的，并不是到处都是“你的文件”，而是可以根据你的请求构建你的文件的数据。如果你使用 Gmail 或 Yahoo Mail 等服务(这是“如果你在使用互联网”的另一种说法)，*你已经在使用云*，你会意识到这有多方便。

进一步解释:你们中的一些人甚至不记得有一天，当电子邮件用客户端程序下载到一台机器上时，如果那台计算机出了什么问题，所有的东西都会丢失，如果在其他地方没有这些文件的备份的话。Gmail 或 Yahoo Mail 只是电子邮件的云版本——一种无处不在、安全可靠的服务，被复制到世界各地用于即时访问……是的，云！

## 桌面服务

有大量的服务，但在这次调查中，我们将列出一些已经运行了几年的服务，它们提供全功能的免费计划，而不仅仅是有限的试用期。其中一些服务有特定的工具，或者与某些类型的存储服务集成在一起，例如音乐文件、图片和文档，我们将在本文后面详细介绍。( **w** 是维基百科条目的链接。)

| 服务 |  | 文件（documents 的简写） | 生产信息控制系统 | 音乐 | 自由的 | 计划 |
| --- | --- | --- | --- | --- | --- | --- |
| [亚马逊大道](https://www.amazon.com/clouddrive/) | [w](https://en.wikipedia.org/wiki/Amazon_Drive) | 不 | [是](https://www.amazon.com/clouddrive/primephotos) | [是](http://www.amazon.com/digitalmusic) | 5GB | [**无限/$ 59.99/年**](https://www.amazon.com/clouddrive/getprime) |
| [苹果 iCloud](http://www.apple.com/icloud/) | [w](https://en.wikipedia.org/wiki/ICloud) | 不 | [是](http://www.apple.com/icloud/photos/) | [是](https://support.apple.com/en-us/HT204146) | 5GB | [50GB/$ 0.99/月；1TB/$ 9.99/月；还有更](http://www.apple.com/icloud/) |
| [iDrive](https://www.idrive.com/) | [w](https://en.wikipedia.org/wiki/IDrive_Inc.) | 不 | 有限的 | 不 | 5GB | [1TB/$52.12/yr](https://www.idrive.com/pricing) |
| [谷歌驱动](https://www.google.com/drive/) | [w](https://en.wikipedia.org/wiki/Google_Drive) | [是](https://www.google.com/docs/about/) | [是](https://photos.google.com/) | [是](https://play.google.com/music/listen) | **15GB** | [100 GB/$ 1.99/月；1TB/$ 9.99/月；还有更](https://www.google.com/settings/storage) |
| [Dropbox](https://www.dropbox.com/) | [w](https://en.wikipedia.org/wiki/Dropbox_(service)) | [是](https://www.dropbox.com/paper) | 有限的 | 不 | 2GB | [1TB/9.99 美元/月](https://www.dropbox.com/plans) |
| [OneDrive](https://onedrive.live.com/about/) | [w](https://en.wikipedia.org/wiki/OneDrive) | [是](http://office.microsoft.com/) | 有限的 | [是](https://music.microsoft.com/) | 5GB | 50GB/1.99 美元/月；**1TB/6.99 美元/月** |

注意你是如何计算空间的！您在同一提供商上使用的所有空间将被视为一个资源。例如，如果您在 Gmail 上使用 5GB，在 Google Drive 上存储的文件上使用 2GB，这意味着您总共使用了 7GB，因此如果您使用免费层，您的可用空间将为:

```
15GB (total space) 
    - 5GB (from Gmail) 
    - 2GB (from Google Drive) 
        = 8GB available 
```

![iCloud](img/f529cf5b644cb49a9a3677e8a914e92f.png)

**[iCloud](http://www.apple.com/icloud/) 更多的是 iPhone 和 Mac 的备份服务**而不是全功能的云服务，因此不能用于非苹果产品。事实上，苹果在互操作性方面名声不佳，虽然在苹果环境中很方便，但如果你需要换成 Android 或暂时换成 PC，你会遇到很多问题。

![OneDrive](img/e36926c6985d7c54980040abebf4c5e4.png)

另一方面， **[OneDrive](https://onedrive.live.com/about/) (以前的 SkyDrive)并不完全是 Windows 工具**，尽管它是由微软开发的。您可以在 Mac、iPhone 和 Android 手机等 Linux 环境中自由使用它。

### 文档

生产力的领域当然是办公文档。您可以将这些文件存储在*任何*存储服务中。毕竟，它们只是文件。然而，我们在这里列出的服务，除了存储之外，还可以让你在那里管理文档，允许用户创建、编辑和共享文档。

![Google Drive](img/d1d0f223b0638353faee27a0c68f93f0.png)

[Google Drive](https://www.google.com/drive/) 是谷歌[文档](https://www.google.com/docs/about/)、[表单](https://www.google.com/sheets/about/)、[幻灯片](https://www.google.com/slides/about/)和[表单](https://www.google.com/forms/about/)的大本营。它们都是成熟的工具，你可以通过你的浏览器无限制地使用*和免费的*。谷歌真的设法让实时协作编辑成为其办公套件的一大亮点。

![Office 365](img/54a6ae7d9d2e7b1d4e74053b033d5793.png)

虽然姗姗来迟，但微软决心加入生产力工具的在线俱乐部。你可以将你的 Office 文档存储在 OneDrive 中，并通过 [Office Online](http://office.microsoft.com/online/) 、 [Office 365](http://office.microsoft.com/) 的免费版本进行*有限的*编辑和免费可视化。全功能产品的[许可方案](https://products.office.com/en/buy/office)在这里复制起来太复杂了，但是如果你需要的是对微软 Office 产品的完全支持，可以看看它。

![Dropbox logo](img/e1a10d0581c7fc0748733bbce8eb9b49.png)

[纸张](https://www.dropbox.com/paper)是由 [Dropbox](https://www.dropbox.com/) 提供的一体化工作解决方案。这是一个相当有限的工具，特别是与谷歌的工具或成熟的 Office 365 相比。但是[它的简单性](https://www.youtube.com/watch?v=XDXgXQFEF3M)可能也是它的主要优势，因为它是一个使用起来非常简单的工具，几乎不需要培训。它支持协作、丰富的格式，当然还有 Dropbox 附件。

![iWork](img/4a3f50a8f5e8d7a3cdf32a5525ea17e3.png)

有一些解决办法和计划将 iCloud 与 iWork(苹果的 T2 办公套件)整合，这将为 iCloud 服务带来文档编辑功能。但是现在，我们认为它是一个缺失的特性。

### 图片

如果有备份服务，你应该现在就开始使用*，就是它了。令人难过的是，时至今日，许多家庭仍将珍贵的记忆保存在台式机或笔记本电脑的硬盘上。如果我之前没说清楚，我现在试着说清楚:**电脑的硬盘(尤其是传统的 HDD)是一个即将爆炸的炸弹**。即使你有一个更可靠的[固态硬盘](https://en.wikipedia.org/wiki/Solid-state_drive) (SSD)，你的笔记本电脑仍然可能被盗，感染病毒，或者消失在蓝色中(为什么不呢？).*

 *说到存储和分享图片，对你们中的一些人来说，脸书可能做得还不错，但它确实缺乏功能:你不能订购照片，不能按日期搜索，也不能编辑。如果有一天你决定彻底摆脱脸书会怎么样？

作为一个解决方案，我们将在这里介绍的所有服务将自动为您上传您在手机上拍摄的或从相机同步到计算机的每张新照片或视频，从而使您能够快速清理空间，并允许您稍后选择保留或不保留。

| 服务 |  | 自由空间 | 计划 |
| --- | --- | --- | --- |
| [亚马逊 Prime 照片](https://www.amazon.com/clouddrive/primephotos) | [w](https://en.wikipedia.org/wiki/List_of_Amazon.com_products_and_services#Amazon_Prime) | 5GB | [**无限/$ 59.99/年**](https://www.amazon.com/clouddrive/getprime) |
| [Flickr](https://www.flickr.com/) | [w](https://en.wikipedia.org/wiki/Flickr) | **1TB** | [2TB/每月 5.99 美元](https://www.flickr.com/account/upgrade/pro) |
| [谷歌照片](http://photos.google.com/) | [w](https://en.wikipedia.org/wiki/Google_Photos) | **无限制** | [100 GB/$ 1.99/月；1TB/$ 9.99/月；还有更](https://www.google.com/settings/storage) |
| [iCloud 照片图库](http://www.apple.com/icloud/photos/) | [w](https://en.wikipedia.org/wiki/Photos_(Apple)#iCloud_Photo_Library) | 5GB | [50GB/$ 0.99/月；1TB/$ 9.99/月；还有更](http://www.apple.com/icloud/) |

Dropbox 曾经捆绑了一个名为 Carousel 的不错的照片服务，不幸的是，他们在 2016 年初决定关闭这个服务。它们仍然支持从手机自动上传照片，并通过应用程序或浏览器进行可视化，但功能远不及这些其他服务。到目前为止，微软还没有将 OneDrive 与专门的照片服务集成在一起。

![Flickr](img/23301917cf74c71aa5a29d3fbb342c5b.png)

可以说，Flickr 是*分享照片的*之地，拥有最大的专业和业余照片爱好者社区，一个自 2004 年就开始运营并增加功能的网站，以及出色的组织工具和隐私设置。

![icloud photo library](img/33ed36d02041dcf4a2ddb3deffc6b716.png)

iCloud 附带了一个名为 [iCloud 照片库](http://www.apple.com/icloud/photos/)的工具，Google Drive 集成了 [Google Photos](http://photos.google.com/) (之前的 Picasa 网络相册)。两者都是全功能的照片服务，可以标记人物、分享和创建相册，甚至编辑图片，还有许多其他功能。

![Google Photos](img/8c1297e93e69c28ac2c2c951d2feea42.png)

一个巨大的好处是，[谷歌照片](http://photos.google.com/)免费给你*无限的图片存储空间*(也就是说，不从你的谷歌驱动账户中减去空间)，只要你不需要存储分辨率高于 1600 万像素的图片原件。如果你选择这个选项，它会把你的高清图片缩小到 2048×2048(换句话说，可以满足大多数家庭使用)。

![Amazon Prime](img/ab1e8126e28249d81a33f24f1b499c2c.png)

一个相当有限的产品，几乎没有组织工具，也没有搜索功能， [Amazon Prime Photos](https://www.amazon.com/clouddrive/primephotos) (付费)在存储方面是最慷慨的*——对所有图像无限制，包括高清图像*

 *### 音乐

您还可以使用云来存储翻录的 CD 和 MP3 文件。你将有这样的好处，可以用你的手机或电脑从任何地方免费播放你的音乐，当你想把你的音乐带到其他地方时，完全忘记把你的文件存在 USB 驱动器和其他媒体上的想法。

除了流媒体，所有这些服务都允许您在一定的限制下上传自己的音乐收藏:

| 服务 |  | 免费上传 | 订阅 | 计划 |
| --- | --- | --- | --- | --- |
| [亚马逊音乐](http://www.amazon.com/digitalmusic) | [w](https://en.wikipedia.org/wiki/Amazon_Music) | Two hundred and fifty | **25 万** | [$ 25/年](https://www.amazon.com/gp/help/customer/display.html?nodeId=201379280)(仅存储) |
| [凹槽音乐](https://www.microsoft.com/groove) | [w](https://en.wikipedia.org/wiki/Groove_Music) | 高达 5 GB | **无限制** | 7 美元/月(流*和*存储) |
| [Google Play 音乐](https://play.google.com/music/listen) | [w](https://en.wikipedia.org/wiki/Google_Play_Music) | **5 万** | Fifty thousand | [$ 9.99/月](https://play.google.com/music/listen)(流媒体*和*存储) |
| [iTunes Match](https://support.apple.com/en-us/HT204439) | [w](https://en.wikipedia.org/wiki/ICloud#iTunes_Match) | 不 | One hundred thousand | [$ 25/年](https://support.apple.com/en-us/HT204146)(仅存储) |

这些服务中的一些还包括家庭套餐，以便不同的用户可以以较低的费用同时访问该服务。Google Play 音乐家庭计划就是这种情况，允许最多六个家庭成员以 14.99 美元/月的价格无限制访问。

请注意，当您支付流媒体订阅费时，您还可以访问数百万首高清音频歌曲。但是正如我们在这里提到的，这并不是将你自己的音乐文件上传到云端的必要条件。

谈到音乐订阅服务，我们不会讨论诸如 [Apple Music](http://apple.com/music/) 、 [Spotify](https://www.spotify.com/) 、 [Deezer](http://www.deezer.com/) 或 [Tidal](http://tidal.com/) 之类的服务，因为这些服务严格用于[流媒体](https://en.wikipedia.org/wiki/Streaming_media)(也就是说，你需要付费才能点播流媒体音乐)，没有上传和存储自己的音乐库的选项。

![Google Play Music](img/66f40010b206369eb34c40cf471cb1a7.png)

[谷歌](https://play.google.com/music/listen)允许通过[音乐管理工具](https://support.google.com/googleplay/answer/1075570)将本地音乐上传到云端。Google Play 音乐的订阅还包括 [YouTube Red](https://www.youtube.com/red) ，这是 YouTube 的无广告版本，同时还可以访问 YouTube Red 原创系列和电影。

![Amazon Music](img/a3235bc4008a48830ba1cd3d6281757e.png)

亚马逊音乐也允许上传音乐，但要通过应用程序。虽然[亚马逊关闭了其音乐导入工具](https://techcrunch.com/2015/10/07/amazon-shuts-down-its-music-importer-software-points-users-to-amazon-music-app-instead/)，但他们并没有完全摆脱上传音乐的选项，现在[可以通过亚马逊音乐应用](https://www.amazon.com/gp/help/customer/display.html?nodeId=201379280)来完成。

![iTunes Match](img/7b9e5fb64d125d13fbeccc1f7c5a0cab.png)

[iTunes Match](https://support.apple.com/en-us/HT204146) 会自动尝试*将你的本地收藏与 iTunes 上的内容进行匹配*，它会自动将匹配的内容添加到你的在线图书馆，而无需你上传任何实际文件。但是，您需要付费才能使用该功能。

![Groove Music](img/be5c5fa0fd0edbed5e917651573412c3.png)

将文件上传到 [Groove Music](https://music.microsoft.com/) 有点麻烦，你需要记住空间。首先，*无法像 iTunes 一样匹配*你的收藏，所以你需要单独上传文件或将`Music`文件夹同步到 OneDrive。其次，与前面提到的服务不同，这里您将使用云存储帐户的空间，在本例中为 OneDrive。但是，如果您已经为 OneDrive 订阅付费，这可能适合您；如果你支付 7 美元/月的 Groove Music 订阅费，情况也是如此。

## 商业存储

对于那些喜欢我们描述的桌面服务的简单性和实用性，并希望将*带到工作环境中的人来说，有一些非常有趣的选择。*

这些服务对公司来说可能非常有吸引力，因为我们已经描述过的一些功能加上一些额外的功能:

*   **轻松的在线协作**(这对企业来说是一大优势)
*   **没有[个 API](https://en.wikipedia.org/wiki/Application_programming_interface)来集成**；
*   **一般来说，不需要对用户进行额外的培训**(例如，如果你已经使用了谷歌文档，你就可以开始了)
*   **管理员控制面板**(管理用户访问、空间配额和其他功能)。

| 服务 | 空间 | 每个用户的计划 |
| --- | --- | --- |
| [框](https://www.box.com/) | 100GB(入门)；**无限制**(商务) | 4 欧元/月(起始价)；每月 12 欧元(商务) |
| [iDrive 业务](https://www.idrive.com/small-business) | 250GB(个)；1.25TB(摄氏度) | 74.62 美元/年(年)；374.62 美元/年 [ **无限用户** |
| [Dropbox 业务](https://www.dropbox.com/business) | **无限制** | [$ 15/月](https://www.dropbox.com/business/plans-comparison) |
| [Google Drive for Work](https://gsuite.google.com/driveforwork/) | 1TB(少于 5 个用户) | [$ 10/月](https://gsuite.google.com/driveforwork/) |
| [OneDrive for Business](https://onedrive.live.com/about/business/) | 1TB(方案一)；5TB(计划 2) | [**【5 美元/月(方案一)】**；每月 10 美元(计划 2)](https://products.office.com/onedrive-for-business/compare-onedrive-for-business-plans) |

评估这些服务的总成本时要小心，因为您需要考虑存储需求和员工数量。

## 用于网站、应用和归档的云存储

当然，也有面向为网站和应用程序存储对象(T1)的服务(T0)，以及在线存档(T3)的服务(T2)，甚至是建立内容传输网络(T5)。关于这些服务的细节，你应该看看“[AWS、Google Cloud 和 Azure](https://www.sitepoint.com/a-side-by-side-comparison-of-aws-google-cloud-and-azure/#storage) 的并排比较”的“存储”部分。

## 结论

我们已经看到了很多使用云来存储数字资产的选择，包括免费的和功能非常丰富的。如果你是一个想要保持简单的家庭用户，你最好选择一个桌面服务并坚持使用它来满足你的所有需求。此外，或者作为一种选择，您可以为特定类型的存储选择特定的服务(比如，Flickr 的图片)。在某些情况下，你可以顺利地将这些实践和工具转移到你的公司。

在任何情况下，都不要冒险丢失您认为重要的文件和信息，并将您的音乐、图片和文档保存在一个安全、可访问的地方。云是你的朋友。

## 分享这篇文章**