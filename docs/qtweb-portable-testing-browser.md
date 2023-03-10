# Qt web——Webkit 测试的替代浏览器

> 原文：<https://www.sitepoint.com/qtweb-portable-testing-browser/>

如果我们只有一个浏览器来测试我们的网站，生活不是很无聊吗？这就像是 2002 年的重现！

在一个快速扩张的浏览器市场中， [QtWeb](http://www.qtweb.net/) 可能逃过了你的注意。这是一个开源项目，才推出了几个月，但它已经是第 2 版了，web 开发人员或依赖于可移植应用程序的人可能会对它感兴趣。

首先也是最重要的，QtWeb 是一个轻量级和敏捷的浏览器。安装包为 5Mb，有一个 ZIP 版本，可以安装在 USB 驱动器或任何其他位置。它可以在 2000 年以后的任何 Windows 版本上运行，大多数用户只需要一个 QtWeb.exe 可执行文件。

![QtWeb screenshot](img/ac63190e0493e566ed6137e4cd73cd88.png "QtWeb screenshot")

它的最低要求意味着 QtWeb 启动迅速，反应灵敏。界面让人想起 Firefox，但有点业余。也就是说，它很实用，图标很明显，工具栏可以随意移动——甚至在浏览器窗口之外。它拥有现代浏览器的所有标准功能:

*   搜索栏
*   书签导入和导出
*   选项卡和会话支持
*   下载管理
*   阻止弹出窗口、cookies、JavaScript 和图像
*   SSL、FTP 和插件支持

它甚至提供了一个独特的虚拟屏幕键盘，因此您可以输入安全的数据或密码，而不必担心键盘记录器。

作为一款便携浏览器，QtWeb 轻松击败竞争对手。将 exe 文件复制到 USB 驱动器，导入书签，你就有了一个可以在任何 Windows PC 上运行的安全浏览器。它比 Firefox 的便携版要快得多，也不那么耗费资源。

虽然很少有人会将 QtWeb 用作默认浏览器，但开发人员应该注意到，它使用了与苹果 Safari 和谷歌 Chrome 相同的 WebKit 渲染引擎(它比 Safari 4 Beta 版本略旧，但比 Chrome 更新)。在我的简短测试中，它呈现的页面与两者完全相同。它还提供了出色的网页检查器，这是 Chrome 所没有的功能:

![web inspector](img/22ab96dc1fe008115147b39e957fe99b.png "web inspector")

我对 QtWeb 印象深刻。开发者们确定了他们的定位，并没有对它接管浏览器市场做出任何假设。它非常适合便携式浏览，但也非常适合快速和肮脏的 WebKit 测试，无需在开发 PC 上安装 Safari 或 Chrome。

[访问 QtWeb 网站](http://www.qtweb.net/)

另请参见:

*   [IE8 评审，第一部分](https://www.sitepoint.com/ie8-review-1/)和[第二部分](https://www.sitepoint.com/ie8-review-2/)
*   [初看 Safari 4](https://www.sitepoint.com/safari-4-first-look/)
*   歌剧能流行起来吗？

你在用 QtWeb 吗？它是 Safari 或 Chrome 的可行测试替代品吗？它已经成为你的默认浏览器了吗？

## 分享这篇文章