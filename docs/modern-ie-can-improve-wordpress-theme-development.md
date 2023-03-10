# 使用现代的。IE 改进 WordPress 主题开发

> 原文：<https://www.sitepoint.com/modern-ie-can-improve-wordpress-theme-development/>

*本文由 [modern 赞助。IE](https://modern.ie/) 感谢你对使 SitePoint 成为可能的赞助商的支持！*

有许多流行的工具可以帮助主题开发者测试他们代码的质量。这不仅有助于减少主题中的错误，还有助于确保主题在多种设备和浏览器上可靠地呈现。

此外，WordPress 性能是一个如此热门的话题，当你开发你的主题时，识别潜在性能问题的能力是非常有用的。

在这篇文章中，我将解释什么是现代。IE 是什么，它如何帮助你开发你的 WordPress 主题，并提供更多信息的链接。我还将带你建立一个测试虚拟图像，这样你就可以在完整的 Windows 和 IE (Internet Explorer)浏览器上测试你的 WordPress 主题。

## 在你的 WordPress 主题中测试真实数据

WordPress 主题不仅仅是主页。同样，带有占位符文本的空主题不会真正给你的主题带来它所需要的锻炼。

一旦你的主题在生产中被真正的用户使用，他们会找到你可能从未想到过的方法来使用它，更不用说测试了。进入 [WP 测试(wptest.io)](http://wptest.io/) 。WP Test 提供了详尽的(你不可能测试所有的东西)测试数据，通过创建各种各样的内容来帮助模拟真实的使用情况；它非常令人印象深刻，非常适合测试主题(对于测试插件也很方便)。

![WP Test](img/b6d30c206f717cd4cb75abfc34c495e4.png)

如果你正在开发自己的 WordPress 主题，我强烈推荐使用 [WP 测试](http://wptest.io/)。

## 什么是现代。IE？

首先，对于那些不熟悉现代的人。IE 是微软提供的免费工具和资源的集合，旨在帮助网络开发者。现代的一个很好的特征。IE 是除了英语之外的其他语言版本。

![Modern.IE Home](img/4911491b76098e5d00cb8425cfebe94a.png)

在深入研究一些现代的测试工具之前，我先给你一个快速的概述。IE 提供。

## 现代。IE 工具

现代。IE 不仅仅是工具，它还是开发者的资源。也就是说，这些工具绝对是提供的核心特性，也是最有趣的玩法。

### 浏览器堆栈

BrowserStack 是一个流行的 web 应用程序，允许您在数百个虚拟机上测试您的主题。这是第三方服务，但是你可以通过 modern.IE 试用 BrowserStack 三个月。

BrowserStack 和类似的工具可以节省您在不同设备和浏览器上预览主题的时间。但是，如果您已经维护了自己的测试环境，您会对下一点感兴趣。

### 虚拟机映像

我们都意识到在多个浏览器版本上进行测试的重要性，除非您使用提供这种服务的测试平台(如 BrowerStack ),否则您可能会维护自己的虚拟机映像集。一个很好的现代资源。IE 提供了各种 Windows 和 IE 版本，你可以下载到你自己的环境中。

![Modern.IE Virtual Machine Images](img/df805489e6ed1a307404989838a75a2c.png)

虚拟化库涵盖了许多不同的 Windows 版本，以及从版本 6 到版本 8 的 IE。这些映像来自各种操作系统、虚拟化平台，在 Windows、Mac 和 Linux 上支持 VirtualBox。

以下是撰写本文时可用的主机操作系统和虚拟化平台:

#### Windows 操作系统

*   SP1 R2 Windows Server 2008 上的 Hyper-V
*   Windows Server 2012 和 Windows 8 专业版上的 Hyper-V，带 Hyper-V
*   适用于 Windows 7 的虚拟 PC
*   Windows 上的 VirtualBox
*   适用于 Windows 的 VMWare 播放器

#### 苹果个人计算机

*   Mac 版 VirtualBox
*   适用于 Mac 的 VMWare Fusion
*   Parallels for Mac

#### Linux 操作系统

*   用于 Linux 的 VirtualBox

以下是提供的 Windows 和 IE 版本:

*   IE11–Windows 8.1
*   IE10–Windows 8
*   IE11–Windows 7
*   IE10–Windows 7
*   IE9–Windows 7
*   IE8–Windows 7
*   IE7–Windows Vista
*   IE8–Windows XP
*   IE6–Windows XP

要安装这些，你必须下载自解压 RAR 档案。由于图像的大小，它们被分成几个文件，最好使用命令行工具 wget 或 [cURL](http://curl.haxx.se/) 下载。在下面的例子中，我将使用 cURL 为 Parallels for OS X 下载 Windows XP/IE6:

```
curl -O -L "https://www.modern.ie/vmdownload?platform=mac&virtPlatform=parallels&browserOS=IE6-WinXP&parts=0&filename=VMBuild_20131127/Parallels/IE6_WinXP/IE6.WinXP.For.MacParallels.sfx" 
```

接下来，我们需要运行自解压的 RAR 文件，但首先需要使它可执行:

```
chmod +x IE6.WinXP.For.MacParallels.sfx 
```

现在我们可以运行文件，这将提取我们的 Parallels 映像所需的 PVM 文件:

```
./IE6.WinXP.For.MacParallels.sfx 
```

输出应该如下所示:

```
RAR SFX archive

Extracting from ./IE6.WinXP.For.MacParallels.sfx

Creating    IE6 - WinXP.pvm                                           OK
Extracting  IE6 - WinXP.pvm/config.pvs                                OK
Extracting  IE6 - WinXP.pvm/config.pvs.backup                         OK
Creating    IE6 - WinXP.pvm/IE6 - WinXP.hdd                           OK
Extracting  IE6 - WinXP.pvm/IE6 - WinXP.hdd/DiskDescriptor.xml        OK
Extracting  IE6 - WinXP.pvm/IE6 - WinXP.hdd/DiskDescriptor.xml.Backup  OK
Extracting  IE6 - WinXP.pvm/IE6 - WinXP.hdd/IE6 - WinXP.hdd           OK
Extracting  IE6 - WinXP.pvm/IE6 - WinXP.hdd/IE6 - WinXP.hdd.0.{5fbaabe3-6958-40ff-92a7-860e329aab41}.hds  OK
Extracting  IE6 - WinXP.pvm/IE6 - WinXP.hdd/IE6 - WinXP.hdd.drh       OK
Extracting  IE6 - WinXP.pvm/parallels.log                             OK
Extracting  IE6 - WinXP.pvm/statistic.log                             OK
Extracting  IE6 - WinXP.pvm/VmInfo.pvi                                OK
Creating    IE6 - WinXP.pvm/Snapshots                                 OK
Creating    IE6 - WinXP.pvm/Windows Disks                             OK
All OK 
```

我们现在需要做的就是将图片导入 Parallels，然后我们就可以启动 Windows XP 和 IE6 了。

![Windows XP and IE6](img/e534577d36b9499394ef4435012e5818.png)

设置所有的图像需要一段时间，但最终你会在你自己的测试环境中覆盖所有的 Windows 和 IE 版本。

### 兼容性报告和站点扫描

兼容性报告/站点扫描(也称为 Compat 报告)是一个很棒的工具，它可以检查你的 HTML、CSS 和 JavaScript 是否有问题，或者可以改进你的主题代码的方法。它还会提醒您常见 JavaScript 库的过时版本。如果您使用的起始主题可能使用特定库的旧版本，这一点很重要。

![Compat Report](img/fcac0f6e164ad6c3ce57c8acbf1e6f91.png)

要使用兼容性报告，只需输入 URL 并单击扫描。

结果显示在逻辑部分中，可以展开这些逻辑部分以获得更多信息。这是一个很好的学习工具，因为它告诉你如何改进你的代码。

你可以通过电子邮件，甚至在脸书上分享 PDF 格式的完整报告(我敢肯定，你发送的是压缩内容，你的朋友会放心的:)。

报告看起来是这样的:

![Modern.IE Site Scan](img/f3706833962a4a2ca03d5bc4a775e238.png)

以下是一个示例报告的链接:

*   [https://modernie pdf . blob . core . windows . net/modernie-pdf/a21c 0076-b92a-4af 0-8c 29-6a 91562 F6 EB 4 . pdf](https://moderniepdf.blob.core.windows.net/modernie-pdf/a21c0076-b92a-4af0-8c29-6a91562f6eb4.pdf)

虽然使用在线版本的兼容性报告很方便，但像大多数这类远程工具一样，它们只对可公开访问的 URL 有效。这有时是一种痛苦，但幸运的是，对于那些需要私人测试的人或者如果你只是喜欢使用自己的工具，代码是开源的，可以在 GitHub 这里获得。

您可能还注意到，除了主页上显示的结果之外，还有两个额外的选项卡。

*   跨浏览器和设备获取您的网站截图
*   扫描现代 IE 不再支持的代码

![Modern.IE Tabs](img/8e32a28bd265a63c26a3042e7129d775.png)

我将简要介绍您将在这些部分中发现的内容。

#### 跨浏览器和设备获取您的网站截图

这是一种在各种平台和设备上获取你的网站截图的又好又快又简单的方法。这个特性使用 BrowserStack 来生成图像。与首页上的 BrowserStack 链接不同，这些功能会在 modern.IE 中生成九个图像的样本。

![BrowserStack Screenshot Example](img/44d1f6016577b8c029f416ae0f732e66.png)

与兼容性报告一样，您也可以共享这些结果。

#### 扫描现代 IE 不再支持的代码

这也被称为“Compat Inspector”。它本质上是一个由 IE 团队开发的 JavaScript 工具，检查以确保您的代码不包含任何已知的问题。

![Compat Inspector](img/72ac3ff06054c10448178ad2ea564517.png)

您可以使用 Compat Inspector 在这里找到关于[的更多信息。](http://ie.microsoft.com/testdrive/HTML5/CompatInspector/Default.html#)

Compat 检测器在现代中的应用。IE 是用酱实验室自动化的。他们提供了一个包含 130 多种浏览器的测试平台，并可以与第三方持续集成服务(如 Jenkins 和 Travis CI)集成。

### Status.IE

可在 [http://status.modern.ie](http://status.modern.ie) ，status 访问。IE 发布了 IE(和其他浏览器)支持的当前和未来功能的实现细节。您可以在[状态查看源代码并获得更多信息。IE GitHub 回购](https://github.com/InternetExplorer/Status.IE/)。

### IE 开发者社区

IE 开发者社区版块是 IE 所有东西的发射台。它列出了事件、IE 参考文档，以及 Twitter 和 StackOverflow 上基于 IE 的讨论的链接。

### 文章和开发人员资源

“[文章和开发者资源](https://modern.ie/en-us/articles)”部分是一个方便的最佳实践和 web 开发者资源的精选列表。

### IE 开发者频道

IE 开发者频道让你可以连接到 IE 工程团队，也可以下载 IE 的一个版本，让你测试即将到来的功能。

## 摘要

如果你正在开发你自己的 WordPress 主题，彻底的测试绝对应该是你工作流程的一个重要部分。现代。IE 为你提供了广泛的工具来帮助你测试你的代码，以及社区资源来帮助你跟上现代 web 开发的最佳实践。

现代还有很多。即比我所涵盖的，但希望你已经看到了足够的开始。如果你有兴趣深入了解现代。IE 和 WordPress 主题开发下面是一些很好的起点:

*   [https://www . site point . com/using-modern-ie-identify-common-coding-problems/](https://www.sitepoint.com/using-modern-ie-identify-common-coding-problems/)
*   [https://www . site point . com/testing-internet-explorer-modern-ie/](https://www.sitepoint.com/testing-internet-explorer-modern-ie/)
*   [http://make.wordpress.org/themes/](http://make.wordpress.org/themes/)
*   [https://www . site point . com/so-you-think-you-know-how-write-WordPress-theme/](https://www.sitepoint.com/so-you-think-you-know-how-write-wordpress-theme/)
*   [https://www . site point . com/store/build-your-own-wicked-WordPress-themes/(Book)](https://www.sitepoint.com/store/build-your-own-wicked-wordpress-themes/)

如果你有任何其他有用的主题开发资源，请在下面的评论区分享。

## 分享这篇文章