# 使用 Reveal.js 创建多用户演示文稿

> 原文：<https://www.sitepoint.com/create-multi-user-presentation-reveal-js/>

创建令人印象深刻的演示文稿是一门艺术。长期以来，PowerPoint 一直是创建演示文稿的实际工具。

现在，事情已经发生了变化，因为网络已经成为所有业务的焦点，而且浏览器功能也有了巨大的改进。现代浏览器现在能够呈现 3d 图形和动画，就像在任何其他本地应用程序中一样。

然后出现了一些基于 HTML5 和 CSS3 的很酷的演示库。Reveal.js 是一个非常受欢迎的库，用于创建令人惊叹的演示文稿。

Websockets 是作为 HTML5 规范的一部分定义的新标准，它支持浏览器中的双向全双工通信。有许多 JavaScript 库使得使用 Websockets 更加容易，其中有哪些 Socket？木卫一就是突出的一个。

在本文中，我们将讨论如何创建一个可以被多个用户控制的 Reveal.js 演示。我们将利用套接字。用于实时发送和接收幻灯片更改事件的 IO。Reveal.js 已经提供了一个多路复用器插件，但是这有点难设置，所以我们暂时忽略它。让我们把重点放在如何编写套接字上。符合我们目的的 IO 服务器。

## 先决条件

本文假设您已经安装并可以使用以下库:

*   节点. js
*   约曼
*   咕哝
*   凉亭

## 初始步骤

首先，我们将设置一个 express.js 服务器。在生成器的帮助下，Yeoman 使安装和运行 express.js 服务器变得很容易。所以我们将首先使用 npm 安装 yeoman express-generator。

```
$ npm install –g generator-express
```

这将在全局范围内安装快速生成器。现在让我们设置服务器。

```
$ yo express
```

这将询问您应该安装哪种类型的 express。可以选择 Basic 或者 MVC 在我们的例子中，我们只需要基本的设置。然后它会安装一堆 npm 模块以及 bower.json 和 Gruntfile.js 文件。只有必要的文件，应用程序目录看起来像这样:

□grunt file . js□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□

现在让我们使用 grunt 启动 express 服务器。

```
$ grunt
Running "develop:server" (develop) task
>> started application "app.js".

Running "watch" task
Express server listening on port 3000
```

Yeoman 为我们创建了一个默认的 app.js 文件，其中包含运行服务器所需的设置。另外，请注意，它附带了“watch”库，该库将跟踪代码中的更改并自动重新加载服务器，这样我们就不需要手动执行了。在我们继续之前，我们将使用 bower 安装和设置 reveal.js 库。安装 reveal.js 非常简单明了。只需在终端中发出以下命令。

```
$ bower install reveal.js --save
```

这将从 Github 获取 reveal.js 库的最新稳定版本，并将安装在 public/components 目录下。`--save`选项自动用 reveal.js 更新 bower.json 文件的依赖部分。

现在我们已经拥有了创建演示服务器所需的一切。我们将从制作演示文稿的第一张幻灯片开始。为此，在 views 文件夹中创建一个 HTML 文件。

```
<!-- views/index.html -->
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Revealer - Reveal.js multiplexer</title>
        <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
        <link rel="stylesheet" href="components/reveal.js/css/reveal.min.css">
        <link rel="stylesheet" href="components/reveal.js/css/theme/default.css" id="theme">
    </head>
    <body>
        <div class="reveal">
            <div class="slides">
                <section>
                    <h1>Revealer</h1>
                    <h3>Reveal.js multiplexer</h3>
                </section>
            </div>
        </div>
        <script src="components/reveal.js/js/reveal.min.js"></script>
        <script type="text/javascript">
            Reveal.initialize({history: true});
        </script>
    </body>
</html>
```

这是我们开始使用 reveal.js 所需要的最基本的 HTML。这里我们包括了 Reveal.js CSS 和 JavaScript 文件。会使上面的 HTML 看起来更好。div 中带有类`slides`的任何部分都将作为幻灯片。

