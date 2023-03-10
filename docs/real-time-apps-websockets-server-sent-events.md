# 利用 Websockets 和服务器发送的事件构建实时应用

> 原文：<https://www.sitepoint.com/real-time-apps-websockets-server-sent-events/>

*这篇文章由[克雷格·比尔纳](https://www.sitepoint.com/author/cbilner)和[丹·普林斯](https://www.sitepoint.com/author/dprince/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

编写富互联网应用程序的一个重要部分是对数据变化做出反应。考虑一下 Guillermo Rauch 的下面这段话，摘自他 2014 年在巴西的演讲[Rich Web Applications 的 7 个原则](https://www.youtube.com/watch?v=p2F-128e3sI)。

当服务器上的数据发生变化时，无需询问就让客户端知道。这是性能改进的一种形式，将用户从手动刷新操作(F5，拉至刷新)中解放出来。新的挑战:(重新)连接管理，状态协调。

在本文中，我们将看看如何使用原始的 [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) API 以及鲜为人知的[event source](https://developer.mozilla.org/en-US/docs/Web/API/EventSource)for[server-sent events(SSE)](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events)来构建自我更新的“实时”UI。如果你不确定我说的是什么意思，我推荐你看上面提到的视频，或者阅读[相应的博文](http://rauchg.com/2014/7-principles-of-rich-web-applications/#react-to-data-changes)。

## 简史

过去我们必须模拟服务器推送，最著名的方法是[长轮询](https://en.wikipedia.org/wiki/Push_technology#Long_Polling)。这涉及到客户端发出一个很长的请求，该请求将保持打开状态，直到服务器准备好推送消息。收到消息后，请求将被关闭，并发出新的请求。其他解决方案包括`<iframe>`黑客和 Flash。这并不理想。

然后，在 2006 年，Opera 从 WHATWG Web Applications 1.0 规范中引入了[服务器发送事件](https://en.wikipedia.org/wiki/Server-sent_events) (SSE)。
SSE 允许你从你的网络服务器向访问者的浏览器连续传输事件。其他浏览器紧随其后，在 2011 年开始实现 SSE，作为 HTML5 规范的一部分。

2011 年，当 [WebSocket 协议](https://en.wikipedia.org/wiki/WebSocket)被标准化后，事情变得越来越有趣。WebSockets 允许您打开客户机和服务器之间的双向持久连接，使您能够在服务器上的数据发生变化时将数据推回到客户机，而无需客户机请求它。这对于具有大量并发连接和快速变化内容的应用程序(例如多人在线游戏)的响应能力非常重要。然而，直到 2014 年[socket . io](http://socket.io)——将 WebSockets 推向大众的最突出的努力——发布后，我们才看到更多关于实时通信的实验。

可以说，今天我们有更简单的方法来实现服务器推送，而不需要发出新的请求或者依赖于非标准插件。这些技术使您能够在服务器上发生事情时将数据流回客户机。

## websocket

理解持久连接允许您做什么的最简单的方法是运行一个工作演示，我们稍后将逐步介绍代码，但现在下载演示并进行演示。

### 演示

```
git clone https://github.com/sitepoint-editors/websocket-demo.git
cd websocket-demo
npm install
npm start 
```

在多个浏览器窗口中打开 [http://localhost:8080/](http://localhost:8080/) ，观察浏览器和服务器中的日志，查看来回的消息。更重要的是，注意在服务器上接收一条消息所花费的时间，以及其余连接的客户端意识到这一变化所花费的时间。

### 客户

`WebSocket`构造器通过`ws`或`wss`(安全)协议启动与服务器的连接。它有一个`send`方法将数据推送到服务器，您可以提供一个`onmessage`处理程序从服务器接收数据。

下面是一个注释示例，显示了所有重要事件:

```
// Open a connection
var socket = new WebSocket('ws://localhost:8081/');

// When a connection is made
socket.onopen = function() {
  console.log('Opened connection 🎉');

  // send data to the server
  var json = JSON.stringify({ message: 'Hello 👋' });
  socket.send(json);
}

// When data is received
socket.onmessage = function(event) {
  console.log(event.data);
}

// A connection could not be made
socket.onerror = function(event) {
  console.log(event);
}

// A connection was closed
socket.onclose = function(code, reason) {
  console.log(code, reason);
}

// Close the connection when the window is closed
window.addEventListener('beforeunload', function() {
  socket.close();
}); 
```

### 服务器

到目前为止，在服务器上使用 WebSockets 最流行的节点库是 [ws](https://www.npmjs.com/package/ws) ，我们将使用它来简化事情，因为[编写 WebSocket 服务器](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers)不是一项简单的任务。

```
var WSS = require('ws').Server;

// Start the server
var wss = new WSS({ port: 8081 });

// When a connection is established
wss.on('connection', function(socket) {
  console.log('Opened connection 🎉');

  // Send data back to the client
  var json = JSON.stringify({ message: 'Gotcha' });
  socket.send(json);

  // When data is received
  socket.on('message', function(message) {
    console.log('Received: ' + message);
  });

  // The connection was closed
  socket.on('close', function() {
    console.log('Closed Connection 😱');
  });

});

// Every three seconds broadcast "{ message: 'Hello hello!' }" to all connected clients
var broadcast = function() {
  var json = JSON.stringify({
    message: 'Hello hello!'
  });

  // wss.clients is an array of all connected clients
  wss.clients.forEach(function each(client) {
    client.send(json);
    console.log('Sent: ' + json);
  });
}
setInterval(broadcast, 3000); 
```

`ws`包使得构建一个支持 WebSocket 的服务器变得简单，但是如果你在生产中使用它们，你应该仔细阅读一下 [WebSocket Security](https://devcenter.heroku.com/articles/websocket-security) 。

### 浏览器兼容性

浏览器对 WebSockets 的支持是稳固的，Opera Mini 和 IE9 及以下版本除外，有一个 [polyfill](https://github.com/gimite/web-socket-js) 可用于在幕后使用 Flash 的旧 IE。

我可以使用 WebSockets 吗？caniuse.com 主要浏览器对 websockets 功能的支持数据。