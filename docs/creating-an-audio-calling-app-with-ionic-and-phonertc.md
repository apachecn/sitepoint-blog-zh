# 创建一个带有离子和语音的音频通话应用程序

> 原文：<https://www.sitepoint.com/creating-an-audio-calling-app-with-ionic-and-phonertc/>

在本教程中，我将使用 [Ionic](http://ionicframework.com/) 创建一个音频通话应用。我们将使用 [PhoneRTC](http://phonertc.io/) 来实现它，这是一个 Cordova 插件，允许你在 Android 和 iOS 设备上使用 WebRTC。在本教程中，我将只展示在 Android 平台上部署，因为我个人使用 Linux。只有部署应用程序会有所不同。

## 建立

我将假设您已经使用 Cordova 开发了混合移动应用程序。如果没有，建议你看一下 [Cordova 平台指南](http://cordova.apache.org/docs/en/5.0.0/guide_platforms_index.md.html#Platform%20Guides)。它提供了您需要的所有信息，帮助您设置如何使用 Cordova。

一旦你为你的平台设置好 SDK。你现在可以安装离子和科尔多瓦。为此，请在终端中执行以下命令:

```
npm install -g cordova ionic
```

上面的命令全局安装 Cordova 和 Ionic，这样这些工具可以在任何终端窗口使用。

我们将使用 [bower](http://bower.io/) 来安装前端依赖项，因此如果您没有安装它，请运行以下命令:

```
npm install -g bower
```

完成后，创建一个新的 Ionic 项目。

```
ionic start koler blank
```

分解上面的命令，`ionic`是命令行工具，`start`是启动新项目的命令，`koler`是应用程序的名称，`blank`是 Ionic 提供的启动项目模板之一。`blank`模板让事情变得简单。

以下是该项目的前端依赖项:

*   **[random . js](https://www.npmjs.com/package/random-js)**——为每个使用应用的用户生成一个随机 ID。
*   **[socket . io](http://socket.io/)**–在对等体之间传递网络信息。
*   **[angular socket . io](https://github.com/btford/angular-socket-io)**–socket . io 的 angular.js 包装器

下面是安装上述程序的命令。

```
cd koler
bower install random socket.io angular-socket-io
```

## 构建应用程序

你可以找到我们将在 [Github](https://github.com/sitepoint-editors/koler) 上构建的完整应用的代码。

打开项目的 *www* 目录下的*index.html*文件，在`script`标签前添加如下链接 *cordova.js* 文件。这允许我们使用刚刚安装的库。

```
<script src="lib/sio-client/socket.io.js"></script>
<script src="lib/angular-socket-io/socket.js"></script>

<script src="lib/random/lib/random.min.js"></script>
```

在 *www* 目录下创建一个*模板*文件夹。这是我们存储视图使用的 HTML 模板的地方。第一个观点是 call.html 的*。将以下代码添加到该文件中。*

```
<ion-header-bar class="bar-stable">
  <h1 class="title">Koler</h1>
</ion-header-bar>
<ion-content class="padding" ng-controller="CallController">

  <div class="card">
    <div class="item item-text-wrap">
      Your User ID: <strong>{{ id }}</strong>
    </div>
  </div>

  <div class="list">
    <label class="item item-input">
      <span class="input-label">Peer ID</span>
      <input type="text" ng-model="peer_id">
    </label>
  </div>

  <button class="button button-positive button-block" ng-click="startCall()">
    Call
  </button>
</ion-content>
```

分解这个。标题区域是通过使用`ion-header-bar`指令创建的。这个角度指示内置于 Ionic 中，专门用于在主要内容上方添加一个固定的标题栏。

```
<ion-header-bar class="bar-stable">
  <h1 class="title">Koler</h1>
</ion-header-bar>
```

接下来是主要内容的包装。`ng-controller`属性用于指定该视图使用哪个控制器。

```
<ion-content class="padding" ng-controller="CallController">
</ion-content>
```

在主要内容里面是一个卡片，用于显示当前用户的随机 ID。

```
<div class="card">
  <div class="item item-text-wrap">
    Your User ID: <strong>{{ id }}</strong>
  </div>
</div>
```

下面是用特定 ID 呼叫用户的表单。注意文本字段的`ng-model`属性的值。您可以通过使用控制器的名称从控制器获取该字段的当前值。这种情况下是`peer_id`。文本字段下方是发起呼叫的按钮。点击按钮时会调用`startCall`函数。

```
<div class="list">
  <label class="item item-input">
    <span class="input-label">Peer ID</span>
    <input type="text" ng-model="peer_id">
  </label>
</div>

<button class="button button-positive button-block" ng-click="startCall()">
  Call
</button>
```

现在我们可以开始编码`CallController`。在 *js* 目录下，创建一个*控制器*文件夹，这是控制器的存放位置。在里面创建一个 *CallController.js* 文件，并添加以下代码:

```
(function(){
    angular.module('starter')
    .controller('CallController', ['$scope', '$state', '$timeout', '$ionicModal', 'SocketService', CallController]);

    function CallController($scope, $state, $timeout, $ionicModal, SocketService){
        ...
    }

})();
```

上面的代码为启动模块创建了一个新的控制器。然后，我们将以下内容注入其中:

*   `$scope`–进入当前范围。
*   `$state`–进入当前状态。
*   `$timeout`–用于在指定的毫秒数后执行功能。
*   `$ionicModal`–启用控制器内部的离子模态组件。
*   `SocketService`–使用 Socket.io 的服务

在控制器内部，生成一个介于 10，000 和 99，999 之间的随机整数，并将其指定为当前用户的 ID。将它存储在`$scope`变量中，这样就可以从视图中访问它。

```
var r = new Random();

var id = r.integer(10000, 99999);
$scope.id = id;
```

创建一个用于存储当前联系人的对象。这是当前用户正在尝试呼叫的用户。

```
$scope.contact = {};
```

创建用于存储当前呼叫状态的变量。默认情况下，呼叫不应该正在进行、被忽略或结束，因此这些都被设置为`false`。

```
$scope.callInProgress = false;
$scope.callIgnored = false;
$scope.callEnded = false;
```

向服务器发送一条登录消息以及之前生成的随机 ID。这允许服务器为用户 ID 分配一个套接字，以便以后使用。

```
SocketService.emit('login', {'id': id});
```

创建呼叫模式。这将在呼叫开始、进行中、结束或忽略时显示给用户。

```
$ionicModal.fromTemplateUrl('templates/call-modal.html', {
    scope: $scope,
    animation: 'slide-in-up'
}).then(function(modal){
    $scope.call_modal = modal;
});
```

创建用于调用的函数。当用户发起或应答呼叫时，将调用该函数。它接受两个参数，`isInitiator`是一个布尔值，允许 phoneRTC 确定发起调用的用户。和用于指定当前用户正在连接的用户的 ID 的`peer_id`。在本教程的剩余部分，我将把当前用户或正在呼叫的用户称为*发起者*，把对等用户或正在接受呼叫的用户称为*加入者*。

```
function call(isInitiator, peer_id){

  var config = {
    isInitiator: isInitiator,
      stun: {
        host: 'stun:stun.l.google.com:19302'
      },
      turn: {
        host: 'turn:numb.viagenie.ca',
        username: 'webrtc@live.com',
        password: 'muazkh'
      },
      streams: {
        audio: true,
        video: false
      }
  };

  var session = new cordova.plugins.phonertc.Session(config);

  session.on('sendMessage', function(data){

    SocketService.emit('sendMessage', {
      'id': id,
      'peer_id': $scope.peer_id,
      'type': 'phonertc_handshake',
      'data': JSON.stringify(data)
    });
});

  session.on('disconnect', function(){
    SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'ignore' });
    $scope.call_modal.hide();
  });

  session.call();
  $scope.contact = session;  
}
```

分解上面的代码。首先是 phoneRTC *STUN* 和 *TURN* 服务器的配置。这允许在任何设备位于防火墙或 NAT 之后的情况下建立对等连接。你可以使用我用过的相同的眩晕和转身服务器配置，或者从[的眩晕服务器列表](https://gist.github.com/zziuni/3741933)中挑选。你可以设置你自己的服务器，但是我不会在本教程中讨论。

最后，是`streams`，这允许你设置你是否只捕捉设备音频，摄像头，或者两者都捕捉。然后，这个配置作为参数传递给 phoneRTC 会话。该会话将代表两个对等体之间的连接。

```
var config = {
  isInitiator: isInitiator,
  stun: {
    host: 'stun:stun.l.google.com:19302'
  },
  turn: {
    host: 'turn:numb.viagenie.ca',
    username: 'webrtc@live.com',
    password: 'muazkh'
  },
  streams: {
    audio: true,
    video: false
  }
};

var session = new cordova.plugins.phonertc.Session(config);
```

创建在当前会话中发送消息时的事件处理程序。当用户通过 Socket.io 发送消息时，该事件被触发。当该事件被触发时，您必须使用`phonertc_handshake`消息进行响应，并传入在该消息上传递的相同数据。使用`JSON.stringify`将数据转换成 JSON 字符串。传递其他数据，如对等 ID 和当前用户的 ID，以便信令服务器知道消息来自哪里以及它将去往哪里。

为什么需要传递相同的数据？这就是 WebRTC 的工作方式。应该在每个对等体之间交换和验证网络信息，以便建立连接。如果你想深入这个话题，我推荐你阅读 HTML5Rocks 网站上的[开始使用 WebRTC 文章。](http://www.html5rocks.com/en/tutorials/webrtc/basics/)

```
session.on('sendMessage', function(data){

  SocketService.emit('sendMessage', {
    'id': id,
    'peer_id': $scope.peer_id,
    'type': 'phonertc_handshake',
    'data': JSON.stringify(data)
  });
});
```

当当前会话断开时，向另一方发送一条消息，说明该呼叫被忽略，然后隐藏呼叫模式。

```
session.on('disconnect', function(){
  SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'ignore' });
  $scope.call_modal.hide();
});
```

最后，您进行一次调用，并将当前会话设置为当前作用域中的 contact 变量。这允许您稍后接收消息、断开或关闭对等连接。

```
session.call();
$scope.contact = session;
```

要发起呼叫，首先将`isCalling`变量设置为`true`。这将控制调用模式中显示的内容。接下来，通过发送包含当前用户 ID、对方 ID 和消息类型的消息，通知对方您正在尝试呼叫他们。最后，展示调用模式。在 call.html 视图的*中点击“调用”按钮时，调用以下函数。*

将此代码添加到控制器文件中:

```
$scope.startCall = function(){

  $scope.isCalling = true;
  $scope.callIgnored = false;
  $scope.callEnded = false;

  SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, type: 'call'});

  $scope.call_modal.show();  
}
```

调用模式包含以下内容。将此代码添加到新的*templates/call-modal . html*文件中:

```
<ion-modal-view>
  <div class="bar bar-header item-input-inset bar-calm">
    <button class="button button-icon icon ion-ios-arrow-left" ng-click="closeModal()"></button>
    Call {{ peer_id }}
  </div>

  <ion-content class="padding has-header">

    <div class="calling-container" ng-if="isCalling &amp;&amp; !callInProgress &amp;&amp; !callIgnored &amp;&amp; !callEnded">
      <p>Calling to <span class="balanced">{{ peer_id }}</span>...</p>

      <button class="button button-assertive" ng-click="ignore()">
        Nevermind
      </button>
    </div>

    <div class="calling-container" ng-if="!isCalling &amp;&amp; !callInProgress &amp;&amp; !callIgnored &amp;&amp; !callEnded">
      <p><span class="balanced">{{ peer_id }}</span> is calling you</p>

      <button class="button button-positive" ng-click="answer()">
        Answer
      </button>

      <button class="button button-assertive" ng-click="ignore()">
        Ignore
      </button>
    </div>

    <div class="calling-container" ng-if="callInProgress &amp;&amp; !callIgnored &amp;&amp; !callEnded">
      <p>Call in progress...</p>

      <button class="button button-assertive" ng-click="end()">
        End
      </button>
    </div>

    <div ng-if="callIgnored &amp;&amp; !callEnded &amp;&amp; !callInProgress">
      <p>Call Ignored</p>
      <button class="button button-positive button-block" ng-click="closeModal()">Go back</button>
    </div>

    <div ng-if="callEnded &amp;&amp; !callIgnored &amp;&amp; !callInProgress">
      <p>Call Ended</p>
      <button class="button button-positive button-block" ng-click="closeModal()">Go back</button>
    </div>

  </ion-content>
</ion-modal-view>
```

分解上面的代码。标题包含一个允许用户关闭模态的按钮和一个告诉当前用户对等体的 ID 的标题。

```
<div class="bar bar-header item-input-inset bar-calm">
  <button class="button button-icon icon ion-ios-arrow-left" ng-click="closeModal()"></button>
  Call {{ peer_id }}
</div>
```

这是将显示当前用户是否是启动器的消息。它还有一个按钮，如果对方不接电话，就可以取消通话。

```
<div class="calling-container" ng-if="isCalling &amp;&amp; !callInProgress &amp;&amp; !callIgnored &amp;&amp; !callEnded">
  <p>Calling to <span class="balanced">{{ peer_id }}</span>...</p>

  <button class="button button-assertive" ng-click="ignore()">
    Nevermind
  </button>
</div>
```

当加入者应答呼叫时，呼叫被设置为*进行中*，然后将显示以下内容。这允许任一用户结束呼叫。

```
<div class="calling-container" ng-if="callInProgress &amp;&amp; !callIgnored &amp;&amp; !callEnded">
  <p>Call in progress...</p>

  <button class="button button-assertive" ng-click="end()">
    End
  </button>
</div>
```

一旦通话被两个用户中的任何一个结束，通话就被标记为结束。然后，用户可以选择关闭调用模式。

```
<div ng-if="callIgnored &amp;&amp; !callEnded &amp;&amp; !callInProgress">
  <p>Call Ignored</p>
  <button class="button button-positive button-block" ng-click="closeModal()">Go back</button>
</div>

<div ng-if="callEnded &amp;&amp; !callIgnored &amp;&amp; !callInProgress">
  <p>Call Ended</p>
  <button class="button button-positive button-block" ng-click="closeModal()">Go back</button>
</div>
```

回到 *CallController.js* 文件，这里是关闭模态的代码。

```
$scope.closeModal = function(){
  $scope.call_modal.hide();
};
```

当一个调用被发起者忽略时，到加入者的连接被断开。如果加入者忽略了这个调用，那么你必须给发起者发送一个消息，通知他们这个调用被忽略了。

```
$scope.ignore = function(){

  if(JSON.stringify($scope.contact) === '{}'){
    $scope.contact.disconnect();
  }else{
    SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'ignore' });
    $scope.call_modal.hide();
  }

};
```

当任一用户结束呼叫时，关闭与对方的连接，并向另一用户发送消息，以便通知他们呼叫已经结束。更新范围变量，以便 UI 也将被更新。

```
$scope.end = function(){
  $scope.contact.close();
  $scope.contact = {};

  SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'end' });
  $scope.callInProgress = false;
  $scope.callEnded = true;
  $scope.call_modal.hide();
};
```

当对等方应答该调用时，将调用设置为正在进行的中的*，并通过调用`call`方法连接到发起方。因为应答呼叫的用户总是加入者，所以您必须为`isInitiator`参数传递`false`。接下来，向发起者发送一条消息，告知加入者已经应答了呼叫。在发送消息之前，您必须给出至少 1.5 秒的填充时间，以便在两个对等体之间建立连接。因为一旦发起方收到应答消息，UI 会立即更新。如果连接还没有建立，我们不希望发起者开始讲话。*

```
$scope.answer = function(){

  if($scope.callInProgress){
    return;
  }

  $scope.callInProgress = true;

  call(false, $scope.peer_id);

  setTimeout(function(){
    SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'answer' });
  }, 1500);
};
```

接下来，创建一个函数，当任何一个用户收到消息时，该函数将处理前端的事情。switch 语句用于确定收到的消息类型。然后，每个案例将相应地处理消息类型。

```
function onMessageReceive(message){

  switch(message.type){

    case 'answer':

      $scope.$apply(function(){
        $scope.callInProgress = true;
      });

      call(true, message.id);
    break;

    case 'ignore':
      $scope.callInProgress = false;
      $scope.callIgnored = true;
      $scope.callEnded = false;
    break;

    case 'phonertc_handshake':
      $scope.contact.receiveMessage(JSON.parse(message.data));
    break;

    case 'call':
      $scope.isCalling = false;
      $scope.callIgnored = false;
      $scope.callEnded = false;

      $scope.call_modal.show();

      $scope.peer_id = message.id;

      $scope.current_modal = 'call_modal';
    break;

    case 'end':
      $scope.callInProgress = false;
      $scope.callEnded = true;
      $scope.callIgnored = false;
    break;

  }
}
```

`answer`消息只能由发起方接收。如前所述，这个消息是在调用了`call`方法之后发送的，该方法试图在加入者和发起者之间建立连接。它做的第一件事是设置对正在进行的*的调用*，以便更新 UI。然后通过调用`call`方法来建立到加入者的连接。`message.id`包含加入者的 ID。

```
case 'answer':

  $scope.$apply(function(){
    $scope.callInProgress = true;
  });

  call(true, message.id);
break;
```

在`ignore`上，将作用域中的`callIgnored`变量设置为`true`，以便更新 UI。

```
case 'ignore':

  $scope.callInProgress = false;
  $scope.callIgnored = true;
  $scope.callEnded = false;

break;
```

接下来是`phonertc_handshake`。每次调用`call`方法时都会发送该消息。它的唯一目的是接收对等体传递的网络信息。这对于在对等体之间建立连接非常重要。

```
case 'phonertc_handshake':

  $scope.contact.receiveMessage(JSON.parse(message.data));

break;
```

当收到`call`消息时，将控制 UI 的作用域变量重置为`false`，显示调用模式，然后将对等 ID 设置为作用域变量。对等 ID 是加入者的 ID。所以这个特定的消息只能被加入者接收到。

```
case 'call':
  $scope.isCalling = false;
  $scope.callIgnored = false;
  $scope.callEnded = false;

  $scope.call_modal.show();
  $scope.current_modal = 'call_modal';

  $scope.peer_id = message.id;
break;
```

一旦接收到呼叫`end`消息，通过将`callEnded`设置为`true`来更新 UI。

```
case 'end':
  $scope.callInProgress = false;
  $scope.callEnded = true;
  $scope.callIgnored = false;
break;
```

每次收到一条消息就触发`onMessageReceive`方法，将这段代码添加到最后一个函数的下面。

```
SocketService.on('messageReceived', onMessageReceive);
```

最后，一旦当前作用域被破坏，侦听器就会被删除。当用户导航到新页面或关闭应用程序时，就会发生这种情况。

```
$scope.$on('$destroy', function(){
  SocketService.removeListener('messageReceived', onMessageReceive);
});
```

在 *js* 目录下创建一个 *services* 文件夹，创建一个 *SocketService.js* 文件，添加以下代码。

```
(function(){

  angular.module('starter')
    .service('SocketService', ['socketFactory', SocketService]);

    function SocketService(socketFactory){
      return socketFactory({
        ioSocket: io.connect('http://yourserver.com:4000')
      });
    }
})();
```

上面的代码用于连接 Socket.io 服务器。这将初始化 Socket.io，以便它可以作为 Angular 内部的服务使用。这使用了之前安装的 *angular-socket-io* 库。

在本教程的下一部分，我们将研究如何创建管理用户间呼叫的服务器。如果您在这一点上有任何问题，请告诉我。* 

## *分享这篇文章*