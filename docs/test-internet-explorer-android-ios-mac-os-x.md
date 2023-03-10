# 如何在 Android、iOS 和 Mac OS X 上测试 Internet Explorer

> 原文：<https://www.sitepoint.com/test-internet-explorer-android-ios-mac-os-x/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

今年早些时候，微软团队[发布了一个新工具](http://blogs.msdn.com/b/ie/archive/2014/11/02/announcing-remoteie-test-the-latest-ie-on-windows-mac-os-x-ios-and-android.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)，使得在 IE 中测试网站变得更加容易，不管你在哪个平台上(说真的)。这是他们在斯巴达项目和[上工作的一部分，他们的新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=12779-DEV-sitepoint-link)和[新用户代理字符串](https://msdn.microsoft.com/en-us/library/ie/hh869301%28v=vs.85%29.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)，这是 Trident 的一个分支，与移动网络的互操作性更强。

在本教程中，我想演示一下这在我的 MacBook 上的 Chrome 中是什么样子，以及如何设置它。

TL；博士？这里有一些藤蔓向你展示它的作用:

*   mac os x

[https://vine.co/v/OO79UZbQIHD/embed/simple](https://vine.co/v/OO79UZbQIHD/embed/simple)

*   机器人

[https://vine.co/v/OO7V2TZ1rp3/embed/simple](https://vine.co/v/OO7V2TZ1rp3/embed/simple)

*   ios

[https://vine.co/v/OO7VplXMIhW/embed/simple](https://vine.co/v/OO7VplXMIhW/embed/simple)

*   Win7

[https://vine.co/v/OO7YKajmrp3/embed/simple](https://vine.co/v/OO7YKajmrp3/embed/simple)

该工具名为 [RemoteIE](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=content&utm_campaign=SitePoint) ，旨在提供 IE 最新版本的虚拟化版本。这允许你测试最新版本的 IE，而不必安装虚拟机。如果你想测试 IE 的旧版本，你可以随时使用 [modern 上的免费虚拟机。即](http://modern.ie/?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint)从[这里开始](https://www.modern.ie/en-us/virtualization-tools?utm_source=SitePoint&utm_medium=content&utm_campaign=SitePoint#downloads)。

## 正在设置

我自己运行了使用该工具的步骤，并希望记录所有的事情，以防您遇到任何问题。

首先，进入 http://[remote . modern . ie](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=content&utm_campaign=SitePoint)/网站，它会带你进入这个页面:

![Select a Server](img/b038972dbbc37e820467f61031595a10.png)

这时你会被要求下载你想要的任何平台的微软远程桌面应用程序。这可能是为了:

*   麦克·OS X

*   iPhone 或 iPad

*   机器人

*   Windows x86 或 x64

*   Windows RT

![Application Permissions](img/41009734e53fa6b282c76e54ee078948.png)

如果你有 Live.com 或 Outlook.com，你可以使用它，或者你可以注册一个新的。不，如果你不想做其他事情，你不需要使用这些服务，但它们实际上已经变得更好了，可能值得一看。

接下来，您需要选择离您最近的服务器，以便获得最佳性能:

![Remote Homepage](img/1c6249e092a14393df83b2510ed654e6.png)

您需要一个 Microsoft 帐户来使用该服务，因为它需要将该服务关联到该帐户。

![Download App](img/eaf582a2416bf081cb48f65dd12bf49e.png)

如您所见，当我说这将跨平台可用时，我是认真的。在 Mac 上，从 Apple App Store 下载应用程序。点击“Mac”链接会将你导向到[在线苹果商店网站](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417)。

![App Store Preview](img/2e8725762ff865f822f8c81234c011ad.png)

单击“在 Mac App Store 中查看”按钮，以便您可以在 Mac 上启动 App Store 应用程序。Chrome(或者你最喜欢的 OS X 浏览器)会给你一个确认通知，让你启动这个外部应用:

![Chrome External Protocol Request](img/447e3938a459ae74420440232661fac4.png)

确认后，您将进入 App Store 入口:

![App Store Entry](img/fa169ab141b9146e6bff33e44297d6e8.png)

在我的情况下，我已经安装了应用程序，这就是为什么它显示“打开”。如果您还没有安装它，请继续安装。安装后，在 Finder 中查找它:

![The App in Finder](img/e5d4ca6437234c996dabc6beb9ce57c8.png)

或者，如果你像我一样，使用令人敬畏的[阿尔弗雷德](http://www.alfredapp.com/?WT.mc_id=12833-DEV-sitepoint-othercontent)找到它:

![App in Alfred](img/d99716e3be57c8abe78459f627a7c757.png)

现在，下一步是为什么我想创建这个教程，因为它不是立即显而易见的，一旦你运行远程桌面做什么。当你启动应用程序时，如果你看一下标题，你会看到一个名为 **Microsoft RemoteApp** 的条目。这就是你想要点击的:

![RemoteApp Launch Screen](img/574af3b56545b032204c301ec624c06f.png)

接下来，系统会询问您的 Microsoft 帐户信息，以确定您可以订阅哪些应用程序:

![Loading Subscriptions](img/547f4e5a63d4c80285b014cde8822bff.png)

![Signing In to Microsoft App](img/aa379b4ffe00b783efe06354591bea54.png)

![Microsoft Account Details](img/c9939273c38fdfd56e33f58539ffa5fb.png)

现在已经知道你是合法的了，你会看到一个对话框显示你的应用程序订阅是什么:

![App Subscriptions List](img/859688b32150046f696c3e4a3a917ac9.png)

同样，我想帮助你避免混淆，因为 UX 在这一点上有点偏离。当你点击“Internet Explorer(email:iewebeco @ microsoft . com)”的复选框时，一个关于 **Internet Explorer- > IE 技术预览版**的条目将被添加到主 Microsoft 远程桌面应用程序中，但是带有我刚才提到的复选框的对话框并没有消失。看这里:

![Close Preview](img/061cbb7da869146ba95af34bcaece3a3.png)

一旦你在主应用程序中看到“IE 技术预览版”的条目，你就可以关闭带有复选框的对话框。你可以在前面的图片中看到我是如何突出显示关闭对话框图标的。

我们快完成了。接下来，继续双击“IE 技术预览”来启动你的 IE 虚拟化版本。只需一分钟即可完成所有工作，请耐心等待:

![Virtualized Preview Loading](img/e611e82ca5d42d67547b7d4554d86a7e.png)

一旦完成，你就有了一个完整版本的 IE 11 技术预览版。请注意下图中 F12 开发人员工具是如何为您准备的:

![Preview Opened](img/80155012fc1e4ba0fc551fe7d1234181.png)

## 更多测试工具

这是一个很棒的新工具，它肯定会降低在最新版本的 IE 上测试的摩擦，但是有一些限制应该注意，包括无法访问本地文件系统。如果可以的话，那当然很好，但是虚拟机可能很难处理，尤其是从安全角度来看。

当然，还有其他免费工具可以帮助你测试 IE:

*   用于 Mac、Linux 和 Windows 的虚拟机(正如我提到的)

*   [代码扫描器](https://www.modern.ie/en-us/report?utm_source=SitePoint&utm_medium=content&utm_campaign=SitePoint)用于检测 IE 中的常见问题

*   [兼容性报告](https://www.modern.ie/en-us/compat-scan?utm_source=SitePoint&utm_medium=content&utm_campaign=SitePoint)(最初为 IE9 和 IE8 开发的用于网页的更健壮、更低级的代码扫描器版本)

*   [浏览器截图服务](https://www.modern.ie/en-us/screenshots?utm_source=SitePoint&utm_medium=content&utm_campaign=SitePoint)(针对那些更倾向于视觉的人)

如果你想知道更多细节，你可以看看去年的[遥控器。IE 博客上的 IE 公告](http://blogs.msdn.com/b/ie/archive/2014/11/02/announcing-remoteie-test-the-latest-ie-on-windows-mac-os-x-ios-and-android.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)。现在你已经设置好了，让我们知道它是否能帮助你减少测试时间。

本文是微软网站开发技术系列的一部分。我们很高兴与你分享斯巴达项目的[和它的](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)[新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=12779-DEV-sitepoint-link)。在 [modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://modern.ie/?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint)。

## 分享这篇文章