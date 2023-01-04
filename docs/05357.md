# App.js 简介——轻松实现移动网络应用

> 原文：<https://www.sitepoint.com/intro-app-js-mobile-webapps-made-easy/>

JavaScript 在移动应用程序开发中越来越受欢迎。它使 web 应用程序开发人员能够在不学习任何母语技能的情况下开发移动 web 应用程序。

在本教程中，我们将讨论一个名为 [App.js](http://code.kik.com/app/2/index.html) 的轻量级 JavaScript UI 库。使用 App.js，你可以在不影响性能和外观的情况下创建移动网络应用。

## 入门指南

在本教程中，我们将使用 *App.js* 创建一个简单的用户注册应用程序。我们将使用 [Firebase](https://www.firebase.com/) 作为后端。首先，[下载 App.js](http://code.kik.com/app/2/index.html) 并解压。里面有 4 个文件。

*   app.min.css:包含所有 android/iOS 样式的默认样式表
*   app.min.js:库
*   index.html:入门的基本模板文件
*   zepto.js:一个移动友好的 jQuery like 库

使用 *zepto.js* 是可选的。在本教程中，我们将使用 [jQuery](http://jquery.com/download/) 。

## 创建主屏幕

下图显示了 app.js web 应用程序的标准格式。

```
<!DOCTYPE html>
<html>
  <head>
    <title>My App</title>
    <meta name="viewport" content="width=device-width,
                                   initial-scale=1.0,
                                   maximum-scale=1.0,
                                   user-scalable=no,
                                   minimal-ui">
    <link rel="stylesheet" href="//cdn.kik.com/app/2.0.1/app.min.css">
    <style>
      /* put your styles here */
    </style>
  </head>
  <body>
    <!-- put your pages here -->
    <script src="//zeptojs.com/zepto.min.js"></script>
    <script src="//cdn.kik.com/app/2.0.1/app.min.js"></script>
    <script>
      /* put your javascript here */
    </script>
  </body>
</html>
```

让我们从头开始。打开`index.html`，去掉身体上除了`app.min.css`、`app.min.js`之外的所有东西，添加下面的脚本。

```
try {
     App.restore(); // it loads/restores the app
 } catch (err) {
     App.load('home'); // in case of error it loads the default page
 }
```

[下载 jQuery](http://jquery.com/download/) 并包含在你的页面中或者参考[jQuery CDN 版本](https://code.jquery.com/jquery-1.9.0.js)。

```
<script src="https://code.jquery.com/jquery-1.9.0.js"></script>
```

创建一个`div`，添加类`app-page`，你就准备好了你的第一个页面。`app-page`类用来定义一个页面。

```
<div class="app-page"></div>
```

`app-page`必须总是有一个`data-page`属性。`data-page`用于从 JavaScript 访问页面。

现在让我们给它添加一个顶栏和标题。

```
<div class="app-page" data-page="home">
    <div class="app-topbar">
        <div class="app-title">My Web App</div>
    </div>
</div>
```

接下来我们需要在主页上添加一个`SignIn`和`SignUp`按钮。所有内容都需要在一个`app-content` div 中定义，因此创建一个`app-content` div 并在其中放置按钮。

```
<div class="app-page" data-page="home">
    <div class="app-topbar">
        <div class="app-title">Simple Web App</div>
    </div>
    <div class="app-content">
        <br />
        <div class="app-button green">SignIn</div>
        <br />
        <div class="app-button blue">SignUp</div>
    </div>
</div>
```

浏览`index.html`，你应该会看到带有`SignIn`和`SignUp`按钮的主页。

## 创建注册屏幕

> App.js 是为静态单页应用的开发者服务的。这意味着它保留了网页会话中的所有页面导航，将“页面”定义为可以实例化的 DOM 节点

来自 App.js 文档

我们将在同一个`index.html`中创建我们所有的页面。让我们创建*注册*屏幕，并把它挂在主屏幕按钮上。这是它的样子:

```
<div class="app-page" data-page="SignUp">
    <div class="app-topbar">
        <div class="app-button left blue" data-target="home">back</div>
    </div>
    <br />
    <div class="app-content">
        <input id="btnEmail" class="app-input" placeholder="Email">
        <input id="btnPassword" " class="app-input " placeholder="Password" type="password">
        <div id="btnSignUp" class="app-button green ">SignUp</div>
    </div>
</div>
```

`data-target`属性用于将屏幕链接在一起。将`data-target`添加到主页上的`SignUp`按钮，链接到该屏幕。如果您浏览*index.html*文件并点击主页上的`SignUp`按钮，它将重定向到`SignUp`屏幕。

### 将您的应用程序连接到 Firebase

Firebase 是一个强大的 api，可以实时存储和同步数据。要开始使用 Firebase，您需要注册一个免费帐户。只需登录，创建一个应用程序，然后点击链接来管理应用程序。您将获得一个唯一的 url 来存储数据。我的情况是:

*https://burning-fire–1723.firebaseio.com/*

在 firebase 仪表板上，点击左侧菜单中的`Simple login`。点击`Email and Password`认证提供者选项卡并选中启用。

创建一个名为 *controller.js* 的控制器脚本，并将其包含在*index.html*中。每个`app-page`都有控制器逻辑。在 *controller.js* 中，我们将定义控制器逻辑来读取电子邮件和密码，并将其存储在 firebase 中。

首先，下载并包含 [firebase](https://www.firebase.com/) 客户端或参考[CDN 版本](https://cdn.firebase.com/js/client/1.0.17/firebase.js)。

```
<script type='text/javascript' src='https://cdn.firebase.com/js/client/1.0.17/firebase.js'></script>
```

我们还需要 firebase 简单登录脚本。

```
<script type='text/javascript' src='https://cdn.firebase.com/js/simple-login/1.6.1/firebase-simple-login.js'></script>
```

首先，我们需要使用 firebase url 创建一个 firebase 实例。使用这个 firebase 实例，创建一个`FirebaseSimpleLogin`实例。

```
var firebaseRef = new Firebase('https://burning-fire-1723.firebaseio.com'); 
var wishRef = new Firebase('https://burning-fire-1723.firebaseio.com/WishList');
var auth = new FirebaseSimpleLogin(firebaseRef, function (error, user) {
    if (!error) {
        if (user) {
            App.load('LoginHome',user);
        }
    }
});
```

当我们尝试验证用户登录时，如果没有错误， *LoginHome* 将被加载。

接下来，我们将为*注册*页面添加控制器逻辑。下面是它的样子:

```
App.controller('SignUp', function (page) {

    $(page)
        .find('#btnSignUp')
        .on('click', function () {
            var email = $('#btnEmail').val();
            var password = $('#btnPassword').val();
            if (email &amp;&amp; password) {
            // on successful validation create the user
                auth.createUser(email, password, function (error, user) {
                    if (!error) {
                        // App.load('SignIn'); 
                    }
                });
            } else {
            // on validation failure show the validation message
                App.dialog({
                    title: 'Validation Error',
                    text: 'Please enter username and password.',
                    okButton: 'Try Again',
                    cancelButton: 'Cancel'
                }, function (tryAgain) {
                    if (tryAgain) {
                        App.load('SignUp');
                    }
                });
            }

        });

});
```

点击*注册*页面上的 *btnSignUp* 按钮，将通过调用`auth.createUser`创建一个用户。

添加我们在成功登录时加载的 *LoginHome* html 页面，如下所示:

```
<div class="app-page" data-page="LoginHome">
    <div class="app-topbar">
        <div class="app-title">Welcome Home!! <span class="user"></span>
        </div>
        <div class="app-button right">Logout</div>
    </div>
    <div class="app-content">
    </div>
</div>
```

浏览*index.html*页面，点击*注册*按钮。输入邮箱和密码，点击*注册*。如果一切顺利，新添加的用户将出现在 firebase 用户列表中。

## 创建登录屏幕

到目前为止，我们所拥有的是一个链接到*注册*和*登录*屏幕的主页。我们已经创建了*注册*屏幕，并将其链接到主页。让我们添加一个*登录*屏幕。

```
<div class="app-page" data-page="SignIn">
    <div class="app-topbar">
        <div class="app-button left blue" data-target="home">back</div>
    </div>
    <br />
    <div class="app-content">
        <input id="btnUsername" class="app-input" placeholder="Username">
        <input id="btnPass" class="app-input" placeholder="Password" type="password">
        <div id="btnSignIn" class="app-button green">SignIn</div>
    </div>
</div>
```

上面的 html 代码类似于*注册*屏幕。现在让我们给这个`data-page`附加一个控制器。

```
App.controller('SignIn', function (page) {

    $(page)
        .find('#btnSignIn')
        .on('click', function () {
            var email = $('#btnUsername').val();
            var password = $('#btnPass').val();
            if (email &amp;&amp; password) {
                auth.login('password', {
                    email: email,
                    password: password
                });
            } else {
                App.dialog({
                    title: 'Validation Error',
                    text: 'Please enter username and password.',
                    okButton: 'Try Again',
                    cancelButton: 'Cancel'
                }, function (tryAgain) {
                    if (tryAgain) {
                        App.load('SignIn');
                    }
                });
            }
        });
});
```

上面的代码调用`auth.login`函数来验证 firebase 数据。如果找到用户，它将重定向到*登录主页*。

让我们为 *LoginHome* 页面添加控制器方法，并定义注销功能。

```
App.controller('LoginHome', function (page,user) {
    $(page)
        .find('.user').text(user.email); //setting the email in welcome message 

    $(page)
        .find('.app-button')
        .on('click', function () {
            auth.logout();  //logs out the user session
            App.load('SignIn'); // loads the Sign In page
        });
});
```

由于我们已经添加了*登录*页面，取消`SignUp`成功回调中`App.load('SignIn')`的注释。使用`data-target`属性将主页链接到*登录*页面。浏览到*index.html*，如果一切正常，登录和注册功能都应该工作正常。

## 在登录主页中添加列表

接下来，让我们为登录的用户创建一个界面，向列表中添加一个条目。我们修改了现有的`LoginHome` html 来包含一个文本框和一个按钮。我们还在顶栏中添加了欢迎信息和链接。下面是修改后的 html 代码:

```
<div class="app-page" data-page="LoginHome">
    <div class="app-topbar">
        <div id="btnShowList" class="app-button red left">Wish List</div>
        <div class="app-title">Welcome Home!! <span class="user"></span>
        </div>
        <div id="btnLogout" class="app-button red right ">Logout</div>
    </div>
    <div class="app-content">

        <input id="txtWish" class="app-input" placeholder="wish">
        <br />
        <div id="btnAdd" class="app-button green">Add Wish</div>

    </div>
</div>
```

我们需要检查有效数据的文本框，并将数据保存到 firebase 中。如果数据无效，我们将使用[对话框](http://code.kik.com/app/2/docs.html#ui-dialogs)显示验证弹出窗口。为了将数据保存到 firebase 中，我们将使用 [push()](https://www.firebase.com/docs/javascript/firebase/push.html) 。下面是`btnAdd`点击的代码:

```
$(page)
    .find('#btnAdd')
    .on('click', function () {
        var wish = $('#txtWish').val();
        if (wish) { // checking if valid data

            // if valid data insert into firebase
            wishRef.push({
                'user_id': user.email,
                'text': wish
            });
            App.load('WishList',user); // load the page to show all wishes
        } 
        else{
            // got invalid data, show validation message
            App.dialog({
                title: 'Validation Error',
                text: 'Looks like you forgot to enter the wish.',
                okButton: 'Try Again',
                cancelButton: 'Cancel'
            }, function (tryAgain) {
                if (tryAgain) {
                    App.load('LoginHome', user);
                }
            });
        }

    });
```

接下来，我们需要提供一个界面来显示用户输入的数据。让我们创建另一个页面， *WishList* ，如下所示:

```
<div class="app-page" data-page="WishList">
    <div class="app-topbar">
        <div class="app-title">Wish List</div>
        <div class="app-button left blue">Back</div>
    </div>
    <div class="app-content">
        <ul class="app-list">

        </ul>
    </div>
</div>
```

注意带有类`app-list`的`ul`。我们将把我们的项目添加到这个列表中。在*登录主页*页面上，我们在顶栏中有一个名为*愿望清单*的链接。让我们为该链接附加一个事件，以便在点击时加载 *WishList* 页面。

```
$(page)
    .find('#btnShowList')
    .on('click', function () {
        App.load('WishList', user);
    });
```

现在我们需要为*愿望列表*页面声明控制器方法。在控制器内部，我们需要定义一个点击事件，当点击顶部栏中的按钮时加载*登录主页*。我们还需要从 firebase 获取数据，并用类`app-list`将其绑定到`ul`。以下是控制器代码:

```
App.controller('WishList', function (page, user) {
    $(page)
        .find('.app-button')
        .on('click', function () {
            App.load('LoginHome', user); //Loading LoginHome page
        });

    // Fetching data from Firebase and passing it to show function
    new Firebase('https://burning-fire-1723.firebaseio.com/WishList')
        .once('value', show);

    function show(snap) {
        $.each(snap.val(), function (i, value) {
            var spanText = $('<span/>').css('font-weight', 'bold').text(value.text);
            var spanUser = $('<span/>').text(' by:: ' + value.user_id);
            $(page).find('.app-list').append($('<li/>').append(spanText, spanUser));
        });
    }
});
```

试着浏览到`index.html`，一切都应该正常。

## 结论

在本教程中，我们使用 app.js 的一些功能创建了一个小应用程序。我们只关注有限的几个特征，如`app-page`、`app-list`和`app.dialog`。app.js 提供的所有特性和功能都可以在 [App.js 文档](http://code.kik.com/app/2/docs.html)中找到。

本教程的源代码可以在 [GitHub](https://github.com/sitepoint-examples/App.jsSample) 获得。

## 分享这篇文章