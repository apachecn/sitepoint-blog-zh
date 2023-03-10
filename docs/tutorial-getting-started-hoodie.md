# 帽衫入门教程

> 原文：<https://www.sitepoint.com/tutorial-getting-started-hoodie/>

JavaScript 已经走了很长的路，而 **[帽衫](http://hood.ie/)** 走得更远。这是我最近遇到的最酷的网络开发工具之一。它有助于构建 web 应用程序，而无需担心后端开发、数据库和服务器。想象一下！看来有人真的让我们的生活变得轻松了。

使用 Hoodie，您可以创建完全由 HTML、CSS 和 JavaScript 等前端技术构建的 web 应用程序，同时允许数据操作，而无需担心服务器端编码。它为您提供诸如用户注册、登录、重置密码、数据存储和检索以及电子邮件支持等功能。只要写几行代码就可以使用。

帽衫最适合使用上述前端技术创建移动应用程序。它利用了[离线优先](http://offlinefirst.org/)和[无备份](http://nobackend.org/)架构，增强了应用程序的性能。Hoodie 遵循基于文档的存储方式，使用 [CouchDB](http://couchdb.apache.org/) 存储数据。每当需要更新数据时，Hoodie 首先将数据存储在本地，然后尽最大努力与中央服务器同步。

在本文中，我们将在本地计算机上安装并运行 Hoodie。我们将创建并分析一个简单的帽衫应用程序。最后，我们将使用 Hoodie 的重要功能创建一个功能性的 web 应用程序。

## 安装帽衫

帽衫支持所有主要平台，如 OSX，Linux 和 Windows。在本教程中，我将展示如何在 Mac 上安装帽衫。对于其他平台，您可以遵循[安装文档](http://hood.ie/#installation)。

要在 OSX 上安装 Hoodie，你需要预装几个工具，即 [Homebrew](http://brew.sh/) 、 [NodeJS](http://nodejs.org/) 、git 和 CouchDB。让我们一个一个来。

启动终端并键入以下命令。

```
$ brew update
```

这个命令会将自制软件更新到最新版本。接下来，我们将使用以下命令安装 git。

```
$ brew install git
```

现在，我们将使用以下命令下载并安装 NodeJS:

```
$ brew install node
```

是时候下载并安装 CouchDb 了，这个数据库是 Hoodie 用来处理数据的。

```
$ brew install couchdb
```

安装完以上所有工具后，我们将使用 Node 的包管理器下载并安装 Hoodie。

```
$ npm install -g hoodie-cli
```

您现在可以通过在终端中键入`hoodie`来验证您的连帽衫安装。将显示图形帽衫标志以及其他帮助选项。

![hoodie startup](img/29688876967f5db5a70392a9d904101a.png)

## 创建一个简单的帽衫应用

Hoodie 提供了许多方便的命令来执行重要的任务。其中最重要的是:

```
$ hoodie new <appname>
```

上述命令创建了一个新的默认帽衫应用程序。这个 app 一般是帽衫团队打造的 Todo app。它给出了许多帽衫方法的概述，并展示了一些重要的功能，如注册/登录，重置密码等。

让我们继续创建一个名为 **`SitePointTodosApp`** 的新帽衫应用程序。

```
$ hoodie new SitePointTodosApp
```

![Creating an app](img/f77789184d7c18bf95fcedee54593c2a.png)

如上图所示，Hoodie 将提示您在新创建的“SitePointTodosApp”文件夹中导航，然后启动 Hoodie 服务器。因此，让我们开始并运行应用程序。

```
$ cd SitePointTodosApp

$ hoodie start
```

![App is running](img/5f29ebce4a3efa3562ceb2b9690d03dc.png)

您将被要求为该应用程序创建一个管理员密码。然后，Hoodie 将在本地主机的端口 6007 上启动应用程序。(对您而言，这可能是不同的端口)

*http://127.0.0.1:6007/*

![App running in a web browser](img/db94058bf2c9bfedcf91b0152918949b.png)

你会看到一个名为“SitePointTodosApp”的 web 应用程序，它具有 Todo 应用程序的所有基本功能。我建议你玩这个应用程序中展示的所有功能。尝试自己注册，创建一些待办事项并注销。切换浏览器，用你的凭证登录，你可以看到同样的待办事项出现在屏幕上。

要停止应用程序，请在您的终端中点击`Ctrl-c`。

现在是时候进入应用程序文件夹，分析是什么造就了一个帽衫应用程序。在`/SitePointTodosApp`文件夹中，你会发现许多子文件夹和文件。

![Hoodie App folder structure](img/41e96a42474033d085f10a79b24f034c.png)

1.  **/www**
    这是 app 的主文件夹，里面有我们 App 的前端文件。我们将在创建下一个应用程序时使用这个文件夹。该文件夹包含一个保存 CSS、JS、图像和其他供应商文件的`/assets`文件夹。它还包含主`index.html`文件。
2.  **/node_modules** NodeJS 已经下载了**帽衫-服务器**、**帽衫-插件-appconfig** 、**帽衫-插件-邮箱**、**帽衫-插件-用户**。这些依赖关系在根文件夹中的文件 **`package.json`** 中提到。
3.  **/data**
    该文件夹包含与 CouchDB 相关的文件。我们不会在这个文件夹上花太多时间。
4.  这是一份部署指南。它包含安装依赖工具的名称、连帽衫的设置说明、运行连帽衫 app 说明等。
5.  **README.md**
    这是一篇关于帽衫的简短友好的自述。你可以阅读它来了解更多关于帽衫的信息。

让我们打开`/www/index.html`文件，开始分析一些行。

下面是标记中的一段代码。

```
<link rel="stylesheet" href="assets/vendor/bootstrap/bootstrap.min.css">
<link rel="stylesheet" href="assets/vendor/prism/prism.css">
<link rel="stylesheet" href="assets/css/main.css">
```

令我们惊讶的是，帽衫已经为我们下载了 Bootstrap。它使用的是 Bootstrap v2.1.1。您可以随时手动更改文件以将其更新到最新版本。Hoodie 还使用了一个名为“PrismJS”的插件来突出显示默认主页中的代码片段。我们将不会使用 PrismJS，因此一旦我们开始构建我们的应用程序，就会删除它。 **`main.css`** 是主要的应用级 CSS 文件。如果你愿意，你可以改变名字。

默认的帽衫应用程序也加载了许多 JS 脚本。它们如下:

```
<script src="assets/vendor/jquery-2.1.0.min.js"></script>
<script src="assets/vendor/bootstrap/bootstrap.js"></script>
<script src="assets/vendor/prism/prism.js"></script>
<!-- Load the dynamic version of hoodie.js that includes all the plugin code-->
<script src="/_api/_files/hoodie.js"></script>
<script src="assets/vendor/hoodie.accountbar.bootstrap.js"></script>
<script src="assets/vendor/bootstrap.modalform.js"></script>
<script src="assets/js/main.js"></script>
```

Hoodie 在上述脚本中已经包含了 jQuery、Bootstap 和 Prism 的 JS 文件。然后它包含了一个动态生成的`hoodie.js`文件。这个文件是所有插件代码的组合。js 文件。这是由位于`/node_modules`内部的节点模块`hoodie-server`完成的。然后我们有另外两个插件叫做“Bootstrap ModalForm”和“Hoodie AccountBar”。我们不会在下一个应用程序中使用这些脚本。最后，我们有一个`main.js`文件，它是应用程序级别的 JS 文件。

## 使用 Hoodie 创建一个用户注册表单

[可从 GitHub 下载的最终项目](https://github.com/sitepoint-examples/hoodieapp-sitepoint-demos)

在本节中，我们将创建一个简单的用户注册表单，并了解如何通过 jQuery 向数据库写入新用户。我们还将把我们的应用程序升级到 Bootstrap 3。

我使用的是上一节中创建的同一个帽衫应用程序`SitePointTodosApp`。让我们修改`index.html`并删除所有不需要的插件。下面是修改后的`index.html`:

**HTML**

```
<!DOCTYPE html>
<html class="no-js">
<head>
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
<title>User Registration</title>
<meta name="description" content="">
<meta name="viewport" content="width=device-width">

<link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css">
<link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap-theme.min.css">
<link href='http://fonts.googleapis.com/css?family=Handlee' rel='stylesheet' type='text/css'>
<link rel="stylesheet" href="assets/css/main.css">
</head>
<body>

<script src="assets/vendor/jquery-2.1.0.min.js"></script>
<script src="//netdna.bootstrapcdn.com/bootstrap/3.1.1/js/bootstrap.min.js"></script>
<!-- Load the dynamic version of hoodie.js that includes all the plugin code-->
<script src="/_api/_files/hoodie.js"></script>
<script src="assets/js/main.js"></script>
</body>
</html>
```

如您所见，我已经从 tag 中移除了除脚本之外的所有 HTML 内容。我还在和中将 Bootstrap 的源文件从 Bootstrap v2 替换为 v3。如果你把这个和旧的`index.html`比较一下，我还移除了默认自带的“棱镜”、“帽衫账号栏”和“Bootstrap Modalform”插件。

让我们继续用新的表单标记填充 HTML 页面。

**HTML**

```
<div class="container-fluid">
<!--Markup for pre-registration-->
<div class="col-xs-6 col-xs-offset-3" id="registrationPage">
<h3>Enter your details</h3>
<hr>
<form class="form-horizontal" id="registerForm">
<div class="form-group">
<label for="username" class="col-sm-2 control-label">Email</label>
<div class="col-sm-10">
<input type="email" class="form-control" id="username" placeholder="Email">
</div>
</div>
<div class="form-group">
<label for="password" class="col-sm-2 control-label">Password</label>
<div class="col-sm-10">
<input type="password" class="form-control" id="password" placeholder="Password">
</div>
</div>
<div class="form-group">
<div class="col-sm-offset-2 col-sm-10">
<button type="submit" class="btn btn-success">Register</button>
<button type="reset" class="btn btn-default">Clear</button>
</div>
</div>
</form>
</div>

<!--Markup for post-registration-->
<div class="col-xs-6 col-xs-offset-3" id="userPage">
<h2></h2>
<a href="#" id="signout" class="btn btn-sm btn-default">Sign out</a>
</div>
</div>
```

![Account creation screen](img/3d4e22321f5c30e94eb8bd4ea69c473f.png)

我使用 Bootstrap 的类来设计表单。如果你不知道 Bootstrap 3，我建议你看看这个很棒的框架。阅读 SitePoint 的“ **[跳转启动引导程序](https://learnable.com/books/jump-start-bootstrap)** ”了解更多信息。

我将上述标记分为两部分:注册前标记和注册后标记。预注册标记包含接受用户名和密码的表单字段。注册后标记包含一个 *h2* 元素和一个“注销”按钮。 *h2* 元素将用于在注册后填充用户名。

是时候更新 CSS 文件`main.css`，隐藏注册后的标记了，该标记包含在 id 为`userPage`的 *div* 元素中。

打开`main.css`并删除所有之前编写的 CSS 属性和规则。添加以下内容:

**CSS (main.css)**

```
#userPage{
display: none;
}
```

是时候写一些 jQuery 了。打开`main.js`，清除所有之前写的 JavaScript 代码。

**JS**

```
"use strict";
var hoodie = new Hoodie();
```

将上述片段插入`main.js`。实例化一个新的帽衫对象是强制性的。这应该在你创建的所有应用程序中完成。这个`Hoodie()`方法来自文件`hoodie.js`。

接下来，声明 jQuery 的`ready`方法。

**JS**

```
$(document).ready(function () {
// all codes goes here
});
```

我们必须编写 jQuery 来捕获提交事件中注册页面的用户名和密码。

**JS**

```
$("#registerForm").submit(function(e){
e.preventDefault();
var username = $("#username").val();
var password = $("#password").val();
hoodie.account.signUp(username, password).done(function (user) {
checkuser();
});
});
```

上面的代码捕获数据，然后将其传递给 Hoodie 的`signUp()`方法来注册新用户。这是注册一个新用户必须要写的唯一一行！用户注册后，您可以使用`.done()`方法来执行一些其他活动。我调用了一个`checkuser()`方法，我们将在下面定义它。

让我们编写一个可重用的 JavaScript 方法来检查用户是否登录。

**JS**

```
function checkuser() {
if(hoodie.account.username){
$("#registrationPage").hide();
$("#userPage").show();
$("#userPage h2").text("Welcome, "+hoodie.account.username+"!");
}else{
$("#registrationPage").show();
$("#userPage").hide();
$("#userPage h2").text("");
}
}
```

上面的方法通过获取`hoodie.account.username`对象的值来检查用户是否登录。当用户**未**登录时，它会导致`undefined`。如果设置好了，我们将显示`#userPage`并更新欢迎信息。我们还隐藏了成功登录时的注册页面。

![Welcome screen](img/60077e6ba7b6430ed50e0413e24ed5ee.png)

让我们使用 jQuery 将一个帽衫函数绑定到注销按钮。

**JS**

```
$("#signout").click(function () {
hoodie.account.signOut().done(function (user) {
checkuser();
});
});
```

我们已经使用了的 Hoodie 的`signOut()`方法来终止用户的会话。

下面是完整版的`main.js`文件:

**JS (main.js)**

```
"use strict";
var hoodie = new Hoodie();

$(document).ready(function () {

checkuser();

$("#registerForm").submit(function(e){
e.preventDefault();
var username = $("#username").val();
var password = $("#password").val();
hoodie.account.signUp(username, password).done(function (user) {
checkuser();
});
});

$("#signout").click(function () {
hoodie.account.signOut().done(function (user) {
checkuser();
});
});
});

function checkuser() {
if(hoodie.account.username){
$("#registrationPage").hide();
$("#userPage").show();
$("#userPage h2").text("Welcome, "+hoodie.account.username+"!");
}else{
$("#registrationPage").show();
$("#userPage").hide();
$("#userPage h2").text("");
}
}
```

如您所见，Hoodie 提供了极其简单的方法来执行用户注册和注销。上面的程序只是一个演示程序，展示了如何在你的代码中使用它们。

## 其他帽衫方法

有许多简单的方法可以让你完成复杂的任务。以下是其中的几个:

#### 登录方法

通过获取用户名和密码并将其传递给`signIn()`方法来登录您的应用程序。

```
hoodie.account.signIn('larry@google.com', 'secret');
```

#### 修改口令

要更改密码，您需要将新旧密码值都传递给`changePassword()`方法。

```
hoodie.account.changePassword('currentpassword', 'newpassword');
```

#### 更改用户名

要更改用户名，请将旧的和新的用户名值都传递给`changeUsername()`方法。

```
hoodie.account.changeUsername('currentpassword', 'newusername');
```

#### 添加新对象

您可以使用 Hoodie 中的`store`对象存储与用户相关的对象。

```
var type = 'notes';
var attributes = {
title: 'History Class 1',
desc: 'Lorem ipsum donor. Lorem ipsum donor.'
};
hoodie.store.add(type, attributes).done(function (newObject) {
console.log("New note created. Object ID:"+newObject.id);
});
```

`store`对象有一个名为`add()`的方法，它有两个参数，类型和属性。这里的类型表现为一个对象类，属性对象是 JSON 数据。您可以添加许多相同类型的对象。如果用户已经登录，这些对象将存储在数据库中，或者存储在本地。

#### 更新对象

要更新数据库中的对象，您需要知道它的 ID，它是自动生成的。

```
var type = 'notes';
var id = 'Ngh123';
var updatedAttributes = {
title: 'History Class 1',
desc: 'Lorem ipsum donor. Lorem ipsum donor. More lorem ipsum donors.'
};
hoodie.store.update(type, id, updatedAttributes).done(function (updatedObject) {
console.log("Note Updated!");
});
```

`update()`方法有 3 个参数:类型、id 和更新的 JSON 对象。

## 从这里去哪里？

帽衫有更多的方法来帮助实现复杂的过程。你可以在这里找到方法[的完整列表。](http://hood.ie/#docs)

当你把它和 MVC 框架如 [AngularJS](https://angularjs.org/) 、 [EmberJS](http://emberjs.com/) 等一起使用时，帽衫会变得更好。在我的下一篇教程中，我将展示如何将帽衫与 AngularJS 搭配使用，敬请关注！

我希望这篇教程对你有帮助。如果你有任何疑问，请在下面的评论区留言。

## 分享这篇文章