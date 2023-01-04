# Action Cable 和 WebSockets:深入教程

> 原文：<https://www.sitepoint.com/action-cable-and-websockets-an-in-depth-tutorial/>

![Ruby on the railway, Vector Flat design](img/43654a218c9ba68b2d4b53dc29abc709.png)

Rails 5 备受期待的特性之一是集成 web socket 通信和 Rails 的官方解决方案，称为 Action Cable。作为一家 Ruby on Rails 代理公司，我们对即将发布的新特性感到非常兴奋。

根据他们的 Github 页面，Action Cable:

> […]将 WebSockets 与 Rails 应用程序的其余部分无缝集成。它允许以与 Rails 应用程序的其余部分相同的风格和形式用 Ruby 编写实时特性，同时仍然具有高性能和可伸缩性。

听起来很牛逼，很有用。忘掉像 Faye 这样直接使用 gems 的工作吧，Action Cable 把 web sockets 的乱七八糟的东西拿了出来，充当了功能的适配器。它为客户端 Javascript 代码和服务器端 Rails 代码提供了一个清晰的接口。

为了全面理解 Rails 5 中的 Action Cable，我们需要了解一些事情:

*   WebSockets 的基础
*   动作电缆 API 和一个例子
*   行动电缆的利与弊

我们开始吧！

## 什么是 WebSockets？

HTTP 已经存在了一段时间，显然，它是浏览器和服务器之间最普遍的通信协议。在典型的浏览器 HTTP 请求中，浏览器向服务器请求单个 HTML、Javascript 或 CSS 文档。之后，连接被关闭，如果浏览器需要额外的资源，浏览器的工作就是再次启动对话。

在 WebSockets 出现之前，开发人员会模仿 WebSockets 的概念，持续轮询服务器，每隔几秒钟检查一次更新。你脑子里可能已经响起了警报。在轮询方面没有很大的可伸缩性。作为开发人员，您会给服务器增加相当大的负载。也就是说，投票仍然在互联网上广泛使用。

那么什么是 WebSockets 呢？与 HTTP 请求不同，WebSockets 是有状态的连接。这意味着客户端和服务器之间的连接保持不变。在这种情况下。任何一方(客户端或服务器)都有能力发起请求或消息。最终结果是浏览器和服务器之间的直接交互。

### 输入行动电缆

作为 2015 Rails Conf 主题演讲的一部分，David Heinemeier Hansson 宣布将 Action Cable 添加到 Rails 核心。这一增加背后的原因是基于应用程序框架的当前形势和对这一特性日益增长的需求。Action Cable 是作为一个新概念引入的，它利用 WebSockets 通过实时消息传递来扩展 Rails 功能。

## 动作电缆 API

