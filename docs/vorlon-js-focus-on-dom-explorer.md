# Vorlon.js:关注 DOM Explorer

> 原文：<https://www.sitepoint.com/vorlon-js-focus-on-dom-explorer/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

2015 年 4 月，我们在微软的工程师和技术传道者团队发布了[vorlon . js](http://www.vorlonjs.com/)——一款开源、可扩展、平台无关的工具，用于远程调试和测试您的 JavaScript。本文是详细研究一个特定特性的系列文章的第一篇，从最大的特性之一开始:DOM Explorer**。**

 **## 概观

如果你不熟悉 vorlon.js，我推荐你先阅读[这篇博文](http://blogs.msdn.com/b/eternalcoding/archive/2015/04/30/why-we-made-vorlon-js-and-how-to-use-it-to-debug-your-javascript-remotely.aspx/?WT.mc_id=13421-DEV-sitepoint-article45)，这篇博文解释了我们为什么要做 vorlon.js 以及如何使用它。您将在本文末尾找到更多的 vorlon.js 信息。

DOM 检查器向您显示远程网页的 DOM。您可以检查 DOM，点击节点将在主机网页中突出显示它们，如果您选择了一个节点，您还可以查看和修改它的 CSS 属性。

![DOM explorer](img/4ee788a1efa35b552d5b059f6d975034.png)

## 安装 Vorlon.js

提醒一下，以下是使用 Vorlon.js 需要做的事情:

*   安装并运行 **Vorlon。来自 npm 的 JS** 服务器:

    ```
    $ npm i -g vorlon
    $ vorlon
    ```

*   一旦 **Vorlon.js** 完成安装，你现在可以运行服务器:

    ```
    $ vorlon
    The Vorlon server is running
    ```

*   服务器运行时，在浏览器中打开`http://localhost:1337`查看 **Vorlon.js** 仪表盘。
*   最后一步是通过将此脚本标记添加到您的应用程序来启用 Vorlon.js:

```
<script src="http://localhost:1337/vorlon.js"></script>
```

现在，当您打开应用程序时，您应该会看到您的客户端出现在仪表盘上。

## 使用 DOM Explorer

默认情况下，DOM explorer 是打开的，但是如果您需要启用它，您必须转到 *`[Vorlon folder]\Server\config.json`* 并启用插件:

```
{
    "useSSL": false,
    "includeSocketIO": true,
    "activateAuth": false,
    "username": "",
    "password": "",
    "plugins": [
        { "id": "DOM", "name": "Dom Explorer", "panel": "top", "foldername": "domExplorer", "enabled": true }
    ]
}
```

一旦启用，你将能够通过插件的主窗口控制几乎所有与 DOM 相关的东西。

![Using DOM explorer](img/a396e5dbb4c7a471b6f7046fae12cde1.png)

以下是您可以做的事情:

### 选择覆盖

将鼠标移动到任何节点上，您将能够看到该节点在客户端的位置:

![Selection Overlay](img/54e03a6c153f302c5119a9c826af5364.png)

### 实时文本编辑

通过双击 DOM explorer 窗口中的任何文本，您可以对其进行实时编辑:

![Live text editing](img/16327a4c8c41f7d7ae3a0fc07c557bac.png)

但是您也可以使用右窗格上的 HTML 部分来编辑 HTML 文本内容:

![Live text editing console](img/1cf8ac0194396bd1a027748d24e8957c.png)

右键单击节点本身也可以实现该功能。

### 属性版本

节点的属性也可以通过单击进行编辑:

![Attributes edition](img/276162023e41c1b0617c049bcf36e074.png)

但是您也可以右键单击节点名称本身来添加新属性:

![Attributes edition](img/9d67dc51c77844165a97ac55abf8e0b1.png)

通过右键单击现有属性，您将获得更多选项，如更新值或名称或删除属性:

![Attributes edition](img/3cc646e58c3eadc76e2cc6ad0f08e77c.png)

### 使用 CSS 选择器搜索

当处理大的 HTML 页面时，你可能想要搜索一个特定的节点。这就是我们引入“通过 CSS 选择器搜索节点”特性的原因。

只需在搜索框中输入您的选择器，就大功告成了！

![Search using CSS selector](img/e5c8845c73bf5dc7b1ae3c3622fae4bd.png)

### 动态刷新

DOM Explorer 窗口既可以在客户端 DOM 改变时自动刷新(注意，即使我们使用增量更新，这也会消耗大量的 CPU 能力和网络带宽),也可以按需刷新。

自动刷新在“设置”面板上控制:

![Dynamic refresh](img/68aa9580f1df6f14dfb9cd0a55f9ed2a.png)

当自动刷新关闭时，刷新按钮可以告诉您客户端上是否有可用的更新(按钮上的小圆点会变红):

![Refresh button](img/e2fbfae8f508577d6ea993f09f3225b5.png)

在这种情况下，只需点击按钮就会启动页面的完全刷新

### 样式编辑器

当您单击一个节点时,“样式”窗格将显示为此节点明确定义的所有样式:

![Styles editor](img/24579c726fa9ae39f04ca36f93fb935b.png)

然后，您可以使用“+”按钮添加新样式或单击现有样式来更改它们的值:

![Styles editor](img/47f0c239b9c52cc956a4b12e80eef2d2.png)

要查看应用于节点的所有样式(包括隐式样式)，只需使用“计算样式”窗格:

![Styles editor computed styles](img/4df74d34447a0e59c3cf755bf3b958f6.png)

### 布局

像浏览器的 F12 工具一样，布局窗格可以帮助您理解您选择的每个节点的布局:

![Layout pane](img/63449438d63f36044fa88a5064e6ffc7.png)

### 带交互式控制台插件的链接

最后，您会发现最后一件有用的事情:当一个节点有一个 ID 时，您可以单击该节点右侧的小按钮，将它直接链接到交互式控制台中，在那里您将能够使用它执行您想要的代码:

![Link interactive console plugin](img/9c8d96bd6c82c80fddf5f3e69a639389.png)

### 更进一步

对于一个插件来说，这已经是很多特性了。我希望它能帮助您调试和修复您的远程站点或 web 应用程序！

如果您有兴趣进一步了解 Vorlon.js，您可能会发现这些文章很有趣:

*   [我们为什么制作 vorlon.js，以及如何使用它远程调试您的 JavaScript】](http://blogs.msdn.com/b/eternalcoding/archive/2015/04/30/why-we-made-vorlon-js-and-how-to-use-it-to-debug-your-javascript-remotely.aspx/?WT.mc_id=13421-DEV-sitepoint-article45)
*   [如何创建 Vorlon.js 插件](http://blogs.msdn.com/b/emargraff/archive/2015/06/01/how-to-create-a-vorlon-js-plugin.aspx/?WT.mc_id=13421-DEV-sitepoint-article45)
*   [Vorlon.js 0.0.15 出了](http://blogs.msdn.com/b/emargraff/archive/2015/06/24/vorlon-js-0-0-15-is-out.aspx/?WT.mc_id=13421-DEV-sitepoint-article45)

我们也在寻找更多的贡献者来帮助我们创造最有用的工具。因此，如果你有兴趣投稿，请[访问我们的 GitHub 知识库](https://github.com/MicrosoftDX/Vorlonjs)。

### JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13421-DEV-sitepoint-article45) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13421-DEV-sitepoint-article45)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article45&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article45&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article45&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article45&utm_campaign=SitePoint)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development)

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=13421-DEV-sitepoint-article45)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=13421-DEV-sitepoint-article45)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com) 和 [babylonJS](http://babylonjs.com) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=13421-DEV-sitepoint-article45)(来自律师奶爸和基里尔·赛克谢诺夫，包括流形。JS 项目)

更多面向网络平台的免费跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13421-DEV-sitepoint-article45)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=13421-DEV-sitepoint-article45) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13421-DEV-sitepoint-article45)

## 分享这篇文章**