# 构建一个 Node.js 支持的聊天室 Web 应用程序:用 WebSockets 连接

> 原文：<https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-connecting-websockets/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

这个 [Node.js](http://nodejs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) 教程系列将帮助您构建一个完全部署在云中的 Node.js 支持的实时聊天室 web 应用程序。在这个系列中，你将学习如何在你的 Windows 机器上设置 [Node.js](http://nodejs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) (或者如果你在 Mac 上只学习概念)，如何用 [Express](http://expressjs.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 开发 web 前端，如何将 Node.js Express 应用部署到 [Azure](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=12779-DEV-sitepoint-link) ，如何使用 [Socket。IO](http://socket.io/?WT.mc_id=12833-DEV-sitepoint-othercontent) 添加实时层，以及如何一起部署。

教程将使用可选的 [Visual Studio](https://www.visualstudio.com/en-us/products/visual-studio-community-vs?WT.mc_id=12779-DEV-sitepoint-link) 和 [Node.js Tools for Visual Studio 插件](http://nodejstools.codeplex.com/releases/view/119433?WT.mc_id=12833-DEV-sitepoint-othercontent)作为开发环境。我提供了这两个工具的免费下载链接。这是一篇初级到中级水平的文章——你应该了解 HTML5 和 JavaScript。

> [第一部分——node . js 简介](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-getting-started/)
> 
> [第二部分——欢迎使用 Node.js 和 Azure 表达](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-expess-azure/)
> 
> 第 3 部分——用 Node.js、Mongo 和 Socket 构建后端。IO
> 
> [第 4 部分——用 Bootstrap 构建聊天室用户界面](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-chatroom-ui-bootstrap/)
> 
> 第 5 部分–用 WebSockets 连接聊天室
> 
> [第六部分——压轴和调试远程 Node.js 应用](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-debugging-remotely/)

## 第 5 部分–用 WebSockets 连接聊天室

欢迎来到 Node.js 实用教程系列的第 5 部分:构建 Node.js 支持的聊天室 web 应用程序。

在这一期中，我将向您展示如何将前端聊天室连接到您在[第 2 部分](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-expess-azure/)、[第 3 部分](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-node-mongodb-socket/)和[第 4 部分](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-chatroom-ui-bootstrap)中构建的 Node.js 聊天室后端。

## 添加 jQuery、SocketIO 和 index.js

在开始编写前端 JavaScript 代码之前，我们要做的第一件事是确保我们需要的文件和依赖项将由 Node.js 服务器提供。让我们先将`jQuery`和`Socket.IO`添加到`layout.jade`文件中，该文件由我们项目中的所有其他 jade 文件扩展。

用一个到我们需要的所有其他文件的链接替换到`bootstrap.min.js`的单个链接。

```
script(type='text/javascript'
src='http://ajax.aspnetcdn.com/ajax/jQuery/jquery­2.1.1.min.js')
script(type='text/javascript' src='/js/bootstrap.min.js')
script(type='text/javascript' src='/socket.io/socket.io.js')
```

注意，第一行链接到托管在微软 Ajax 内容交付网络上的 jQuery。这个 CDN 托管流行的第三方 JavaScript 库，如 jQuery，使您能够轻松地将它们添加到您的 Web 应用程序中。通过使用 CDN，可以显著提高 Ajax 应用程序的性能。CDN 的内容缓存在世界各地的服务器上。CDN 支持 SSL (HTTPS ),以防您需要使用安全套接字层提供网页服务。

现在，我将在末尾再添加一行来创建一个新块。

```
block body_end
```

我这样做是为了让任何扩展了`layout.jade`的 Jade 文件也可以在 body 标签的末尾添加脚本标签。

现在我们想使用这个新块添加一个链接到我们将在`public/js`文件夹中创建的`index.js`文件，确保创建这个文件。

```
block body_end
    script(type='text/javascript' src='/js/index.js')
```

确保块从零缩进开始，以遵循 Jade 编码约定。如果您运行 Node.js 服务器并在浏览器中导航到主页，您将在 F12 工具中注意到文件被正确地提供。

## 对 app.js 的快速更改

我想在`app.js`中改变一些事情。首先，我想颠倒排序方向，让最老的消息先发，后发。我还想在计划接收新消息的同一频道上发送之前收到的聊天消息。更改将出现在`app.js`中的第 49 行和第 50 行。这是结果:

```
var stream = collection.find().sort().limit(10).stream();
stream.on('data', function (chat) { socket.emit('chat', chat.content); });
```

不要忘记在重新运行`app.js`文件之前设置`CUSTOMCONNSTR_MONGOLAB_URI`环境变量，否则 Node.js 后端将在无法连接到 MongoDB 时崩溃。

## 打开发送按钮的电源

是时候启动 send 按钮，使用 WebSockets 向聊天频道上后端服务器发送消息框中的消息了。

```
var socket = io();
$('#send-message-btn').click(function () {
    var msg = $('#message-box').val();
    socket.emit('chat', msg);
    $('#messages').append($('<p>').text(msg));
    $('#message-box').val('');
    return false;
});
socket.on('chat', function (msg) {
    $('#messages').append($('<p>').text(msg));
});
```

#### 第一行

我们想要创建一个套接字，我们可以通过调用`socket.io-client.js`文件中的`io()`函数来实现。

#### 第二行

之后，我们希望使用 jQuery 的`$()`函数和`click()`方法，在单击“发送消息”按钮时执行一个函数。

#### 第 3 行

我们将使用 jQuery 的`$()`函数和`val()`方法获得消息框中的字符串值。

#### 第 4 行

我们在第 1 行创建的 socket 变量上使用`emit()`函数，在“聊天”通道上发送包含消息框值的消息。

#### 第 5-7 行

此时，我们将把消息框中的消息附加到 ID 为`messages`的`div`中，这样用户就可以看到消息已经发送。我们将把消息框的值赋给一个空字符串来清除它。

#### 第 9-10 行

我们希望将聊天频道上从其他用户收到的消息附加到 ID 为`messages`的`div`中。Node.js 后端不会重新发送客户端写回给自己的消息，但这没关系，因为我们马上在`click()`函数处理程序中添加了消息。

![What you’ll see if you successfully connected your backend and your front-end using WebSockets](img/d7e04185ea227a86979c28364c75ae1e.png)

## 结论

瞧啊。您已经使用 WebSockets 连接了后端和前端。如果你想识别聊天室里的人或者给每个用户添加一个头像，这由你决定，但是你可以继续使用这个聊天室。在下一期中，我将向您展示如何将这个匿名聊天室部署到 Azure，并向您展示如何调试它。

## 敬请关注第 6 部分！

第 6 部分—结束和调试远程节点应用程序！这里的[是](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-debugging-remotely/)。你可以通过关注[我的推特账号](https://twitter.com/ramisayar?WT.mc_id=12833-DEV-sitepoint-othercontent)来了解这篇文章和其他文章的最新动态

## Azure 上的更多 Node.js 学习

为了更深入地学习 node，我的课程可以在微软虚拟学院的这里[找到。](http://www.microsoftvirtualacademy.com/training-courses/building-apps-with-node-js-jump-start?WT.mc_id=12833-DEV-sitepoint-othercontent)

或者类似节点题材的一些较短格式的视频:

*   六部分系列:用 node 构建应用程序。JS

*   [漫步穿越节点(Coding4Fun)](http://channel9.msdn.com/coding4fun/blog/A-Stroll-Through-Node?WT.mc_id=12833-DEV-sitepoint-othercontent)

本文是微软网站开发技术系列的一部分。我们很高兴与你分享斯巴达项目的[和它的](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)[新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=12779-DEV-sitepoint-link)。在 [modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://modern.ie/?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint) *。*

## 分享这篇文章