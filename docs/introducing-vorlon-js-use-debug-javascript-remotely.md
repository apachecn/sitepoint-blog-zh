# Vorlon.js 简介:如何使用它远程调试您的 Javascript

> 原文：<https://www.sitepoint.com/introducing-vorlon-js-use-debug-javascript-remotely/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

最近在 [//BUILD/ 2015](http://channel9.msdn.com/coding4fun/blog/build-2015?WT.mc_id=13398-DEV-sitepoint-article22) 上，我们宣布了[vorlon . js](http://www.vorlonjs.com/?WT.mc_id=13398-DEV-sitepoint-article22)——一款开源、可扩展、平台无关的工具，用于远程调试和测试您的 JavaScript。我有机会在微软一些有才华的工程师和技术传播者的帮助下创建了 vorlon.js(正是这些人给你带来了 http://www.babylonjs.com/)。

[Vorlon.js](http://www.vorlonjs.com/?WT.mc_id=13398-DEV-sitepoint-article22) 由 Node.js，Socket 供电。木卫一，还有深夜咖啡。我想和你分享我们为什么要做它，如何将它整合到你自己的测试工作流程中，并且分享一些更多的细节来构建一个类似的 JavaScript 库。

![Vorlon.js logo](img/56d5da0bc25100bd355abe363d23c18c.png)

## 为什么是艾普松. js？

Vorlon.js 帮助您远程加载、检查、测试和调试运行在任何带有 web 浏览器的设备上的 JavaScript 代码。无论是游戏机、移动设备，甚至是物联网连接的冰箱，都可以远程连接多达 50 台设备，并在每台或所有设备上执行 JavaScript。这里的想法是，开发团队也可以一起调试——每个人都可以编写代码，并且所有人都可以看到结果。我们在这个项目中有一个简单的座右铭:**没有本地代码**，**不依赖特定的浏览器**，**只有 JavaScript** ，HTML 和 CSS 在你选择的设备上运行。

Vorlon.js 本身是一个小型 web 服务器，您可以从本地机器上运行，或者安装在服务器上供您的团队访问，它服务于 Vorlon.js 仪表板(您的指挥中心)并与远程设备通信。在您的网站或应用程序中安装 Vorlon.js 客户端就像添加一个脚本标签一样简单。它也是可扩展的，开发人员可以编写插件来为客户端和仪表板添加特性，例如:特性检测、日志记录和异常跟踪。

那么为什么取这个名字呢？其实有两个原因。第一个原因是因为我对电视节目《巴比伦 5 号》非常着迷。基于这一点，第二个原因是因为[伏龙族](https://www.youtube.com/watch?v=r3QVbyesRmc?WT.mc_id=13398-DEV-sitepoint-article22)是宇宙中最智慧和最古老的种族之一，因此，他们作为年轻种族之间的外交官是有帮助的。他们的帮助鼓舞了我们。对于 web 开发人员来说，编写在各种设备和浏览器上可靠运行的 JavaScript 仍然太难了。Vorlon.js 试图让它变得简单一点。

## 你提到 Vorlon.js 有外挂？

Vorlon.js 的设计使得您可以通过编写或安装附加插件来轻松扩展仪表板和客户端应用程序。您可以调整仪表板的大小或向仪表板添加额外的窗格，仪表板可以与客户端应用程序进行双向通信。首先有三个插件:

### 控制台日志记录

“控制台”选项卡将控制台消息从客户端传输到仪表板，您可以使用它进行调试。任何带有`console.log()`、`console.warn()`或`console.error()`的记录都会出现在仪表盘中。像**F12 #开发工具** DOM explorer 一样，您可以看到 DOM 树，选择一个节点(它将在设备上高亮显示，并更新或添加新的 CSS 属性)。交互性:您也可以通过在输入中键入代码来与远程网页进行交互。输入的代码将在页面的上下文中进行评估。

![console](img/6d81d8bf445debfbfa2504aa60bfc069.png)

### DOM 浏览器

DOM 检查器向您显示远程网页的 DOM。您可以检查 DOM，点击节点将在主机网页中突出显示它们，如果您选择了一个节点，您还可以查看和修改它的 CSS 属性。

![DOM explorer](img/874b2ad1432f57bab80aa5fd28dcadaa.png)

### 现代化 zr

Modernizr 选项卡将显示由 [Modernizr](http://modernizr.com/?WT.mc_id=13398-DEV-sitepoint-article22) 报告的支持的浏览器功能。您可以使用它来确定哪些功能实际上是可用的。这在不常见的移动设备或游戏机上可能特别有用。

![Modernizr demo](img/42c840604b3409e93aca15180579cc6d.png)

## 我如何使用它？

在节点命令行中，只需执行以下命令:

```
$ npm i -g vorlon
$ vorlon
```

现在，您在本地主机的端口 1337 上运行了一个服务器。
要访问仪表板，只需导航到[http://localhost:1337/dashboard/session ID](http://localhost:1337/dashboard/SESSIONID?WT.mc_id=13398-DEV-sitepoint-article22)，其中`SESSIONID`是当前仪表板会话的 id。这可以是您想要的任何字符串。

然后，您必须在客户端项目中添加一个引用:

```
<script src="http://localhost:1337/vorlon.js/SESSIONID"></script>
```

请注意，`SESSIONID`可以省略，在这种情况下，它将被自动替换为`default`。

就是这样！现在，您的客户机将把调试信息无缝地发送到您的仪表板。现在让我们看一个使用真实站点的例子。

## 使用 Vorlon.js 调试 babylonjs.com

让我们以 http://www.babylonjs.com/的[为例。首先，我必须启动我的服务器(使用`/server`文件夹中的`node start.js`)。然后，我必须将这一行添加到我的客户站点:](http://www.babylonjs.com/?WT.mc_id=13398-DEV-sitepoint-article22)

```
<script src="http://localhost:1337/vorlon.js"></script>
```

因为我没有定义一个`SESSIONID`，所以我可以直接进入`[http://localhost:1337/dashboard](http://localhost:1337/dashboard)`。仪表板看起来像这样:

![Vorlon.js dashboard](img/168c58b2231003abeec923d15fa287ca.png)

*旁注*:上面显示的浏览器是微软 Edge(原名 [Project Spartan](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx?WT.mc_id=13398-DEV-sitepoint-article22) )，微软为 Windows 10 新推出的浏览器。你也可以在 http://modern.ie/[的 Mac、iOS、Android 或 Windows 设备上远程测试你的网络应用。或者也试试 vorlon.js。](http://modern.ie/?utm_source=SitePoint&utm_medium=article22&utm_campaign=SitePoint)

回到正题:例如，我可以看到控制台消息，这在我在移动设备(如 iOS、Android 或 Windows Phone)上调试 babylon.js 时很有用。我可以点击 DOM Explorer 上的任何节点来获取 CSS 属性的信息:

![Checking the CSS properties of a node](img/db1b6d06faf1b0a79905c8a9a7132bb3.png)

在客户端，选定的节点以红色边框突出显示:

![The client demo](img/0b2a2dff085e3142836bff2ff34f1509.png)

此外，我可以切换到 Modernizr 选项卡来查看我的特定设备的功能:

![Device capabilities](img/72cd9993ba9629b28b48aa295d472042.png)

在左侧，您可以看到当前连接的客户端列表，并且您可以使用“识别客户端”按钮来显示每个连接设备的编号。

## 关于我们如何构建 vorlon.js 的更多信息

从一开始，我们就希望确保 Vorlon.js 尽可能保持**移动优先**和**平台无关**。所以我们决定使用开源技术，它可以在更广泛的环境中工作。

我们的开发环境是 Visual Studio 社区，你现在可以免费得到它。我们在 Visual Studio 中使用了 [Node.js 工具，在后端使用了 Azure](https://github.com/Microsoft/nodejstools?WT.mc_id=13398-DEV-sitepoint-article22) 。我们的前端是 JavaScript 和 TypeScript。如果你不熟悉 TypeScript，你可以在这里[了解我们为什么用它来构建 babylon.js。最近用 TypeScript](http://blogs.msdn.com/b/eternalcoding/archive/2014/04/28/why-we-decided-to-move-from-plain-javascript-to-typescript-for-babylon-js.aspx?WT.mc_id=13398-DEV-sitepoint-article22) 打造了 [Angular 2。但是使用 vorlon.js 不一定要了解它。](http://blogs.msdn.com/b/typescript/archive/2015/03/05/angular-2-0-built-on-typescript.aspx?WT.mc_id=13398-DEV-sitepoint-article22)

下面是它如何工作的全球模式:

![Vorlon.js’ schema](img/4092c42366d3f492c0d65e0592aaee7d.png)

这是我们建造它的部件:

*   一个 [Node.js](https://nodejs.org/?WT.mc_id=13398-DEV-sitepoint-article22) 服务器正在托管一个仪表板页面(使用 express 提供)和一个服务

*   该服务使用 [socket.io](http://socket.io/?WT.mc_id=13398-DEV-sitepoint-article22) 与仪表板和各种设备建立直接连接

*   设备必须引用服务器提供的一个简单的 vorlon.js 页面。它包含所有插件的客户端代码，这些代码与客户端设备交互，并通过服务器与仪表板通信。

*   每个插件都分为两部分:

    *   客户端，用于捕获信息并与设备交互

    *   仪表板侧，用于为仪表板内的插件生成命令面板

例如，控制台插件是这样工作的:

*   客户端在`console.log()`、`console.warn()`或`console.error()`之上生成一个钩子。这个钩子用于将这些函数的参数发送到仪表板。它还可以从仪表板端接收订单，并对其进行评估

*   仪表板端收集这些参数并显示在仪表板上

结果只是一个远程控制台:

![console demo](img/637f29a745ed80ab210200d1fff580f5.png)

您可以更好地理解 vorlon.js 的可扩展性，包括如何构建自己的插件[在这里](http://www.vorlonjs.com/plugins/?WT.mc_id=13398-DEV-sitepoint-article22)。

## 下一步是什么？

Vorlon.js 建立在可扩展性的理念之上。我们鼓励您投稿！我们已经在考虑如何将 vorlon.js 集成到浏览器开发工具以及网络音频调试中。

如果你想试试，你只需点击一下:[vorlonjs.com
T2【更多技术文档在](http://www.vorlonjs.com/?WT.mc_id=13398-DEV-sitepoint-article22)[我们的 GitHub](https://github.com/MicrosoftDX/Vorlonjs?WT.mc_id=13398-DEV-sitepoint-article22) 上。

## JavaScript 的更多实践

这可能会让你有点惊讶，但微软有许多关于开源 JavaScript 主题的免费学习，我们的任务是用 [Microsoft Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13398-DEV-sitepoint-article22) 创造更多。查看我自己的:

*   [web GL 3D 和 HTML5 以及巴比伦介绍。JS](http://www.microsoftvirtualacademy.com/training-courses/introduction-to-webgl-3d-with-html5-and-babylon-js?WT.mc_id=13398-DEV-sitepoint-article22)

*   [用 ASP.NET 和安古拉杰构建单页应用](http://channel9.msdn.com/Events/Build/2014/3-644?WT.mc_id=12833-DEV-sitepoint-othercontent)

*   [HTML 中的前沿图形](http://channel9.msdn.com/events/Build/2014/3-558?WT.mc_id=13398-DEV-sitepoint-article22)

或者我们团队的学习系列:

*   [让你的 HTML/JavaScript 更快的实用性能技巧](http://channel9.msdn.com/Series/Practical-Performance-Tips-to-Make-Your-HTMLJavaScript-Faster/06?WT.mc_id=13398-DEV-sitepoint-article22)(从响应式设计到休闲游戏再到性能优化的 7 部分系列)

*   现代网络平台快速启动(HTML、CSS 和 JS 的基础)

*   [使用 HTML 和 JavaScript JumpStart 开发通用 Windows 应用](http://www.microsoftvirtualacademy.com/training-courses/developing-universal-windows-apps-with-html-and-javascript-jump-start?WT.mc_id=13398-DEV-sitepoint-article22)(使用您已经创建的 JS 来构建应用)

以及一些免费工具: [Visual Studio 社区](http://www.visualstudio.com/en-us/products/free-developer-offers-vs.aspx?WT.mc_id=13398-DEV-sitepoint-article22)、 [Azure 试用版](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13398-DEV-sitepoint-article22)，以及[针对 Mac、Linux 或 Windows 的跨浏览器测试工具](http://modern.ie/?utm_source=SitePoint&utm_medium=article22&utm_campaign=SitePoint)。

本文是微软网站开发技术系列的一部分。我们很高兴与您分享[微软 Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13398-DEV-sitepoint-article22) 和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13398-DEV-sitepoint-article22)。在[http://dev.modern.ie/](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article22&utm_campaign=SitePoint)获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。

## 分享这篇文章