在开始演示之前，我们需要设置我们的服务器来根据请求提供这个文件。所以我们将用下面给出的代码更新 app.js。

```
var express = require('express')
  , http = require('http')
  , path = require('path')
  , app = express();

app.configure(function(){
  app.use(express.static(path.join(__dirname, 'public')));
});

app.get('/', function(req, res){
  res.sendfile(__dirname + '/views/index.html');
});

var server = http.createServer(app).listen(3000, function(){
  console.log("Express server listening on port 3000");
});
```

前几行需要我们的服务器的必要依赖项，然后创建一个 express 对象。下一行将 public 文件夹配置为静态目录，服务器将在该目录中查找对静态文件的请求。然后，我们添加一个路由来服务 index.html 文件并启动服务器。

现在我们可以使用 http://localhost:3000/ url 在浏览器中看到演示文稿。但这不是我们真正需要的。我们需要这个演示是多路复用的，这样当一个用户改变幻灯片时，它应该反映在另一个用户的浏览器上。

接下来，我们将安装和设置 Socket.io 模块，以便使用 Websockets 实现双向通信。

```
$ npm install socket.io --save
```

一旦安装完成，我们就可以在我们的演示服务器中启用 websockets 了。首先，通过在变量声明部分添加以下行，在 app.js 文件中需要 socket.io 库。

```
var io = require(“socket.io”);
```

现在，我们需要将之前创建的 express 服务器传递给 socket.io，然后告诉服务器在连接新客户端时发送一条欢迎消息。

```
io.listen(server);
io.sockets.on('connection', function (socket) {
  socket.emit("message", "Welcome to Revealer");
});
```

当客户端连接时，服务器可以响应客户端。在上面的代码中，连接事件的回调函数将客户端的套接字作为参数，并将欢迎消息发送回客户端。

让我们转到客户端 JavaScript，它将连接到这个服务器。首先，我们需要在 HTML 中包含 socket.io 客户端库。

```
<script src="/socket.io/socket.io.js"></script>
```

接下来，我们将连接到我们创建的 Websocket 服务器。

```
var socket = io.connect("http://localhost:3000");
socket.on("message", function(data){
    console.log(data);
});
```

io.connect 将使用给定的 URL 连接到服务器。一旦连接上，我们知道我们的服务器将会用一条欢迎消息来响应，我们已经将这条消息登录到控制台中。

现在我们的客户机和服务器都准备好了，我们可以开始真正的工作了。当演示者改变幻灯片时，它应该通知服务器更新所有其他客户端。

```
notifyServer = function(event){
    data = {
      indexv : Reveal.getIndices().v,
      indexh : Reveal.getIndices().h,
      indexf : Reveal.getIndices().f || 0
    }
    socket.emit("slidechanged" , data);
  }

  Reveal.addEventListener("slidechanged", notifyServer);

  Reveal.addEventListener("fragmentshown", notifyServer);

  Reveal.addEventListener("fragmenthidden", notifyServer);
```

当幻灯片发生变化时，Reveal.js 会调度一个`slidechanged`事件。在幻灯片片段的情况下，它会创建一个`fragmentshown`或`fragmenthidden`事件。我们在这里处理所有这些情况，当这样的事件发生时，它将调用`notifyServer`回调函数。在任何时间点`Reveal.getIndices()`返回当前幻灯片的位置——水平、垂直和片段索引。当调用`notifyServer`函数时，它会将幻灯片位置存入一个数据对象。然后，客户机将向服务器发出一个`slidechanged`事件以及创建的数据。

在服务器端，我们需要处理客户端发出的`slidechanged`事件的能力，这将更新所有连接的客户端。为此，请在连接处理程序中添加以下代码。

```
socket.on("slidechanged", function(data){
   socket.broadcast.emit("slidechanged", data);
 });
```

