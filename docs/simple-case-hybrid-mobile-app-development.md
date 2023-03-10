# 混合移动应用程序开发的简单案例

> 原文：<https://www.sitepoint.com/simple-case-hybrid-mobile-app-development/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

## 什么是混合手机 App？

混合移动应用类似于你从应用商店下载的任何应用，但是它们如何工作的基本原理不同于本地应用。

像网站一样，混合移动应用程序是使用 HTML、CSS 和 JavaScript 等 web 技术构建的，因此开发人员在 web 上使用的大多数 JS 库也可以在混合应用程序中重用。关键区别在于，混合应用托管在利用移动平台 WebView 的本地应用中。这使他们能够使用加速计、摄像头和许多其他系统 API 等设备功能。运行在移动设备上的网站通常无法获得这些功能。网络也比以往任何时候都更具互操作性——包括像微软 Edge 的 [EdgeHTML](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16535-DEV-sitepoint-article71) 这样的现代浏览器引擎——所以如果你之前已经排除了混合解决方案，这是一个重新考虑托管 web 应用解决方案的好时机(下文将详细介绍)。

## 混合移动应用从何而来？

![Mobile and Desktop Users graphical representation](img/da5fccf1ac84135a80593a8362dd1a7e.png)

上图清楚地表明，移动用户的数量已经明显超过了桌面用户，这导致企业重新将重点放在迎合移动用户上。虽然企业已经在网络上有了自己的存在——在大多数情况下是一个网站——但对他们来说，扩展到所有移动平台的最简单的选择是考虑混合方法。这有助于他们保留具有相似技能的团队，而不必一次性对 iOS、Android 或 Windows 原生开发人员进行大量投资。这听起来像是一个划算的策略，所以让我们深入探讨一下。

## 移动应用程序中的 HTML5、CSS3 和 ES6。真的吗？

