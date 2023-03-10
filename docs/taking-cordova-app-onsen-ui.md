# 使用 Onsen UI 进一步使用 Cordova 应用程序

> 原文：<https://www.sitepoint.com/taking-cordova-app-onsen-ui/>

在本教程的第一部分中，我们看了如何使用[温泉 UI](http://onsenui.io/) 开始移动应用开发，设计用户登录和注册页面。在本教程中，我们将使这些网页的功能。我们将利用 Firebase 作为应用程序的后端。在 Onsen UI 中，我们可以同时使用 [jQuery](http://jquery.com/) 和 [AngularJS](https://angularjs.org/) 进行开发。在本教程中，我们将使用 AngularJS。

本教程的源代码可以在 [GitHub](https://github.com/sitepoint-examples/OnsenUI--Part2) 获得。

## 入门指南

[下载](https://github.com/sitepoint-examples/OnsenUI--Part1)或克隆第一个教程源代码。安装所有必需的依赖项，如下所示:

```
git clone https://github.com/sitepoint-examples/OnsenUI--Part1
cd OnsenUI--Part1
npm install
npm install -g gulp
gulp serve
```

将你的浏览器指向`http://localhost:8901/index.html`，你应该会看到我们在第一个教程中设计的应用程序。你也可以像我们在第一个教程中所做的那样，使用 android 模拟器来运行应用程序。

## 实现登录

让我们首先为我们的应用程序定义一个控制器。如果你打开`/OnsenUI--Part1/www/js/app.js`，你应该会看到一个已经定义好的控制器。

```
(function() {
    'use strict';
    var module = angular.module('app', ['onsen']);

    module.controller('AppController', function($scope) {
      // more to come here
    });

})();
```

首先，我们将验证用户名和密码是否为空值。如果验证有任何问题，我们将显示一个带有验证消息的模态窗口。否则我们将通过 Firebase 数据库进行验证。

让我们在控制器`AppController`中定义一个名为`SignIn`的方法，如下所示:

```
$scope.data = [];

$scope.SignIn = function() {
    var user = $scope.data.username;
    var pass = $scope.data.password;
    if (user && pass) {
        // success logic
    } else {
        // failure logic
    }
};
```

将 [ngModel](https://docs.angularjs.org/api/ng/directive/ngModel) 指令添加到`index.html`中的用户名和密码输入文本框，如下所示:

```
<input ng-model="data.username" type="text" placeholder="Username" class="text-input text-input--transparent" style="margin-top:8px; width: 100%;" />
<input ng-model="data.password" type="password" placeholder="Password" class="text-input text-input--transparent" style="margin-top:8px; width: 100%;" />
```

接下来，我们将使用 Onsen UI 的 [ons-modal](http://onsenui.io/guide/overview.html#UsingModal) 组件来显示一个验证弹出窗口。将以下 html 代码添加到`ons-page`中的标志中(应该在第 92 行左右)。

```
<ons-modal var="modal">
    <br>
    <br>Invalid Username or Password.
    <br>
</ons-modal>
```

现在将模态显示代码添加到`SignIn`的故障逻辑中，替换`// failure logic`注释:

```
modal.show();
```

将模态隐藏代码添加到我们刚刚添加的 HTML 的`ng-click`事件中，如下所示:

```
<ons-modal var="modal" ng-click="modal.hide()">
    <br>
    <br>Invalid Username or Password.
    <br>
</ons-modal>
```

将 [ngClick](https://docs.angularjs.org/api/ng/directive/ngClick) 指令添加到登录按钮，如下所示:

```
<ons-button id="btnSignIn" modifier="large" ng-click="SignIn()">
    SignIn
</ons-button>
```

启动服务器，使用空用户名或密码点击登录按钮。将出现模式验证消息。点击弹出窗口上的任何地方，它就会消失。

[这里的](http://codepen.io/jay3dec/pen/cpFsI)是上面代码的演示。

现在，让我们对照 [Firebase](https://www.firebase.com/) 来验证用户名和密码。要开始使用 Firebase，您需要注册一个免费帐户。注册并登录后，您会得到一个 firebase url，在我的例子中是:

```
https://burning-fire-1723.firebaseio.com/
```

在`index.html`中包含以下脚本:

```
<script type='text/javascript' src='https://cdn.firebase.com/js/client/1.0.17/firebase.js'></script>
<script type='text/javascript' src='https://cdn.firebase.com/js/simple-login/1.6.1/firebase-simple-login.js'></script>
```

首先，我们需要使用 firebase url 创建一个 firebase 实例。然后使用这个 firebase 实例，创建一个`FirebaseSimpleLogin`实例。将此代码放在`app.js`中的`module.controller`定义之后:

```
var firebaseRef = new Firebase('https://burning-fire-1723.firebaseio.com');

var auth = new FirebaseSimpleLogin(firebaseRef, function(error, user) {
    if (!error) {
        if (user) {
            // On success authentication
            console.log(user);
        }
    }
});
```

在`SignIn`函数中，我们将验证用户名和密码，如下所示，用下面的代码替换当前函数:

```
$scope.SignIn = function() {
    var user = $scope.data.username;
    var pass = $scope.data.password;
    if (user && pass) {
        // success logic
        auth.login('password', {
            email: user,
            password: pass
        });
    } else {
        // failure logic
        modal.show();
    }
};
```

我们已经使用了`auth.login`来验证 firebase 数据库的用户名和密码。认证成功后，用户将登录。

为了启用身份验证过程，首先登录 firebase 并打开您当前正在使用的应用程序。从左侧菜单中，点击`Login & Auth`。在`Email & Password`选项卡下，检查`Enable Email & Password Authentication`。

使用用户名和密码向 firebase 数据库添加一个新用户。现在，运行应用程序，尝试使用新的用户名和密码登录。检查浏览器控制台，该控制台将使用户成功登录用户身份验证。

[这里的](http://codepen.io/jay3dec/pen/Ecsgn)是上面代码的演示。尝试使用用户名`sam@sam.com`和密码`sam`登录。

接下来，当用户成功登录时，我们会将用户重定向到`home.html`。用下面的代码创建一个名为`home.html`的新文件。

```
<ons-template id="home.html">
    <ons-page>
        <ons-toolbar>

            <div class="center">Welcome</div>
            <div class="right">

                <ons-icon icon="ion-log-out" size="40px" title="Logout" style="cursor:pointer;" ng-click="logout()"></ons-icon>

            </div>
        </ons-toolbar>

        <div style="text-align: center">
            <br />
            <ons-page>

                <p style="padding-top: 100px; color: #999; text-align: center">Welcome Home {{username}}!!</p>

            </ons-page>
        </div>
    </ons-page>
</ons-template>
```

添加一个名为`logout`的新方法，如图所示:

```
$scope.logout = function() {
    auth.logout();         // logging out the firebase
    $scope.data = [];      // clearing user data
    myNavigator.popPage(); // redirecting to sign in page
};
```

现在修改`FirebaseSimpleLogin`回调，如下所示:

```
var auth = new FirebaseSimpleLogin(firebaseRef, function(error, user) {
    if (!error) {
        if (user) {
            $scope.username = user.email;
            myNavigator.pushPage('home.html', {
                animation: 'slide'
            });
        }
    }
});
```

我们已经使用 [ons-navigator](http://onsenui.io/guide/overview.html#PageNavigation) 进行页面导航。`myNavigator.pushPage`用于导航到`home.html`页面。

[这里的](http://codepen.io/jay3dec/pen/wdzrb)是上面代码的演示。

## 实现注册

我们将使用 firebase 实现注册功能。在注册页面中，我们目前有三个注册输入字段。我们将只使用电子邮件和密码，因为这是 firebase 用户注册所需要的。

首先将 [ngModel](https://docs.angularjs.org/api/ng/directive/ngModel) 指令添加到两个字段中，如下所示:

```
<ons-list-item>
    <input type="text" placeholder="Email Address" ng-model="reg.email" class="text-input text-input--transparent" style="margin-top:58px; width: 100%;">
</ons-list-item>

<ons-list-item>
    <input type="text" ng-model="reg.pass" placeholder="Password" class="text-input text-input--transparent" style="margin-top:8px; width: 100%;">
</ons-list-item>
```

在`AppController`中创建一个`SignUp`方法，该方法将验证电子邮件和密码是否为空值。使用`auth.create`方法注册新用户，如下所示:

```
$scope.reg = [];
$scope.SignUp = function() {
    var email = $scope.reg.email;
    var password = $scope.reg.pass;
    auth.createUser(email, password, function(error, user) {
        if (!error) {
            myNavigator.popPage();
        } else {
            alert('error');
        }
    });
};
```

将 [ngClick](https://docs.angularjs.org/api/ng/directive/ngClick) 指令添加到注册按钮，如下所示:

```
<ons-button modifier="large" ng-click="SignUp()">
    Sign Up
</ons-button>
```

添加一个新的模式到注册页面，我们将显示在用户没有输入电子邮件或密码的情况下。这是它的样子:

```
<ons-modal ng-click="modal.hide()" var="modal">
    <br>
    <br>Enter Email Address and Password.
    <br>
</ons-modal>
```

修改注册功能，如下所示:

```
$scope.SignUp = function() {
    var email = $scope.reg.email;
    var password = $scope.reg.pass;
    if (email && password) {
        auth.createUser(email, password, function(error, user) {
            if (!error) {
                myNavigator.popPage();
            } else {
                alert('error');
            }
        });
    } else {
        modal.show();   // to show validation pop up message
    }
};
```

现在重启应用程序并尝试注册。注册成功后，它将导航到登录页面。尝试使用新凭据登录，您应该会登录。

[这里的](http://codepen.io/jay3dec/pen/KemkI)是上述登录和注册功能的演示。

## 结论

在本教程中，我们使用 [Onsen UI](http://onsenui.io/) 框架和 [Firebase](https://www.firebase.com/) 作为后端实现了登录和注册功能。

看看 Onsen UI 提供的其他[组件](http://onsenui.io/guide/components.html)的数量。关于温泉 UI 框架的深入探索，请参考[官方文件](http://onsenui.io/guide/overview.html)。

请在下面的评论中告诉我们你的想法！

## 分享这篇文章