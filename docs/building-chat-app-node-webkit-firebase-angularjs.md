# 使用 node-webkit、Firebase 和 Angular 构建聊天应用程序

> 原文：<https://www.sitepoint.com/building-chat-app-node-webkit-firebase-angularjs/>

在这篇文章中，我们将构建一个基于桌面的聊天应用，名为 **vTak** 。我们将使用 [node-webkit](https://github.com/rogerwang/node-webkit) 来驱动核心桌面应用程序和 [Firebase](https://www.firebase.com) 作为我们的实时数据存储。我们将使用一个名为[slush-wein](https://github.com/arvindr21/slush-wean)的 [slush 生成器](http://slushjs.github.io/generators)来搭建一个基本的 node-webkit/Express/Angular 应用程序。最终的产品将类似下图。

![Application Screenshot](img/567221242515a1f61c22194d9bf6eb27.png)

## 先决条件

如果您不熟悉 node-webkit，请参考以下资源:

[Node Webkit–利用 Node 和 Web 技术构建桌面应用](http://thejackalofjavascript.com/getting-started-with-node-webkit-apps)
[Node Webkit 驱动的仪表板应用](http://thejackalofjavascript.com/node-webkit-powered-dashboard-app)
[Node webkit 和 angular js–一款 MovieStub 应用](http://thejackalofjavascript.com/node-webkit-and-angularjs-a-moviestub-app/)

如果您是 Firebase 的新手，您应该查看以下资源:

【Firebase 入门

我建议在使用 node-webkit 应用程序时使用 Sublime text。更多信息请参考此处的。

## 应用程序

如前所述，我们将使用 node-webkit、Firebase 和 Angular 构建一个名为 vTak 的聊天应用程序。vTak 将允许用户通过注册或使用 Twitter、Google 和脸书等社交认证提供商登录。为此，我们将利用 Firebase 的简单登录。一旦用户登录，我们将显示要加入的聊天室列表。一旦用户选择了一个房间，我们将把用户连接到那个房间，并获取所有的聊天消息。然后用户就可以开始聊天了。

## 入门指南

为了构建应用程序基础，我们将利用名为`slush-wean`的 slush generator。这将为我们搭建一个具有快速和角度集成的基本节点 webkit 应用程序。

创建一个名为`vTak`的新文件夹，并在此处打开一个新的终端/提示。首先，我们将使用以下命令在全球安装`gulp`、`slush`和`slush-wean`:

```
$ npm i -g gulp slush slush-wean
```

要搭建断奶应用程序，运行`slush wean`。输入项目名称`vTak`，完成搭建。Slush 需要一点时间来设置项目并下载所需的依赖项。要运行应用程序，请执行`gulp run`。

`gulp run`将触发 [node-webkit-builder](https://github.com/mllrsohn/node-webkit-builder) 获取运行 node-webkit 应用程序所需的库，因此您无需担心。这是一个一次性过程，最多需要 5 分钟。下载完成后，dekstop 应用程序将会启动。

当应用程序启动时，你会看到一个大约 3 秒钟的闪屏，然后出现主页。如果您签出脚手架项目，您应该会看到以下结构:

```
.
├── app.js
├── gulpFile.js
├── index.html
├── package.json
├── public
│   ├── css
│   │   ├── app.css
│   │   └── bootstrap.css
│   ├── fonts
│   │   ├── ...
│   ├── js
│   │   └── app.js
│   ├── lib
│   │   ├── angular-resource.min.js
│   │   ├── angular-route.min.js
│   │   ├── angular.min.js
│   │   ├── bootstrap.min.js
│   │   └── jquery.min.js
│   └── partials
│       └── head.html
├── routes
│   └── index.js
└── views
    └── index.ejs
```

快速概述:

*   *app.js* :快递服务器配置
*   *gulpFile.js* :任务运行器
*   *index.html*:应用主页面/闪屏
*   *公共*文件夹:静态资源(客户端——我们的应用程序将在这里开发)
*   *路线*:快速路线
*   *视图*:我们 Angularjs 应用程序的初始视图，由 Express 提供

我们的第一步是设置身份验证。为了保持这篇文章的简单，我写了另一篇名为 [node-webkit 和 Firebase-Simple and Social authentic ation](http://thejackalofjavascript.com/node-webkit-firebase-simple-social-authentication)的文章。您可以按照帖子将身份验证与我们的 node-webkit 应用程序集成。

我将继续从上述职位的输出。你可以从上面的帖子中构建应用程序，也可以从[的这个报告](https://github.com/arvindr21/nwk-fb-auth)中克隆`ng-auth`文件夹。一旦你克隆/下载了回购，清除`vTak`文件夹的内容，并将`ng-auth`的**内容**复制到`vTak`。

*注意:我们将使用上面帖子中的角形版本。*

一旦您复制了内容，运行`npm i`来安装依赖项。接下来，打开`gulpFile.js`并根据您的操作系统注释/取消注释任务。最后打开`/public/js/controllers.js`，将第 5 行更新为:

```
var ref = new Firebase('https://nwkchatapp.firebaseio.com/');
```

如果您愿意，您可以配置自己的 Firebase 帐户。无论你采用什么方法，你最终的项目结构应该是:

![Project Directory Structure](img/fd9a33a787061cf512dd4d64cefb8f3b.png)

并且，当您运行应用程序(`gulp run`)时，您应该会看到以下登录屏幕。

![Login Screen](img/19691981ed6f396b84543e8b0419022f.png)

## 继续发展

您可以在`index.html`和`package.json`中将应用名称更新为`vTak`。

接下来，我们将下载所需的 Firebase 文件。下载 [firebase.js](https://cdn.firebase.com/js/client/1.0.18/firebase.js) 、 [angularfire.min.js](https://cdn.firebase.com/libs/angularfire/0.8.0/angularfire.min.js) 和 [firebase-simple-login.js](https://cdn.firebase.com/js/simple-login/1.6.3/firebase-simple-login.js) 并转储到`public/lib`文件夹中。

更新`views/index.ejs`如下图所示。

```
<!DOCTYPE html>
<html ng-app="ng-auth">

<head>
  <title>vTak</title>
  <link rel="stylesheet" href="css/bootstrap.css">
  <link rel="stylesheet" href="css/app.css">
  <script src="lib/angular.min.js"></script>
  <script src="lib/angular-route.min.js"></script>
  <script src="lib/angular-resource.min.js"></script>
  <script type="text/javascript" src="lib/firebase.js"></script>
  <script type="text/javascript" src="lib/firebase-simple-login.js"></script>
  <script type="text/javascript" src="lib/angularfire.min.js"></script>
  <script type="text/javascript" src="lib/jquery.min.js"></script>
  <script type="text/javascript" src="lib/bootstrap.min.js"></script>
  <script src="js/app.js"></script>
  <script src="js/factory.js"></script>
  <script src="js/controllers.js"></script>
  <script src="js/directives.js"></script>
</head>
</head>

<body>

  <div class="container" ng-controller="AppCtrl">
    <div ng-include src="'partials/head.html'"></div>
    <hr/>
    <ng-view></ng-view>
  </div>
</body>

</html>
```

现在，我们将对应用程序的主页(用户登录后被定向到的页面)进行一些清理。打开`public/partials/head.html`并更新，如下图所示:

```
<div class="header" ng-controller="Toolbar">
  <div class="btn-group pull-right">
    <a href="#" class="btn btn-xs btn-default" ng-show="user" ng-click="logoutUser()">Logout</a>
    <button ng-click="minimize()" type="button" class="btn btn-default btn-xs">
      <span class="glyphicon glyphicon-minus"></span>
    </button>
    <button ng-click="toggleFullscreen()" type="button" class="btn btn-default btn-xs">
      <span class="glyphicon glyphicon-fullscreen"></span>
    </button>
    <button ng-click="close()" type="button" class="btn btn-default btn-xs">
      <span class="glyphicon glyphicon-remove"></span>
    </button>
  </div>
  <h1>vTak</h1>
</div>
```

我们已经把登出按钮从页面的主体移到了标题，在窗口动作图标的旁边。

接下来，打开`public/js/controllers.js`并更新`AppCtrl`，如下所示:

```
ngAuth.controller('AppCtrl',
  function($rootScope, $scope, $window, $firebaseSimpleLogin) {
    $rootScope.URL = 'https://nwkchatapp.firebaseio.com/';
    var ref = new Firebase($rootScope.URL);
    $rootScope.authClient = $firebaseSimpleLogin(ref);

    $rootScope.redirect = function(user) {
      if ($window.location.href.indexOf('home') < 0)
        $window.location.assign('http://localhost:3000/#home');

      if (user.provider == 'password') {
        user.name = user.email;
        user.img = '/img/user.png'
      } else if (user.provider == 'facebook') {
        user.name = user.displayName;
        user.img = user.thirdPartyUserData.picture.data.url;
      } else if (user.provider == 'twitter') {
        user.name = user.displayName;
        user.img = user.thirdPartyUserData.profile_image_url;
      } else if (user.provider == 'google') {
        user.name = user.displayName;
        user.img = user.thirdPartyUserData.picture;
      }

      $rootScope.user = user;
    };

    $rootScope.$on('$firebaseSimpleLogin:login', function(e, user) {
      if (user) {
        $rootScope.redirect(user);
      }
    });
  }
)
```

这段代码有两处变化。首先，我们将 Firebase URL 移动到一个变量中。其次，我们在多个服务提供者之间统一了用户对象的用户名和图像。你可以在这里下载用户图片[。](https://github.com/arvindr21/vTak/tree/master/public/img)

接下来，我们将把注销功能移到`Toolbar`控制器中。像这样更新`public/js/controllers.js`中的`Toolbar`控制器:

```
controller('Toolbar', ['$rootScope', '$scope', 'Window',
  function($rootScope, $scope, Window) {
    $scope.minimize = function() {
      Window.minimize();
    };

    $scope.toggleFullscreen = function() {
      Window.toggleKioskMode();
    };

    $scope.close = function() {
      Window.close();
    };

    $scope.logoutUser = function() {
      $rootScope.user = '';
      $rootScope.authClient.$logout();
    };
  }
])
```

如果您现在重新运行应用程序，登录后，您会看到`logout`被移动到顶部。您还会在页面中央看到一个注销，我们稍后会处理这个问题。

现在，我们将在主页上工作。当用户登录 vTak 时，我们将向用户显示一个聊天室列表，并且我们将提供一个选项来创建他们自己的聊天室。我们将更新`public/partials/home.html`的标记，如下所示:

```
<div class="container" ng-controller="HomeCtrl">
  <label class="pull-right welcome-panel" ng-show="user">
    Welcome, <u>
        <span id="email">{{user.name}}</span></u>
    <img class="prof-img" ng-src="{{user.img}}" width="39" />
  </label>
  <br/>
  <h3 class="room-head">Chat Rooms
    <a href="javascript:" class="btn btn-xs btn-primary" ng-hide="isNew == true" ng-click="isNew = true; roomName = ''; ">New Room</a>
    <input ng-show="isNew == true" class="input-sm form-control cust-text" type="text" ng-model="roomName" placeholder="Room Name" />
    <a href="javascript:" ng-disabled="!roomName" ng-show="isNew == true" class="btn btn-xs btn-info" ng-click="newRoom()">Create</a>
  </h3>

  <input type="text" class="form-control" placeholder="Search Rooms" ng-model="search">
  <div class="rooms">
    <div ng-repeat="item in rooms | filter:search" class="room">
      <h3>{{item.roomname}}</h3>By : {{item.createdby}}
      <a class="btn btn-primary btn-xs pull-right join-room" href="javascript:" ng-click="joinChat($index)">Join</a>
      <a class="btn btn-danger btn-xs pull-right" ng-show="user.name == item.createdby" ng-click="deleteRoom($index)">Delete</a>

    </div>
    <div class="room" ng-show="rooms.length == 0">
      <h3>No Rooms Available. Create your own!</h3>
    </div>
  </div>

</div>
```

*   一行 2，我们显示用户的显示名称和个人资料图像。用户登录后，我们已经在`AppCtrl`中收集了这些信息。
*   在第 8 行，我们提供了一个创建新房间的按钮。一旦用户点击它，我们隐藏创建按钮，显示一个文本框和保存按钮。一旦用户保存了房间，它将出现在第 16 行填充的列表中。
*   在第 14 行，我们有一个搜索栏，用户可以在那里搜索可用的房间。
*   还要注意第 19 行。如果当前登录的用户与创建房间的用户相同，我们将显示“删除”按钮。

请注意，所有相关的 JavaScript 代码都将在`HomeCtrl`中处理。打开`public/js/controllers.js`并导航到最底部找到`HomeCtrl`。如下所示更新它:

```
.controller('HomeCtrl', function($rootScope, $scope, $firebase, $location) {
  var ref = new Firebase($rootScope.URL + 'chatRooms');
  var sync = $firebase(ref);

  $scope.rooms = sync.$asArray();

  $scope.newRoom = function() {
    sync.$push({
      createdby: $rootScope.user.name,
      roomname: $scope.roomName,
      createddate: Date.now()
    });
    $scope.isNew = false;
  };
  $scope.deleteRoom = function(room) {
    sync.$remove($scope.rooms[room].$id);
  };

  $scope.joinChat = function(room) {
    $location.path('/chat/' + $scope.rooms[room].$id);
  };
})
```

*   第 2 行——我们创建一个对`chatRooms`的新引用，并在第 3 行同步它。
*   第 4 行——我们查询 Firebase 数据存储，并将`chatRooms`中列出的所有房间填充为一个数组。
*   第 8 行——当用户创建一个新房间时，我们使用 push 方法保存所需的详细信息。
*   第 15 行–当用户删除一个房间时
*   第 19 行——当用户想要加入一个房间时，我们将他们重定向到一个新的路径。(*我们将很快创建*)

最后，添加所需的样式。打开`public/css/app.css`并添加以下类:

```
body {
  overflow-x: hidden;
}

.room-head {
  margin-top: -35px;
  border-bottom: 2px solid #CCC;
  padding: 20px 20px 8px;
}

.prof-img {
  vertical-align: bottom;
}

.welcome-panel {
  padding-right: 20px;
}

.cust-text {
  width: 22%;
  display: initial;
  vertical-align: middle;
  margin-left: 11px;
}

.rooms,.messages {
  border: 1px solid #e8e7e8;
  margin: 20px;
}

.room {
  border: 1px solid #c7c7c7;
  margin-bottom: -1px;
  background: #fff;
  -webkit-transition: background .5s;
  padding: 10px;
}

.room:hover {
  background: #e6e6e6;
}

.join-room {
  margin-left: 5px;
}

.messages {
  height: 377px;
  overflow: auto;
  border: 1px solid #e8e7e8;
}

.message {
  border-bottom: 1px solid #c7c7c7;
  background: #fff;
  height: 75px;
  -webkit-transition: background .5s;
  padding: 3px 3px 3px 10px;
}

.message img {
  vertical-align: baseline;
  margin-right: 9px;
}

.chat-input {
  position: absolute;
  bottom: 0;
  width: 93%;
  text-align: center;
  margin-bottom: 14px;
  padding-left: 8px;
}

.back-btn {
  vertical-align: bottom;
  margin-left: 20px;
}
```

保存所有文件并重新运行应用程序。如果您上次没有注销，我们的身份验证逻辑会将您重定向到主页。您可以点击‘新建房间`button and create a new room`。新创建的房间将自动神奇地出现在下面的列表中。

如果您希望调试应用程序，您可以在`package.json`中将`toolbar`和`frame`设置为`true`。

现在我们已经创建了一个新房间，让我们编写加入房间的逻辑，并开始与房间中的所有用户聊天。为此，我们将创建一条新路线。打开`public/js/app.js`并添加以下路线:

```
$routeProvider.when('/chat/:roomid', {
  templateUrl: 'partials/chat.html',
  controller: 'ChatCtrl'
});
```

接下来，在`public/partials`文件夹中创建一个名为`chat.html`的新文件。这个文件有聊天视图的模板。如下所示更新它:

```
<div class="container" ng-controller="ChatCtrl">
  <label class="pull-right welcome-panel" ng-show="user">
    <a href="/#/home" class="btn btn-info btn-xs back-btn">Back</a>
    Welcome, <u>
        <span id="email">{{user.name}}</span></u>
    <img class="prof-img" ng-src="{{user.img}}" width="39" />
  </label>
  <br/>

  <h3 class="room-head">Welcome to {{roomInfo.roomname}}</h3>

  <div class="messages" scroll-glue>
    <div ng-repeat="msgs in chatMessages" class="message">
      <h4>
        <img ng-src="{{msgs.userimg}}" width="20" />{{msgs.message}}
      </h4>
      <span>{{msgs.postedby}}
        {{msgs.posteddate | date:'yyyy-MM-dd HH:mm:ss'}}
      </span>
    </div>
    <div class="message" ng-show="chatMessages && chatMessages.length == 0">
      <h4>No message yet!</h4>
    </div>
  </div>
  <div class="chat-input">
    <input type="text" class="form-control" placeholder="Send Message" ng-model="message" ng-keypress="sendMessage($event)" autofocus>
  </div>

</div>
```

注意事项:
第 12 行–将保存所有消息。请注意`scroll-glue`指令，它会自动将聊天窗格滚动到最后一条消息。(*我们将在一会儿* )
第 26 行–输入框中输入信息。

现在，从[这里](https://rawgithub.com/Luegg/angularjs-scroll-glue/master/src/scrollglue.js)下载[的卷轴胶水](https://github.com/Luegg/angularjs-scroll-glue)，并将其转储到`public/lib`文件夹中。接下来，更新`public/js/app.js`模块依赖关系，如下所示。

```
var ngAuth = angular.module('ng-auth', ['ngRoute', 'ngResource', 'firebase', 'luegg.directives']).config(['$routeProvider',
  function($routeProvider) {
    $routeProvider.when('/', {
      templateUrl: 'partials/auth.html',
      controller: 'AuthCtrl'
    });
    $routeProvider.when('/home', {
      templateUrl: 'partials/home.html',
      controller: 'HomeCtrl'
    });
    $routeProvider.when('/chat/:roomid', {
      templateUrl: 'partials/chat.html',
      controller: 'ChatCtrl'
    });
    $routeProvider.otherwise({
      redirectTo: '/'
    });
  }
]);
```

更新`views/index.ejs`以包括`scrollglue.js`:

```
<script type="text/javascript" src="lib/scrollglue.js"></script>
```

在`public/js/controllers.js`中，我们将添加管理聊天的逻辑。将以下代码添加到所有控制器的末尾:

```
.controller('ChatCtrl', function($rootScope, $scope, $firebase, $routeParams) {
  // get room details
  var chatRoom = new Firebase($rootScope.URL + 'chatRooms/' + $routeParams.roomid);
  var roomSync = $firebase(chatRoom);
  $scope.roomInfo = roomSync.$asObject();

  var msgsSync = $firebase(chatRoom.child('chatMessages'));
  $scope.chatMessages = msgsSync.$asArray();

  $scope.sendMessage = function($event) {
    if (!($event.which == 13)) return;
    if ($scope.message.length == 0) return;

    msgsSync.$push({
      postedby: $rootScope.user.name,
      message: $scope.message,
      posteddate: Date.now(),
      userimg: $rootScope.user.img
    });

    $scope.message = '';
  };
});
```

需要注意的事项:
第 3 行——我们创建一个新的对聊天室的 Firebase 引用
第 4 行和第 5 行——我们创建一个 AngularFire 引用，然后将其作为一个对象同步
第 7 行——我们创建一个对聊天室内聊天消息对象的引用
第 8 行——我们同步/获取所有消息
第 14 行——我们将聊天消息和一些附加数据推送到服务器。

保存所有文件并运行应用程序。现在，当您单击 join 时，您应该会看到我们已经创建的新视图。添加新消息，您可以看到聊天窗口更新。如果你想测试聊天，打开浏览器并导航到`http://localhost:3000`。向服务提供商登录，与您已经登录的服务提供商相同或不同，您可以与自己聊天。

简单易行！！

## 分发应用程序

您可以创建本地安装程序并分发应用程序。执行以下命令来构建 OSX 安装程序:

```
$ gulp build-osx
```

或者，使用以下命令创建 Windows installer:

```
$ gulp build-win
```

或者，使用以下命令创建 Linux 安装程序:

```
$ gulp build-linux
```

## 结论

希望您对如何使用 node-webkit 和 Firebase 构建端到端应用程序有了基本的了解。你可以在 [GitHub](https://github.com/arvindr21/vTak) 上找到这篇文章的完整代码。

感谢阅读。欢迎评论。

## 分享这篇文章