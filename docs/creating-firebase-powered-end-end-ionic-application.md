# 创建一个基于 Firebase 的端到端离子应用

> 原文：<https://www.sitepoint.com/creating-firebase-powered-end-end-ionic-application/>

自从人类用石头生火以来，科技已经取得了长足的进步。曾经有一段时间，互联网意味着在几台机器上提供超文本文档。但是今天，我们已经达到了一个复杂的状态，你的心率被一个设备监测，然后传输到你的电脑上。如果心率不正常，你甚至可能会看到一辆救护车在你冲刺的终点等着。

这就是我们现在的生活方式。为了实现这些惊人的功能，我们需要惊人的技术。在本帖中，我们将讨论两种这样的前沿技术，Ionic Framework 和 Firebase。

### 什么是离子框架？

Ionic 是一个强大的 AngularJS 驱动的移动 web 框架，使构建混合移动应用程序变得容易。它不仅拥有双向数据绑定的能力，还拥有一个非常棒的接口来处理 RESTful APIs。这使得 Ionic 非常适合开发应用程序，并使它们在不同设备之间保持同步。

### 什么是 Firebase？

有时，配置一台机器来部署一个简单的网站需要几周时间。然后，亚马逊出现了。你只要告诉亚马逊你想要什么样的系统，它就给你提供一个服务器。接下来，我们看到了 Heroku 的崛起，它提供 PaaS(平台即服务)来托管您的应用程序。这使得开发人员可以更专注于应用程序，而不是担心应用程序的供应和部署。最后，我们有 Firebase，它是一个由 NoSQL 数据存储驱动的自给自足的“服务器即服务”。在 Firebase 中，您需要做的只是定义一个数据集合，Firebase 会将它作为 RESTful API 提供给您。

### Bucketlist 应用程序

