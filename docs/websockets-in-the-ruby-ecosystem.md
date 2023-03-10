# Ruby 生态系统中的 WebSockets

> 原文：<https://www.sitepoint.com/websockets-in-the-ruby-ecosystem/>

![Ruby is learning to love WebSockets](img/e6143aa6d4993bbf6c142e2b8bca6d1f.png)

Ruby 正在学习爱上 WebSockets

到底什么是“网络插座”？我们中的一些人已经听说了 Rails 在 WebSockets 方面的变化(例如 Rails 5 中的 Action Cable ),但是很难准确指出 WebSockets 要做什么。它们解决了什么问题，它们如何适应 HTTP、web 和 Ruby 的领域？这就是我们将在本文中讨论的内容。

## 为什么？

让我们把时光机拨到网络的开头。众所周知，很久以前，网站是由静态页面和它们之间的链接组成的。这些页面被称为“静态的”,因为它们在每个用户的基础上没有什么真正的变化。服务器只是根据用户请求的路径向每个用户提供相同的东西。我们很快意识到，如果我们想让网络相当于一本容易获得的书，这种事情很好，但我们实际上可以做得更多。因此，有了输入元素和 CGI(公共网关接口——外部脚本与 web 服务器对话的一种方式),动态元素就悄悄进入了网页。

现在，我们实际上可以处理输入数据并对其进行处理。随着网站变得越来越繁忙，我们意识到 CGI 在缩放方面相当糟糕。为了解决这个问题，出现了大量的选项，比如 FastCGI。我们想出了各种各样的框架来简化后端的编写:Rails、Django 等等。所有这些进展都发生了，但是在一天结束的时候，我们仍然提供一些 HTML(通过各种方法)，用户正在阅读这个大部分是静态的 HTML，然后请求一些不同的 HTML。

然后，开发者通过 AJAX 实现了 Javascript 的强大和与服务器的通信。网页不再仅仅是 HTML 的小块。相反，Javascript 用于根据与服务器的异步通信来改变这些页面的内容。通常，发生在服务器上的状态变化必须反映在客户机上。举一个非常简单的例子，当服务器上的用户数量超过一定限制时，我们可能希望在管理面板上显示一个通知。然而，用来做这类事情的方法并不是最好的。一种常见的解决方案是 HTTP 长轮询。通过它，客户端(即浏览器中运行的 Javascript 代码)向 HTTP 服务器发送请求，服务器保持该请求打开(即服务器不发送任何数据，但不关闭连接)，直到某种更新可用。

你可能想知道:如果客户端可以以某种方式要求服务器在更新到来时告诉客户端，我们为什么要做这些等待的事情？不幸的是，HTTP 并不允许我们这样做。HTTP 并不是被设计成一个服务器驱动的协议。客户端发送请求，HTTP 服务器应答它们。句号。长轮询并不是一个很好的解决方案，因为当涉及到扩展、用户在 Wi-Fi 和蜂窝之间切换等问题时，它会导致各种各样的麻烦。我们如何解决这个让服务器与客户端对话的问题？

## websocket

