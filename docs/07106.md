# HTML5 WebSockets API 简介

> 原文：<https://www.sitepoint.com/introduction-to-the-html5-websockets-api/>

HTML5 引入了许多新的有趣的 API，包括 WebSockets。WebSockets 允许开发人员通过在浏览器和服务器之间建立套接字连接来创建强大的实时应用程序。换句话说，由于持久连接，客户端和服务器可以随时交换数据。本教程解释了如何使用 WebSockets 创建实时 web 应用程序。

# 问题是

在实时应用程序中，服务器和客户机之间的连接必须是持久的。因此，为了制造服务器发起传输的假象，通常使用长轮询。WebSockets 通过在客户机和服务器之间建立持久的套接字连接来解决这个问题。一旦建立了连接，它将保持打开状态，直到客户端或服务器想要关闭它。它大大减轻了服务器的负担，最适合低延迟应用。

# 入门指南

打开 WebSocket 连接相当简单。您只需要调用`WebSocket()`构造函数来创建一个连接。

```
var connection=new WebSocket("ws://localhost:8787",['soap','json']);
```

`ws:`和`wss:`分别是普通和安全 WebSocket 连接的 URL 模式。第二个参数用于定义子协议名称，它可以是字符串数组或字符串。然而，服务器将只接受一个子协议。在连接的生命周期中，浏览器会收到几个事件，如连接打开、消息收到和连接关闭。要处理这些事件，请使用以下代码:

```
var connection=new WebSocket("ws://localhost:8787",'json');
connection.onopen = function () {
  connection.send('Hello, Server!!'); //send a message to server once connection is opened.
};
connection.onerror = function (error) {
  console.log('Error Logged: ' + error); //log errors
};
connection.onmessage = function (e) {
  console.log('Received From Server: ' + e.data); //log the received message
};
```

一旦连接打开，浏览器就使用`connection.send()`向服务器发送消息。如果遇到错误，上面的代码会简单地记录下来。如果服务器在任何时候向浏览器发送消息，就会触发`onmessage`回调。事件处理程序获取一个事件对象，该对象的`data`属性包含接收到的消息。

`connection.send()`方法也可以用来发送二进制数据。为此，您可以使用`Blob`或`ArrayBuffer`。下面的代码演示了如何使用`ArrayBuffer`将画布上绘制的图像发送到服务器。

```
var image = canvas2DContext.getImageData(0, 0, 440, 300);
var binary_data = new Uint8Array(image.data.length);
for (var i = 0; i < image.data.length; i++) {
  binary_data[i] = image.data[i];
}
connection.send(binary_data.buffer);
```

类似地，接收到的消息可以是字符串或二进制数据。二进制数据可以作为`blob`或`arraybuffer`接收。

# 简单 WebSocket 应用程序

为了创建一个有效的应用程序，你还需要一个服务器端的实现。可以使用 node.js、Java、.NET、Ruby 或 C++来创建服务器端实现。本节将向您展示如何使用 WebSockets 创建一个简单的应用程序。

示例应用程序将允许用户向服务器提出特定的问题。服务器端的实现是在 Windows 7 上使用 Java jWebSocket 框架完成的。因此，要设置环境，请遵循以下简单步骤。我假设你已经在你的 Windows 7 电脑上安装了最新的 JDK (JDK 7)。

### 第一步

