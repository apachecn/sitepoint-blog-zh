# 通过人行横道和 PeerJS 进行 Android 视频通话

> 原文：<https://www.sitepoint.com/android-video-calling-with-crosswalk-and-peerjs/>

# 在 Android 中创建一个带有人行横道和 PeerJS 的视频通话应用程序

在本教程中，我将展示如何在 Android 中创建视频通话应用程序。将使用[离子](http://ionicframework.com)、[科尔多瓦](https://cordova.apache.org/)、[人行横道](https://crosswalk-project.org/)和 [PeerJS](http://peerjs.com/) 来实现。

## 我们将建造什么

这里有一些截图是关于我们在本教程中将要构建的内容的。默认页面是登录页面。

![Login Screen](img/5c81e05962063d97abac226b2a252aa0.png)

一旦通话开始，应用程序将显示另一个用户的实时视频。

![App Screenshot](img/5ea96b5a0e8d25386e24be172a8884f4.png)

## 建立

我假设你以前已经使用 Cordova 开发过混合移动应用程序。如果你刚接触科尔多瓦，我建议你先阅读一下[科尔多瓦平台指南](http://cordova.apache.org/docs/en/5.0.0/guide_platforms_index.md.html#Platform%20Guides)。一旦你在你的机器上安装了 Android SDK 和 Cordova，返回这里。

如果你之前没有在项目中使用过 Ionic，用 [npm](https://www.npmjs.org/) 安装。

```
npm install -g ionic
```

创建新的 Ionic 应用程序。

```
ionic start VideoKoler blank
```

## 安装前端资产

如果你的机器上没有安装 [bower](http://bower.io) 的话，安装它。它将用于安装该项目所需的前端包。

```
npm install -g bower
```

接下来，导航到 *VideoKoler* 目录，安装[角度本地存储](#)和 PeerJS。

```
bower install angular-local-storage peerjs --save
```

Angular Localstorage 用于将数据保存到浏览器或 WebView 本地存储中。PeerJS 是一个 JavaScript 库，它使得 WebRTC 的实现更加容易。

## 安装人行过道

默认情况下，Ionic 使用默认 Android 浏览器的 WebView。该浏览器不支持 WebRTC，因此我们使用 [Crosswalk](https://crosswalk-project.org/) 赋予它超能力。Crosswalk 在你的应用程序中安装了一个更新版本的 Chrome，这样你就可以使用默认 Android 浏览器中不常见的 JavaScript APIs。这使得应用程序在其中呈现的 WebView 对于所有设备都是相同的。这意味着，如果你在应用中使用的特定功能没有实现，你不必担心失眠。

您可以使用以下命令列出可用的浏览器。

```
ionic browser list
```

这应该会产生如下所示的输出。

![ionic browser list](img/155120464f4f0ce26e7dfe4cb70e805e.png)

我总是坚持使用不是金丝雀的最新版本。这是因为 Canary 是 Chrome 的前沿版本，还不稳定。然后，您可以使用`ionic browser add`添加版本。

```
ionic browser add crosswalk@12.41.296.5
```

最后，加上 Android 这个平台。

```
ionic platform add android
```

## PeerServer

该应用程序将使用 [PeerServer 云服务](http://peerjs.com/peerserver)。在撰写本文时，它对多达 50 个并发连接是免费的。

![peerserver cloud service](img/b5e2b93fdd73f9ba48e378799f5cbbfc.png)

点击*开发者免费*，这将打开一个允许你注册服务的模式。

![peerserver sign up](img/c71b6ac75893eaf2abb506f53bb2b7ee.png)

创建您的帐户后，您将被重定向到仪表板。点击*创建新的 API 密钥*按钮，生成一个新的密钥。

![create new api key](img/71c950ebdff6728d17569e7c8653e2ff.png)

然后会要求您输入“最大并发对等点”和“每个 IP 的最大并发对等点”，默认值就可以了，如果需要，您可以稍后更新这些值。

![key settings](img/b02ad3686bcc4dd2dbf501dc8d4dbacc.png)

如果您想在自己的服务器上运行 PeerServer，可以遵循以下步骤。请注意，当您运行自己的服务器时，PeerServer 云服务的限制并不适用。

首先，在 *www* 目录外创建一个新文件夹，并将其命名为 *videokoler-server* 。导航到该目录并安装[对等服务器](https://github.com/peers/peerjs-server)和 [Express](http://expressjs.com/) 。

```
npm install peer express --save
```

创建一个 *server.js* 文件，并添加以下内容。

```
var express = require('express');
var express_peer_server = require('peer').ExpressPeerServer;
var peer_options = {
    debug: true
};

var app = express();
var port = 3000;
var server = app.listen(port);

app.use('/peerjs', express_peer_server(server, peer_options));
```

上面的代码从 express server 创建了一个新的`PeerServer`。您可以通过节点运行服务器。

```
node server.js
```

要检查 peerserver 是否正常运行，请在浏览器中访问以下 URL，用域名替换 localhost。

*[http://localhost:3000/peer js](http://localhost:3000/peerjs)*

输出应该类似于:

```
{"name":"PeerJS Server","description":"A server side element to broker connections between PeerJS clients.","website":"http://peerjs.com/"}
```

如果没有服务器可以测试，可以使用 [ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) 将本地主机暴露给互联网。下载 ngrok 并使用以下命令执行它。

```
ngrok http 3000
```

Ngrok 将分配一个 URL，我们稍后可以将它用于对等配置。

## 构建应用程序

你可以在 [Github repo](https://github.com/sitepoint-editors/videokoler) 上找到这个应用使用的所有代码。

导航到 *VideoKoler* 目录并打开*index.html*。在包含 *cordova.js* 文件的`script`标签后添加以下内容。这些是之前安装的 Angular Localstorage 和 PeerJS 库的路径。

```
<script src="lib/angular-local-storage/dist/angular-local-storage.js"></script>
<script src="lib/peerjs/peer.min.js"></script>
```

在链接到 *app.js* 文件的`script`标签后添加我们需要的控制器。有两个控制器，一个用于处理登录，一个用于呼叫。

```
<script src="js/controllers/IndexController.js"></script>
<script src="js/controllers/CallController.js"></script>
```

在`body`标签内，添加`ion-nav-view`指令。这允许根据当前状态显示不同的模板。用下面的内容替换`body`标签的当前内容。

```
<body ng-app="starter">

    <ion-nav-view></ion-nav-view>

</body>
```

在 *www* 内创建一个*模板*文件夹，并添加一个 index.html 文件和 videocall.html*文件*。

*index.html*文件包含应用程序主页的模板。它有一个用于输入用户名的文本字段和一个登录按钮。记下控制器的名称、分配给文本字段的型号名称以及单击登录按钮时调用的函数。这些将在控制器中声明和使用。

```
<ion-header-bar class="bar-stable">
  <h1 class="title">VideoKoler</h1>
</ion-header-bar>
<ion-content class="padding has-header" ng-controller="IndexController">

  <div class="list">
    <label class="item item-input">
      <span class="input-label">Your Username</span>
      <input type="text" ng-model="username">
    </label>
  </div>

  <button class="button button-positive button-block" ng-click="login()">
    Login
  </button>
</ion-content>
```

*videocall.html*包含调用另一个用户的模板。它在布局的顶部有一个视频容器，默认情况下是空的。一旦通话开始，视频将被添加。它显示用户选择的用户名，并询问要呼叫的用户的用户名。

在布局的底部是一个发起呼叫的按钮。就像前面的模板一样，记下赋予`ng-controller`、`ng-model`和`ng-click`属性的值。

```
<ion-header-bar class="bar-stable">
  <h1 class="title">VideoKoler</h1>
</ion-header-bar>
<ion-content class="padding has-header" ng-controller="CallController">

  <div class="card">
    <video id="contact-video" autoplay></video>
  </div>

  <div class="card">
    <div class="item item-text-wrap">
      Your Username: <strong>{{ username }}</strong>
    </div>
  </div>

  <div class="list">
    <label class="item item-input">
      <span class="input-label">Contact Username</span>
      <input type="text" ng-model="contact_username">
    </label>
  </div>

  <button class="button button-positive button-block" ng-click="startCall()">
    Call
  </button>
</ion-content>
```

转到应用程序的 JavaScript 端。打开`js/app.js`并添加 Angular Localstorage 作为依赖。默认情况下会添加`ionic`,因此在其后添加`LocalStorageModule`。

```
angular.module('starter', ['ionic', 'LocalStorageModule'])
```

添加状态配置。对于这个应用程序，有两种状态。*登录默认页面*和进行视频通话的主应用页面*应用*。

使用由`$stateProvider`提供的`state`方法设置状态。该方法接受州名作为第一个参数，接受包含选项的对象作为第二个参数。

对于选项来说，`url`和`templateUrl`是所有需要的。其中`url`是激活状态下访问的 url。`templateUrl`是该状态使用的模板的文件系统路径。

```
.config(function ($stateProvider, $urlRouterProvider) {
  $stateProvider
    .state('login', {
      url: '/login',
      templateUrl: 'templates/index.html'
    })

    .state('app', {
      url: '/app',
      templateUrl: 'templates/videocall.html'
    });

    $urlRouterProvider.otherwise('/login'); //set default page
});
```

接下来，在 *js/controllers* 目录中创建一个 *IndexController.js* 文件，并添加以下内容。

```
(function(){
    angular.module('starter')
    .controller('IndexController', ['localStorageService', '$scope', '$state', IndexController]);

    function IndexController(localStorageService, $scope, $state){

        $scope.login = function(){

            var username = $scope.username;
            localStorageService.set('username', username);
            $state.go('app');

        };

    }

})();
```

破解这个密码。一切都包装在一个立即调用的函数表达式中。

```
(function(){

})()
```

将控制器连接到`starter`模块。这个模块是早先在 *js/app.js* 文件中创建的。控制器使用角度本地存储模块提供的`localStorageService`。除此之外，还有在这个控制器中使用的`$scope`,用于将变量和函数设置和获取到当前作用域中。最后是`$state`，用于导航到不同的状态。

```
 angular.module('starter')
    .controller('IndexController', ['localStorageService', '$scope', '$state', IndexController]);
```

控制器做一件事。这是`login`函数附加到`$scope`的地方。单击登录按钮后，将执行该功能。

它获取`$scope`中的`username`变量的当前值，将其保存在 localstorage 中，并重定向到主应用程序页面。

```
function IndexController(localStorageService, $scope, $state){

    $scope.login = function(){

        var username = $scope.username;
        if(username){
            localStorageService.set('username', username);
            $state.go('app');
        }

    };

}
```

仍然在 *js/controllers* 目录中，创建一个 *CallController.js* 文件并添加以下内容。

```
(function(){
    angular.module('starter')
    .controller('CallController', ['localStorageService', '$scope', '$ionicPopup', CallController]);

    function CallController(localStorageService, $scope, $ionicPopup){

            $scope.username = localStorageService.get('username');

            var peer = new Peer($scope.username, {
              key: 'your peerserver cloud key',
              config: {'iceServers': [
                { url: 'stun:stun1.l.google.com:19302' },
                { url: 'turn:numb.viagenie.ca', credential: 'muazkh', username: 'webrtc@live.com' }
              ]}
            });

            /* if you run your own peerserver
            var peer = new Peer($scope.username, {
              host: 'your-peerjs-server.com', port: 3000, path: '/peerjs',
              config: {'iceServers': [
                { url: 'stun:stun1.l.google.com:19302' },
                { url: 'turn:numb.viagenie.ca', credential: 'muazkh', username: 'webrtc@live.com' }
              ]}
            });
          */

            function getVideo(successCallback, errorCallback){
                navigator.webkitGetUserMedia({audio: true, video: true}, successCallback, errorCallback);
            }

            function onReceiveCall(call){

                $ionicPopup.alert({
                    title: 'Incoming Call',
                    template: 'Someone is calling you. Connecting now..'
                });

                getVideo(
                    function(MediaStream){
                        call.answer(MediaStream);
                    },
                    function(err){
                        $ionicPopup.alert({
                            title: 'Error',
                            template: 'An error occurred while try to connect to the device mic and camera'
                        });
                    }
                );

                call.on('stream', onReceiveStream);
            }

            function onReceiveStream(stream){
                var video = document.getElementById('contact-video');
                video.src = window.URL.createObjectURL(stream);
                video.onloadedmetadata = function(){
                    $ionicPopup.alert({
                        title: 'Call Ongoing',
                        template: 'Call has started. You can speak now'
                    });
                };

            }

            $scope.startCall = function(){
                var contact_username = $scope.contact_username;

                getVideo(
                    function(MediaStream){

                        var call = peer.call(contact_username, MediaStream);
                        call.on('stream', onReceiveStream);
                    },
                    function(err){
                        $ionicPopup.alert({
                            title: 'Error',
                            template: 'An error occurred while try to connect to the device mic and camera'
                        });
                    }
                );

            };

            peer.on('call', onReceiveCall);

    }

})();
```

破解密码。我们首先从本地存储中检索用户名。

```
$scope.username = localStorageService.get('username');
```

创建一个新的对等体，使用用户名作为对等体 ID。然后提供 peerserver 云键作为`key`选项的值。(您可以在 [PeerJS 仪表盘](http://peerjs.com/dash)上找到您的钥匙。)

接下来，使用`config`选项添加 ICE(交互式连接建立)服务器配置。这一点很重要，因为 peerserver cloud 只充当信令服务器。这意味着它仅用于对等体之间的数据交换。ICE 服务器需要穿越 NAT(网络)和防火墙。你可以在这里找到免费的 ICE 服务器列表[。](https://gist.github.com/zziuni/3741933)

```
var peer = new Peer($scope.username, {
    key: 'your peerserver cloud key',
    config: {'iceServers': [
        { url: 'stun:stun1.l.google.com:19302' },
        { url: 'turn:numb.viagenie.ca', credential: 'muazkh', username: 'webrtc@live.com' }
    ]}
});
```

如果你使用自己的服务器，你必须提供`host`、`port`和`path`，而不是`key`。然后，您可以对`config`使用相同的值。

```
var peer = new Peer($scope.username, {
        host: 'your-peerjs-server.com', port: 3000, path: '/peerjs',
        config: {'iceServers': [
            { url: 'stun:stun1.l.google.com:19302' },
            { url: 'turn:numb.viagenie.ca', credential: 'muazkh', username: 'webrtc@live.com' }
        ]}
    }
);
```

创建从浏览器请求麦克风和摄像头的功能。这使用了[媒体捕获 API](https://www.w3.org/TR/media-capture-api/) 。使用`navigator.webkitGetUserMedia`可以在 Chrome 中使用。它接受一个包含约束的对象作为它的第一个参数。在下面的例子中，`audio`和`video`都被设置为`true`。这告诉浏览器请求访问麦克风和摄像头。第二个和第三个参数是成功和错误回调。一旦函数被调用，它们就会被传递给函数。

```
function getVideo(successCallback, errorCallback){
    navigator.webkitGetUserMedia({audio: true, video: true}, successCallback, errorCallback);
}
```

创建当收到另一个对等方的呼叫时要执行的函数。包含当前调用的对象作为参数传递。在该功能中，它通过使用离子警报来通知用户收到了呼叫。然后继续调用`getVideo`函数。当成功回调被执行时，它返回一个`MediaStream`,然后可以用它来回答调用。如果执行了错误回调，它会通知用户存在错误。最后，收听电话中的`stream`事件。当这个事件发生时，调用`onReceiveStream`函数。

```
function onReceiveCall(call){

    $ionicPopup.alert({
        title: 'Incoming Call',
        template: 'Someone is calling you. Connecting now..'
    });

    getVideo(
        function(MediaStream){
            call.answer(MediaStream);
        },
        function(err){
            $ionicPopup.alert({
                title: 'Error',
                template: 'An error occurred while try to connect to the device mic and camera'
            });
        }
    );

    call.on('stream', onReceiveStream);
}
```

当从对方接收到媒体流时，执行`onReceiveStream`功能。然后，该流可以用作视频元素的源。加载完所有元数据后，通知用户呼叫已经开始。

```
function onReceiveStream(stream){
    var video = document.getElementById('contact-video');
    video.src = window.URL.createObjectURL(stream);
    video.onloadedmetadata = function(){
        $ionicPopup.alert({
            title: 'Call Ongoing',
            template: 'Call has started. You can speak now'
        });
    };

}
```

声明用于发起调用的函数。这将获取`$scope`中`contact_username`的当前值。这是其他用户登录时使用的用户名。然后执行`getVideo`功能，它返回媒体流，该媒体流用于向另一个对等体发起呼叫。这是通过对等体中的`call`方法完成的。它接受对方的用户名作为第一个参数，媒体流作为第二个参数。然后它返回一个对象，您可以用它来监听`stream`事件。就像在`onReceiveCall`函数上一样，一旦这个事件发生，就会调用`onReceiveStream`。这允许呼叫的发起者从另一个对等体接收流。

```
$scope.startCall = function(){
    var contact_username = $scope.contact_username;

    getVideo(
        function(MediaStream){

            var call = peer.call(contact_username, MediaStream);
            call.on('stream', onReceiveStream);
        },
        function(err){
            $ionicPopup.alert({
                title: 'Error',
                template: 'An error occurred while try to connect to the device mic and camera'
            });
        }
    );

};
```

## 部署

现在我们准备在 Android 设备上部署应用程序。导航到*平台/android* 目录，打开 *AndroidManifest.xml* 文件。如果下面的标签不存在，添加它们作为`manifest`标签的子标签。

```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

执行以下命令来构建应用程序。

```
ionic build android
```

构建完成后，您可以在*平台/android/build/outputs/apk* 目录中获得 apk 文件。由于使用了 Crosswalk，因此为 ARM 和 x86 处理器生成了单独的 apk。你已经知道 Crosswalk 安装了 Chrome 最新版本的网络视图。这意味着 apk 的大小变得比通常的更大(默认的 Android WebView)。这就是为每个架构生成单独的 apk 的原因。因为如果把它合并成一个在两个处理器上都工作的 apk 文件，它会更大。根据您的设备，您可以选择以下任一文件。
–Android-arm V7-debug . apk
–Android-x86-debug . apk

## 结论

就是这样！在本教程中，您已经学习了如何使用 Ionic、Crosswalk 和 PeerJS 为 Android 构建视频通话应用程序。如果你有任何问题，评论或问题，请在下面的评论中告诉我。

## 分享这篇文章