# 在跨平台应用中进一步发展 Angular Mobile UI

> 原文：<https://www.sitepoint.com/taking-angular-mobile-ui-cross-platform-apps/>

在我们关于移动 Angular UI 的教程的第一部分中。我们看到了如何通过实现*登录*功能开始。在这一部分，我们将实现*注册*功能，并将 [ladda](http://lab.hakim.se/ladda/) 集成到我们的应用程序中，以增强其 UI。

## 入门指南

我们将创建的应用程序的演示可在 [Heroku](http://infinite-hamlet-4942.herokuapp.com/) 上获得。源代码可以在 [GitHub](https://github.com/sitepoint-examples/MobileAngularUIApp_Part_2) 上获得。

要开始，复制教程的第一部分并安装它，如下所示:

```
git clone https://github.com/sitepoint-examples/MobileAngularUIApp_Part_1.git
cd MobileAngularUIApp_Part_1
npm install
grunt
```

将你的浏览器指向`http://localhost:3000/BucketApp`，你应该会看到在[第一教程](https://www.sitepoint.com/getting-started-mobile-angular-ui/)中创建的应用。

## 创建注册屏幕

首先在 *BucketApp* 文件夹中创建一个新的`signUp.html`，如下所示:

```
<div class="scrollable">
    <div class="scrollable-content section">
        <form action="" id="" ng-submit="signUp()">
            <div bs-panel title="">
                <input bs-form-control type="email" ng-model="user.username" label="" label-class="col-xs-3 col-sm-2 col-lg-1" class="col-xs-10 col-sm-11 col-lg-12" placeholder="Email Id" />
                <input bs-form-control type="password" ng-model="user.password" label="" label-class="col-xs-3 col-sm-2 col-lg-1" class="col-xs-10 col-sm-11 col-lg-12" placeholder="Password" />

                <button type="submit" id="btnSignUp" ng-disabled="user.username==undefined||user.username=='' || user.password=='' || user.password==undefined" class="btn btn-success">
                    <span>Sign Up</span>
            </div>
        </form>
    </div>
</div>
```

注意`ng-submit`函数`signUp`，我们将在后面声明它。

[nd disabled](https://docs.angularjs.org/api/ng/directive/ngDisabled)指令用于验证电子邮件和密码，并启用/禁用注册按钮。

为`BucketApp.js`中的`signUp.html`定义路线，如下图所示:

```
$routeProvider.when('/signUp', {
    templateUrl: 'signUp.html'
});
```

重启服务器，将浏览器指向`http://localhost:3000/BucketApp`。点击右上角的`SignUp`链接，您应该会看到注册页面。

在`BucketApp.js`中定义`signUp`函数，如下所示:

```
$scope.signUp = function() {
    var email = $scope.user.username;
    var password = $scope.user.password;

    if (email && password) {
        // if non-empty email and password
        auth.$createUser(email, password)
            .then(function(user) {
                // do things if success
                console.log(user);
            }, function(error) {
                // do things if failure
                console.log(error);
            });
    }
}
```

我们已经使用了 [createUser](https://www.firebase.com/docs/web/api/firebasesimplelogin/createuser.html) api 函数来创建一个新用户。重启服务器并尝试注册。成功注册后，用户对象将被登录到浏览器控制台。

接下来包含移动 angular ui [覆盖组件](http://mobileangularui.com/docs/#overlays)，向用户显示注册成功消息。在`signUp.html`页面中包含以下 html 代码:

```
<div overlay="myOverlay">
    <h4 id="statusMsg" class="overlay-title">{{signUpMessage}}</h4>
    <p toggle="off" bubble target="myOverlay">
        <span class="btn btn-primary">Ok</span>
    </p>
</div>
```

在覆盖 html 中，我们有一个变量`signUpMessage`来设置弹出消息。

我们将需要 [rootScope](https://docs.angularjs.org/api/ng/service/%24rootScope) 服务来触发覆盖，因此将其注入控制器。

```
app.controller('MainController', ['$scope', '$firebaseSimpleLogin', '$location', '$rootScope',

    function($scope, $firebaseSimpleLogin, $location, $rootScope) {}
```

在`signUp`功能中，成功的用户创建设置覆盖消息并触发覆盖，如下所示:

```
$scope.signUpMessage = "User Registration Successful. Please SignIn :)";
$rootScope.toggle('myOverlay', 'on');
```

出现错误时，设置如下所示的覆盖信息:

```
$scope.signUpMessage = "Error Occurred: Enter valid email id.";
$rootScope.toggle('myOverlay', 'on');
```

在点击 *Ok* 按钮时，我们需要将用户重定向到一个登录页面，所以在 Ok 按钮上包含 [ngClick](https://docs.angularjs.org/api/ng/directive/ngClick) 指令。

```
<span ng-click="showSignIn()" class="btn btn-primary">Ok</span>
```

如下图所示定义`showSignIn`:

```
$scope.showSignIn = function() {
    //Reset the overlay
    $rootScope.toggle('myOverlay', 'off');
    //Initialized the user object
    $scope.user = {
        username: "",
        password: ""
    };
    //Redirected to Sign In page
    $location.path('/');
}
```

现在重启服务器，浏览应用程序。用户注册成功后，`OK`按钮将带您进入登录页面。

## 添加 Ladda 负载指示器

接下来，我们将把 [Ladda](http://lab.hakim.se/ladda/) 装载指示器添加到我们的应用程序中。

我们将[创建一个 AngularJS 指令](https://www.sitepoint.com/practical-guide-angularjs-directives/)供 ladda 在我们的应用中使用。以下是我们的 ladda 指令的最小代码:

```
app.directive('uiLadda', [
     function() {
         return {
             link: function(scope, element, attrs) {
                 // Code logic will be here
             }
         };
     }
 ]);
```

修改`signIn.html`中的签到按钮，如下图所示:

```
<button data-ui-ladda="login.loading" ng-disabled="user.username==undefined||user.username=='' || user.password=='' || user.password==undefined" type="submit" id="btnSignIn" class="btn btn-primary segoe-ui-light ladda-button" data-style="expand-right">
    <span class="ladda-label">Sign In</span>
</button>
```

我们已经应用了带有`login.loading`属性的`uiLadda`指令。我们添加了一个名为`data-style="expand-right"`的属性，它定义了加载指示器的样式。添加了额外的类`segoe-ui-light ladda-button`来设计 ladda 按钮的样式。

接下来在`index.html`中包含来自[拉达区](https://github.com/hakimel/Ladda/tree/master/dist)的样式和 javascript 文件，如下所示:

```
<link rel="stylesheet" href="/BucketApp/assets/css/style.css" />
<script src="/BucketApp/assets/js/spin.min.js"></script>
<script src="/BucketApp/assets/js/ladda.js"></script>
```

为了知道何时启动/停止 ladda 装载指示器，我们将设置`login.loading`为真和假。在`uiLadda`指令中，我们将观察`login.loading`来启动/停止指示器。因此，在 app controller 中添加一个新变量，如下所示:

```
var login={};
$scope.login=login;
```

修改指令`uiLadda`,如下所示:

```
app.directive('uiLadda', [
    function() {
        return {
            link: function(scope, element, attrs) {
                var Ladda = window.Ladda;
                ladda = Ladda.create(element[0]);
                // Watching login.loading for change
                scope.$watch(attrs.uiLadda, function(newVal, oldVal) {
                    // if true show loading indicator
                    if (newVal) {
                        ladda.start();
                    } else {
                        ladda.stop();
                    }
                });
            }
        };
    }
]);
```

在`$scope.signin`函数中，将`login.loading`设置为真。论成败把`login.loading`设定为假。

保存更改，重新启动服务器并尝试登录。单击“登录”按钮，它将向右展开，显示加载指示器。

修改注册按钮，如下所示:

```
<button type="submit" data-ui-ladda="login.loading" id="btnSignUp" ng-disabled="user.username==undefined||user.username=='' || user.password=='' || user.password==undefined" class="btn btn-success segoe-ui-light ladda-button" data-style="expand-right">
    <span class="ladda-label">Sign Up</span>
</button>
```

并且还在`$scope.signUp`中设置`login.loading`为真。成功和失败时将`login.loading`重置为假。

## 结论

在本教程中，我们使用 Mobile Angular UI 和 firebase 实现了注册功能。我们看到了如何将 [Ladda](http://lab.hakim.se/ladda/) 指示器添加到我们的应用程序中。我们使用了[覆盖组件](http://mobileangularui.com/docs/#overlays)来显示弹出消息。你可以在[官方文档](http://mobileangularui.com/docs/)中找到 Mobile Angular UI 提供的许多其他有用的组件。看看吧，在下面的评论中告诉我们你的想法。

## 分享这篇文章