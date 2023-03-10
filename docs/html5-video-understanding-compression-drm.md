# HTML5 视频:了解压缩和 DRM

> 原文：<https://www.sitepoint.com/html5-video-understanding-compression-drm/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

![A rough diagram of the video streaming process](img/7b9902a3e5cb757244a717dca773bbcb.png)

在之前的教程中，我给出了 HTML5 视频和许多我们可以使用的传输标准的高层次概述。在本教程中——使用 Azure Media Services 创建和消费 HTML5 视频系列的第二篇。我将介绍使用视频录制工具摄取内容的方法，以及加密或保护视频的方法，以便只有获得许可的观众才能观看。最后，我们将讨论压缩编解码器、容器和传输之间的区别。

你需要在 Azure 中创建一个媒体服务帐户，然后才能执行任何操作。这里有一个关于如何做到这一点的教程。如果你没有 Azure 账户，你可以[注册一个 30 天的试用版( $ 价值 200)](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13392-DEV-sitepoint-article16)，或者[联系我](mailto:dvoyles@microsoft.com)获得一个 [BizSpark 账户](http://davevoyles.azurewebsites.net/bizspark-free-software-cloud-services-o/?WT.mc_id=13392-DEV-sitepoint-article16)以获得团队中每个开发人员每月 $ 150，为期三年。

## 最近更新

以下是微软媒体服务产品的最新更新列表。

*   [现场编码](http://techcrunch.com/2015/04/13/microsoft-updates-azure-media-services-with-live-encoding-new-media-player-and-more/?WT.mc_id=13392-DEV-sitepoint-article16)

    *   可以与动态打包、动态加密、子剪辑、动态清单操作、广告标记插入和无缝直播点播功能相结合
*   [Azure 媒体播放器](http://azure.microsoft.com/en-us/services/media-services/media-player/?WT.mc_id=13392-DEV-sitepoint-article16)

    *   自动平台检测和回放

        *   为浏览器/设备选择最佳格式。
    *   统一的 HTML5 和 JS APIs

    *   播放受保护的内容

        *   AES 和播放就绪
    *   视频播放器调试

        *   日志记录、监控比特率流的质量、诊断信息

## 什么是 RTMP？

我们需要一种格式来从我们的视频录制工具中摄取内容，这将在下一个教程中介绍。为此，我们使用 RTMP 向 Azure 提供我们的内容。

实时消息协议最初是由 Macromedia(现归 Adobe 所有)开发的专有协议，用于在 Flash 播放器和服务器之间通过互联网传输音频、视频和数据。然而，今天它不再依赖闪存，我们将在今天的示例中看到这一点。

我在上一篇文章中讨论了几种流媒体形式，但我没有提到 RTMP。这种方法的缺点是它需要专门的网络服务器，这些服务器只传送用户当前正在观看的视频内容的帧。另一方面，自适应流式传输不需要特殊的服务器或协议。

这是一个把容器(. mp4，FLV)送到你的视频播放器的传输工具。媒体服务团队的资深项目经理坚克·丁吉洛格鲁给我们上了更深入的一课。

我们将使用 RTMP 从我们的录像机(笔记本电脑、手机或外部设备上的摄像头)摄取视频内容，并在我们的频道上分发。我们频道的观众可以通过各种方式接收视频，包括 HLS、平滑流或 MPEG-DASH。

## mpeg 4 和 h.264 有什么区别？

我听到的一个常见问题是“这两者有什么区别？”人们经常互换使用这两个词，但事实上它们是两个非常不同的东西。

### . MP4 (MPEG-4):容器(也可以是压缩编解码器)

MP4 无疑是目前最流行的视频格式，因为它允许在一个文件中保存音频、视频、字幕和图像的组合。此外，它可以在几乎所有设备上播放，将其他格式如 AVI、WMV、MOV 远远甩在后面；它可以在 YouTube 等许多在线视频网站上共享。它是用 MPEG-4 视频编码和 AAC 音频压缩来压缩的。

### H.264:一种视频压缩编解码器

H.264 是目前常用的视频编解码器之一，是一种流行的高清视频压缩技术。由于 H.264 可以以相对较低的比特率获得高质量的视频，因此它通常用于 AVCHD 摄录机、HDTV、蓝光和 HD DVD。MP4(.mp4)是 H.264 编码视频格式之一。

H.264，它具有比基本 MPEG-4 压缩更先进的压缩方法。H.264 的优势之一就是压缩率高。它的效率大约是 MPEG-4 编码的 1.5-2 倍。

因此，H.264 相对于 MPEG-4 压缩的两个主要优势是:

1.  1.较小的文件大小
2.  2.更好的实时回放视频质量

![A diagram of the structure of an mp4](img/803415756fa18be1e2753c5e738beeb9.png)

现在我们可以看到，MP4 是一种文件容器格式，而 H.264 实际上是一种视频压缩编解码器，需要一个视频容器来承载编码后的视频。

## 保护您的内容

所以你把你的视频放在互联网上，但是如果你想保护它不被盗版呢？我知道，我知道，[“信息想要免费”](http://www.amazon.com/Information-Doesnt-Want-Be-Free/dp/1940450284?WT.mc_id=13392-DEV-sitepoint-article16)，但是你仍然要为你的工作获得报酬。

你有没有试过在 Hulu、Xfinity 或 Amazon 等在线资源上观看视频，却发现你没有权限观看，需要升级订阅？他们通过加密标准来保护这些资产。

![An example of the Xfinity catalog](img/e963aba8d80e1d210ba4932fdcee3796.png)

他们通常可以通过令牌传递来实现这一点，令牌传递是一种加密对象，向应用程序或视频说明您有权访问什么，这被称为“特权”或 DRM。我今天在网飞偶然发现了一个 DRM 错误。

![An example of a Netflix DRM error](img/a28ec4db743fa826d80fbc4237cc619e.png)

让我们看看康卡斯特这样的大提供商，它拥有 Xfinity 点播目录。如果我没有登录我的账户就点击了一个视频，它可能会说*“HBO 不是你订阅的一部分。为了观看《副总统》，你需要订阅 HBO。”就在我选择节目之前，它清楚地表明我也需要订阅才能播放，这是由*“仅限 HBO 订户”文本和旁边带按键的*“播放”*按钮标记的。

![An example of the DRM restrictions from Comcast](img/174fc5b056a2cd633d52c6403ce46abc.png)

当我登录我的帐户时，康卡斯特给了我一个令牌，上面写着我是谁，我有权访问哪些服务。当我单击“Play”按钮时，令牌被传递给视频服务，视频服务打开令牌，查看我的权限，并声明我是否可以查看这些内容。

![An HBO DRM error](img/05425609598a8573e6ba07fbe90d3e1c.png)

## AES 与 PlayReady 加密

AES 是高级加密标准的缩写，并不限于视频内容，因为它也适用于电信、金融和政府通信。这适用于各种流协议，如 HLS、Smooth Streaming 和 MPEG-DASH。这是普渡大学标准的技术概述。

Microsoft PlayReady 是一种广泛的、工作室认可的加密技术，可以保护您的内容免受盗版，目前它在许多最流行的设备上受到支持。现在，您可以使用 PlayReady 来保护视频点播和直播流。

## 把所有的放在一起

现在我们已经了解了什么是压缩、容器和传输协议，让我们把它们放在一起。

在下一课中，我们将使用 RTMP 录制视频内容并摄取它，我们将使用 H.264 压缩标准对其进行编码，将其存储在. mp4 容器中，并使用上一教程中列出的各种协议之一通过我们的频道传输它。

![An expanded diagram of streaming components](img/e20e2852d293cd31ff6480802ebee244.png)

## 了解有关 Azure 媒体服务的更多信息

这里有一些很好的资源，可以让你更深入地了解媒体、Azure 以及如何快速设置:

*   [蔚蓝媒体服务 101——蔚蓝星期五(第九频道)](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-?WT.mc_id=13392-DEV-sitepoint-article16)
*   【Azure 媒体服务入门(视频)
*   [使用 Azure 媒体服务进行动态打包](http://channel9.msdn.com/Series/Windows-Azure-Media-Services-Tutorials/Introduction-to-dynamic-packaging?wt.mc_id=player?WT.mc_id=13392-DEV-sitepoint-article16)

或者我们团队关于 HTML、CSS 和 JS 的更广泛的学习系列:

*   [让你的 HTML/JavaScript 更快的实用性能技巧](http://channel9.msdn.com/Series/Practical-Performance-Tips-to-Make-Your-HTMLJavaScript-Faster/06?WT.mc_id=13392-DEV-sitepoint-article16)(从响应式设计到休闲游戏再到性能优化的 7 部分系列)
*   现代网络平台快速启动(HTML、CSS 和 JS 的基础)
*   [使用 HTML 和 JavaScript JumpStart 开发通用 Windows 应用](http://www.microsoftvirtualacademy.com/training-courses/developing-universal-windows-apps-with-html-and-javascript-jump-start?WT.mc_id=13392-DEV-sitepoint-article16)(使用您已经创建的 JS 来构建应用)

本文是微软网站开发技术系列的一部分。我们很高兴与你分享斯巴达项目的[和它的](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx?WT.mc_id=13392-DEV-sitepoint-article16)[新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=13392-DEV-sitepoint-article16)。在 [modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://modern.ie/?utm_source=SitePoint&utm_medium=article16&utm_campaign=SitePoint)。

## 分享这篇文章