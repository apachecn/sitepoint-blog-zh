# 用 SignalR 构建聊天应用程序

> 原文：<https://www.sitepoint.com/building-chat-application-signalr/>

SignalR 是一个面向 ASP.NET 开发者的开源库。这就相当于什么[插座。IO](http://socket.io/) 是针对 Node.js 的(如果你愿意，可以查看[关于堆栈溢出](http://stackoverflow.com/questions/9537641/node-js-socket-io-vs-signalr-vs-c-sharp-websocket-server)的比较)。SignalR 可用于为您的应用程序提供实时 web 功能。通常，如果你喜欢 Ember 和 JavaScript，你可能会倾向于选择 Socket。IO 并坚持使用 JavaScript。我选择 SignalR 的原因之一是它有更丰富的文档和一些参考资料。此外，你可以免费获得 ASP.NET 世界的所有好处。

在本文中，我将向您展示如何使用 SignalR 构建一个简单的聊天应用程序。如果你还没做过，建议你看看我之前的一个名为“[一个使用 Socket 的聊天应用。IO](https://www.sitepoint.com/chat-application-using-socket-io/) ”以便对这些主题有一个更完整的概述，然后比较两种方法的优缺点。

## ember-cli 入门

我们将从创建一个新的 Ember 应用程序开始，我们将利用 [ember-cli](http://ember-cli.com) 。作为第一项任务，让我们安装一些依赖项:

```
$ ember new chatr
$ ember install semantic-ui-ember
```

在这里，我们安装了 [semantic-ui](http://semantic-ui.com) 即<q>，这是一个开发框架，使用人性化的 HTML 帮助创建漂亮的、响应性强的布局。</q>它与 [Bootstrap](http://getbootstrap.com/) 非常相似，它将帮助我们设计网站的布局和主题。一旦完成，我们必须将其导入到应用程序中。

现在，将以下几行添加到`Brocfile.js`文件中:

```
app.import('bower_components/semantic-ui/dist/semantic.css');
app.import('bower_components/semantic-ui/dist/semantic.js');
```

我们现在准备创建我们的路线，并添加一些模板。为此，请执行以下命令:

```
$ ember g route chat
```

这将创建我们的余烬路线`app/routes/chat.js`和模板`app/templates/chat.hbs`。在向模板添加任何东西之前，我们将利用一些 Ember 组件来封装模板并使它们可重用。让我们从一个`chat-room`组件开始:

```
$ ember g component chat-room
$ ember g component chat-userlist
$ ember g component chat-area
$ ember g component chat-useritem
```

如你所见，我们有很多组件。每个组件都有一个关联的模板文件(`app/templates/components/chat-room.hbs`)和一个 Ember 组件脚本文件(`app/components/chat-room.js`)。这样，我们可以隔离我们的聊天功能，使其易于测试和推理。此时，我们可以在我们的聊天路线中添加一个`chat-room`:

```
{{#chat-room
  users=room.users
  messages=room.messages
  topic=room.topic
  onSendChat="sendChat"}}{{/chat-room}}
```

`users`、`messages`和`topic`是我们传递给组件的数据，而`onSendChat`是由组件触发的动作。如果你想加深这些概念，你可以通过阅读余烬指南找到[更多信息。](http://guides.emberjs.com)

最后，我们需要一个 Ember 控制器(来处理我们聊天路径的逻辑)和一些 Ember 模型。为此，请执行以下命令:

```
$ ember g controller chat
$ ember g model chat-room
$ ember g model chat-user
$ ember g model chat-message
```

模型是继承自`Ember.Object`的有用类。它们将保存我们的数据，并提供到模板的数据绑定。同样的动作由控制器完成，控制器修饰模型，也可以处理用户动作。

打开`app/controllers/chat.js`文件，向其中添加以下代码:

```
export default Ember.Controller.extend({
  initRoom: function(users, messages, topic) {
    var room = Room.create({
      users: users,
      messages: messages,
      topic: topic
    });

    this.set('room', room);
  },

  addMessage: function(msg) {
    var room = this.get('room');
    room.get('messages').addObject(msg);
  },

  userJoin: function(user) {
    var room = this.get('room');
    room.get('users').addObject(user);
  },

  actions: {
    sendChat: function(msg) {
      // use these methods here to test if they are working
      //this.addMessage('ME', msg);
      //this.userJoin(msg);
    }
  }
});
```

`addMessage()`和`userJoin()`是助手方法，我们可以在需要添加新聊天消息或新用户时调用它们。`sendChat`是当用户想要发送消息时触发的动作处理程序。`initRoom()`是我们设置绑定的构造函数。你可以在我们聊天路径的`setupController()`钩子中用空数据调用它来测试一切是否正常。

现在让我们通过添加下面列出的代码来编辑`app/routes/chat.js`文件:

```
export default Ember.Route.extend({
  setupController: function(controller) {
    // use this method to test everything is working when data is bound.
    //controller.initRoom([],[], 'hello world');
  }
});
```

## 用 SignalR 构建服务器端

在使用 SignalR 时，我们需要使用的另一个有用的工具是 [Visual Studio](https://www.visualstudio.com/) 。下载后，打开它并创建一个新的`Empty Web Application`项目，用包管理器控制台安装所需的包:

```
Install-Package Microsoft.AspNet.Signalr
```

该命令将安装 SignalR 及其所有依赖项，包括“Microsoft。Owin”。然后，我们继续创建我们的 OWIN 启动类来引导我们的服务器。为此，我们将在`App_Start/Startup.cs`文件中包含以下代码:

```
public class Startup {
  public void Configuration(IAppBuilder app) {
    app.MapSignalR();
  }
}
```

这就是运行一个基于 OWIN 的网络服务器。我们向 OWIN 添加了 SignalR 中间件，但是您可以根据需要添加其他中间件(比如身份验证或 Web APIs)。现在，我们的 SignalR 应用程序可以通过按`F5`来启动。我们没有托管任何数据，所以浏览器不会显示任何对我们有用的内容。这个 JavaScript 代码是由 SignalR 动态生成的，并准备好供我们的 Ember 应用程序使用。它为我们提供了 JavaScript 方法，这些方法将进一步调用服务器端的方法。

### 创建大厅中心

集线器类用于与客户端通信。它可以在客户端调用方法，并定义从客户端调用的方法。SignalR 在每次新的客户机连接或调用服务器时都会创建一个新的 Hub 类。现在，让我们看看如何创建一个大厅中心:

```
public class Lobby : Hub {
    private IChatRRepository _repository;

    public Lobby(IChatRRepository repository) {
      _repository = repository;
    }

    public void Join(string name) {
      ChatUser currentUser = new ChatUser(name, Context.ConnectionId);
      _repository.AddUser(currentUser);

      var users = _repository.Users.ToList();
      var topic = "Welcome to EmberJS on SignalR";

      Clients.Caller.lobbyEntered(topic, users);
    }

    public void SendChat(string msg) {
      ChatUser user = _repository.GetUserById(Context.ConnectionId);
      Clients.All.chatSent(user.Name, msg);
    }

    public override Task OnDisconnected(bool stopCalled) {
      _repository.RemoveUser(Context.ConnectionId);
      return base.OnDisconnected(stopCalled);
    }
  }
```

对于每个客户端请求，SignalR 都会实例化一个新的 Hub 实例。集线器不维护任何客户端状态。因此，我们需要某种数据库来跟踪客户端状态。`IChatRepository`接口通过提供`AddUser()`、`RemoveUser()`和`Users()`等方法来检索所有用户，从而为我们提供所需的状态。中枢方法可以从客户端调用，中枢类可以使用`Clients`属性调用客户端方法。

下面您可以找到一个指定哪些客户端将接收方法调用的列表:

*   `Clients.All.someMethod()`:所有连接的客户端
*   `Clients.Caller.someMethod()`:仅呼叫客户端
*   `Clients.Others.someMethod()`:除呼叫者外的所有客户
*   `Clients.Client(Context.ConnectionId).someMethod()`:特定客户

如你所见，它有一个直观的 API。`someMethod()`是动态调度的，所以可以是任何东西。有关 Hubs API 的更多信息，请[参考指南](http://www.asp.net/signalr/overview/guide-to-the-api/hubs-api-guide-server)。

回到我们的例子，我们有两个 Hub 方法:`Join()`和`SendChat()`。当客户端连接时，它会用一个`username`调用`Join()`。我们将用户添加到我们的存储库中，并在`Clients.Caller`上调用`lobbyEntered()`方法。

当客户端发送聊天消息时，调用`SendChat()`方法。我们从存储库中检索调用者，并通过调用`Clients.All.chatSent()`方法将消息广播给所有连接的客户端。反过来，它在所有连接的客户机上调用`chatSent()`方法。

最后，有一些方法，如`OnConnected()`和`OnDisconnected()`，当用户连接/断开连接时，我们可以覆盖它们以获得通知。关于 SignalR API 的更多信息，请看一下[的 SignalR 指南](http://www.asp.net/signalr/overview/guide-to-the-api)。

## 使用 Ember 在客户端设置信号

现在，让我们回到我们的客户端应用程序并集成 SignalR。首先，用[扳手](http://bower.io/)安装信号装置:

```
$ bower install signalr --save
```

接下来，将其导入到我们的应用程序中。为此，再次打开`Brocfile.js`文件并添加以下行:

```
app.import('bower_components/signalr/jquery.signalR.js');
```

最后，将`http://localhost:<port>/signalr/hubs`脚本包含在您的`app/index.html`页面中:

```
<script src="assets/vendor.js"></script>
<script src="http://localhost:53246/signalr/hubs"></script>
<script src="assets/chatr.js"></script>
```

在这个阶段，注意元素的顺序，这一点很重要，因为 SignalR 是作为 jQuery 插件导出的。所以我们需要首先包含 jQuery(在`assets/vendor.js`里面)，然后是在`/signalr/hubs`的动态脚本文件，最后是我们在它上面的应用程序(`assets/chatr.js`)。

### 使用 Ember 初始化器将信号注入聊天路径

当我们的应用程序启动时，我们必须创建一个 SignalR 连接，然后在我们的控制器中使用它。这里的架构由你决定。我们将使用 Ember 初始化器在我们的路线中注入信号。让我们看看如何使用前面提到的 [ember-cli](http://ember-cli.com/) 来创建它。

```
$ ember g initializer signalr
```

现在让我们初始化 SignalR 并将其注入到我们的路线中。下面的代码片段进入了`app/initializer/signalr.js`文件:

```
import SignalRConnection from 'chatr/utils/net/chatr-realtime';

export function initialize(container, application) {
  var realtime = new SignalRConnection('http:/localhost:<port>/signalr');

  application.register('realtime:signalr', realtime, { instantiate: false });

  application.inject('route:chat', 'signalr', 'realtime:signalr');
}
```

是一个围绕 SignalR 的包装类，它肯定会让我们的生活变得更容易。我们创建它，并使用依赖注入将其注入到聊天路由中。再次，[如果你需要更多信息，请参考完整的烬指南](http://guides.emberjs.com/v1.10.0/understanding-ember/dependency-injection-and-service-lookup/)。

你可以查看一下[signal connection](https://github.com/sitepoint-editors/ChatRClient/blob/master/app/utils/net/chatr-realtime.js%22)类，看看它是如何实现的。这里我们有两个感兴趣的方法:

```
configureHubs(ctrl) {
  this.OnLobby = new LobbyCallbacks(this, ctrl);

  var lobby = Ember.$.connection.lobby;

  lobby.client['lobbyEntered'] = this.OnLobby['lobbyEntered'];
  lobby.client['chatSent'] = this.OnLobby['chatSent'];
}
```

在启动 SignalR 连接之前，我们需要设置客户机方法，服务器可以在大厅集线器上调用这些方法。`Ember.$.connection`是我们的信号连接，而`Ember.$.connection.lobby`是我们的大厅枢纽。这些是在动态生成的信号代码中定义的。我们通过将这些方法分配给我们大厅中心的`client`属性，即`Ember.$.connection.lobby.client`属性来设置这些方法。

在我们的例子中，它们是在 [LobbyCallbacks](https://github.com/sitepoint-editors/ChatRClient/blob/master/app/utils/net/lobby-callbacks.js) 类中定义的:

```
start(name) {
  var self = this;

  var hub = Ember.$.connection.hub;

  hub.error(function(reason) {
    console.log('connection error: ' + reason);
  });

  return hub.start({
    withCredentials: false
  }).then(function() {
    console.log('connected');
    Ember.$.connection.lobby.server.join(name);
  });
}
```

在定义了客户端方法之后，我们可以使用这个方法来启动应用程序。首先我们得到一个对`Ember.$.connection.hub`的引用，这里我们设置了`error`钩子来通知任何连接错误。最后，我们运行一个`start`调用来启动连接，并得到一个承诺作为回报。

一旦接通，我们就叫`Ember.$.connection.lobby.server.join()`。这个方法将调用服务器端`Lobby`中心的`Join()`方法。[有关 SignalR 客户端 API 的更多信息，请访问 SignalR 指南](http://www.asp.net/signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client)。

## 与 CORS 打交道

此时，我们可以从 Ember 应用程序连接到我们的服务器。但是，我们可能会遇到如下一些浏览器错误:

```
XMLHttpRequest cannot load http://localhost:53246/signalr/negotiate?clientProtocol=1.5&connectionData=%5B%7B%22name%22%3A%22lobby%22%7D%5D&_=1433597715652\. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://192.168.56.103:4200' is thus, so not allowed access.
```

此错误可能是由于您的服务器和客户端在不同的域上造成的。你需要允许服务器上的`CORS`绕过它。因此，让我们在 Visual Studio 软件包管理器控制台上安装软件包:

```
Install-Package Microsoft.Owin.Cors
```

然后，配置 Owin 中间件以允许跨域请求(编辑`App_Start/Startup.cs`文件):

```
public void Configuration(IAppBuilder app) {
  app.Map("/signalr", map =>
  {
    var corsPolicy = new CorsPolicy
    {
      AllowAnyHeader = true,
      AllowAnyMethod = true
    };

    // Add the domain where your client is hosted on.
    corsPolicy.Origins.Add("http://192.168.56.103:4200");
    map.UseCors(new CorsOptions
    {
      PolicyProvider = new CorsPolicyProvider {
      PolicyResolver =
        r => Task.FromResult(corsPolicy)
      }
    });

    map.RunSignalR(config);
  });
}
```

## 结论

在本文中，我们看到了如何将 SignalR 与 Ember 结合起来，并通过几个简单的步骤创建一个聊天应用程序。如果你想看它的运行，在 chatembar】有一个有趣的[现场演示，如果你想亲自参与这个项目，GitHub 上有完整的源代码，包括](http://chatembar.herokuapp.com/chat)[客户端](https://github.com/sitepoint-editors/ChatRClient)和[服务器端](https://github.com/sitepoint-editors/ChatRServer)。此外，您可以参考另一个使用 SignalR 的协作聊天应用程序的很好的例子，名为 [JabbR](https://github.com/Jabbr/Jabbr) 。

我强烈建议您深入研究本文中我没有机会涉及的几个要点:OWIN 和身份验证。好消息是，SignalR 不需要任何特殊授权，因为它与现有的 ASP.NET 认证解决方案，如 ASP.NET 身份。

如果您想了解更多，这里有一些关于 Owin、SignalR 和 ASP.NET 身份的有用资源:

*   [使用 ASP.NET Web API 2、Owin 和身份的基于令牌的认证](http://bitoftech.net/2014/06/01/token-based-authentication-asp-net-web-api-2-owin-asp-net-identity/)
*   [向现有的 ASP.NET MVC 应用程序添加最小 OWIN 身份认证](http://weblog.west-wind.com/posts/2015/Apr/29/Adding-minimal-OWIN-Identity-Authentication-to-an-Existing-ASPNET-MVC-Application)
*   [ASP.NET 身份推荐资源](http://www.asp.net/identity/overview/getting-started/aspnet-identity-recommended-resources)
*   这是关于什么的？
*   [武士刀项目入门](https://msdn.microsoft.com/en-us/magazine/dn451439.aspx)
*   Nancy:轻量级、简单的框架，用于在。网络和单声道

## 分享这篇文章