要开始使用 Action Cable，首先创建一个新项目并安装 actioncable gem。为了便于使用，在这个[示例存储库](https://github.com/kylesziv/action-cable-house-example)中检查这个项目。

在你的终端中，切换到项目运行目录`bundle install`来安装项目依赖项。

对于这个项目，我们将使用 puma，并将独立于我们的应用服务器运行 Action Cable 服务。Action Cable 与许多流行的应用服务器框架一起工作，如 Unicorn、Puma 和 [Passenger](https://gorails.com/episodes/deploy-actioncable-and-rails-5) 。

对于这个项目，您需要安装 Redis。下面是一些关于如何安装 Redis 的快速指导:

Linux:

```
wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable
make
make install 
```

Mac:

```
brew install redis 
```

安装 Redis 后，继续启动您的服务器。在一个终端窗口运行`./bin/cable`来运行你的有线服务器。

在新的终端窗口中运行`./bin/rails server`来启动 Rails 服务器。最后，在另一个窗口中，运行`redis-server`启动 Redis 服务器。咻！

一旦所有这些都成功运行，您就可以在浏览器中导航到`localhost:3000`。你会注意到美国宾夕法尼亚州匹兹堡的地图。打开另一个浏览器访问相同的 url。它可以是不同的浏览器或同一浏览器的匿名会话，以便两个会话不共享相同的 cookies，等等。

### 行动索在行动

在第一个浏览器窗口中，单击主页上 houses 标签下方的 create a house 链接。这将把你带到一个有两个字段的表单。填写一所房子甚至一个著名的[匹兹堡景点](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=pittsburgh%20attractions)的地址和邮政编码字段。一旦你点击“保存”，你会注意到在你的另一个浏览器窗口，一个大头针下降，一个新的房子列在地图上。这一切都是通过 Action Cable 和 WebSockets 完成的。

### 潜入行动电缆

#### 创建频道

在有线电视中，频道是最重要的。请注意**频道**文件夹中的以下文件:

*   **app/channels/application _ channel/connection . Rb**
*   **app/channels/application _ channel/channel . Rb**
*   **app/channels/houses _ channel . Rb**

`ApplicationCable::Connection`用于一般授权。例如，我们可以使用这个模块在数据库中查询正在建立连接的特定用户，并确保他们被允许监听。对于这个项目，我们没有任何用户，因此，这里不需要任何代码。

`ApplicationCable::Channel`的用法很像普通堆栈中的`ApplicationController`。这个类是所有其他通道的基础。请注意，项目中的所有其他通道都继承自该类。您可以使用此类来执行可在通道之间使用的共享逻辑。

最后，还有`HouseChannel`。当一个客户端试图订阅一个频道时，该频道上的`follow`方法被调用，任何通过内部流广播的内容都将被发送到客户端。

记住有两种方法可以达到类似的效果:`stream_from`和`stream_for`。`stream_from`方法用于订阅您定义的字符串。例如，在我们的项目中，我们订阅了与字符串“houses”相关联的广播。当一个新房子被创建时，我们想知道它。相比之下，`stream_for`用于我们需要更新的特定记录(或记录和关联)的情况。在幕后，Action Cable 正在为该记录或该记录及其关联生成一个惟一的字符串，然后调用`stream_for`方法。

> 注意:`stream_for`方法的另一个好处是它接受一个可选的回调方法作为第二个参数。

#### 广播变化

对于我们的例子，房子频道上的广播发生在房子被添加之后。为此，我们为模型创建了一个 [ActiveJob](https://www.sitepoint.com/new-rails-shiny-activejob/) 和一个回调。

我们的`House`模型中的回调如下所示:

```
after_commit { HouseRelayJob.perform_later(self) } 
```

这将创建一个`HouseRelayJob`，并安排在不久的将来执行。

`HouseRelayJob.perform`方法看起来像:

```
class HouseRelayJob < ApplicationJob
  def perform(house)
    ActionCable.server.broadcast "houses",
      house: house
  end
end 
```

这告诉`HouseChannel`已经创建了一个新房子，它将新创建的房子对象发送给正在积极订阅的人。对于这个例子，它将向客户机发送 JSON。

#### 客户端订阅

houses 通道的最后一部分是客户端代码。有两个文件定义了这种关系。第一个文件是**app/assets/JavaScript s/channels/application _ channel . coffee**。该文件仅定义了电缆:

```
#= require cable

@App = {}
App.cable = Cable.createConsumer 'ws://localhost:28080' 
```

第二个文件是**app/assets/JavaScript s/channels/houses . coffee**,它建立了对`HousesChannel`的订阅，然后在新房子广播后创建 pin 标记。

```
App.houses = App.cable.subscriptions.create 'HousesChannel',

  dropMarker: (house) ->
    new google.maps.Marker
      position: { lat: house.latitude, lng: house.longitude},
      map: window.map,
      animation: google.maps.Animation.DROP
      title: "#{house.address} #{house.zipcode}"

  received: (data) ->
    @dropMarker(data.house) 
```

## Action 电缆/网络插座的利弊

在决定一项技术是否能满足您的需求时，充分了解这项技术非常重要。为了技术而实施新技术可能是一个代价高昂的错误。由于 Action Cable 是 WebSockets 的包装器，我们应该真正理解使用 WebSockets 的利弊。

### 赞成者

WebSockets 和 Action Cable 的最大好处之一是它是一个开放的连接，与普通的 HTTP 请求相比，它是非常轻量级的:

> HTML5 Web Sockets 在实时 Web 的可伸缩性方面向前迈进了一大步[…] HTML5 Web Sockets 可以提供 500:1，甚至 1000:1 的不必要 HTTP 报头流量减少和 3:1 的延迟减少，这取决于 HTTP 报头的大小。这不仅仅是一个渐进的改进；这是一个革命性的飞跃——一个巨大的飞跃！

html 5 Web socket:Web 可扩展性的巨大飞跃
。

另一个好处是，一旦建立了连接，它就保持开放。服务器和客户端都可以通过该开放线路进行通信，而不必重新建立新的连接。

最后，WebSocket 连接是一种全双工连接。服务器和客户端可以使用这个连接在同一时间互相通信。与普通的半双工 HTTP 连接相比，这是非常不同的。

在回顾了优点之后，似乎将 WebSockets 添加到应用程序中可以通过减少需要通过网络发送的数据来大大提高性能。

### 坏处

反对 WebSockets 的最大理由之一是无法缓存这些请求。在研究 Action Cable 的时候，我偶然发现了 Nate Berkopec 写的一篇关于 Action Cable 的非常深入的文章，名为[Action Cable——朋友还是敌人？](https://www.nateberkopec.com/2015/09/30/action-cable.html)。他提到了 WebSockets 的许多不同替代方案，以及 Action Cable 是否值得。

当提到 Action Cable 和 WebSockets 时，他说:

> 我不确定我是否相信“WebSockets 完全消除了对 HTTP 的需求！”修辞。HTTP 带来了很多好处，如果远离 HTTP，我们将会失去所有好处。缓存、路由、多路复用、gzipping 等等。你可以在 Action Cable 中重新实现所有这些东西，但是为什么呢？

他接着说，也许值得再等一会儿。Action Cable 和 WebSockets 都是较新的技术，我们可能希望等到尘埃落定后再加入它，只是为了技术上的原因。

内特继续说，在某些情况下，Action Cable 完全有道理:

> [F]在高吞吐量的情况下，客户端每秒钟与服务器通信几次，我认为 Action Cable 可能是一个很好的选择。

WebSocket 的另一个缺点是浏览器之间的支持。目前，70%的浏览器市场允许 WebSocket 连接。这使得 30%的人需要第二种解决方案。

在使用 Action Cable 解决您的问题之前，请确保您已经进行了研究，并确定 Action Cable 符合您的需求。

## 包扎

Action Cable 是对 Rails 框架的一个强大补充。希望这篇文章已经给了你决定 Action Cable 和 WebSockets 是否值得进一步尝试和研究的工具。

我很想听到更多关于你如何在现场项目中或只是为了娱乐而使用 Action Cable 的信息。请不吝赐教。我们来聊聊吧！

## 分享这篇文章