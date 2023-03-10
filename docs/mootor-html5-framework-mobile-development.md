# Mootor，一个用于移动开发的 HTML5 框架

> 原文：<https://www.sitepoint.com/mootor-html5-framework-mobile-development/>

在本教程中，我们将了解用于开发移动应用程序的 HTML 5 框架 [Mootor 框架](https://mootor.voolks.com/)。它相当新(2014 年 3 月),没有一些类似框架的开发热潮，但它总是值得一看。

本教程的源代码可以在 [GitHub](https://github.com/sitepoint-examples/MootorFrameworkApp_Part1) 获得。

## 入门指南

我们将使用 [angular seed](https://github.com/angular/angular-seed) 项目为 Mootor 项目建立一个本地服务器。因此，首先克隆 angular seed 项目并安装所需的依赖项:

```
git clone https://github.com/angular/angular-seed.git
cd angular-seed
npm install
```

完成后，克隆 Mootor 框架，并将`dist`、`source`和`examples`文件夹复制到`angular-seed`目录中。

```
git clone https://github.com/emi420/Mootor.git
// copy dist, source and examples to angular-seed
```

从`angular-seed`目录，使用`npm start`启动服务器，并将浏览器指向`http://localhost:8000/examples/demo/`。您应该会看到演示应用程序:

![Demo preview](img/999f23a35fc0186a9a3a765df165f183.png)

## 我们将创造什么

使用 Mootor 框架，我们将创建一个简单的遗愿清单应用程序，用户可以登录并创建他们的遗愿清单。我们将把应用程序连接到 [Firebase](https://www.firebase.com/) 后端来保存我们的数据。

## 创建登录屏幕

在`angular-seed/examples`中创建一个名为`BucketApp`的目录。创建一个名为`index.html`的 HTML 文件作为根文件。在这个文件中包含所有需要的库。

接下来创建`index.js`，我们将在这里为我们的`BucketApp`定义路由。

下面是`examples/BucketApp/index.html`应该有的样子:

```
<!DOCTYPE HTML>
<html>

<head>

    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

    <meta name="viewport" content="initial-scale = 1.0, user-scalable = no">
    <meta name="apple-mobile-web-app-capable" content="yes" />

    <link rel="stylesheet" href="../../source/css/mootor.css">
    <link rel="stylesheet" href="../../source/css/color.css">
    <link rel="stylesheet" href="../../source/css/icons.css">

    <script defer type="text/javascript" src="../../dist/js/zepto-bundle.js"></script>
    <script defer type="text/javascript" src="../../dist/js/mootor.js"></script>
    <script defer type="text/javascript" src="index.js"></script>

</head>

<body>
    <header>
        <h1><img src="img/logo.png" /></h1>
    </header>
</body>

</html>
```

在`BucketApp`中创建一个名为`views`的目录。在`views`中创建一个名为`index`的文件夹，并在其中创建`index.html`、`index.js`和`index.css`。

`index.html`将是我们应用程序的默认视图，`index.js`将是支持脚本，`index.css`将是与`index`视图相关的 css。

下面是`views/index/index.html`应该有的样子:

```
<form id="myForm" class="m-form m-padding">
    <fieldset class="m-form-top-labels">

        <div class="m-field">
            <input id="txtEmail" type="text" class="m-text" placeholder="Username" />
        </div>

        <div class="m-field">
            <input id="txtPass" type="password" class="m-text" placeholder="Password" />
        </div>

    </fieldset>

    <button type="submit" class="m-button m-button-success m-button-block">Sign In</button>

</form>
```

接下来我们将在`examples/BucketApp/index.js`中定义路线，如下所示:

```
(function() {

    "use strict";
    // Created the Mootor app
    var app = m.app({
        views: [
            "index"
        ]
    });
    //default route
    app.route("^$", app.view("index"));

    // initialize the app
    app.init();

}());
```

重启服务器，将浏览器指向`http://localhost:8000/examples/BucketApp`，你应该可以看到登录界面。

![Login Screen](img/5a7f02ff0b21a33bbe2ff7ada7b68228.png)

## 实现登录

打开`examples/BucketApp/views/index.js`。这是我们在逻辑中定义符号的地方。我们将使用 [Firebase](https://www.firebase.com/) 作为后端。

让我们从将提交事件附加到登录表单开始。

```
(function($) {

    "use strict";

    m.app.view("index").on("ready", function() {
        $("#myForm").on("submit", function() {
            // logic will be here
        });
    });

}(window.$));
```

firebase 的入门非常简单。首先在 Firebase 上注册一个免费帐户。一旦你登录到 firebase，从左侧菜单点击*登录&认证*并检查*启用电子邮件&密码认证*。

当用户成功登录后，应用程序将重定向到用户主页，并显示一条欢迎消息。让我们为用户主页创建一个视图。

在`examples/BucketApp/views/`中创建一个文件夹`userhome`。里面创造了`userhome.html`、`userhome.js`和`userhome.css`。

下面是`userhome.html`应该有的样子:

```
<h2>Welcome to User Home!!</h2>

<button id="btnLogout" type="button" class="m-button m-button-success m-button-block">Logout</button>
```

打开`userhome.js`，为`btnLogout`定义`onclick`事件，如下图所示:

```
(function($) {

    "use strict";

    m.app.view("userhome").on("ready", function() {

        // create the firebase object
        var firebaseRef = new Firebase('https://burning-fire-1723.firebaseio.com');

        // create auth object
        var auth = new FirebaseSimpleLogin(firebaseRef, function(error, user) {
            if (!error) {
                if (user) {

                }
            }
        });

        // Logout click event
        $("#btnLogout").click(function() {
            auth.logout();
            window.location.href = "";
        });
    });

}(window.$));
```

接下来将`userhome`的路线添加到`examples/BucketApp/index.js`中，如下图所示:

```
app.route("^#userhome", app.view("userhome"));
```

让我们实现用户登录。一旦用户提交了登录表单，我们将验证用户名和密码是否为空值，并调用 firebase [auth.login](https://www.firebase.com/docs/web/api/firebasesimplelogin/login.html) 函数。

修改`examples/BucketApp/views/index/index.js`如下图所示:

```
(function($) {

    "use strict";

    m.app.view("index").on("ready", function() {

        // create the firebase object
        var firebaseRef = new Firebase('https://burning-fire-1723.firebaseio.com');

        // create auth object
        var auth = new FirebaseSimpleLogin(firebaseRef, function(error, user) {
            if (!error) {
                if (user) {
                    window.location.href = "#userhome";
                }
            }
        });
        // form on submit event
        $("#myForm").on("submit", function() {
            var email = $('#txtEmail').val();
            var password = $('#txtPass').val();
            auth.login('password', {
                email: email,
                password: password
            });
            return false;
        });
    });

}(window.$));
```

打开`examples/BucketApp/index.html`并包含对 Firebase 的脚本引用，如下所示:

```
<script type='text/javascript' src='https://cdn.firebase.com/js/client/1.0.17/firebase.js'></script>
<script type='text/javascript' src='https://cdn.firebase.com/js/simple-login/1.6.1/firebase-simple-login.js'></script>
```

重启服务器，将浏览器指向`http://localhost:8000/examples/BucketApp/`。尝试使用用户名*sam@sam.com*和密码*山姆*登录，成功后您应该会看到下面的屏幕:

![User Home Screen](img/e8e7d81e5c0d0587f671ffce46669a47.png)

## 结论

在本教程中，我们看到了如何开始使用 [Mootor 框架](https://mootor.voolks.com/)创建移动应用程序。我们使用 Mootor 框架和 [Firebase](https://www.firebase.com/) 实现了用户登录功能。在下一个教程中，我们将尝试使用这个框架提供的一些其他特性，并实现注册功能。

关于框架的详细信息可以在[官方文档](http://emi420.github.io/Mootor/)中找到。你有什么想法？与其他现有框架相比，它提供了什么新的东西吗？

## 分享这篇文章