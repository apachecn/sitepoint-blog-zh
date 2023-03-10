# 第一眼:谷歌 Gears

> 原文：<https://www.sitepoint.com/first-look-google-gears/>

以下内容转载自《科技时报》第 166 期。

随着网络应用程序越来越成为我们日常生活琐事的无处不在的储存库([电子邮件](http://mail.google.com/)、[联系人](http://www.plaxo.com/)、[照片](http://www.flickr.com/)、[约会](http://calendar.google.com/)、[待办事项](http://www.tadalist.com/))，当你从互联网上拔下笔记本电脑的电源时，有一场幕后竞赛要让它们继续工作。来自 [Scrybe](http://iscrybe.com/) 、 [Dojo](http://dojotoolkit.org/offline) 、 [Zimbra](http://www.zimbra.com/blog/archives/2006/11/taking_zimbra_offline.html) 和其他人的演示承诺了伟大的事情，但随着 [Google Gears](http://gears.google.com/) 的推出，谷歌似乎后来居上赢得了这场比赛。

正如上周 SitePoint 网络技术博客报道的那样，谷歌宣布了[谷歌齿轮](http://gears.google.com/)的测试版，这是一个网络浏览器扩展，允许网络应用程序在用户断开互联网连接时继续工作。

Gears 可以在 Windows、Mac OS X 和 Linux 上的 Internet Explorer 和 Firefox 中运行，并最终支持 Safari。

Gears 远不是你所期望的即插即用的解决方案，它实际上只是一层相对底层的功能，使得离线网络应用成为可能。许多艰苦的工作仍然取决于你。

Gears 提供了三个特性，您可以使用 JavaScript 代码在 web 应用程序中访问和控制这些特性:

*   注册一个 URL 集合，Gears 会下载并为用户保留一个本地副本。然后，用户的浏览器将透明地接收这些本地副本，离线时仍可访问，而不是获取在线版本。本地副本可能被设置为自动更新，或者仅在您需要时更新。
*   **数据库**
    将数据存储在关系数据库中，就像在服务器端代码中一样，除了你的每个用户都有自己的数据库。您还可以通过 JavaScript 代码更快地访问这个数据库，因为数据库存储在用户的计算机上。当用户离线时，本地服务器缓存的 JavaScript 代码仍然可以访问它。
*   《Gears》中最无趣的部分，除非你像我一样是个十足的 JavaScript 怪胎。本质上使 JavaScript 成为多线程的，使您能够在后台运行耗时的操作(如解析大型 XML 文档，或将大量数据下载到 Google Gears 数据库中)，而不会冻结浏览器或导致脚本执行时间警告对话框出现。

有了这些工具，您不仅可以构建使用 HTML、CSS、JavaScript 和图像文件的本地副本继续工作的应用程序，而且可以更快地工作，因为它们可以在本地存储特定于用户的数据，而不必一直从服务器检索这些数据。

毫无疑问，谷歌的全方位在线服务将很快得到 Gears 支持的离线支持的增强。然而，首先，谷歌只开始了其中一项服务:[谷歌阅读器](http://reader.google.com/)。安装 Gears 后，你现在可以将 Reader 切换到离线模式，此时它会从你订阅的订阅源中下载多达 2，000 篇文章供你离线阅读。这个手动开关工作得足够好，但它确实依赖于用户在离线前记得点击它。

谷歌选择用手动开关启动 Reader 离线模式的主要原因是因为构建离线应用程序并不容易。拥有本地副本的自由带来了保持它们与服务器端资源同步的负担(否则用户将无法从另一台计算机访问他们的数据，您还不如构建一个桌面应用程序)。这是一个不小的问题，而且最合适的解决方案会因项目而异。在 [Gears 开发者网站](http://code.google.com/apis/gears/)上有[一整篇关于所涉及问题的文章](http://code.google.com/apis/gears/architecture.html)。

不，Gears 并没有让这些事情变得容易，但它确实让这些事情变得可能，而且就目前而言，这可能已经足够创新了。毫无疑问，建立在这些基础上的易于使用的应用程序框架已经上路了。

## 分享这篇文章