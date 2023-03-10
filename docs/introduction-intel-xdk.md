# 新的跨平台选项，推出英特尔 XDK

> 原文：<https://www.sitepoint.com/introduction-intel-xdk/>

[XDK](https://software.intel.com/en-us/html5/tools)是开发跨平台移动应用的新工具。它试图通过将所有可能的目标平台包含在一个包中，并收集几个您可能熟悉和有用的工具来简化这个过程。

在本文中，我将解释什么是 XDK，并帮助您开始使用 HTML5 为每个移动平台构建应用。界面很简单，但与其他 ide 不同。我们将一步一步探索一切。

## 装置

您可以在[官方网站](https://software.intel.com/en-us/html5/tools)找到英特尔 XDK。它适用于 Windows、Linux 和 Mac。当你第一次启动软件时，你需要创建一个帐户，这样你就可以使用构建功能。有了这项服务，你可以在云端托管你的项目，并在那里构建它们。创建帐户后，登录即可开始使用。

> **注意:**我在 Debian 上安装 XDK 时遇到了一些问题。在基于 Debian 的 Ubuntu 上运行良好。我还没有在其他 Linux 发行版上试过，但是如果你发现任何问题，请留下评论。

## 工作流程

我最喜欢 XDK IDE 的一点是，它包含了从开始到发布完整开发一个应用程序的所有工具。开发、仿真、测试、调试、分析和构建都包括在内。第三方服务也有一个特殊功能。所有这些工作流程被组织到 7 个选项卡中，这些选项卡按照适当的顺序排列。

“发展”选项卡是您将花费最多时间的地方。您可以使用拖放工具(App Designer 和 App Starter)或文本编辑器开发您的应用程序。文本编辑器基于[括号编辑器](http://brackets.io/)，这是一个使用 HTML5 构建的开源编辑器。支架由 Adobe 维护。对于拖放原型，您可以选择应用程序设计器或应用程序启动器。App Starter 是一个简单的工具，面向初学者。仅支持 [App 框架](http://app-framework-software.intel.com/)。App Designer 比 App Starter 更复杂，功能更多。支持 [App 框架](http://app-framework-software.intel.com/)、 [Bootstrap 3](http://getbootstrap.com/) 、 [JQuery Mobile](http://jquerymobile.com/) 和 [TopCoat](http://topcoat.io/) 。当使用拖放工具和代码编辑器进行开发时，您可以来回切换。你也可以同时使用它们。我个人选择不使用 GUI 构建工具，因为它们会产生太多不必要的代码。我喜欢从头开始编码，因为当你从头开始编码时，整个项目更容易维护，你的代码更干净。我喜欢玩的一个功能是设备上的实时预览，你可以很容易地在连接的设备上进行编码和实时预览。

![Intel XDK Develop Tab](img/3a8b2357667b6fc9fdf751fd8e43940e.png)

第二个选项卡是“仿真”选项卡。模拟器基于 [Apache Ripple](http://ripple.incubator.apache.org/) 。它是一种 Chrome 浏览器加上一些额外的 API，它支持 [Cordova](http://cordova.apache.org/) 和[英特尔 XDK API](https://software.intel.com/en-us/node/492826)。使用该工具测试不同屏幕尺寸和不同设备非常简单，因为您可以从各种设备中进行选择。英特尔集成了 Chrome 开发工具，用于实时调试和检查您的前端。这很像开发选项卡上的实时预览，但在这里你可以处理一个硬件模拟器。

![Intel XDK Emulate Tab](img/a3031ccb2d84ce20533e929b7def8ee9.png)

测试功能使开发时间更快，因为你不必每次都要测试应用程序。首先，你下载应用预览到你的测试机器(手机，平板电脑等)。在 [IOS](https://itunes.apple.com/us/app/intel-app-preview/id725023841?mt=8) 、 [Android](https://play.google.com/store/apps/details?id=com.intel.html5tools.apppreview&hl=en) 和 [Windows Store](http://apps.microsoft.com/windows/en-us/app/intel-app-preview/96b71a70-5444-4cbf-9d7f-e7433a6b5ec9) 都有。有两种方法可以测试。第一种方法是从服务器中提取项目，并在应用程序预览中执行。第二种是使用 WiFi，将智能手机或平板电脑与连接笔记本电脑的 WiFi 连接起来。第二种方法更快，因为您不必每次都在服务器之间推和拉数据。还可以用 [weinre](http://people.apache.org/~pmuellr/weinre-docs/latest/) 远程调试(不完全支持 javascript)。

![Intel XDK Testing WiFi Tab](img/5d82efb7dc36e057f1d600cbdec3eb3d.png)

调试选项卡仅适用于 Android 4.x 设备。这是一个 usb 调试，很像远程测试调试。这种调试方法使用 Chrome Developers 工具，并额外支持 Javascript，提供更深入的调试工具。该选项卡提供了有关如何设置您的设备以使用该功能的信息。

“个人资料”选项卡也仅适用于 Android 4.x 设备。在此选项卡中，您可以收集关于应用性能的信息。

XDK 支持构建 Android、iOS、Windows 8、Windows 8 Phone、Tizen、Firefox OS (beta)和 Amazon 应用。它还支持构建脸书和 Chrome 网络应用。

建筑是最容易的部分。你所要做的就是配置一些关于应用的信息。不同的平台有不同的配置要求。例如，iOS 需要一些关于您的 iOS 开发者帐户的信息。我喜欢在 XDK 英特尔开发应用的原因是它提供了无限的私人项目。 [Phonegap](https://build.phonegap.com/) 只给你限于 5 个私有项目的免费云构建。

![Intel XDK Build Tab](img/397c4a24522efa52df01c0869314086b.png)

最后一个选项卡是服务选项卡。在此选项卡中，您将看到各种附加服务的列表。广告、数据馈送、通知和社交。所有这些服务都不属于 XDK 英特尔公司，它们可以在所有平台上使用。比方说，我们希望在应用程序中使用推送通知。我们可以使用 [AppMobi PushMobi](http://www2.appmobi.com/documentation/content/Articles/Article_UsingPushMobi/index.html?r=5101) 服务。或者我们想定期更新应用程序，我们可以通过 [App Mobi](http://www.appmobi.com/) 实现实时更新。或者我们想用 Dropbox Api，也容易做到。

## 结论

HTML5 作为移动应用程序开发的一个可行选项，日益强大。每天都有新的工具和新的公司在推动这一趋势。世界上最负盛名的科技公司之一(英特尔)现在投资于混合移动应用。这是对 HTML5 技术的巨大信任投资。

构建混合应用已经很容易，XDK 让这一切变得更加简单，因为它在一个地方就能满足您开发移动应用的所有需求。在以后的文章中，我将向您展示我们可以选择的框架，以启动一个项目并开始构建一个简单的应用程序。

## 分享这篇文章