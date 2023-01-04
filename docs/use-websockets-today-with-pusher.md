# 如何在 Pusher 中使用 WebSockets

> 原文：<https://www.sitepoint.com/use-websockets-today-with-pusher/>

Brian Raymor 最近的 SitePoint 文章, [WebSockets:稳定并为开发者做好准备](https://www.sitepoint.com/websockets-stable-and-ready-for-developers/)提供了该技术的精彩概述。本质上，WebSockets 允许客户端和服务器之间的异步双向通信。连接保持打开，因此可以随时从任一设备发送轻量级消息。这是实时聊天和游戏的理想解决方案，可能比 Ajax 轮询更有效。

也就是说，有几个缺点:

*   WebSocket 技术相对较新，在撰写本文时，它只受到 Firefox 和 Chrome 的完全支持。
*   即使你的浏览器支持 WebSockets，也不能保证你的服务器平台会支持它。

稳定准备好了吗？也许还没有。

幸运的是，如果你今天不顾一切地利用 WebSocket 技术，还有另一个选择。 [Pusher](http://pusher.com/) 是一项新服务，它填补了 WebSocket 实现中的空白:

*   Pusher 是一种代理服务，位于您的客户端和服务器之间。
*   在客户端，Pusher 为 JavaScript、iOS、Android、Flash、.NET，Silverlight，Ruby 和 Arduino。
*   如果您使用的浏览器不提供原生支持，JavaScript 库会自动导入一个基于 Flash 的 shim，该 shim 在 IE6 到 9、Chrome 1 到 4、Firefox 1 到 3、Safari 1 到 4、Opera 和 Android 上实现 WebSocket 技术。
*   在服务器端，Pusher 为 PHP(通用和框架选项)、Ruby、ASP.NET(c#和 VB)提供了一系列 WebSocket 库。NET)、Python、Node.js、Java、Groovy/Grails、Clojure、Coldfusion 和 Perl。
*   如果您不喜欢使用 WebSocket API 来触发服务器事件，您可以采用 [Pusher 的 REST API](http://pusher.com/docs/rest_api) 。

整个过程简单得令人放心。一旦你[注册了一个推销商账户](https://app.pusherapp.com/accounts/sign_up)，你就会收到一个 API 密匙。您的 HTML5 页面包含 JavaScript 库，建立连接并订阅命名通道:

```
 <script src="http://js.pusher.com/1.11/pusher.min.js"></script>
<script>
var pusher = new Pusher("YOUR-PUSHER-API-KEY");
var channel = pusher.subscribe("my-channel"); 
```

然后，您可以定义绑定到通过 Pusher 套接字传入的事件的回调:

```
 channel.bind("my-event", function(data) {
	alert("An event was triggered with message: " + data.message);
}); 
```

从 PHP 发送消息:

```
 require('Pusher.php');
$pusher = new Pusher($key, $secret, $app_id);
$pusher->trigger( 'my-channel', 'my-event', array('message' => 'hello world') ); 
```

[Pusher 网站](http://pusher.com/)为[提供了完整的文档](http://pusher.com/docs)，一系列针对 PHP 和 Ruby 的[教程](http://pusher.com/tutorials)，以及一个包含案例研究的[展示](http://pusher.com/showcase)，这些案例研究可能会激发你下一个世界一流的实时应用。

我喜欢 Pusher 背后的概念。如果你想在支持大多数浏览器和本地操作系统平台的同时尝试 WebSockets，这是一个很好的解决方案。有实验原型的免费计划，也有大量使用的付费账户。

更多信息，请参考[Pusher.com](http://pusher.com/)。

## 分享这篇文章