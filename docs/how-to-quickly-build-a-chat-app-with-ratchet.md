# 如何用棘轮快速搭建聊天 App

> 原文：<https://www.sitepoint.com/how-to-quickly-build-a-chat-app-with-ratchet/>

在本教程中，我们将看一看[棘轮](http://socketo.me/)，一个用于 WebSockets 的 PHP 库。让我们从定义什么是 WebSockets 开始。 [MDN](https://developer.mozilla.org/en/docs/WebSockets) 说道:

> WebSockets 是一项高级技术，它使得在用户的浏览器和服务器之间打开交互式通信会话成为可能。有了这个 API，您可以向服务器发送消息并接收事件驱动的响应，而不必轮询服务器的回复。

![Connected computers image](img/f04613089652931f3d0ea0d0425a37d5.png)

WebSockets 允许我们编写能够在浏览器和服务器之间实时传递数据的应用程序。

## 设置

首先，让我们使用 Composer 安装棘轮:

```
composer require cboden/ratchet
```

## 构建应用程序

现在我们已经准备好构建应用程序了。在`class/ChatApp`目录下创建一个`Chat.php`文件。这将是一个在`ChatApp`命名空间下的类，它将使用 use 棘轮的`MessageComponentInterface`和`ConnectionInterface`。`MessageComponentInterface`用作棘轮应用的基本构建模块，而`ConnectionInterface`代表与应用的连接。

```
<?php namespace  ChatApp;  use  Ratchet\MessageComponentInterface;  use  Ratchet\ConnectionInterface;
```

让全班实现`MessageComponentInterface`。这包含了我们需要实现的方法的蓝图，例如`onOpen`、`onClose`和`onMessage`。

```
class  Chat  implements  MessageComponentInterface  {  }
```

在类内部，我们声明了一个名为`$clients`的变量。这是我们稍后将在聊天应用程序中存储当前连接的客户端列表的位置。从构造函数中，您会看到我们正在使用`SplObjectStorage`。这为我们提供了一种存储对象的方式。在这种情况下，我们需要存储的对象是每个客户端的连接对象。

```
protected $clients;  public  function __construct()  { $this->clients =  new \SplObjectStorage;  }
```

接下来，我们实现`onOpen`方法。每次在浏览器中打开新连接时，都会调用此方法。这是使用`attach`方法存储新的连接对象。我们还输出有人已经连接，作为测试`onOpen`方法是否正常工作的一种手段。

```
public  function onOpen(ConnectionInterface $conn)  {  //store the new connection $this->clients->attach($conn); echo "someone connected\n";  }
```

接下来是`onMessage`法。每当浏览器中的特定客户端发送消息时，都会调用此方法。每次调用此方法时，发送消息的客户端的连接对象以及实际消息都作为参数传递。我们所做的就是遍历所有当前连接的客户端，并向它们发送消息。在下面的代码中，我们检查当前循环迭代中的客户端是否是发送消息的客户端。我们不想向发送信息的人发送相同的信息。

```
public  function onMessage(ConnectionInterface $from, $msg)  {  //send the message to all the other clients except the one who sent.  foreach  ($this->clients as $client)  {  if  ($from !== $client)  { $client->send($msg);  }  }  }
```

接下来是`onClose`法。顾名思义，每当客户端从浏览器关闭 WebSocket 连接时，都会调用该方法。当用户刷新或完全关闭浏览器选项卡时，会触发此方法。我们所要做的就是调用客户端列表上的`detach`方法，并将连接作为参数传入。这将删除该特定连接。

```
public  function onClose(ConnectionInterface $conn)  { $this->clients->detach($conn); echo "someone has disconnected";  }
```

最后，我们有一个`onError`方法，每次出现连接错误时都会触发这个方法。当这种情况发生时，我们输出发生的错误，然后调用连接中的`close`方法来关闭它。

```
public  function onError(ConnectionInterface $conn, \Exception $e)  { echo "An error has occurred: {$e->getMessage()}\n"; $conn->close();  }
```

现在我们准备创建条目文件，它将利用我们刚刚创建的文件。我们将从命令行运行它。将其命名为`cmd.php`，保存在您的工作目录的根目录下，然后添加以下代码。

```
<?php require  'vendor/autoload.php';  use  Ratchet\Server\IoServer;  use  Ratchet\Http\HttpServer;  use  Ratchet\WebSocket\WsServer;  use  ChatApp\Chat; $server =  IoServer::factory(  new  HttpServer(  new  WsServer(  new  Chat()  )  ),  8080  ); $server->run();
```

这个文件的作用是创建一个运行在端口 8080 上的新 WebSocket 服务器。稍后，我们将从客户端连接到该服务器。

在我们继续之前，让我们分解文件。首先，我们包括自动加载文件，以便我们可以使用我们的文件不同的棘轮组件。

```
require  'vendor/autoload.php';
```

接下来，我们指定我们需要哪些具体的棘轮组件。对于这个聊天应用程序，我们需要`IoServer`、`HttpServer`和`WsServer`。

```
use  Ratchet\Server\IoServer;  use  Ratchet\Http\HttpServer;  use  Ratchet\WebSocket\WsServer;
```

以下是对每个组件的简要描述:

*   `IoServer`允许我们接收、读写和关闭连接，以及处理可能出现的错误。这也提供了基本的服务器功能，所以我们可以用它来创建一个新的服务器实例。

*   `HttpServer`允许我们解析传入的 HTTP 请求。每当用户连接到服务器或用户发送消息时，都会使用该组件。

*   `WsServer`是 WebSocket 服务器。这允许我们与实现 WebSocket API 的浏览器对话。大多数现代浏览器已经实现了 WebSockets ，所以如果你不打算支持旧的浏览器，这不会有问题。如果是这样，那么您可以考虑在您的应用程序中添加一个[闪存策略](http://socketo.me/docs/flash)。

回到`cmd.php`文件，我们还使用了之前创建的`Chat`类。

```
use  ChatApp\Chat;
```

一旦完成，我们就可以创建 WebSockets 服务器了。为此，我们需要在`IoServer`组件上调用`factory`方法，然后传入一个新的`HttpServer`实例。这个新的`HttpServer`实例然后接受一个新的`WsServer`实例。最后，我们将`Chat`类的一个新实例传递给`WsServer`。您可以在文档中看到哪个特定的服务器组件包装了什么[。](http://socketo.me/docs/server)

```
$server =  IoServer::factory(  new  HttpServer(  new  WsServer(  new  Chat()  )  ),  8080  );
```

我们通过调用`run`方法来运行服务器。

```
$server->run();
```

此时，您可以从终端开始运行服务器:

```
php cmd.php
```

### 超文本标记语言

接下来，我们转向客户端。在工作目录的根目录下创建一个`index.html`文件，然后添加以下代码:

```
<!DOCTYPE html>  <html  lang="en">  <head>  <meta  charset="UTF-8">  <title>chatapp</title>  <script  src="https://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.4/jquery.min.js"></script>  <script  src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/3.0.3/handlebars.min.js"></script>  <script  src="http://cdnjs.cloudflare.com/ajax/libs/moment.js/2.10.2/moment.min.js"></script>  <link  rel="stylesheet"  href="css/style.css">  </head>  <body>  <div  id="wrapper">  <div  id="user-container">  <label  for="user">What's your name?</label>  <input  type="text"  id="user"  name="user">  <button  type="button"  id="join-chat">Join Chat</button>  </div>  <div  id="main-container"  class="hidden">  <button  type="button"  id="leave-room">Leave</button>  <div  id="messages">  </div>  <div  id="msg-container">  <input  type="text"  id="msg"  name="msg">  <button  type="button"  id="send-msg">Send</button>  </div>  </div>  </div>  <script  id="messages-template"  type="text/x-handlebars-template">  {{#each messages}}  <div class="msg">  <div class="time">{{time}}</div>  <div class="details">  <span class="user">{{user}}</span>: <span class="text">{{text}}</span>  </div>  </div>  {{/each}}  </script>  <script  src="js/main.js"></script>  </body>  </html>
```

对于客户端，我们将使用 [jQuery](http://jquery.com/) 来监听点击事件和操作 DOM。[手柄](http://handlebarsjs.com/)将处理模板[时刻](http://momentjs.com/)将显示信息发送的时间。

### 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

对于我们的样式表，我们坚持使用最小的东西。只是最低限度的造型，所以它看起来足够体面。下面是`css/style.css`文件:

```
.hidden { display: none;  }  #wrapper { width:  800px; margin:  0  auto;  }  #leave-room { margin-bottom:  10px;  float: right;  }  #user-container { width:  500px; margin:  0  auto; text-align: center;  }  #main-container { width:  500px; margin:  0  auto;  }  #messages { height:  300px; width:  500px; border:  1px solid #ccc; padding:  20px; text-align: left; overflow-y: scroll;  }  #msg-container { padding:  20px;  }  #msg { width:  400px;  }  .user { font-weight: bold;  }  .msg { margin-bottom:  10px; overflow: hidden;  }  .time {  float: right; color:  #939393; font-size:  13px;  }  .details { margin-top:  20px;  }
```

### 射流研究…

对于我们的 JavaScript 文件(`js/main.js`)，我们有以下内容:

```
(function(){  var user;  var messages =  [];  var messages_template =  Handlebars.compile($('#messages-template').html());  function updateMessages(msg){ messages.push(msg);  var messages_html = messages_template({'messages': messages}); $('#messages').html(messages_html); $("#messages").animate({ scrollTop: $('#messages')[0].scrollHeight},  1000);  }  var conn =  new  WebSocket('ws://localhost:8080'); conn.onopen =  function(e)  { console.log("Connection established!");  }; conn.onmessage =  function(e)  {  var msg = JSON.parse(e.data); updateMessages(msg);  }; $('#join-chat').click(function(){ user = $('#user').val(); $('#user-container').addClass('hidden'); $('#main-container').removeClass('hidden');  var msg =  {  'user': user,  'text': user +  ' entered the room',  'time': moment().format('hh:mm a')  }; updateMessages(msg); conn.send(JSON.stringify(msg)); $('#user').val('');  }); $('#send-msg').click(function(){  var text = $('#msg').val();  var msg =  {  'user': user,  'text': text,  'time': moment().format('hh:mm a')  }; updateMessages(msg); conn.send(JSON.stringify(msg)); $('#msg').val('');  }); $('#leave-room').click(function(){  var msg =  {  'user': user,  'text': user +  ' has left the room',  'time': moment().format('hh:mm a')  }; updateMessages(msg); conn.send(JSON.stringify(msg)); $('#messages').html(''); messages =  []; $('#main-container').addClass('hidden'); $('#user-container').removeClass('hidden'); conn.close();  });  })();
```

分解 JavaScript 文件。首先，我们将所有内容包装在一个立即调用的函数表达式中，这样我们就可以确保我们包含的代码不会与我们以后可能包含的任何其他 JavaScript 进行交互。

```
(function(){  })();
```

初始化用户、消息和消息模板。我们将使用`user`变量来存储进入聊天室的用户名。`messages`用于存储当前发送的消息，`messages_template`是 handlebars 模板，我们将使用它来构建显示所有消息的 HTML。

```
var user;  var messages =  [];  var messages_template =  Handlebars.compile($('#messages-template').html());
```

然后，我们创建一个方法，该方法将在特定用户每次发送或接收消息时执行。这样做的目的是将作为参数提供的消息推入 messages 数组。然后，它使用 messages 数组和`messages_template`为消息构建 HTML。然后，它更新消息容器的 HTML。最后，我们向下滚动到消息容器的底部，这样用户就可以看到最新的消息。

```
function updateMessages(msg){ messages.push(msg);  var messages_html = messages_template({'messages': messages}); $('#messages').html(messages_html); $("#messages").animate({ scrollTop: $('#messages')[0].scrollHeight},  1000);  }
```

然后我们创建一个新的 WebSocket 连接。这将 WebSocket 服务器的 URL 作为其参数。在本例中，我们连接到`ws://localhost:8080`,这是我们之前从终端运行的服务器。

```
var conn =  new  WebSocket('ws://localhost:8080');
```

之后，我们监听我们创建的 WebSocket 连接中的`onopen`事件。这并不真正做任何相关的事情，我们只是用它来检查我们是否已经成功地连接到服务器。

```
conn.onopen =  function(e)  { console.log("Connection established!");  };
```

接下来，我们监听`onmessage`事件。每次从任何连接的客户端发送新消息时，都会触发此事件。当这个事件发生时，我们使用`JSON.parse`方法将从服务器获得的 JSON 字符串转换成 JavaScript 对象。然后我们调用`updateMessages`方法并传入结果。

```
conn.onmessage =  function(e)  {  var msg = JSON.parse(e.data); updateMessages(msg);  };
```

## 加入聊天室

当用户点击“加入聊天”按钮时，就会加入聊天室。这样做的目的是将用户在 username 字段中输入的值赋给用户变量。然后，它隐藏包含用户名字段的用户容器，并显示包含消息和用于输入新消息的字段的主容器。之后，我们构造一个新的消息对象。消息对象具有`user`、`text`和`time`属性。然后我们调用`updateMessages`方法，这样消息被追加到消息框中。接下来，我们调用 WebSocket 连接上的`send`方法，并传入新消息的 JSON 字符串表示。最后，我们清空用户名字段。

```
$('#join-chat').click(function(){ user = $('#user').val(); $('#user-container').addClass('hidden'); $('#main-container').removeClass('hidden');  var msg =  {  'user': user,  'text': user +  ' entered the room',  'time': moment().format('hh:mm a')  }; updateMessages(msg); conn.send(JSON.stringify(msg)); $('#user').val('');  });
```

### 发送新消息

这里我们做的事情和新用户加入聊天室时一样，只是这次文本是用户输入的文本。

```
$('#send-msg').click(function(){  var text = $('#msg').val();  var msg =  {  'user': user,  'text': text,  'time': moment().format('hh:mm a')  }; updateMessages(msg); conn.send(JSON.stringify(msg)); $('#msg').val('');  });
```

### 离开聊天室

这将向所有连接的客户端发送一条消息，告知特定用户已经离开房间。然后它清理消息 HTML 和数组。我们还隐藏主容器并显示用户容器，以便新用户可以加入。

```
$('#leave-room').click(function(){  var msg =  {  'user': user,  'text': user +  ' has left the room',  'time': moment().format('hh:mm a')  }; updateMessages(msg); conn.send(JSON.stringify(msg)); $('#messages').html(''); messages =  []; $('#main-container').addClass('hidden'); $('#user-container').removeClass('hidden');  });
```

## 结论

在本教程中，我们创建了一个简单的聊天应用程序，通过棘轮利用 WebSockets 提供的实时功能。本教程使用的文件可以在这个 [Github repo](https://github.com/anchetaWern/sitepoint_codes/tree/master/ratchet_chatapp) 上获得。

有问题吗？评论？把它们留在下面！

## 分享这篇文章