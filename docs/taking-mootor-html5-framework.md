# 进一步发展 Mootor HTML5 框架

> 原文：<https://www.sitepoint.com/taking-mootor-html5-framework/>

在我的[之前的教程](https://www.sitepoint.com/mootor-html5-framework-mobile-development/)中，我们看到了如何开始使用 [Mootor 框架](https://github.com/emi420/Mootor)创建移动应用。我们使用 Mootor 框架和 [Firebase](https://www.firebase.com/) 实现了用户登录功能。

在本教程中，我们将使用 [Firebase](https://www.firebase.com/) 作为后端来实现注册功能。
我们将修改登录实现，因为 firebase 发布了一个新版本，我们在第一篇教程中使用的独立 [FirebaseSimpleLogin](https://www.firebase.com/docs/web/api/firebasesimplelogin/) 客户端已被弃用并添加到 Firebase 核心中。

本教程的源代码可以在 [GitHub](https://github.com/sitepoint-examples/MootorFrameworkApp_Part2) 获得。

## [](#getting-started)入门

要开始，从 [GitHub](https://github.com/sitepoint-examples/MootorFrameworkApp_Part1) 克隆之前的教程。

```
git clone https://github.com/sitepoint-examples/MootorFrameworkApp_Part1
```

克隆源代码后，导航到项目目录并安装所需的依赖项:

```
cd MootorFrameworkApp_Part1
npm install
```

接下来，启动服务器

```
npm start
```

在浏览器中打开`http://localhost:8000/examples/BucketApp/`，应用程序应该正在运行。

## [](#include-the-firebase-script)包含了火基脚本

导航到`examples/BucketApp/index.html`并用新版本替换现有的 firebase 脚本:

```
<script src="https://cdn.firebase.com/js/client/2.0.5/firebase.js"></script>
```

## [](#modifying-sign-in-implementation)修改签到实现

由于`FirebaseSimpleLogin`已经被弃用，我们不需要创建`FirebaseSimpleLogin`实例。打开`examples/BucketApp/views/index/index.js`并删除对`auth`对象的引用，产生如下代码:

```
(function($) {

        "use strict";

        m.app.view("index").on("ready", function() {

            // create the firebase object
            var firebaseRef = new Firebase('https://burning-fire-1723.firebaseio.com');

            // form on submit event
            $("#myForm").on("submit", function() {
                var email = $('#txtEmail').val();
                var password = $('#txtPass').val();

                firebaseRef.authWithPassword({
                  "email": email,
                  "password": password
              }, function(error, authData) {
                  if (error) {
                    console.log("Login Failed!", error);
                } else {
                    console.log("Authenticated successfully ", authData);
                    window.location.href = "#userhome";
                }
            });
                return false;
            });
        });

    }(window.$));
```

相反，使用 Firebase 实例`firebaseRef`，我们可以使用`authWithPassword`方法验证电子邮件地址和密码。

如上所示，如果没有问题，调用`authWithPassword`将返回`authData`，如果有问题，将返回`error`。如果没有错误，那么我们将用户重定向到主页。

保存所有更改，重启服务器，在浏览器中打开`http://localhost:8000/examples/BucketApp/`，并尝试使用电子邮件地址`sam@sam.com`和密码`sam`登录。

使用新的 firebase 版本，我们还需要修改 firebase 注销实现。打开`examples/BucketApp/views/userhome/userhome.js`并移除 auth 对象，因为不再需要它了。使用`firebaseRef`我们将调用`unauth`方法，该方法将注销当前用户会话。下面代码中的结果:

```
(function($) {

    "use strict";

    m.app.view("userhome").on("ready", function() {

        // create the firebase object
        var firebaseRef = new Firebase('https://burning-fire-1723.firebaseio.com');

        $("#btnLogout").click(function() {
            firebaseRef.unauth();
            window.location.href = "";
        });
    });

}(window.$));
```

## [](#creating-a-sign-up-page)创建报名页面

在创建注册页面之前，我们将修改注册页面以包含一个指向注册页面的链接。打开`BucketApp/views/index/index.html`并添加下面的头 HTML 代码。

```
<header>
    <h1>
        Simple Mootor App
    </h1>

    <nav>
        <a target="_blank" href="#signup">
    <span class="m-icon-add-circle"></span>
</a>

    </nav>
</header>
```

保存更改并重新启动服务器。在你的浏览器中打开`http://localhost:8000/examples/BucketApp/`，你应该会在应用程序的右上角看到一个加号图标。

![Original App](img/08b2a800b38301a95b9d6cc3c8093b1c.png)

现在让我们创建注册页面。打开`examples/BucketApp/views`并创建一个名为`signup`的文件夹。在`signup`文件夹中创建一个`signup.html`、`signup.js`和`signup.css`。将以下 html 代码添加到`signup.html`:

```
<header>
    <h1>
        User Registration
    </h1>
</header>
<form id="myForm" class="m-form m-padding">
    <fieldset class="m-form-top-labels">

        <div class="m-field">
            <input id="txtEmailId" type="text" class="m-text" placeholder="Email Id" />
        </div>

        <div class="m-field">
            <input id="txtPassword" type="password" class="m-text" placeholder="Password" />
        </div>

    </fieldset>

    <button id="btnSignUp" type="button" class="m-button m-button-success m-button-block">Sign Up</button>

</form>
```

接下来，我们将为添加的`signup`视图设置路线。打开`examples/BucketApp/index.js`，为`signup`页面添加路线:

`app.route("^#signup", app.view("signup"));`

保存所有更改并重新启动服务器。打开应用程序，在登录屏幕中，单击加号图标导航至注册屏幕。注册屏幕应该是这样的:

![Sign Up Form](img/518d5ce51e07d061778ab4373517c763.png)

## [](#implementing-signup-functionality)实现注册功能

当注册视图加载时，我们将附加`Sign Up`按钮点击事件。打开`examples/BucketApp/views/signup/signup.js`并声明`onready`状态功能块，如图所示:

```
(function($) {

    "use strict";

    m.app.view("signup").on("ready", function() {

       // we'll attach the button click here !!

    });

}(window.$));
```

现在在`ready`函数中添加注册按钮 click 事件调用。

```
(function($) {

    "use strict";

    m.app.view("signup").on("ready", function() {

        // on click
        $("#btnSignUp").on("click", function() {

           // Sign Up logic will be here

        });
    });

}(window.$));
```

Firebase 提供了一个名为 [createUser](https://www.firebase.com/docs/web/api/firebase/createuser.html) 的 API 方法，我们将使用它来注册我们的用户。
因此，首先使用 firebase url 创建一个 Firebase 对象:

```
var firebaseRef = new Firebase('https://burning-fire-1723.firebaseio.com');
```

单击注册按钮后，我们将从表单中读取电子邮件地址和密码，并调用 Firebase `createUser` API 方法来创建一个新用户。

```
$("#btnSignUp").on("click", function() {
    var email = $('#txtEmailId').val();
    var password = $('#txtPassword').val();
    var ref = new Firebase("https://burning-fire-1723.firebaseio.com");

    ref.createUser({
        email: email,
        password: password
    }, function(error) {
        if (error) {
            switch (error.code) {
                case "EMAIL_TAKEN":
                    console.log("The email is already in use.");
                    break;
                case "INVALID_EMAIL":
                    console.log("The email is not a valid email.");
                    break;
                default:
                    console.log("Error creating user:", error);
            }
        } else {
            console.log("User account created successfully!");
        }
    });
});
```

正如您所看到的，一旦成功执行了`createUser`调用，就可以在浏览器控制台中看到一条成功消息。

保存所有更改，重启服务器，在浏览器中打开`http://localhost:8000/examples/BucketApp/#signup`并注册一个新用户。注册成功后，您将在浏览器控制台中看到成功消息。

让我们添加一个重定向到成功用户注册的登录页面。在成功控制台消息后添加以下代码，以重定向到登录页面。

`window.location.href = "";`

保存更改并重新启动服务器。打开注册页面，尝试使用新的电子邮件地址和密码注册。用户注册成功后，您将被重定向到登录页面。

## [](#conclusion)结论

在本教程中，我们使用较新的 firebase 版本在一个使用 [Mootor 框架](https://mootor.voolks.com/)的 web 应用程序中重新实现了注册功能。我们还根据新的 firebase APIs 修改了登录功能。

关于框架的详细信息可以在[官方文档](http://emi420.github.io/Mootor/)中找到。请在下面的评论中告诉我你的想法！！

## 分享这篇文章