前往 [jWebSocket Downloads](http://jwebsocket.org/downloads/downloads.htm) 并下载第一个标记为 server 的 zip 文件。

### 第二步

解压缩档案文件，并将其放在 C:中的某个位置。然后，创建一个名为 **JWEBSOCKET_HOME** 的新环境变量，它引用 JWEBSOCKET 安装的根目录。这是 jWebSocket-1.0 文件夹的路径。将以下 jar 添加到您的类路径中:

*   jweb socket _ HOME/libs/JWEBSOCKET server-1.0 . jar
*   JWEBSOCKET_HOME/libs/jWebSocketServerAPI-1.0.jar
*   JWEBSOCKET _ HOME/libs/jWebSocketCommon-1.0 . jar

### 第三步

创建一个新的 Java 源文件，将其命名为`SocketListener.java`。将以下代码添加到该文件中。

```
import java.util.Date;
import java.text.SimpleDateFormat;
import org.jwebsocket.factory.JWebSocketFactory;
import org.jwebsocket.server.TokenServer;
import org.jwebsocket.kit.WebSocketServerEvent;
import org.jwebsocket.api.WebSocketServerListener;
import org.jwebsocket.api.WebSocketPacket;
import org.jwebsocket.config.JWebSocketConfig;
import org.jwebsocket.instance.JWebSocketInstance;
class JWebSocketListener implements WebSocketServerListener {
  public void processOpened(WebSocketServerEvent event) {
    System.out.println("Connection Opened");
  }
  public void processPacket(WebSocketServerEvent event, WebSocketPacket packet) {
    switch(packet.getString()){
      case "1":
        packet.setString("My Name is jWebSocketServer");
        break;
      case "2":
        packet.setString("Windows 7 64 Bit");
        break;
      case "3":
        SimpleDateFormat sdf=new SimpleDateFormat("hh:mm:ss");
        packet.setString(sdf.format(new Date()));
        break;
    }
    event.sendPacket(packet);
  }
  public void processClosed(WebSocketServerEvent event) {
  }
}
public class SocketListener{
  public static void main(String[] args){
    JWebSocketFactory.printCopyrightToConsole();
    JWebSocketConfig.initForConsoleApp(new String[]{});
    JWebSocketFactory.start();
    TokenServer server = (TokenServer)JWebSocketFactory.getServer("ts0");
    if(server!=null) {
      server.addListener(new JWebSocketListener());
    }
    while (JWebSocketInstance.getStatus() != JWebSocketInstance.SHUTTING_DOWN){
      try {
        Thread.sleep(250);
      }
      catch (InterruptedException e) {
      }
    }
  }
}
```

#### 说明

代码实现了`WebSocketServerListener`接口。该接口声明了下面三个应该在我们的类中实现的方法，`JWebSocketListener`。

*   `processOpened()`
*   `processPacket()`
*   `processClosed()`

一旦连接打开，就调用`processOpened()`。例如，启动一个线程，定期向客户端发送更新。类似地，当连接关闭时会调用`processClosed()`，以便您可以执行任何清理。

在我们的应用程序中，主要的处理在`processPacket()`中完成。每当浏览器向服务器发送消息时，都会调用此方法。它接收两个类型为`WebSocketServerEvent`和`WebSocketPacket`的对象。首先，我们使用`WebSocketPacket#getString()`来读取消息(这是客户端提出的问题)。根据问题，服务器发送一个答案。使用`WebSocketPacket#setString()`将要发送的消息包装在一个`WebSocketPacket`中。然后，我们调用`WebSocketServerEvent#sendPacket()`，将数据包作为参数传递。接下来，我们创建一个名为`SocketListener`的公共类。这个类启动一个 WebSocket 实现服务器，并向它注册我们的自定义侦听器。

### 第四步

编译`SocketListener.java`，并使用命令`java SocketListener`启动您的服务器。

### 第五步

现在您已经完成了服务器端的实现，是时候创建将与服务器交互的客户机了。以下是我们最初的 HTML 标记:

```
<html>
  <head>
    <title>WebSocket Test</title>
    <style>
      #response{
        width: 200px;
        background: #F2F2F2;
        padding: 120px 0px 120px 0px;
        font-size:18px;
      }
    </style>
  </head>
  <body>
    <div align="center">
      Choose a question to ask:
      <select id="question" name="question" onchange="sendMessage(this.value);">
        <option value="1">What's Your Name</option>
        <option value="2">Which OS You Are Running On?</option>
        <option value="3">What Time Is It?</option>
      </select>
      <br/><br/>
      <div id="response">
        My Name is jWebSocketServer
      </div>
    </div>
  </body>
</html>
```

现在，将以下 JavaScript 添加到 HTML 中:

```
<script type="text/javascript">
var connection = new WebSocket('ws://localhost:8787', 'json');
connection.onopen = function () {
  console.log('Connection Opened');
};
connection.onerror = function (error) {
  console.log('WebSocket Error ' + error);
};
connection.onmessage = function (e) {
  if(e.data.indexOf("subProtocol")==-1)
    document.getElementById("response").innerHTML=e.data+"<br/>";
};
function sendMessage(msg){
  connection.send(msg);
}
</script>
```

#### 说明

我们创建了一个 HTML 文件，允许用户从下拉菜单中选择问题。当一个`onchange`事件触发时，我们获取所选项的值，并将该值发送给服务器。然后，服务器处理该请求，并向浏览器发回一个回答。当收到来自服务器的消息时，执行`onmessage`回调，在`response`T3 中显示响应。行`if(e.data.indexOf("subProtocol")==-1)`不是强制的。我之所以包含它，是因为最初当连接打开时，服务器会向浏览器发送一个包含信息的长字符串。因为我们不想显示这个字符串，所以我包含了上面的条件。

**注意:**不要直接打开这个 HTML 文件。将其放入`localhost`服务器，然后在浏览器中访问。

# 结论

使用 WebSocket API，您可以创建非常强大的实时应用程序。但是，请记住 WebSockets 允许跨源通信。因此，您应该只与您信任的服务器和客户端通信。以下是您可以使用此 API 创建的一些示例应用程序:

*   实时社交流更新
*   HTML5 多人游戏
*   在线聊天应用程序

查看 [Mozilla 开发者网络](https://developer.mozilla.org/en-US/docs/WebSockets)了解更多关于 WebSockets API 的信息。

## 分享这篇文章