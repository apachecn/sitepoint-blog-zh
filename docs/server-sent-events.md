# 服务器发送的事件

> 原文：<https://www.sitepoint.com/server-sent-events/>

1.  [简介](#intro)
2.  [订阅流:`EventSource`对象](#eventsource)
3.  [从服务器发送事件](#sendingevents)
    1.  [发送`message`事件](#messageevents)
    2.  [发送`custom`事件](#customevents)
    3.  [以`retry`间隔管理重连](#managingreconnections)
    4.  [用`id`字段](#lastEventId)设置唯一标识符
4.  [处理事件](#handlingevents)
5.  [处理错误](#handlingerrors)
6.  [浏览器实现差异](#bugs)
7.  [浏览器支持和回退策略](#browsersupport)

想象一下，你们国家的国家篮球队正在为世界篮球锦标赛而战。你想跟踪比赛，但你不能看，因为它发生在你工作的时候。

幸运的是，你的国家新闻服务有一个杰出的网络开发团队。他们已经建立了一个体育自动收报机，随着每一次犯规或得分而更新。你访问一个 URL，更新就会被直接推送到你的浏览器。当然，你想知道他们是怎么做到的。答案？[服务器发送的事件](http://dev.w3.org/html5/eventsource/)。

服务器发送事件是使用流将数据和/或 DOM 事件从服务器推送到客户端的一种方式。对于股票行情、体育比分、航班跟踪、电子邮件通知——任何数据会定期更新的情况，它都很方便。

<q>但是等等！</q>我听到你说，<q>难道我们不能用像`XMLHttpRequest`或[网络套接字](http://dev.w3.org/html5/websockets/)这样的技术做到这一点吗？嗯，是的。然而，这样做需要扩展这些对象来做`EventSource`本来要做的事情。</q>

## 服务器端考虑事项

因为服务器发送的事件是数据流，所以它们需要长期连接。你会希望使用一个可以同时处理大量连接的服务器。当然，事件驱动的服务器特别适合流事件。其中包括 [Node.js](http://nodejs.org/) 、[剑圣](http://juggernaut.rubyforge.org/)、[扭曲](http://twistedmatrix.com/)。对于 [Nginx](http://www.nginx.org/) ，有 [nginx 推流模块](https://github.com/wandenberg/nginx-push-stream-module/)。然而，服务器配置超出了本文的范围，它将随您使用的服务器而变化。

让我们看看使用`EventSource`对象订阅流。然后我们将看看发送和处理事件。

## 订阅事件流:`EventSource`对象

创建一个`EventSource`对象很简单。

```
var evtsrc = new EventSource('./url_of/event_stream/',{withCredentials:false});
```

`EventSource`构造函数最多接受两个参数:

*   一个 **URL** 字符串，这是必需的；和
*   一个*可选的* **字典**参数，它定义了`withCredentials`属性的值。

字典在语法上类似于对象，但它们实际上是定义了名称-值对的关联数据数组。在这种情况下，`withCredentials`是唯一可能的字典成员。其值可以是`true`或`false`。(要了解更多关于字典的一般知识，请参考 [Web IDL 规范](http://dev.w3.org/2006/webapi/WebIDL/)。)

只有需要用户凭证(cookies)的跨来源请求才需要包含 dictionary 参数。迄今为止，还没有浏览器支持跨源`EventSource`请求。因此，我们不会在示例中包含第二个参数。

当`EventSource`连接打开时，它将触发一个 **`open`事件**。我们可以通过设置`onopen`属性来定义一个函数来处理该事件。

```
var evtsrc = new EventSource('./url_of/event_stream/');
evtsrc.onopen = function(openevent){
    // do something when the connection opens
}

```

如果我们的连接出现问题，一个`error`将被解雇。我们可以使用`onerror`属性为这些事件定义一个处理函数。我们将在[处理错误](#handlingerrors)部分讨论错误事件的一些原因。

```
evtsrc.onerror = function(openevent){
    // do something when there's an error
}

```

默认情况下，流式事件是`message`事件。为了处理消息事件，我们可以使用`onmessage`属性来定义一个处理函数。

```
evtsrc.onmessage = function(openevent){
    // do something when we receive a message event.
}

```

我们也可以使用`addEventListener()`来监听事件。这是处理定制事件的唯一方法，我们将在[处理事件](#handlingevents)部分看到。

```
var onerrorhandler = function(openevent){
    // do something
}
evtsrc.addEventListener('error',onerrorhandler,false); 
```

要**关闭**连接，请使用`close()`方法。

```
evtsrc.close();
```

所以我们已经创建了我们的`EventSource`对象，并为`open`、`message`和`error`事件定义了处理程序。但是为了实现这一点，我们需要一个流事件的 URL。

## 从服务器发送事件

服务器发送的事件是作为来自 URL 的流的一部分传递的文本片段。为了让浏览器将我们的数据视为一个流，我们必须:

*   为我们的内容提供一个值为`text/event-stream`的`Content-type`头；
*   使用 UTF-8 字符编码。

服务器发送事件的语法很简单。它由一个或多个冒号分隔的字段名-值对组成，后跟一个行尾字符。字段名可以包含四个可能值之一。

*   `data`:要发送的信息。
*   `event`:正在调度的事件类型。
*   `id`:客户端重新连接时使用的事件的标识符。
*   `retry`:在浏览器尝试重新连接到 URL 之前，应该等待多少毫秒。

其中，只有`data`字段是必需的。

### 发送`message`事件

在本例中，我们将发送一个事件，宣布哪些球队将参加锦标赛。当浏览器接收到这个文本时，它将调度一个`message`事件。

```
data: Brazil v. United States

```

`data`字段的值成为消息事件的`data`属性的值。如上所述，默认情况下，服务器发送的事件是`message`事件。但是正如我们稍后将要讨论的，我们也可以通过包含一个`event`字段来[分派定制事件](#customevents)。

我们也可以将几段数据作为一个事件发送。每个数据块后面都应该跟一个行尾字符(换行符、回车符或两者都有)。这里我们附加了一个事件，包含这个游戏的位置和出席情况。

```
data: Brazil v. United States

:Comments begin with a colon. Events must be followed a blank line.
data: Air Canada Centre
data: Toronto, Ontario, Canada
data: Attendance: 19,800
```

对于这个事件，`data`属性的值将是:`Air Canada CentrenToronto, Ontario, CanadanAttendance: 19,800`。

注意事件之间的空行。为了让客户端接收事件，它后面必须跟一个空行。注释以冒号开头。

### 发送自定义事件

事件的类型是`message`，除非我们另外指定。为此，我们需要包含一个`event`字段。在下面的例子中，我们将向我们的流中添加两个`startingfive`事件，并将我们的数据作为 [JSON](http://dev.opera.com/articles/tags/json) 格式的字符串发送。

```
event: startingfive
data: {"team":{"country":"Brazil","players":[{"id":15,"name":"de Sousa","position":"C"},{"id":12,"name":"Dantas","position":"F"},
{"id":7,"name":"Jacintho","position":"F"},{"id":6,"name":"de Oliveira Ferreira","position":"G"},{"id":4,"name":"Moisés Pinto","position":"G"}]}}

event: startingfive
data: {"team":{"country":"USA","players":[{"id":15,"name":"Charles","position":"C"},{"id":11,"name":"Cash","position":"F"},
{"id":5,"name":"Jones","position":"F"},{"id":7,"name":"Montgomery","position":"G"},{"id":4,"name":"Pondexter","position":"G"}]}}

```

这里我们需要监听`startingfive`事件，而不是`message`事件。然而，我们的`data`字段仍将成为事件的`data`属性的值。

我们将在[处理事件](#handlingevents)部分讨论`data`属性和`MessageEvent`接口。

### 管理连接和重新连接

现在，虽然服务器确实将事件推送到浏览器，但实际情况要稍微微妙一些。如果服务器保持连接打开，`EventSource`请求将是一个扩展请求。如果关闭，浏览器将等待几秒钟，然后重新连接。例如，如果 URL 发送一个文件结束标记，连接可能会关闭。

每个浏览器都设置自己的默认重新连接间隔。大多数在 3 到 6 秒后重新连接。但是，您可以通过包含一个`retry`字段来控制这个时间间隔。`retry`字段指示客户端在重新连接到 URL 之前应该等待的毫秒数。让我们以上面的例子为基础，将我们的事件更改为包括一个 5 秒(5000 毫秒)的重试间隔。

```
event: startingfive
data: {"team":{"country":"USA","players":[{"id":15,"name":"Charles","position":"C"},{"id":11,"name":"Cash","position":"F"},
{"id":5,"name":"Jones","position":"F"},{"id":7,"name":"Montgomery","position":"G"},{"id":4,"name":"Pondexter","position":"G"}]}}
retry: 5000

```

只要客户端处于连接状态，事件流就可以保持活动状态。根据您的体系结构和应用程序，您可能希望服务器定期关闭连接。

### 用`id`字段设置唯一标识符

当浏览器重新连接到 URL 时，它将接收重新连接时可用的任何数据。但是在游戏滚动条的情况下，我们可能希望让我们的访问者补上他或她错过的内容。这就是为什么为每个事件设置一个`id`是最佳实践。在下面的例子中，我们将发送一个`id`作为`score`事件的一部分。

```
event: score
retry: 3000
data: Brazil 14
data: USA 13
data: 2pt, de Sousa
id: 09:42 
```

它的值对于流应该是唯一的。在这种情况下，我们使用的是得分时间。

`id`字段成为该事件对象的`lastEventId`属性。但它有另一个目的。如果连接关闭，浏览器将在下一个请求中包含一个`Last-Event-ID`头。把它想象成一个流的书签。如果`Last-Event-ID`头存在，您可以调整您的应用程序的响应，只发送那些在它之后的事件。

## 处理事件

如上所述，所有事件默认都是`message`事件。每个`message`事件都有三个属性，由 [`MessageEvent`接口](http://www.whatwg.org/specs/web-apps/current-work/multipage/comms.html#messageevent)定义。

`event.data`

Returns the data or message sent as part of the message event.

`event.origin`

Returns the origin of the message, which is typically a string containing the scheme (ex: http, https), host name, and port from which the message was sent.

`event.lastEventId`

Returns the unique identifier of the last event received.

任何时候一个`message`事件被触发，我们的`onmessage`函数就会被调用。这对于那些只*发送消息事件的应用程序来说很好。但是，如果您想像我们的例子那样发送`score`或`startingfive`事件，它的局限性就变得很明显了。使用`addEventListener`更加灵活。在下面的代码中，我们使用`addEventListener`处理一个`startingfive`事件。*

```
var evtsrc = new EventSource('./url_of/event_stream/');

var startingFiveHandler = function(event){
    var data = JSON.parse(event.data), numplayers, pl;

    console.log( data.team.country );

    numplayers = data.team.players.length;

    for(var i=0; i 
```

## 处理错误

智能错误处理比仅仅设置`onerror`属性需要更多的工作。我们还需要知道错误是导致连接失败，还是暂时中断。连接失败后，浏览器不会尝试重新连接。如果这是一个暂时的中断——如果计算机处于睡眠状态或者服务器关闭了连接，浏览器就会再次尝试。浏览器会因为以下原因调度一个`error`事件。

*   URL 发送了一个带有错误值的`Content-type`响应头。
*   URL 返回了一个 HTTP 错误标题，如 <q>404 文件未找到</q>或 <q>500 内部服务器错误。</q>
*   网络或 DNS 问题阻止了连接。
*   服务器关闭了连接。
*   URL 不允许请求来源。

最后一点值得澄清。迄今为止，还没有浏览器支持跨来源的服务器发送的事件请求。在 Firefox 和 Opera 中，尝试跨原点请求会在`EventSource`对象上触发一个`error`事件，连接会失败。在 Chrome 和 Safari 中，它会触发一个 DOM 安全异常。

当处理错误时，检查`readyState`属性很重要。让我们看一个例子。

```
var onerror = function(event){
    var txt;
    switch( event.target.readyState ){
        // if reconnecting
        case EventSource.CONNECTING:
            txt = 'Reconnecting...';
            break;
        // if error was fatal
        case EventSource.CLOSED:
            txt = 'Connection failed. Will not retry.';
            break;
    }
    alert(txt);
}
```

在上面的代码中，如果`e.target.readyState`的值是`EventSource.CONNECTING`(规范定义的常数；它的值为 0)，我们将提醒用户我们正在重新连接。如果它的值等于`EventSource.CLOSED`(另一个值为 2 的常数)，我们将警告用户浏览器不会重新连接。

## 浏览器实现差异

当电脑从睡眠模式中醒来时，Firefox 和 Opera 都不会改变`EventSource`对象的`readyState`属性。即使连接暂时丢失，`EventSource.readyState`的值仍然是 1。相比之下，Chrome 和 Safari 将`readyState`值更改为 0，表示浏览器正在重新建立连接。然而，在测试中，所有浏览器似乎在唤醒后几秒钟自动重新连接到 URL。

## 浏览器支持和后备策略

截至发稿，Opera 11.60+，Firefox 6.0+，Safari 5.0+，iOS Safari 4.0+，Chrome 6.0+都支持服务器发送事件。Android 的 WebKit 和 Opera Mini 没有。由于`EventSource`是全局对象的属性(在浏览器中，这通常是`window`对象)，我们可以使用下面的代码来确定支持。

```
if(window.EventSource !== undefined){
    // create an event source object.
} else {
    // Use a fallback or throw an error.
}
```

[`XMLHttpRequest`](https://www.w3.org/TR/XMLHttpRequest/) 可以作为不支持`EventSource`的浏览器的后备。使用 XHR 回退的聚合填充包括 Yaffle 的 [EventSource](https://github.com/Yaffle/EventSource) 和 Remy Sharp 的 [EventSource.js](https://github.com/remy/polyfills/blob/master/EventSource.js) 。

请记住，当使用 XHR 时，您的 URL 应该在每次请求后关闭连接。这样做可以确保最大程度的浏览器兼容性。

当然，您的应用程序并不确切知道请求对象是`EventSource`还是`XMLHttpRequest`，因此也不知道*是否应该*关闭连接。要解决这个问题，在使用如下所示的`XMLHttpRequest`时包含一个自定义请求头。

```
var xhr = new XMLHttpRequest();
xhr.open('GET','./url_of/event_stream/');
xhr.setRequestHeader('X-Requestor','XHR');
xhr.send(null);
```

然后，确保您的应用程序在该自定义标头存在时关闭连接。为此，将`Content-type:`头的值设置为`text/plain`，并(可选地)在 URL 的响应中包含一个`Connection: close`头。

[相通的节点](http://www.shutterstock.com/cat.mhtml?lang=en&search_source=search_form&version=llv1&anyorall=all&safesearch=1&searchterm=server+side&search_group=&orient=&search_cat=&searchtermx=&photographer_name=&people_gender=&people_age=&people_ethnicity=&people_number=&commercial_ok=&color=&show_color_wheel=1#id=84800629&src=1a5c9eb77ad5f7b3cfdf9cce3f20c022-1-7)图像

## 分享这篇文章