近年来，网络已经向标准发展。我们很少发现新的 web 应用程序带有与 IE6 兼容的元标签。这些 HTML5、CSS3 和 ES6 标准意味着网络现在更加干净，可以跨标准浏览器工作，而无需更改代码。事实上，你可以浏览你的网站，寻找互操作性的最佳实践[这里](https://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint)也有助于跨平台混合应用的开发。

*   有鉴于此，**我们可以利用标准来确保我们最终在所有平台的混合移动应用中使用相同的 DOM**。这种方法有助于我们编写干净的代码，并有助于轻松维护它。在 JSFiddle 上查看 iOS、Android 和 Windows Phone 的[原生外观标题栏的代码。](https://jsfiddle.net/prud/dnebx02p/)
*   有了新的 HTML5 和 CSS 元素，我们可以处理混合应用程序运行的各种屏幕分辨率。为了让你的图像对每个人都清晰，你通常需要提供两倍于应用程序中实际显示的尺寸。为所有不同分辨率提供合适大小的图像是响应式网站设计中讨论最多的话题之一。有各种方法，每种方法都有其与带宽、代码可维护性和浏览器支持相关的优点和缺点。让我们快速回顾一下最受欢迎的:
    *   从服务器端调整图像大小
    *   通过 JavaScript 进行客户端检测和替换
    *   [HTML5 图片元素](https://dev.windows.com/en-us/microsoft-edge/platform/status/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint/?filter=f3f0000bf&search=picture)
    *   [HTML5 srcset 属性](https://dev.windows.com/en-us/microsoft-edge/platform/status/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint/?filter=f3f0000bf&search=srcset)
    *   [CSS 图像集](https://dev.windows.com/en-us/microsoft-edge/platform/status/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint/?filter=f3f0000bf&search=image-set)
    *   [CSS 媒体查询](https://dev.windows.com/en-us/microsoft-edge/platform/status/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint/?filter=f3f0000bf&search=media%20queries)
    *   [分辨率无关图像(SVG)](https://dev.windows.com/en-us/microsoft-edge/platform/status/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint/?filter=f3f0000bf&search=svg)
*   CSS3 media query 可以很好地根据混合应用的屏幕大小来加载徽标:

    ```
     /* Normal-resolution CSS */
    .logo {
      width: 120px;
      background: url(logo.png) no-repeat 0 0;
    }

    /* HD and Retina CSS */
    @media only screen and (-webkit-min-device-pixel-ratio: 1.25),
    only screen and ( min--moz-device-pixel-ratio: 1.25),
    only screen and ( -o-min-device-pixel-ratio: 1.25/1),
    only screen and ( min-device-pixel-ratio: 1.25),
    only screen and ( min-resolution: 200dpi),
    only screen and ( min-resolution: 1.25dppx) {
      .logo {
        background: url(logo@2x.png) no-repeat 0 0;
        background-size: 120px; /* Equal to normal logo width */
      }
    } 
    ```

*   同样，我们可以通过查询设备类型来使用**平台特定的字体**。

    ```
     /* Windows Phone */
    font-family: 'Segoe UI', Segoe, Tahoma, Geneva, sans-serif;

    /* Android */
    font-family: 'RobotoRegular', 'Droid Sans', sans-serif;

    /* iOS */
    font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; 
    ```

*   **单页应用** (SPA)是一个适合单个网页的 web 应用，目标是提供类似于桌面应用的更流畅的用户体验。有一些库可以帮助 web 开发人员创造更流畅的 web 体验:
    *   [角型](https://angularjs.org/)
    *   [人](http://emberjs.com/)
    *   [BackboneJS](http://backbonejs.org/) 等。

## 进入混合应用程序开发平台

今天，大多数混合移动应用程序都利用了 Apache Cordova(T1)，这是一个平台，它提供了一组一致的 JavaScript APIs 来通过插件访问设备功能，这些插件是用本地代码构建的。约翰·布里斯托在他的博客上提供了一个[很好的解释](http://developer.telerik.com/featured/what-is-a-hybrid-mobile-app/)为什么和如何。它早先被称为 [PhoneGap](http://phonegap.com/) 。如今，PhoneGap 是作为 Apache Cordova 的发行版存在的，其中包括 Adobe 提供的附加工具。关于历史参考 [PhoneGap，Cordova，和什么是名字？](http://phonegap.com/2012/03/19/phonegap-cordova-and-what%E2%80%99s-in-a-name/)。

这些插件包括用于访问设备的加速度计、联系人、摄像头等的 API。还有许多插件是由开发人员社区构建和维护的。这些可以在 [Apache Cordova 插件注册表](http://plugins.cordova.io/)中找到。

HTML、CSS、JavaScript 等应用程序资产通过 Apache Cordova 提供的工具打包到目标平台 SDK 中。一旦构建完成，您就拥有了一个可以像设备上任何其他类型的应用程序一样运行的应用程序。虽然 Apache Cordova 提供的工具主要是通过命令行界面驱动的，但开发人员也可以使用像 [Visual Studio](http://www.visualstudio.com/?WT.mc_id=16535-DEV-sitepoint-article71) 这样的 ide。我会告诉你怎么做:

![Visual Studio plus HTML5, CSS3 and Javascript](img/46333727acff08efdecbe9ee9a5d36b5.png)

![Apache Cordova](img/7fefb48ca215462104b5736eae7929a9.png)

## 使用 Visual Studio 2015 开始使用混合应用

*   在[安装 Visual Studio 2015](http://www.visualstudio.com/?WT.mc_id=16535-DEV-sitepoint-article71) 时(尝试 [VS Code for Mac、Linux 或 Windows](https://code.visualstudio.com/?WT.mc_id=16535-DEV-sitepoint-article71) 或新的 [Dev Essentials](https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx/?WT.mc_id=16535-DEV-sitepoint-article71) 产品，其中包括跨平台工具)，确保您选择了自定义安装，并选择了“工具用于 Apache Cordova”复选框。![Visual Studio installation](img/55e1c62094ce774220ef3f513d339770.png)
*   在 Visual Studio 中创建“新项目”。选择 JavaScript 下的“空白应用程序(Apache Cordova)”模板。![Apache Cordova sample app](img/2cc3742f5db853f7f367d60d44cd641d.png) ![Apache Cordova sample app](img/6faddcf17337905feee30afc0a00d00b.png)
*   通过 [Intellisense](https://msdn.microsoft.com/en-us/library/hcw1s69b.aspx?WT.mc_id=16535-DEV-sitepoint-article71) ，你可以使用 AngularJS 或者你选择的任何其他 JS 库。因此，开发人员使用 Visual Studio 可以提高工作效率。不仅如此，调试体验也很棒。Visual Studio 提供了一个 Android 模拟器，与 SDK 提供的模拟器相比，它的启动速度非常快。![Android emulator](img/6f2123387dfc6f60afb8522534a92c1a.png)

## 尝试托管 Web 应用解决方案

越来越多的情况下，托管 web 应用是一种新的低成本方式，可以使用现有的 web 体验来构建跨平台应用。web 应用程序托管在服务器上，HTML/CSS/JS 文件不在本地包中。像 [manifold 这样的开源库。JS](http://manifoldjs.com/?WT.mc_id=16535-DEV-sitepoint-article71) 已经越来越让这条路变得容易——你建立你的清单文件，上传标识，你可以快速地把你的网络应用放到 Android、iOS 和 Windows 商店里。一些平台，如通用 Windows 平台，也允许使用本地 API，如通知和货币化功能。你可以按照[这个教程](https://microsoftedge.github.io/WebAppsDocs/en-US/win10/CreateHWA.htm)在几分钟内为 Windows 10 创建一个托管网络应用。

优点:

*   对于已经拥有响应式 web 应用程序的 web 开发人员来说，这听起来是一个很好的选择，他们可以开始使用 UWP API，而不必从头开始。
*   应用程序更新由服务器控制，因此应用程序总是更新的。
*   通过商店赚钱。
*   原生 API 访问/Cortana/Windows Hello 等。

缺点:

*   它需要一致的网络连接，因为应用程序文件托管在服务器上。

## 那为什么要用 Native 呢？

过去[脸书曾利用混合方法](https://www.facebook.com/notes/facebook-engineering/under-the-hood-rebuilding-facebook-for-ios/10151036091753920)让他们的应用跨平台发布，后来他们用原生应用取代，声称性能更快，因为原生应用利用了特定平台的 API。我并不是说混合应用总是失败的，但是你需要根据你的应用需求、用户期望、你想要使用的 API 等来明智地选择方法。在一天结束的时候，你想要的是用户尽可能地喜欢和使用你的应用程序。

以下来自 flurry 的统计数据显示了 Android 和 iOS 上不同类别应用的使用情况。

![IOS and Android's browser and apps usage graphical representation](img/df84f12ff88cfc1214a9986e803662bd.png)

了解这一点很有意思，因为它让我们深入了解终端用户在每个平台上最喜欢做什么。如果你的应用程序的类别与高百分比使用应用程序一致，你就有更多的受众，但可能会面临来自其他应用程序的激烈竞争。在进行混合或本地应用程序开发时，您可能会考虑这一点。

## 结论

我见过许多 B2B 应用程序使用混合方法来满足业务需求，而不是关注最终用户体验，其中许多人都有能力这样做，因为他们的受众在使用现有应用程序方面已经训练有素。

在 B2C 场景中，关注点有所改变。最终用户期望从你的应用程序中获得最简单和最好的体验，所以你需要关注 UX、性能、简单性、粘性和其他消费者保留问题。没有什么比在这里使用本机平台特定功能更好的了，比如通过扫描用户的视网膜或利用 iOS 应用程序中的[touch id](http://www.cio.com/article/2833799/mobile-apps/3-great-ios-apps-that-use-apples-touch-id-finger-scanner.html)来验证用户。

## 更多的 Web 开发实践

这篇文章是微软布道者和工程师关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16535-DEV-sitepoint-article71) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16535-DEV-sitepoint-article71)。

我们鼓励您在 dev.microsoftedge.com 使用免费工具进行跨浏览器和设备测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint)
*   [下载适用于 Mac、Linux 和 Windows 的免费虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint)
*   [跨浏览器检查网络平台状态，包括微软 Edge 路线图](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article71&utm_campaign=SitePoint)

**向我们的工程师和传道者进行更深入的学习:**

*   **互操作性最佳实践** ( [系列](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-5-things-to-consider-as-a-web-developer/?WT.mc_id=16535-DEV-sitepoint-article71)):
    *   [如何避免浏览器检测](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Avoiding-Browser-Detection/?WT.mc_id=16535-DEV-sitepoint-article71)
    *   [使用 CSS 前缀的最佳实践](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-CSS-Vendor-Prefixes/?WT.mc_id=16535-DEV-sitepoint-article71)
    *   [保持你的 JS 框架&库更新](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-JavaScript-Libraries/?WT.mc_id=16535-DEV-sitepoint-article71)
    *   [构建插件免费网络体验](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-Plugins/?WT.mc_id=16535-DEV-sitepoint-article71)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development/?WT.mc_id=16535-DEV-sitepoint-article71)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=16535-DEV-sitepoint-article71)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=16535-DEV-sitepoint-article71)(来自 David Catuhe)
*   托管网络应用和网络平台创新

**我们的社区开源项目:**

*   伏龙。JS (跨设备远程 JavaScript 测试)
*   [manifoldJS](//manifoldjs.com/?WT.mc_id=16535-DEV-sitepoint-article71) (部署跨平台托管的 web 应用)
*   [babylonJS](http://babylonjs.com/?WT.mc_id=16535-DEV-sitepoint-article71) (轻松制作 3D 图形)

**更多免费工具和后端 web 开发工具:**

*   [Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=16535-DEV-sitepoint-article71)(用于 Mac、Linux 或 Windows 的轻量级代码编辑器)
*   [Visual Studio 开发基础知识](https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx/?WT.mc_id=16535-DEV-sitepoint-article71)(基于订阅的免费培训和云优势)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=16535-DEV-sitepoint-article71) 与[在蔚蓝云上试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=16535-DEV-sitepoint-article71)

## 分享这篇文章