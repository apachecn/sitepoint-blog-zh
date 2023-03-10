# 使用服务器发送的事件实现推送技术

> 原文：<https://www.sitepoint.com/implementing-push-technology-using-server-sent-events/>

使用 XmlHttpRequest 对象进行 Ajax 调用是一种用于生成动态服务器请求的成熟技术。然而，Ajax 不允许服务器直接向客户端发起数据传输——这种技术被称为 [*推送技术*](http://en.wikipedia.org/wiki/Push_technology "Push technology - Wikipedia") 。这就是[服务器发送事件](http://dev.w3.org/html5/eventsource/ "Server-Sent Events") API 发挥作用的地方。服务器发送的事件专门采用推送技术，通过保持打开的连接将数据作为连续流传输到客户端，称为*事件流*。而且，通过保持一个开放的连接，消除了重复建立新连接的开销。


## 与 WebSockets 的比较

许多人完全不知道服务器发送事件的存在。这是因为它们经常被更强大的 [WebSockets API](http://cjihrig.com/blog/how-to-use-websockets/ "How to Use WebSockets") 所掩盖。虽然 WebSockets 支持客户端和服务器之间的双向全双工通信，但服务器发送的事件只允许消息从服务器推送到客户端。要求接近实时性能或双向通信的应用程序可能更适合 WebSockets。

但是，服务器发送的事件也比 WebSockets 有一定的优势。例如，服务器发送的事件支持自定义消息类型和丢失连接的自动重新连接。这些特性可以在 WebSockets 中实现，但是默认情况下它们在服务器发送的事件中是可用的。WebSockets 应用程序还需要支持 WebSockets 协议的服务器。相比之下，服务器发送的事件建立在 HTTP 之上，可以在标准的 web 服务器中实现。

## 检测支架

服务器发送的事件得到了相对较好的支持，Internet Explorer 是唯一一个[还不支持它们的主流浏览器](http://caniuse.com/eventsource "Can I use Server-sent DOM events")。然而，只要 IE 落后，就仍然有必要提供特征检测。在客户端，服务器发送的事件使用`EventSource`对象实现——全局对象的一个属性。下面的函数检测`EventSource`构造函数在浏览器中是否可用。如果函数返回`true`，那么可以使用服务器发送的事件。否则，应该使用回退机制，如长轮询。

```
function supportsSSE() {
  return !!window.EventSource;
}
```

## 连接

要连接到事件流，调用`EventSource`构造函数，如下所示。您必须指定试图订阅的事件流的 URL。构造函数会自动打开连接。

```
EventSource(url);
```

### onopen 事件处理程序

当一个连接建立后，调用`EventSource`的`onopen`事件处理程序。事件处理程序将`open`事件作为唯一的参数。下面的例子显示了一个通用的`onopen`事件处理程序。

```
source.onopen = function(event) {
  // handle open event
};
```

`EventSource`事件处理程序也可以使用`addEventListener()`方法来编写。这种替代语法比`onopen`更可取，因为它允许多个处理程序附加到同一个事件。之前的`onopen`事件处理程序已经使用`addEventListener()`重写如下。

```
source.addEventListener("open", function(event) {
  // handle open event
}, false);
```

## 接收消息

客户端将事件流解释为一系列 DOM `message`事件。从服务器收到的每个事件都会触发`EventSource`的`onmessage`事件处理程序。`onmessage`处理程序将一个`message`事件作为它唯一的参数。下面的例子创建了一个`onmessage`事件处理程序。

```
source.onmessage = function(event) {
  var data = event.data;
  var origin = event.origin;
  var lastEventId = event.lastEventId;
  // handle message
};
```

`message`事件包含三个重要属性― `data`、`origin`和`lastEventId`。顾名思义，`data`包含字符串格式的实际消息数据。数据可能是一个 [JSON](http://cjihrig.com/blog/json-overview/ "A JSON Overview") 字符串，它可以被传递给`[JSON.parse()](http://cjihrig.com/blog/native-browser-support-for-json-part-1/ "Native Browser Support for JSON (Part 1 of 2)")`方法。属性包含重定向后事件流的最终 URL。应检查`origin`以验证消息仅从预期来源接收。最后，`lastEventId`属性包含在事件流中看到的最后一个消息标识符。服务器可以使用此属性将标识符附加到各个消息。如果从未见过标识符，那么`lastEventId`将是空字符串。

也可以使用`addEventListener()`方法编写`onmessage`事件处理程序。下面的例子显示了先前的`onmessage`事件处理程序，重写后使用了`addEventListener()`。

```
source.addEventListener("message", function(event) {
  var data = event.data;
  var origin = event.origin;
  var lastEventId = event.lastEventId;
  // handle message
}, false);
```

## 命名事件

单个事件流可以通过实现*命名事件*来指定各种类型的事件。命名事件不由`message`事件处理程序处理。相反，每种类型的命名事件都由其自己唯一的处理程序来处理。例如，如果一个事件流包含名为`foo`的事件，那么就需要下面的事件处理程序。请注意，`foo`事件处理程序与`message`事件处理程序完全相同，只是事件类型不同。当然，任何其他类型的命名消息都需要单独的事件处理程序。

```
source.addEventListener("foo", function(event) {
  var data = event.data;
  var origin = event.origin;
  var lastEventId = event.lastEventId;
  // handle message
}, false);
```

## 处理错误

如果事件流出现问题，就会触发`EventSource`的`onerror`事件处理程序。错误的一个常见原因是连接中断。虽然`EventSource`对象会自动尝试重新连接到服务器，但是在断开连接时也会触发一个错误事件。下面的例子显示了一个`onerror`事件处理程序。

```
source.onerror = function(event) {
  // handle error event
};
```

当然，也可以使用`addEventListener()`重写`onerror`事件处理程序，如下所示。

```
source.addEventListener("error", function(event) {
  // handle error event
}, false);
```

## 分离

客户端可以通过调用`close()`方法随时终止`EventSource`连接。`close()`的语法如下所示。`close()`方法不接受任何参数，也不返回值。

```
source.close();
```

## 连接状态

一个`EventSource`连接的状态存储在它的`readyState`属性中。在生命周期的任何时候，连接都可能处于三种状态之一:连接、打开和关闭。下表描述了每种状态。

*   连接–当一个`EventSource`对象被创建时，它最初进入连接状态。在此期间，连接尚未建立。如果已建立的连接丢失，则`EventSource`也将转换到连接状态。处于连接状态的`EventSocket`的`readyState`值为 0。该值被定义为常数`EventSource.CONNECTING`。
*   open-建立的连接被称为处于打开状态。`EventSource`处于打开状态的对象可以接收数据。值为 1 的`readyState`对应于打开状态。该值被定义为常数`EventSource.OPEN`。
*   关闭–如果没有建立连接并且没有尝试重新连接，则称`EventSource`处于关闭状态。这种状态通常是通过调用`close()`方法进入的。处于关闭状态的`EventSource`的`readyState`值为 2。该值被定义为常数`EventSource.CLOSED`。

下面的例子展示了如何使用`readyState`属性来检查一个`EventSource`连接。为了避免硬编码`readyState`值，这个例子使用了状态常量。

```
switch (source.readyState) {
  case EventSource.CONNECTING:
    // do something
    break;
  case EventSource.OPEN:
    // do something
    break;
  case EventSource.CLOSED:
    // do something
    break;
  default:
    // this never happens
    break;
}
```

## 结论

本文介绍了服务器发送事件的客户端方面。如果您有兴趣了解更多关于服务器发送事件的信息，我推荐您阅读[服务器发送事件](http://cjihrig.com/blog/the-server-side-of-server-sent-events/ "The Server Side of Server-Sent Events")的服务器端。我还写了一篇关于 Node.js 中[服务器发送事件的文章。尽情享受吧！](http://cjihrig.com/blog/server-sent-events-in-node-js/ "Server-Sent Events in Node.js")

## 分享这篇文章