这就是 WebSockets 的用武之地。一群非常聪明的人聚在一起，想出了一个可以用于客户机和服务器之间双向通信的协议。然后，他们在 RFC(即征求意见稿)中详细说明了这一点；这些或多或少是互联网各个部分的文档)，特别是 [RFC 6455](https://tools.ietf.org/html/rfc6455#section-1) 。该文档定义了一个类似 TCP 的协议，允许服务器和客户端进行通信。客户端被赋予一个特定的 API 来与服务器通信(称为 WebSocket API)。当您意识到客户机现在可以基于来自服务器的输入创建事件时，WebSockets 的威力就变得显而易见了。想在股票涨到一定价位的时候更新你的 UI？没问题，只要让服务器告诉客户端什么时候达到价格限制，并编写一些事件驱动的代码。

所有这些东西是如何与 HTTP 一起工作的？它的大部分工作或多或少独立于 HTTP。然而，“握手”必须通过 HTTP 进行。如果你熟悉 TCP，你会知道只要你打开一个新的连接，握手就会发生。握手意味着交换一些基本信息并启动连接。对于 WebSockets，握手的存在是为了让服务器知道它应该打开一个与客户端的 WebSocket 通信通道。HTTP 用于通过一个特殊的“HTTP 升级”请求来传递该消息，该请求具有一些特定于 WebSockets 的字段。

但是，这意味着我们使用的 HTTP 服务器必须实现 WebSockets，我们的 web 框架必须为我们提供某种方式来处理 WebSocket 连接，并且我们在浏览器(充当客户端)上运行的 Javascript 必须能够使用 WebSocket。

## Javascript 和 WebSockets

问题的最后一点(Javascript 对话 WebSocket)来自浏览器供应商提供的 WebSocket 实现的形式。最近的浏览器版本包含了对 WebSocket 的支持，因此该技术在很多情况下都是一个合理的选择。

WebSockets Javascript API 有来自 MDN 的非常好的文档。虽然我们主要关注 WebSockets 背后的概念，但是快速浏览一下 Javascript API 是值得的，因为大多数知识可以在后端重用(记住:WebSockets 是双向的，所以我们希望客户机和服务器上的 API 非常相似)。我们可以创建一个 WebSocket 对象:

```
var socket = new WebSocket("ws://domain.com/server", "protocol")
```

其中“协议”可以由表示由给定服务器处理的协议的字符串代替。注意，URL 以`ws://`开头。明文 WebSockets 就是这种情况，`wss://`可以用于 SSL 上的 WebSockets。我们也可以发送数据:

```
socket.send("whatever")
```

最重要的是，我们可以从服务器接收数据:

```
socket.onmessage = function(ev) {
  console.log(ev.data);
}
```

所以，Javascript 支持相当不错。在运行 Ruby 的服务器端呢？

## Ruby 和 WebSockets

实时消息和 Ruby 之间的关系一直很复杂。当 Node.js 不知从哪里冒出来并获得大量关注时，Ruby 社区被炒得沸沸扬扬。出现这种情况的原因并不是 Node 更适合编写普通的 web 应用程序。事实上，有人可能会说，与功能更全的 Rails 相比，像 Express 这样的框架使用起来相当麻烦。但是，Node 简化了实时和事件驱动的交互。我认为 Socket.io 在这方面发挥了巨大的作用:让客户端和服务器以一种简单、无障碍的方式进行通信终于变得容易了。在相当长的一段时间里，Rails 在实时方面落后于潮流，从某种程度上来说，现在仍然如此。但是，我们确实有几个选项使 WebSockets 在 Rails 中成为可能。

### 王菲

谈到实时和 Rails，Faye 在某种程度上是一个标准的解决方案。尽管它使用了一种叫做 Bayeux 协议的东西，但它的最新版本还包括一个[标准 WebSocket 服务器](https://github.com/faye/faye-websocket-ruby)。事实上，我们最近有一个[系列](https://www.sitepoint.com/realtime-mini-chat-rails-faye/)详细介绍了如何使用 Faye 构建一个实时应用。

### 事件机器

[EventMachine](https://github.com/eventmachine/eventmachine) 是一个用于 Ruby 的事件处理库，碰巧也支持使用 [em-websocket](https://github.com/igrigorik/em-websocket) 的基于 WebSocket 的通信。这是一个非常简单的 API，允许我们非常快速地进行实时通信。EngineYard 有一篇关于在 EventMachine 上使用 WebSockets 的不错的[博客文章](https://blog.engineyard.com/2013/getting-started-with-ruby-and-websockets)。

### 外包

这种技术有点争议。其思想是，即使您的应用程序大部分都使用 Rails，您也不必将它用于应用程序的实时消息传递组件。为此，你基本上可以做任何事情(比如说，Node)并在不同的子域下运行它。我不喜欢这种方法有很多原因。一般来说，两种不同语言之间的上下文切换非常烦人，尤其是因为应用程序的实时和非实时部分可能紧密相连。其次，使用这种方法，代码复制是免费的，因为您可能需要在 Node 中重新实现一些模型，以便提供正确的实时数据。然而，在一些情况下，这种事情是有意义的。如果你有一个非常传统的 web 应用程序，只有一两个非常简单的实时组件，那么在 Node 中实现这些组件*可能*是合理的。

### 行动电缆

这是街区里新来的孩子，还没搬进来。ActionCable 是 Rails 5 的新特性之一，基本上，它允许我们在 Rails 中毫无困难地使用 WebSockets。然而，当 Rails 5 交到我们手中(2015 年秋季)时，我们并不知道 ActionCable 会变成什么样。我绝对相信 ActionCable 可以让 web 开发社区对 Rails 重新产生兴趣。Javascript/节点领域的大量活动已经让 Rails 黯然失色了一段时间，特别是当实时和 SPAs 成为游戏的名称时。希望这种情况会改变。

## 包装它

WebSockets 非常重要，因为它改变了我们对 HTTP 的客户机-服务器关系的看法。从某种意义上说，它们模糊了客户机和服务器之间的区别，固定了两者之间的平衡。随着越来越多的浏览器实现 WebSockets 标准，它们可能会成为我们开发 web 应用程序的关键。希望这篇文章能让你了解 WebSockets 是如何出现的，以及如何在 Ruby 生态系统中使用它们。

## 分享这篇文章