我写过一篇名为[Ionic Restify MongoDB——端到端混合应用](http://thejackalofjavascript.com/an-end-to-end-hybrid-app/)的文章，解释了如何使用 Restify 和 MongoDB 作为 API 服务器，Ionic 作为混合客户端来构建端到端混合应用。在这篇文章中，我们将看到如何用 Firebase 完全消除 API 服务器层。

我们将要构建的 Bucketlist 应用程序将有一个身份验证层，允许用户注册和登录。通过身份验证后，用户可以选择创建新的 bucketlist 条目。

应用程序的主视图显示未完成项目的列表，辅助视图显示已完成项目的列表。用户可以选择将项目标记为完成或删除它。

在我们开始构建应用程序之前，您应该:

*   看看这个应用程序的真实实例。
*   下载[原生安装程序](https://build.phonegap.com/apps/943461/share)。
*   通过派生 [repo](https://github.com/jsprodotcom/IonicFirePGInstaller) 将完成的应用程序提交给 PhoneGap Build。
*   看看我们将要构建的[完整代码](https://github.com/jsprodotcom/myIonicFireApp)。

### 应用架构

我们的应用程序将主要由两层组成。第一个是客户端(*在我们的例子中是 Ionic App，但这可以是任何其他可以使用 RESTful API* 的客户端)，第二个是服务器( *Firebase* )。

![Application Architecture](img/0b3e47624e3706b900377778f1443c83.png)

从上图中可以看出，在客户端，我们有一个 Angularfire 层，它与 Firebase 交互，充当 Ionic 应用程序的服务层。正是这一层提供了在 Firebase 和我们的 Ionic 客户端之间保持数据同步的能力。

在 Firebase 端，我们将配置一个简单的登录来处理身份验证。

我们的 Ionic 应用将有五个关键控制器:

1.  注册控制器
2.  登录控制器
3.  创建新的项目控制器
4.  显示不完整项目控制器
5.  显示已完成项目控制器

除此之外，我们将有几个方法来将一个项目标记为完成并删除它。

### 设计数据结构

Firebase 非常适合用于实时数据同步，全球的多个客户端几乎可以在同一时刻看到相同的数据。我们的 app 就不是这样的。我们真的不是在寻找一个多设备同步。我们所需要的是让 Firebase 为我们管理 bucketlist 数据。

Firebase 的惊人之处在于它提供了开箱即用的身份验证 API。我们需要做的就是启用它并包含客户端，Firebase 会为我们处理剩下的事情。

对于 bucketlist 集合，我们需要用户和 bucketlist 条目之间的关系，有点像外键。这将使我们能够显示仅由用户创建的 bucketlist 项目。

下面显示了一个小块列表集合示例:

```
"BucketListCollection":
[{
  "item": "test",
  "isCompleted": false,
  "user": "test@bla.com",
  "created": 1400801853144,
  "updated": 1400801853144
}, {
  "item": "tes message",
  "isCompleted": false,
  "user": "test@bla.com",
  "created": 1401008504927,
  "updated": 1401008504927
}, {
  "item": "Just to check",
  "isCompleted": true,
  "user": "test@bla.com",
  "created": 1401008534451,
  "updated": 1401008534451
}, ....]
```

在上面的 JSON 示例中，`user`键保存登录用户和他们的项目之间的链接。因此，当我们获取数据时，我们获取匹配登录用户的记录。这就是我们使用 RESTful 端点表示查询的方式:

```
https://bucketlist-app.firebaseio.com/bucketList/test@bla.com
```

不幸的是，在 Firebase 中没有简单的方法来实现这一点。

根据[这个栈溢出 post](http://stackoverflow.com/a/11588115/1015046) ，有三种方式:

*   明智地使用位置名称和优先级。
*   做客户端查询。
*   运行单独的服务器。

对于一个简单的 API 来说，这些方法有点矫枉过正。然后，我偶然发现了[这篇栈溢出文章](http://stackoverflow.com/a/14965065/1015046)，它提到了如何翻转数据结构，使其更以用户为中心，而不是以功能为中心。所以我改了 app 数据结构如下图。

```
"test@bla,com" : [{
  "item": "test",
  "isCompleted": false,
  "created": 1400801853144,
  "updated": 1400801853144
}, {
  "item": "tes message",
  "isCompleted": false,
  "created": 1401008504927,
  "updated": 1401008504927
}....]

"test2@bla,com" : [{
  "item": "test2",
  "isCompleted": false,
  "created": 14008012853144,
  "updated": 14008012853144
}, {
  "item": "tes message2",
  "isCompleted": false,
  "created": 14010028504927,
  "updated": 14010028504927
}....]
```

现在，每个用户都有自己的集合，而不是公共的 bucketlist 集合，这在我们的应用程序中更有意义。因此，我们将使用这种结构来管理我们的数据。我们的网址看起来会像这样:

```
https://bucketlist-app.firebaseio.com/test@bla,com
```

注意:我不能 100%确定大型用户群是否会影响单个查询的整体响应时间(*更多用户=更多集合*)。

### 设置 Firebase

对于我们要去哪里，我们有一个好主意。我们的第一步是设置一个 Firebase 帐户，创建一个新的 Firebase 应用程序实例，并为其配置身份验证。

导航至[Firebase.com](https://www.firebase.com/signup/)并创建一个新账户(如果您没有)。接下来，导航到[账户页面](https://www.firebase.com/account/#/)并创建一个新的应用程序。提供所需的名称和 URL。创建应用程序后，单击应用程序名称导航至数据和配置页面。这是后端的鸟瞰图。继续之前，请随意浏览。

接下来，我们将为我们的应用程序设置身份验证。单击页面左侧的简单登录选项卡，在主要内容区域您将看到可用的选项。在*认证提供者*部分，点击*电子邮件和密码*，然后*勾选*启用*复选框*。这将为我们设置简单的登录。

### 建立一个离子项目

接下来，我们将使用 Ionic 命令行界面(CLI)从空白模板构建一个新的 Ionic 应用程序。创建一个名为`myIonicFireApp`的新文件夹，在这里打开终端/提示符。首先，我们将安装科尔多瓦和离子。执行以下命令:

```
$ npm i -g cordova ionic
```

接下来，我们将搭建一个新的 Ionic 应用程序。一般来说，我喜欢让我的代码有条理。由于这是一个测试应用，我们不打算使用任何版本控制来管理开发和生产，我们将创建两个文件夹，`myIonicFireApp/dev`和`myIonicFireApp/prod`。这一步是可选的，完全是首选。接下来，`cd`进入`dev`文件夹(如果你已经创建了一个)并运行以下命令:

```
$ ionic start bucketListApp blank
```

`bucketListApp`是应用程序的名称。这将为我们搭建 Ionic + PhoneGap 模板。一旦设置完成，第一步就是将 *config.xml* 从`bucketListApp`文件夹移动到`www`文件夹(*PhoneGap 构建需求*)。

接下来，在您最喜欢的编辑器中打开 *config.xml* ,更新小部件 ID、名称、描述和作者字段。这些将是你的应用程序的元数据，当它通过 Phonegap Build 运行时。更新后的文件如下所示:

```
<?xml version='1.0' encoding='utf-8'?>
<widget id="com.ionicfire.bucketlist" version="0.0.1"  xmlns:cdv="http://cordova.apache.org/ns/1.0">
  <name>BucketList App</name>
  <description>An Awesome App</description>
  <author email="hi@bucketlist.com" href="http://bucketlist.com/">Arvind Ravulavaru</author>
  <content src="index.html" />
  <access origin="*" />
  <preference name="fullscreen" value="true" />
  <preference name="webviewbounce" value="false" />
  <preference name="UIWebViewBounce" value="false" />
  <preference name="DisallowOverscroll" value="true" />
  <!-- Don't store local date in an iCloud backup. Turn this to "cloud" to enable storage
         to be sent to iCloud. Note: enabling this could result in Apple rejecting your app.
  -->
  <preference name="BackupWebStorage" value="none" />
  <feature name="StatusBar">
    <param name="ios-package" value="CDVStatusBar" onload="true" />
  </feature>
</widget>
```

请参考 [PhoneGap 3 CLI 在 Mac 和 Windows 上的设置](http://thejackalofjavascript.com/phonegap-3-cli-setup-mac-windows/)以全面了解和设置 PhoneGap 在 Windows 和 Mac 上的设置。

要添加 iOS 平台支持(仅限 Mac)，请运行以下命令:

```
$ ionic platform add ios
```

要添加 Android 平台支持，请运行以下命令:

```
$ ionic platform add android
```

接下来，我们将运行以下命令来构建应用程序:

```
$ ionic platform build ios
```

或者

```
$ ionic platform build ios
```

接下来，要模拟应用程序，请执行:

```
$ ionic emulate ios
```

或者

```
$ ionic emulate android
```

您可以使用上述方法来测试您的代码。但是，每次修改`www`文件夹中的代码时，您都需要为各自的平台构建代码。

鉴于我的懒惰，我绝不会那样做。Ionic 项目获得了大量支持。让我们利用这一点。回到终端，执行以下命令:

```
$ npm install
```

这将安装 *package.json* 中列出的所有依赖项。接下来，使用命令安装*大口连接*:

```
$ npm install gulp-connect --save
```

然后，打开`bucketListApp`文件夹根目录下的 *gulfile.js* ，用下面的代码替换它:

```
var gulp = require('gulp');
var gutil = require('gulp-util');
var bower = require('bower');
var concat = require('gulp-concat');
var sass = require('gulp-sass');
var minifyCss = require('gulp-minify-css');
var rename = require('gulp-rename');
var sh = require('shelljs');
var connect = require('gulp-connect');

var paths = {
  sass: ['./scss/**/*.scss'],
  www : ['www/**/*.*']
};

gulp.task('default', ['sass']);
gulp.task('serve', ['connect', 'watch']);

gulp.task('sass', function(done) {
  gulp.src('./scss/ionic.app.scss')
    .pipe(sass())
    .pipe(gulp.dest('./www/css/'))
    .pipe(minifyCss({
      keepSpecialComments: 0
    }))
    .pipe(rename({ extname: '.min.css' }))
    .pipe(gulp.dest('./www/css/'))
    .on('end', done);
});

gulp.task('reload', function () {
  return gulp.src(['www/index.html'])
    .pipe(connect.reload());
});

gulp.task('watch', function() {
  // Uncomment below line if you wish to work wit SASS
  //gulp.watch(paths.sass, ['sass']);

  gulp.watch([paths.www], ['reload']);
});

gulp.task('install', ['git-check'], function() {
  return bower.commands.install()
    .on('log', function(data) {
      gutil.log('bower', gutil.colors.cyan(data.id), data.message);
    });
});

gulp.task('git-check', function(done) {
  if (!sh.which('git')) {
    console.log(
      '  ' + gutil.colors.red('Git is not installed.'),
      '\n  Git, the version control system, is required to download Ionic.',
      '\n  Download git here:', gutil.colors.cyan('http://git-scm.com/downloads') + '.',
      '\n  Once git is installed, run \'' + gutil.colors.cyan('gulp install') + '\' again.'
    );
    process.exit(1);
  }
  done();
});

gulp.task('connect', function() {
  connect.server({
    root: 'www',
    port: '1881',
    livereload: true
  });
});
```

回到终端，运行:

```
$ gulp serve
```

这将启动服务器。现在，你需要做的就是打开`http://localhost:1881`观察！。

注意 *cordova.js* 在开发过程中将是一个 404。此外，由于我们增加了实时重新加载支持，您需要做的只是进行更改并切换到您的浏览器来查看更改。

*注意:如果你正在构建一个带有本地插件的应用程序，如联系人或照相机，这种方法将不起作用！您需要将应用程序部署到设备上进行测试。*

我们的 Ionic 应用程序设置已经完成。让我们开始构建实际的应用程序。

### 离子型和燃烧型

我们要做的第一件事是打开 *www/index.html* 并添加所需的 Firebase、AngularFire 和 Firebase-simple-log in JavaScript 引用。

```
<script src="https://cdn.firebase.com/v0/firebase.js"></script>
<script src="https://cdn.firebase.com/libs/angularfire/0.5.0/angularfire.min.js"></script>
<script src="https://cdn.firebase.com/v0/firebase-simple-login.js"></script>
```

它们被指向 CDN，但是你也可以下载文件并在本地提供服务。接下来将 body 标签上的`ng-app`指令值从`starter`更新为`bucketList`。这将是我们的模块名称。最后，我们将添加后退按钮支持。将以下代码添加到页面正文中:

```
<ion-nav-bar class="bar-stable nav-title-slide-ios7">
  <ion-nav-back-button class="button-icon icon ion-chevron-left">
    Back
  </ion-nav-back-button>
</ion-nav-bar>
```

完成的 *www/index.html* 将看起来像:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">
    <title></title>
    <link href="lib/ionic/css/ionic.css" rel="stylesheet">
    <link href="css/style.css" rel="stylesheet">
    <!-- IF using Sass (run gulp sass first), then uncomment below and remove the CSS includes above
      <link href="css/ionic.app.css" rel="stylesheet">
    -->
    <!-- ionic/angularjs js -->
    <script src="lib/ionic/js/ionic.bundle.js"></script>
    <script src="https://cdn.firebase.com/v0/firebase.js"></script> <!-- firebase -->
    <script src="https://cdn.firebase.com/libs/angularfire/0.5.0/angularfire.min.js"></script> <!-- angularfire -->
    <script src="https://cdn.firebase.com/v0/firebase-simple-login.js"></script> <!-- firebase-simple-login -->
    <!-- cordova script (this will be a 404 during development) -->
    <script src="cordova.js"></script>
    <!-- your app's js -->
    <script src="js/app.js"></script>
    <script src="js/controllers.js"></script>
  </head>
  <body ng-app="bucketList" animation="slide-left-right-ios7">
    <ion-nav-bar class="bar-stable nav-title-slide-ios7">
      <ion-nav-back-button class="button-icon icon ion-chevron-left">
        Back
      </ion-nav-back-button>
    </ion-nav-bar>
    <ion-nav-view></ion-nav-view>
  </body>
</html>
```

请注意，我们已经添加了对 *controllers.js* 的引用。我们马上会解决这个问题。如果你回到浏览器并检查开发者控制台，你会看到几个 404 和一个`Uncaught object`错误。`Uncaught object`错误是因为，我们已经更新了*index.html*中的`ng-app`指令，但没有更新 *www/js/app.js* 中的指令。你可以取消吞咽任务，因为我们要做一些改动。一旦一切完成，我们可以重新启动服务器。

在你喜欢的编辑器中打开 *www/js/app.js* 。首先，让我们更新模块名。然后，我们将添加几个依赖项。用以下内容更新现有模块声明:

```
angular.module('bucketList', ['ionic', 'firebase', 'bucketList.controllers'])
```

主要依赖是*离子*，其次是*火基*，最后是控制器。

为了开发我们的应用程序，我们将使用两对[离子标签](http://ionicframework.com/docs/api/directive/ionTabs/)组件。第一组选项卡将用于显示登录&注册屏幕，第二组选项卡将用于显示未完成的桶列表项目和已完成的桶列表项目屏幕。

我们将在另一个抽象选项卡中包装我们的选项卡，以获得更多的控制。这将使我们的航线总数达到 6 条。在`run`方法中，我们将注入一些变量和方法到`$rootScope`变量中。这将包括 Firebase 实例 URL、`checkSession`、`logout`和更好的 UX 加载器。最终的 *app.js* 会是

```
angular.module('bucketList', ['ionic', 'firebase', 'bucketList.controllers'])

.run(function($ionicPlatform, $rootScope, $firebaseAuth, $firebase, $window, $ionicLoading) {
  $ionicPlatform.ready(function() {
    // Hide the accessory bar by default (remove this to show the accessory bar above the keyboard
    // for form inputs)
    if (window.cordova && window.cordova.plugins.Keyboard) {
      cordova.plugins.Keyboard.hideKeyboardAccessoryBar(true);
    }
    if (window.StatusBar) {
      StatusBar.styleDefault();
    }

    $rootScope.userEmail = null;
    $rootScope.baseUrl = 'https://bucketlist-app.firebaseio.com/';
    var authRef = new Firebase($rootScope.baseUrl);
    $rootScope.auth = $firebaseAuth(authRef);

    $rootScope.show = function(text) {
      $rootScope.loading = $ionicLoading.show({
        content: text ? text : 'Loading..',
        animation: 'fade-in',
        showBackdrop: true,
        maxWidth: 200,
        showDelay: 0
      });
    };

    $rootScope.hide = function() {
      $ionicLoading.hide();
    };

    $rootScope.notify = function(text) {
      $rootScope.show(text);
      $window.setTimeout(function() {
        $rootScope.hide();
      }, 1999);
    };

    $rootScope.logout = function() {
      $rootScope.auth.$logout();
      $rootScope.checkSession();
    };

    $rootScope.checkSession = function() {
      var auth = new FirebaseSimpleLogin(authRef, function(error, user) {
        if (error) {
          // no action yet.. redirect to default route
          $rootScope.userEmail = null;
          $window.location.href = '#/auth/signin';
        } else if (user) {
          // user authenticated with Firebase
          $rootScope.userEmail = user.email;
          $window.location.href = ('#/bucket/list');
        } else {
          // user is logged out
          $rootScope.userEmail = null;
          $window.location.href = '#/auth/signin';
        }
      });
    }
  });
})

.config(function($stateProvider, $urlRouterProvider) {
  $stateProvider
    .state('auth', {
      url: "/auth",
      abstract: true,
      templateUrl: "templates/auth.html"
    })
    .state('auth.signin', {
      url: '/signin',
      views: {
        'auth-signin': {
          templateUrl: 'templates/auth-signin.html',
          controller: 'SignInCtrl'
        }
      }
    })
    .state('auth.signup', {
      url: '/signup',
      views: {
        'auth-signup': {
          templateUrl: 'templates/auth-signup.html',
          controller: 'SignUpCtrl'
        }
      }
    })
    .state('bucket', {
      url: "/bucket",
      abstract: true,
      templateUrl: "templates/bucket.html"
    })
    .state('bucket.list', {
      url: '/list',
      views: {
        'bucket-list': {
          templateUrl: 'templates/bucket-list.html',
          controller: 'myListCtrl'
        }
      }
    })
    .state('bucket.completed', {
      url: '/completed',
      views: {
        'bucket-completed': {
          templateUrl: 'templates/bucket-completed.html',
          controller: 'completedCtrl'
        }
      }
    })
    $urlRouterProvider.otherwise('/auth/signin');
});
```

请注意，我们使用以下代码初始化 Firebase Auth 服务:

```
$rootScope.baseUrl = 'https://bucketlist-app.firebaseio.com/';
var authRef = new Firebase($rootScope.baseUrl);
$rootScope.auth = $firebaseAuth(authRef);
```

*不要忘记用你的 Firebase 实例*替换`baseURL`

现在，让我们构建*控制器。在`www/js`创建一个新文件，命名为 *controllers.js* 。顾名思义，这个文件将保存所有的控制器。接下来，创建一个名为`templates`的新文件夹。我们将在进行过程中填充每个模板。*

首先，我们有注册控制器。让我们首先创建所需的模板。在`templates`文件夹中创建一个名为*auth.html*的新文件。这将是登录和注册选项卡的抽象选项卡。用以下代码填充它:

```
<ion-tabs class="tabs-icon-top">
  <ion-tab title="Sign In" icon-on="ion-ios7-locked"
    icon-off="ion-ios7-locked-outline" href="#/auth/signin">
    <ion-nav-view name="auth-signin"></ion-nav-view>
  </ion-tab>
  <ion-tab title="Sign Up" icon-on="ion-ios7-personadd"
    icon-off="ion-ios7-personadd-outline" href="#/auth/signup">
    <ion-nav-view name="auth-signup"></ion-nav-view>
  </ion-tab>
</ion-tabs>
```

接下来，让我们添加注册模板。在`templates`文件夹中创建一个名为*auth-signup.html*的新文件，并添加以下代码:

```
<ion-header-bar class="bar-positive">
  <h1 class="title">Sign Up</h1>
</ion-header-bar>
<ion-content class="has-header padding">
  <div class="list">
    <label class="item item-input">
      <span class="input-label">Email</span>
      <input type="text" ng-model="user.email">
    </label>
    <label class="item item-input">
      <span class="input-label">Password</span>
      <input type="password" ng-model="user.password">
    </label>
    <label class="item item-input">
      <button class="button button-block button-positive" ng-click="createUser()">
        Sign Up
      </button>
    </label>
  </div>
</ion-content>
```

当用户点击提交时，我们调用`createuser()`。控制器看起来像这样:

```
angular.module('bucketList.controllers', [])
  .controller('SignUpCtrl', [
    '$scope', '$rootScope', '$firebaseAuth', '$window',
    function ($scope, $rootScope, $firebaseAuth, $window) {
      $scope.user = {
        email: "",
        password: ""
      };
      $scope.createUser = function () {
        var email = this.user.email;
        var password = this.user.password;

        if (!email || !password) {
          $rootScope.notify("Please enter valid credentials");
          return false;
        }

        $rootScope.show('Please wait.. Registering');
        $rootScope.auth.$createUser(email, password, function (error, user) {
          if (!error) {
            $rootScope.hide();
            $rootScope.userEmail = user.email;
            $window.location.href = ('#/bucket/list');
          }
          else {
            $rootScope.hide();
            if (error.code == 'INVALID_EMAIL') {
              $rootScope.notify('Invalid Email Address');
            }
            else if (error.code == 'EMAIL_TAKEN') {
              $rootScope.notify('Email Address already taken');
            }
            else {
              $rootScope.notify('Oops something went wrong. Please try again later');
            }
          }
        });
      }
    }
  ])
```

注意事项:

1.  `$rootScope.show()`、`$rootScope.hide()`、`$rootScope.notify()`在 *app.js* 中定义，显示加载叠加。
2.  `$rootScope.auth.$createUser()`负责与 Firebase 交互，创建新用户。
3.  请注意 Firebase 返回的各种错误消息。你可以在这里找到整个列表[。](https://www.firebase.com/docs/security/simple-login-overview.html#full-error-listing)
4.  注册成功后，我们会将用户重定向到我们的主视图。

接下来是登录控制器。在`templates`文件夹中创建一个名为*auth-signin.html*的新文件，并添加以下标记:

```
<ion-header-bar class="bar-positive">
  <h1 class="title">Sign In</h1>
</ion-header-bar>
<ion-content class="has-header padding">
  <div class="list">
    <label class="item item-input">
      <span class="input-label">Email</span>
      <input type="text" ng-model="user.email">
    </label>
    <label class="item item-input">
      <span class="input-label">Password</span>
      <input type="password" ng-model="user.password">
    </label>
    <label class="item item-input">
      <button class="button button-block button-positive" ng-click="validateUser()">Sign In</button>
    </label>
  </div>
</ion-content>
```

当用户点击提交时，我们调用`validateUser()`。控制器将(*从*上方继续):

```
.controller('SignInCtrl', [
  '$scope', '$rootScope', '$firebaseAuth', '$window',
  function ($scope, $rootScope, $firebaseAuth, $window) {
     // check session
     $rootScope.checkSession();
     $scope.user = {
        email: "",
        password: ""
     };
     $scope.validateUser = function () {
        $rootScope.show('Please wait.. Authenticating');
        var email = this.user.email;
        var password = this.user.password;
        if (!email || !password) {
           $rootScope.notify("Please enter valid credentials");
           return false;
        }
        $rootScope.auth.$login('password', {
           email: email,
           password: password
        })
        .then(function (user) {
          $rootScope.hide();
          $rootScope.userEmail = user.email;
          $window.location.href = ('#/bucket/list');
        }, function (error) {
          $rootScope.hide();
          if (error.code == 'INVALID_EMAIL') {
            $rootScope.notify('Invalid Email Address');
          }
          else if (error.code == 'INVALID_PASSWORD') {
            $rootScope.notify('Invalid Password');
          }
          else if (error.code == 'INVALID_USER') {
            $rootScope.notify('Invalid User');
          }
          else {
            $rootScope.notify('Oops something went wrong. Please try again later');
          }
        });
     }
  }
])
```

注意事项:

1.  `$rootScope.auth.$login()`负责 Firebase 认证。
2.  `$rootScope.auth.$login()`返回一个承诺，该承诺将在请求完成后得到解决。
3.  身份验证成功后，我们将重定向到我们的主视图。

接下来，让我们构建应用程序的主视图。在`templates`文件夹中创建一个名为*bucket.html*的新文件，并添加以下代码:

```
<ion-tabs class="tabs-icon-top">
  <ion-tab title="My List" icon-on="ion-ios7-browsers"
    icon-off="ion-ios7-browsers-outline" href="#/bucket/list">
    <ion-nav-view name="bucket-list"></ion-nav-view>
  </ion-tab>
  <ion-tab title="Completed" icon-on="ion-ios7-checkmark"
    icon-off="ion-ios7-checkmark-outline" href="#/bucket/completed">
    <ion-nav-view name="bucket-completed"></ion-nav-view>
  </ion-tab>
</ion-tabs>
```

这是抽象视图，包含我们的 bucketlist 完整和不完整视图。接下来，在`templates`文件夹中创建一个名为*bucket-list.html*的新文件，并添加以下代码:

```
<ion-header-bar class="bar-positive">
  <button class="button button-clear" ng-click="newTask()">New</button>
  <h1 class="title">My Bucket List</h1>
  <button class="button button-clear" ng-click="logout()">Logout</button>
</ion-header-bar>
<ion-content class="has-header padding" has-tabs="true" on-refresh="onRefresh()">
  <div class="card" ng-repeat="item in list" id="{{item.key}}" >
    <div class="item item-text-wrap">
      <span>{{ item.item }}</span>
      <br/>  <br/>
      <p class="actions padding">
        <i class="ion-checkmark-circled icon-actions margin" ng-click="markCompleted('{{item.key}}')"></i>
        <i class="ion-trash-b icon-actions margin" ng-click="deleteItem('{{item.key}}')"></i>
      </p>
    </div>
  </div>
  <div class="card" >
    <div class="item item-text-wrap" ng-show="noData">
      <span>
      No Items in your bucket List. Click  <a href="javascript:" ng-click="newTask()">Here</a> and create one
      </span>
    </div>
  </div>
</ion-content>
```

注意事项:

1.  我们在标题中添加了一个新的按钮。这将打开一个弹出窗口，用户可以在其中输入项目描述并创建它。
2.  视图主体呈现一个显示物品描述的[卡片](http://ionicframework.com/docs/components/#cards)和一个*删除*和*标记为已完成的*图标。

控制器看起来像这样:

```
.controller('myListCtrl', function($rootScope, $scope, $window, $ionicModal, $firebase) {
  $rootScope.show("Please wait... Processing");
  $scope.list = [];
  var bucketListRef = new Firebase($rootScope.baseUrl + escapeEmailAddress($rootScope.userEmail));
  bucketListRef.on('value', function(snapshot) {
    var data = snapshot.val();

    $scope.list = [];

    for (var key in data) {
      if (data.hasOwnProperty(key)) {
        if (data[key].isCompleted == false) {
          data[key].key = key;
          $scope.list.push(data[key]);
        }
      }
    }

    if ($scope.list.length == 0) {
      $scope.noData = true;
    } else {
      $scope.noData = false;
    }
    $rootScope.hide();
  });

  $ionicModal.fromTemplateUrl('templates/newItem.html', function(modal) {
    $scope.newTemplate = modal;
  });

  $scope.newTask = function() {
    $scope.newTemplate.show();
  };

  $scope.markCompleted = function(key) {
    $rootScope.show("Please wait... Updating List");
    var itemRef = new Firebase($rootScope.baseUrl + escapeEmailAddress($rootScope.userEmail) + '/' + key);
    itemRef.update({
      isCompleted: true
    }, function(error) {
      if (error) {
        $rootScope.hide();
        $rootScope.notify('Oops! something went wrong. Try again later');
      } else {
        $rootScope.hide();
        $rootScope.notify('Successfully updated');
      }
    });
  };

  $scope.deleteItem = function(key) {
    $rootScope.show("Please wait... Deleting from List");
    var itemRef = new Firebase($rootScope.baseUrl + escapeEmailAddress($rootScope.userEmail));
    bucketListRef.child(key).remove(function(error) {
      if (error) {
        $rootScope.hide();
        $rootScope.notify('Oops! something went wrong. Try again later');
      } else {
        $rootScope.hide();
        $rootScope.notify('Successfully deleted');
      }
    });
  };
})
```

注意事项:

*   正如在`Designing the data structure`一节中所讨论的，我们将基于登录用户构建 Firebase 参考。

```
var bucketListRef = new Firebase($rootScope.baseUrl + escapeEmailAddress($rootScope.userEmail));
```

我们正在创建一个以转义用户电子邮件地址命名的集合。您可以在 *controllers.js* 的底部添加`escapeEmailAddress()`定义。

```
function escapeEmailAddress(email) {
  if (!email) return false
  // Replace '.' (not allowed in a Firebase key) with ','
  email = email.toLowerCase();
  email = email.replace(/\./g, ',');
  return email.trim();
}
```

*   接下来，我们将使用这个动态引用，通过`value`事件的`on`监听器来提取所有的 buckelist 条目。每当收藏发生变化的时候就会触发(*Firebase*最好的部分之一)。
*   我们检查该项目是否未完成`data[key].isCompleted == false`，然后将其添加到要显示的项目列表中。
*   我们还注册了`newTask()`，这将打开`Create New`项目弹出窗口。
*   `$scope.markCompleted()`和`$scope.deleteItem()`，分别与 Firebase API 交互，将`isCompleted`值更新为真，并从集合中删除一条数据。

接下来，我们将添加`newCtrl`，负责创建一个新的控制器。在`templates`文件夹中创建一个名为 newItem.html*的新文件，并添加以下代码:*

```
<div class="modal slide-in-up" ng-controller="newCtrl">
  <header class="bar bar-header bar-secondary">
    <button class="button button-clear button-primary" ng-click="close()">Cancel</button>
    <h1 class="title">New Item</h1>
    <button class="button button-positive" ng-click="createNew()">Done</button>
  </header>
  <ion-content class="padding has-header">
    <input type="text" placeholder="I need to do..." ng-model="data.item">
  </ion-content>
</div>
```

点击`Done`，我们调用`createUser()`。在 *controller.js* 中追加以下代码:

```
.controller('newCtrl', function($rootScope, $scope, $window, $firebase) {
  $scope.data = {
    item: ""
  };

  $scope.close = function() {
    $scope.modal.hide();
  };

  $scope.createNew = function() {
    var item = this.data.item;

    if (!item) return;

    $scope.modal.hide();
    $rootScope.show();
    $rootScope.show("Please wait... Creating new");

    var form = {
      item: item,
      isCompleted: false,
      created: Date.now(),
      updated: Date.now()
    };

    var bucketListRef = new Firebase($rootScope.baseUrl + escapeEmailAddress($rootScope.userEmail));
    $firebase(bucketListRef).$add(form);
    $rootScope.hide();
  };
})
```

注意事项:

*   我们构建一个表单对象，它将包含所有的基本数据，以创建一个新的 bucketlist 条目。
*   我们将生成到用户集合的新连接，然后使用`$firebase(bucketListRef).$add(form);`将数据插入到集合中。
*   一旦数据被插入，Firebase 就会触发`value`事件，这将刷新我们的 bucketlist 条目视图。

最后，让我们添加控制器来显示所有完成的 bucketlist 条目。在`templates`文件夹中创建一个名为*bucket-completed.html*的新文件，并添加以下代码:

```
<ion-header-bar class="bar-positive">
  <h1 class="title">Completed Items</h1>
  <button class="button button-clear" ng-click="logout()">Logout</button>
</ion-header-bar>
<ion-content class="has-header padding" has-tabs="true" on-refresh="onRefresh()">
  <div class="card" ng-repeat="item in list" >
    <div class="item item-text-wrap">
      <span>{{ item.item }}</span>
      <br/>  <br/>
      <p class="actions padding">
        <i class="ion-trash-b icon-actions margin" ng-click="deleteItem('{{item.key}}')"></i>
      </p>
    </div>
  </div>
  <div class="card" >
    <div class="item item-text-wrap" ng-show="noData || incomplete">
      <span ng-show="incomplete">
      You can have not completed any of your Bucket List items yet. Try harder!!
      </span>
      <span ng-show="noData">
      No Items in your bucket List.
      </span>
    </div>
  </div>
</ion-content>
```

该控制器类似于不完整 bucketlist 控制器，除了`Create New`项和`Mark Item Incomplete`。如果你愿意，你也可以在这里添加它们。控制器看起来像这样:

```
.controller('completedCtrl', function($rootScope, $scope, $window, $firebase) {
  $rootScope.show("Please wait... Processing");
  $scope.list = [];

  var bucketListRef = new Firebase($rootScope.baseUrl + escapeEmailAddress($rootScope.userEmail));
  bucketListRef.on('value', function(snapshot) {
    $scope.list = [];
    var data = snapshot.val();

    for (var key in data) {
      if (data.hasOwnProperty(key)) {
        if (data[key].isCompleted == true) {
          data[key].key = key;
          $scope.list.push(data[key]);
        }
      }
    }
    if ($scope.list.length == 0) {
      $scope.noData = true;
    } else {
      $scope.noData = false;
    }

    $rootScope.hide();
  });

  $scope.deleteItem = function(key) {
    $rootScope.show("Please wait... Deleting from List");
    var itemRef = new Firebase($rootScope.baseUrl + escapeEmailAddress($rootScope.userEmail));
    bucketListRef.child(key).remove(function(error) {
      if (error) {
        $rootScope.hide();
        $rootScope.notify('Oops! something went wrong. Try again later');
      } else {
        $rootScope.hide();
        $rootScope.notify('Successfully deleted');
      }
    });
  };
});
```

最后，我们来补充一点 CSS。打开`www/css`文件夹中的 *style.css* ，添加以下代码:

```
.margin {
  margin-left: 9px;
  margin-right: 9px;
}
.icon-actions {
  font-size: 23px;
}
.checkbox {
  vertical-align: middle;
}
.actions {
  float: right;
}
.item-text-wrap {
  overflow: auto;
}
.ion-checkmark-circled.icon-actions.margin{
  margin-right: 35px;
}
```

我们完了！让我们运行应用程序，看看它看起来怎么样。在终端中，运行:

```
gulp serve
```

这将启动服务器。接下来，导航到`http://localhost:1881`，你应该会看到一个登录视图。点击注册，注册一个帐户。注册成功后，您将被重定向到 bucketlist 视图。玩你的新*火基动力离子应用*。

注意:你也可以进入你的 Firebase 应用账户，查看那里的数据结构。

### 发布 PhoneGap 版本

我们已经成功构建了一个在浏览器中运行良好的应用程序。让我们构建一个原生安装程序，看看该应用程序如何在实际设备上工作。

注意:如果您是 PhoneGap 新手，我建议您在继续之前阅读 [PhoneGap 快速入门](http://thejackalofjavascript.com/phonegap-quick-start/)。

第一步:首先将`myIonicFireApp/dev/bucketListApp/www`文件夹及其内容复制到`myIonicFireApp/prod`。这就是我们发布 PhoneGap build 所需的全部内容。

步骤 2:创建一个名为`IonicFirePGInstaller`的新 GitHub repo。

第三步:`cd`进入`myIonicFireApp/prod`文件夹(*不在`www`文件夹*中)并运行以下命令:

```
$ git init
$ git add -A
$ git commit -am "Initial Commit"
$ git remote add origin git@github.com:sitepoint/IonicFirePGInstaller.git
```

确保更新 repo 路径以指向您创建的路径。最后，签入代码:

```
$ git push origin master
```

这会将代码推送到 GitHub。

第 4 步:导航到 [PhoneGap Build](https://build.phonegap.com/apps) 并登录。

第五步:点击`+ New App`，在*开源*下提交 GitHub repo URL(*https 的而不是 ssh 的*)。现在，PhoneGap 服务将访问 GitHub 并获取回购。一旦回购被加载，您将看到一个`Ready to Build`按钮。点击它发布一个 PhoneGap 构建。

构建完成后，您可以下载设备的安装程序并测试应用程序。

## 结论

关于使用 Firebase 和 Ionic 框架构建混合应用程序的文章到此结束。希望你已经对如何建造自己的房子有了一个合理的想法。

*   你可以在 [GitHub](https://github.com/jsprodotcom/myIonicFireApp) 上找到我们开发的代码库。
*   你也可以在 [GitHub](https://github.com/jsprodotcom/IonicFirePGInstaller) 上找到可以提交给 PhoneGap build 的`www`文件夹。
*   你可以在这里下载应用安装程序[。](https://build.phonegap.com/apps/943461/share)

感谢阅读！

## 分享这篇文章