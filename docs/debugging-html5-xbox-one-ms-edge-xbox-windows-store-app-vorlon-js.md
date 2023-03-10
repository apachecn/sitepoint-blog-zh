# 在 Microsoft Edge for Xbox One 中调试 HTML5

> 原文：<https://www.sitepoint.com/debugging-html5-xbox-one-ms-edge-xbox-windows-store-app-vorlon-js/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

11 月 12 日，[Xbox One](http://news.xbox.com/2015/11/12/new-xbox-one-experience-begins-today/?WT.mc_id=16534-DEV-sitepoint-article70)进行了新的更新。作为一名游戏玩家，我非常喜欢新的仪表盘和 Xbox 360 的向后兼容性。但是作为一名网络开发人员，我非常高兴现在有**微软 Edge 在我的控制台**上运行！这意味着您现在可以在 Xbox One 浏览器中运行非常现代的内容！

## 在 Xbox One 和 Microsoft Edge 上测试您的 HTML5 内容

例如，它支持 **WebGL、Web Audio、Gamepad API** ，正如您在该视频中看到的:

[https://www.youtube.com/embed/vz3OZzOP2J4?feature=oembed](https://www.youtube.com/embed/vz3OZzOP2J4?feature=oembed)

我们在 **Xbox One** 上使用 [Babylon.js Mansion 演示](http://www.babylonjs.com/Demos/MANSION/?WT.mc_id=16534-DEV-sitepoint-article70)。它运行得非常好！我仍然对 web 标准的美丽感到惊讶。如果您遵循最佳实践，您的代码将在任何地方运行！更好的是，我在这个视频中使用了 **Xbox Windows Store 应用程序**，在我的 **Windows 10 PC** 上传输我的 Xbox One 的视频输出。这意味着我不用离开椅子就可以在 Xbox One 上测试我网页内容；)

![Remotely test the MS Edge Xbox One browser from your Windows 10 PC](img/46d33fb0398a2a179fbb349d71dac068.png)

*借助 Xbox Windows Store 应用程序，您可以从 Windows 10 PC 远程测试 MS Edge Xbox One 浏览器！*

![WebGL Babylon.js Flight Arcade demo on Xbox One](img/c83e46d8410e3726524e46e7232f6dc7.png)

在 Xbox One 上玩 WebGL Babylon.js 飞行街机演示！

## 借助 Vorlon.js 在 Xbox One 上远程调试您的 HTML5 内容

Vorlon.js 是一个开源的跨平台远程调试工具，可以从任何设备轻松地远程调试任何网页。让我们在下面的视频中一起检查一下:

[https://www.youtube.com/embed/zdiuEwMi8_8?feature=oembed](https://www.youtube.com/embed/zdiuEwMi8_8?feature=oembed)

只需在 HTML 页面的开头添加一个脚本引用，您就可以使用 Vorlon.js 仪表板从任何浏览器远程调试您的站点。在这个视频中，我使用 **Modernizr 插件**检查对 **Gamepad API** 的支持，使用**交互控制台**检查潜在错误，使用**在 Xbox One 上执行 JavaScript** ，最后使用 **DOM Explorer** 更新 HTML 和一些 **Flexbox 属性**。同样，使用 Xbox Windows Store 应用程序对实时调试页面非常有用。只需在左侧捕捉它，并在右侧捕捉 Vorlon.js 仪表板。更好的是，在您的 Windows 10 电脑上使用多个屏幕:

![DOM explorer](img/63eddeaf163682c80346af735fef9242.png)

我非常确信 **Web 标准和 WebGL 为游戏行业提供了新的可能性**！我在以前的一篇文章中谈到过这个问题:[网络:下一个游戏前沿？](http://blogs.msdn.com/b/davrous/archive/2014/06/20/the-web-the-next-game-frontier.aspx?WT.mc_id=16534-DEV-sitepoint-article70)好像真的开始成真了。；——)

## 更多的 Web 开发实践

这篇文章是微软布道者和工程师关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16534-DEV-sitepoint-article70) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16534-DEV-sitepoint-article70)。

我们鼓励您在 dev.microsoftedge.com 使用免费工具进行跨浏览器和设备测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article70&utm_campaign=SitePoint)
*   [下载适用于 Mac、Linux 和 Windows 的免费虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article70&utm_campaign=SitePoint)
*   [跨浏览器检查网络平台状态，包括微软 Edge 路线图](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article70&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article70&utm_campaign=SitePoint)

**向我们的工程师和传道者进行更深入的学习:**

*   **互操作性最佳实践** ( [系列](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-5-things-to-consider-as-a-web-developer/?WT.mc_id=16534-DEV-sitepoint-article70)):
    *   [如何避免浏览器检测](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Avoiding-Browser-Detection/?WT.mc_id=16534-DEV-sitepoint-article70)
    *   [使用 CSS 前缀的最佳实践](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-CSS-Vendor-Prefixes/?WT.mc_id=16534-DEV-sitepoint-article70)
    *   [保持你的 JS 框架&库更新](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-JavaScript-Libraries/?WT.mc_id=16534-DEV-sitepoint-article70)
    *   [构建插件免费网络体验](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-Plugins/?WT.mc_id=16534-DEV-sitepoint-article70)
*   GitHub 上的编码实验室:跨浏览器测试和最佳实践
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=16534-DEV-sitepoint-article70)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=16534-DEV-sitepoint-article70)(来自 David Catuhe)
*   托管网络应用和网络平台创新

**我们的社区开源项目:**

*   伏龙。JS (跨设备远程 JavaScript 测试)
*   [manifoldJS](http://manifoldjs.com/?WT.mc_id=16534-DEV-sitepoint-article70) (部署跨平台托管的 web 应用)
*   [babylonJS](//babylonjs.com/?WT.mc_id=16534-DEV-sitepoint-article70) (轻松制作 3D 图形)

**更多免费工具和后端 web 开发工具:**

*   [Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=16534-DEV-sitepoint-article70)(用于 Mac、Linux 或 Windows 的轻量级代码编辑器)
*   [Visual Studio 开发基础知识](https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx/?WT.mc_id=16534-DEV-sitepoint-article70)(基于订阅的免费培训和云优势)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=16534-DEV-sitepoint-article70) 与[在蔚蓝云上试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=16534-DEV-sitepoint-article70)

## 分享这篇文章