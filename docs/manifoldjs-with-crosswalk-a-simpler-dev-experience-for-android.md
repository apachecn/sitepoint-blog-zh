# 带有人行横道的 manifoldJS，更简单的 Android 开发体验

> 原文：<https://www.sitepoint.com/manifoldjs-with-crosswalk-a-simpler-dev-experience-for-android/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

有了 0.3.0 版的 manifoldJS，你现在可以选择用 Crosswalk 而不是传统的 Android webview 来构建你的 Android 应用。实现起来也很简单。

如果你不熟悉， [manifoldJS](http://www.manifoldjs.com/) 是一个新的开源框架，它可以为 Windows、iOS、Android、Chrome 和 Firefox 创建一个网站和应用程序，简化跨平台托管应用程序的创建。它于去年 4 月在微软 [Build 2015](https://channel9.msdn.com/Events/Build/2015?sort=sequential&direction=desc&term=&tag=web-platform/?WT.mc_id=13414-DEV-sitepoint-article38) 大会上首次亮相。manifoldJS 通过 node.js 作为命令行工具运行，或者你可以在 http://www.manifoldJS.com[使用基于网络的工具。](http://www.manifoldjs.com)

![The manifoldJS and Crosswalk logos](img/0adda478a1ed729df9bece7e5886c7d6.png)

在本教程中，我将向您展示安装和运行它的简单步骤，以便您可以自己尝试。首先，确保您已经安装并运行了 manifoldJS:

1.  从 nodejs.org 安装 node.js
2.  打开你最喜欢的命令提示符(mac 或 linux 上的终端)，输入:

    ```
    npm install manifoldjs -g
    ```

3.  现在，您只需将人行横道标志添加到您的启动参数中，然后观察接下来会发生什么:

    ```
    manifoldjs http://www.thishereweb.com –p Android –c
    ```

然后…嘣！您刚刚构建了一个带有人行横道的托管 web 应用程序。

![An example of a cross-platform app](img/a6df0f82f33be7eb9d0d8b3783451cae.png)

## 人行横道的一个实例

有了 Crosswalk，您可以确信最新的 HTML5 功能，如 WebGL、IndexedDB、Web Sockets 和 CSS3，都在您的应用程序中。例如，下面是使用默认 webview 的 WebGL 应用程序的输出，它是由 manifoldJS 用下面的命令行生成的。

```
manifoldjs http://get.webgl.org/
```

![An error message indicating a device does not support WebGL](img/55c5459fd49479dd061061601102a7dc.png)

这是启用人行横道的相同应用。它是在前面的命令行中添加-c(或–人行横道)开关后生成的。

```
manifoldjs http://get.webgl.org/ --crosswalk
```

或者

```
manifoldjs http://get.webgl.org/ -c
```

![A message indicating a device does have WebGL support](img/be148d743a66f8d0be925c7dcd7b3d77.png)

有了人行横道，你可以确保所有用户都能获得预期的体验。

## 什么是人行横道？

Crosswalk 是由 crosswalk 项目设计的一个 web 运行时环境。Crosswalk 采用了开源的 Chromium 和 Blink 引擎，并将它们编译成一个现代化的运行时环境。你可以把人行横道看作是一个强大的 webview，事实上，当人行横道标志被设置时，我们用它来代替传统的 Android webview。

## 为什么人行横道很重要

人行横道支持带来两个主要优势。首先，它是一个“更新的”web 运行时环境。对于使用最新版本 Android 操作系统的 Android 用户来说，这可能算不上什么优势，但对于使用旧版本操作系统的用户来说，这是一个巨大的进步。人行横道 webview 将为您提供所有最新的 HTML5 功能，以及优于传统 webview 的性能提升。

其次，人行横道提供了一致的运行时环境。如今使用的 Android 版本各不相同，你有那么多不同版本的 Android webview，所以你不得不写最小公分母。使用人行横道可以消除这种障碍。此外，运行时仅在您在应用中更新时才会改变，而不会随着操作系统而改变。我们知道，许多企业用户的应用程序依赖于这种类型的一致性。

## 为什么我不走人行横道？

我只能想到一个你不想走人行横道的原因。应用程序大小。平均值。我们制作的 apk 文件(Android 的应用程序文件)只有几兆字节。将人行横道添加到应用程序中会额外增加 20mb，一旦安装到设备上，将增加近 60mb。您需要做出决定，以确定资源成本是否值得。

将运行时与应用程序捆绑在一起是最简单的分发方法，但是 Crosswalk 应用程序也可以共享一个运行时库(以“共享模式”)来减轻负载。一个支持共享模式的包是 Android 分发的一部分。但是，您必须自己分发这个共享的运行时包。更多详情，请访问[人行横道维基](https://github.com/crosswalk-project/crosswalk-website/wiki/Shared-mode-for-Crosswalk-Core-Library-on-Android)。

请记住，托管 web 应用程序的本质是在 web 服务器上更新应用程序，因此在大多数情况下，增加包大小的成本会在初次下载时感受到，而不是像普通的原生应用程序那样每次更新。

## 走团队人行横道！

我们很高兴能够支持 Crosswalk web 运行时环境。它填补了 Android 系统的一个空白，使开发更简单、更可靠。用你的下一个 manifoldJS 应用试试看，看看你有什么想法。有关人行横道的更多信息，请访问位于[https://crosswalk-project.org/](https://crosswalk-project.org/)的站点。要开始从您的网站构建商店应用，请前往 www.manifoldjs.com[的](http://www.manifoldjs.com/)开始。

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13414-DEV-sitepoint-article38) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13414-DEV-sitepoint-article38)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article38&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article38&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article38&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article38&utm_campaign=SitePoint)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development/?WT.mc_id=13414-DEV-sitepoint-article38)

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=13414-DEV-sitepoint-article38)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=13414-DEV-sitepoint-article38)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com) 和 [babylonJS](http://babylonjs.com) 项目)
*   [托管的网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=13414-DEV-sitepoint-article38)(来自律师奶爸和基里尔·塞克瑟诺夫，包括 [manifoldJS](http://manifold.js.com) 项目)

更多面向网络平台的免费跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13414-DEV-sitepoint-article38)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=13414-DEV-sitepoint-article38) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13414-DEV-sitepoint-article38)

## 分享这篇文章