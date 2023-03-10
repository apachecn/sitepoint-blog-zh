# 构建 Node.js 支持的聊天室 Web 应用程序:入门

> 原文：<https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-getting-started/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

这个 [Node.js](http://nodejs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) 教程系列将帮助您构建一个完全部署在云中的 Node.js 支持的实时聊天室 web 应用程序。在这个系列中，你将学习如何在你的 Windows 机器上设置 [Node.js](http://nodejs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) (或者如果你在 Mac 上只学习概念)，如何用 [Express](http://expressjs.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 开发 web 前端，如何将 node express 应用部署到 [Azure](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=12779-DEV-sitepoint-link) ，如何使用 [Socket。IO](http://socket.io/?WT.mc_id=12833-DEV-sitepoint-othercontent) 添加实时层，以及如何一起部署。

本教程将使用可选的 Visual Studio 插件的 [Visual Studio](https://www.visualstudio.com/en-us/products/visual-studio-community-vs?WT.mc_id=12779-DEV-sitepoint-link) 和 [Node.js 工具作为开发环境——我已经提供了这两个工具的免费下载链接。这是一篇初级到中级水平的文章——你应该了解 HTML5 和 JavaScript。](http://nodejstools.codeplex.com/releases/view/119433?WT.mc_id=12833-DEV-sitepoint-othercontent)

> **第一部分——node . js 简介**
> 
> [第二部分——欢迎使用 Node.js 和 Azure 表达](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-expess-azure/)
> 
> 第 3 部分——用 Node.js、Mongo 和 Socket 构建后端。IO
> 
> [第 4 部分——用 Bootstrap 构建聊天室用户界面](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-chatroom-ui-bootstrap/)
> 
> [第 5 部分——用网络套接字连接聊天室](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-connecting-websockets)
> 
> [第六部分——压轴和调试远程 Node.js 应用](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-debugging-remotely/)

欢迎来到 Node.js 实践教程系列的第 1 部分。这一期我会讲解什么是 [Node.js](http://nodejs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) ，为什么要关注 Node.js，如何设置你的机器。

## Node.js 是什么？为什么要用？

[Node.js](http://nodejs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) 是一个运行环境和库，用于在浏览器之外运行 JavaScript 应用程序。Node.js 主要用于运行实时服务器应用程序，并通过使用非阻塞 I/O 和异步事件来展示其性能。围绕 Node.js 已经构建了一个完整的 web 生态系统，有几个 web app 框架和协议实现可供使用。这无疑是当今在 web 上开发实时应用程序的最简单、最快的方法之一。

为什么要用 Node.js？一个词:JavaScript。JavaScript 是一种非常流行的语言，被认为是将网络变成今天这样的动态仙境的驱动力之一。

JavaScript 出现在前端，但是由于 V8 JavaScript 引擎和 Ryan Dahl 的工作，你现在可以在浏览器之外运行网络 JavaScript 应用程序来构建 web 应用程序。Node.js 可以让你统一应用程序使用的编程语言。您不再需要为您的后端使用不同的语言——您可以全程使用 JavaScript。如果你的背景是用 HTML、CSS 和 JavaScript 构建和设计网站和 web 应用前端，你不需要学习另一种语言来为你的应用开发复杂的数据驱动的后端。

Node.js 作为前端和后端之间实时通信的方法，在 WebSockets 的发展中发挥了关键作用。使用 WebSockets 和基于该协议构建的库，如 Socket。IO 确实推动了人们对 web 应用程序的期望，并让我们开发人员探索创建 web 的新途径。

## 如何在 Windows 8 上设置 Node.js

首先，你需要一台相当先进的机器。我将向您展示如何在 Windows 8.1 上安装 Node.js。

### 1.安装 Node.js

首先，[下载安装 Node.js 运行时](http://nodejs.org/download/?WT.mc_id=12833-DEV-sitepoint-othercontent)。选择 Windows Installer 是最简单的开始方式之一。

或者，如果您是 Windows 软件包管理器 [Chocolatey](http://chocolatey.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) 的粉丝，您可以通过运行以下命令来安装 Node.js:

```
choco install nodejs.install
```

### 2.确认设置

您应该仔细检查 Node.js 可执行文件是否已添加到 PATH 系统环境变量中。[如果您想了解如何在 Windows 8 和 Windows 8.1 上更改环境变量，请观看此视频](https://www.youtube.com/watch?v=W9pg2FHeoq8?WT.mc_id=12833-DEV-sitepoint-othercontent)。您需要确保以下文件夹已添加到 PATH 变量中:

```
C:\Program Files (x86)\nodejs\
```

如果在命令提示符下键入 node–h，应该会看到 node.js 可执行文件的帮助文档。

与 Node.js 一起， [npm](https://www.npmjs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) ，用于管理 node.js 包的系统，应该也已经安装，并且可以在命令提示符下使用。只需输入`npm –h`，您应该会看到 npm 的帮助文档。

### 如果出现错误…

如果您遇到类似这样的错误…

```
Error: ENOENT, stat 'C:\Users\someuser\AppData\Roaming\npm'
```

…解决方法是在上面指定的路径下创建一个文件夹，如这个 [StackOverflow](http://stackoverflow.com/questions/25093276/nodejs-windows-error-enoent-stat-c-users-rt-appdata-roaming-npm?WT.mc_id=12833-DEV-sitepoint-othercontent) 问题所示。这只是最新 Node.js 安装程序中的一个问题，应该会在下一版本中得到解决。您可以像这样创建文件夹:

```
mkdir -r C:\Users\someuser\AppData\Roaming\npm
```

## 选择开发工具

安装 Node.js 后，是时候选择一个开发工具了。你可以随意使用任何你想要的编辑工具。在这些教程中，我将使用 [Visual Studio](http://www.visualstudio.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 来开发、调试和部署聊天引擎；以及 Node.js Tools for Visual Studio(NTVS)——一个免费的 Visual Studio 开源插件，支持开发 node . js 应用。

NTVS 的酷之处在于它增加了 Node.js 对编辑、智能感知、性能分析、npm、TypeScript、本地和远程调试(包括在 Windows/MacOS/Linux 上)的支持。它还使快速创建网站并将其部署到 Microsoft Azure 网站或云服务变得容易。

如果你没有 Visual Studio，你可以下载一个免费的 [Visual Studio 2013 社区版](https://www.visualstudio.com/en-us/products/visual-studio-community-vs?WT.mc_id=12779-DEV-sitepoint-link)。然后，下载 Visual Studio 插件的免费 [Node.js 工具。您也可以使用 Visual Studio 2012 Pro 或更高版本(需要更新 4)或 Visual Studio 2013 或更高版本(需要更新 2)。NTVS 插件与这两个版本都兼容。](http://nodejstools.codeplex.com/releases/view/119433?WT.mc_id=12833-DEV-sitepoint-othercontent)

## 在 Visual Studio 中启动一个新的 Node.js 项目

*注:截图显示的是 Visual Studio 2013 旗舰版。*

开始一个新的 Node.js 项目相当简单。

1.  启动 Visual Studio。在文件菜单上，点击**新建**，然后点击**项目**。

![](img/ed7c5321d239c7d895c1ee083bb56ab0.png)

3.  在新建项目窗口中，展开**已安装**菜单选项，展开**模板**，点击 **JavaScript** 。在主窗口中，选择**空白 Node.js Web 应用**。为你的项目选择一个位置和名称，然后点击 **OK** 。

![](img/accd447a314d560419d1a2cbf97ba8bd.png)

5.  您将看到以下屏幕。现在，请随意探索 Visual Studio。您将希望在解决方案浏览器中打开生成的`server.js`文件(通常在右边，但也可能位于屏幕上的其他地方。)

![](img/989fb9358b23e5186e9bb12942e1b940.png)

7.  现在，您可以在首选浏览器中调试 node.js web 应用程序。

![](img/33fbbaba5328b1bd798ba4903f41ee85.png)

## [node . js 中的一款“Hello World”app](https://gist.github.com/sayar/a3518e26176a45e14aa2?WT.mc_id=12833-DEV-sitepoint-othercontent)

下面是上面链接的 server.js 文件中使用的代码。

```
var http = require('http');
var port = process.env.port || 1337;
http.createServer(function (req, res) {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World\n');
}).listen(port);
```

和其他语言一样，生成的代码示例向您展示了如何在浏览器中输出“Hello World”。我来解释一下`server.js`中生成的代码是如何一行一行的工作的。正如本教程系列描述中所述，我假设您了解 JavaScript、HTML5 以及 HTTP/Internet 的工作原理。

#### 第一行

```
var http = require(‘http’);
```

js 有一个简单的模块和依赖加载系统。您只需使用包含您想要加载的模块的文件或目录的路径来调用`function “require”`，此时会返回一个包含该模块所有导出函数的变量。

#### 第二行

```
var port = process.env.port || 1337;
```

在这一行，我们想确定服务于 HTML 的 HTTP 服务器应该在哪个端口上运行。如果在环境变量中指定了端口号，我们将使用该端口号，或者我们将简单地使用 1337。

#### 第 3 行

```
http.createServer(function (req, res) {
```

我们想创建一个服务器来处理 HTTP 请求。我们还将向 createServer 函数传递一个包含两个参数的函数回调，以处理每个单独的请求并返回一个响应。如果您从未在 JavaScript 中遇到过回调函数，可以看看 Michael Vollmer 的文章。接收到的请求在`req parameter`中传递，响应被写入 res 参数。

#### 第 4 行

```
res.writeHead(200, { ‘Content-Type’: ‘text/plain’ });
```

任何 HTTP 响应都需要状态行和消息头，要了解更多关于 HTTP 消息头及其工作原理，请查看这篇文章。在这种情况下，我们希望返回 200 OK 作为状态响应，并将内容类型指定为纯文本。我们通过调用响应对象上的`writeHead function`来指定这一点。

#### 第 5 行

```
res.end(‘Hello World\n’);
```

一旦我们写完响应，我们想调用`end function`。我们还可以通过`end function`传递最终内容，在本例中，我们希望以纯文本形式发送字符串“Hello World”。

#### 第 6 行

```
}).listen(port);
```

我们关闭回调并在我们之前定义的端口调用`function listen`,这将启动服务器并开始接受发送到定义的端口的请求。

要查看结果，您可以点击前面截图中显示的按钮开始调试。你可以在浏览器里看到“Hello World”。

![](img/e3235ca0c0ecf51c4952e2ddc51d3f2b.png)

瞧啊。您现在已经使用 Visual Studio 2013 在 Windows 8.1 上成功运行了 Node.js 应用程序。

## 请继续关注第 2 部分！

第 2 部分—如何将您的“Hello World”部署到云中是这里的。你可以通过关注[我的推特账号](https://twitter.com/ramisayar?WT.mc_id=12833-DEV-sitepoint-othercontent)来了解这篇文章和其他文章的最新动态。

## Azure 上节点的更多学习

为了更深入地学习 node，我的课程可以在微软虚拟学院的这里[找到。](http://www.microsoftvirtualacademy.com/training-courses/building-apps-with-node-js-jump-start?WT.mc_id=12833-DEV-sitepoint-othercontent)

或者类似节点题材的一些较短格式的视频:

*   六部分系列:用 node 构建应用程序。JS

*   [漫步穿越节点(Coding4Fun)](http://channel9.msdn.com/coding4fun/blog/A-Stroll-Through-Node?WT.mc_id=12833-DEV-sitepoint-othercontent)

本文是微软网站开发技术系列的一部分。我们很高兴与你分享斯巴达项目和它的 T2 新渲染引擎。在 [modern 获得免费虚拟机或在你的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://modern.ie/?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint)。

## 分享这篇文章