socket.broadcast.emit 会将数据发送给除发送方以外的所有客户端。因此，在这里，当服务器接收到`slidechanged`事件时，它会简单地将幻灯片数据转发给所有其他客户端。

客户端还应该通过移动到相应的幻灯片或片段来处理服务器转发的这个`slidechanged`事件。为此，在客户端添加

```
socket.on('slidechanged', function (data) {
    Reveal.slide(data.indexh, data.indexv, data.indexf);
  });
```

`Reveal.slide()`有三个参数，水平索引、垂直索引和片段索引，在发生`fragmentshown`或`fragmenthidden`事件时会有值。

## 添加安全性

现在我们已经创建了一个简单的多用户 Reveal.js 演示。但是这有一个严重的问题，因为任何用户都可以控制表示。我们可以通过在服务器端代码中添加一个基本的身份验证来解决这个问题，并为未通过身份验证的用户提供一个替代路径。

```
var masterUser = 'username'
      , masterPass = 'password';

// Authentication
var auth = express.basicAuth(masterUser, masterPass);

app.get('/', auth, function(req, res){
  res.sendfile(__dirname + '/views/master.html');
});

app.get('/client', function(req, res){
  res.sendfile(__dirname + '/views/client.html');
});
```

现在，当用户请求“/”路由时，浏览器会要求提供身份验证凭据。`express.basicAuth`创建一个基本的认证中间件，我们将它传递到“/”路由中。如果登录成功，它将发送 master.html。其他用户可以使用“/client”路由来查看演示文稿，我们不会向服务器发送任何幻灯片更改事件。

完整的代码将如下所示。

```
// server
var express = require('express')
  , http = require('http')
  , path = require('path')
  , ioServer = require('socket.io')
  , app = express()
  , masterUser = 'username'
  , masterPass = 'password';

app.configure(function(){
  app.use(express.static(path.join(__dirname, 'public')));
});

// Authentication
var auth = express.basicAuth(masterUser, masterPass);

app.get('/', auth, function(req, res){
  res.sendfile(__dirname + '/views/presentation.html');
});

app.get('/client', function(req, res){
  res.sendfile(__dirname + '/views/client.html');
});

var server = http.createServer(app).listen(3000, function(){
  console.log("Express server listening on port 3000");
});

var io = ioServer.listen(server);
io.sockets.on('connection', function (socket) {
  socket.emit("message", "Welcome to Revealer");
  socket.on("slidechanged", function(data){
    socket.broadcast.emit("slidechanged", data);
  });
});

//client
(function(){
  var host = 'http://localhost:3000',
    , socket = io.connect(host);
  Reveal.initialize({
    history: true
  });

  /** start - only in master.js **/
  notifyServer = function(event){
    data = {
      indexv : Reveal.getIndices().v,
      indexh : Reveal.getIndices().h,
      indexf : Reveal.getIndices().f || 0
    }
    socket.emit("slidechanged" , data);
  }
  // listeners for slide change/ fragment change events
  Reveal.addEventListener("slidechanged", notifyServer);
  Reveal.addEventListener("fragmentshown", notifyServer);
  Reveal.addEventListener("fragmenthidden", notifyServer);
  /** end - only in master.js **/

  // Move to corresponding slide/ frament on receiving 
  // slidechanged event from server
  socket.on('slidechanged', function (data) {
    Reveal.slide(data.indexh, data.indexv, data.indexf);
  });

})();
```

你可以在 [Github](https://github.com/shameerc/Revealer.js) 上找到所有的源代码。

## 摘要

在本文中，我们看到了如何构建一个简单的 Reveal.js 演示，它可以被多个用户控制。这里我们使用了插座。IO 库，用于实时更新所有连接的客户端。我们还增加了一个基本的安全措施，防止未经授权的用户控制演示。

您可以添加更多的功能，并使用像 WebRTC 这样的技术来使它变得更普遍，所以我希望您能看到这篇文章只是一个开始。

## 分享这篇文章