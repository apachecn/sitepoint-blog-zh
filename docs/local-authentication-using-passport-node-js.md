# Node.js 中使用 Passport 的本地身份验证

> 原文：<https://www.sitepoint.com/local-authentication-using-passport-node-js/>

构建 web 应用时的一个常见需求是实现一个登录系统，这样用户就可以在访问受保护的视图或资源之前验证自己的身份。幸运的是，对于那些构建节点应用程序的人来说，有一个名为 [Passport](http://www.passportjs.org/) 的中间件，可以放入任何基于 [Express](https://expressjs.com/) 的 web 应用程序中，只需几个命令就可以提供认证机制。

在本教程中，我将演示如何使用 Passport 通过 MongoDB 后端实现本地身份验证(即使用用户名和密码登录)。如果你想通过脸书或 GitHub 实现认证，请参考本教程。

与以往一样，本文的所有代码都可以在 [GitHub](https://github.com/sitepoint-editors/LocalPassportAuth) 上下载。

## 先决条件

按照本教程，您需要在您的机器上安装 Node 和 MongoDB。

你可以通过前往[的官方节点下载页面](https://nodejs.org/en/download/)并获取适合你系统的正确二进制文件来安装**节点**。或者，您可以使用版本管理器—一个允许您安装多个版本的 Node 并随意在它们之间切换的程序。如果你想走这条路，请参考我们的快速提示，“[使用 nvm](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/) 安装多个版本的 Node.js”。

**MongoDB** 有各种版本。我们感兴趣的是 MongoDB 社区版。

该项目的主页有很好的文档，我不会在这里重复。相反，我将为您提供每个主要操作系统的说明链接:

*   [在 Windows 上安装 MongoDB 社区版](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)
*   [在 macOS 上安装 MongoDB 社区版](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)
*   [在 Ubuntu 上安装 MongoDB 社区版](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

如果你使用的不是基于 Ubuntu 的 Linux 版本，你可以查看[这个页面](https://docs.mongodb.com/manual/installation/#mongodb-community-edition-installation-tutorials)来获得其他发行版的安装说明。MongoDB 通常也可以通过官方的 Linux 软件渠道获得，但有时这会带来一个过时的版本。

*注:下载 MongoDB 不需要输入姓名和地址。如果出现提示，您通常可以关闭该对话框。*

如果你想快速复习一下如何使用 MongoDB，请查看我们的初学者指南“MongoDB 简介”。

## 身份验证策略:会话 vs JWT

在我们开始之前，让我们简单地讨论一下身份验证的选择。

如今，许多在线教程会选择使用 [JSON Web 令牌](https://www.sitepoint.com/using-json-web-tokens-node-js/) (JWTs)进行基于令牌的认证。这种方法可能是当今最简单也是最流行的方法。它将身份验证的部分责任移交给客户端，并让他们签署一个令牌，该令牌随每个请求一起发送，以保持用户得到身份验证。

基于会话的认证已经存在很久了。这种方法将身份验证的任务交给了服务器。它使用 cookies 并看到节点应用程序和数据库协同工作来跟踪用户的身份验证状态。

在本教程中，我们将使用基于会话的认证，这是 [passport-local 策略](http://www.passportjs.org/packages/passport-local/)的核心。

这两种方法各有优缺点。如果您想进一步了解这两者之间的区别，[这个堆栈溢出线程](https://stackoverflow.com/q/43452896)可能是一个不错的起点。

## 创建项目

一旦所有的必备软件都设置好了，我们就可以开始了。

我们将首先为我们的应用程序创建文件夹，然后在终端上访问该文件夹:

```
mkdir AuthApp
cd AuthApp 
```

为了创建节点应用程序，我们将使用以下命令:

```
npm init 
```

系统会提示您为节点的`package.json`提供一些信息。继续点击`Return`接受默认配置(或者使用`-y`标志)。

## 设置 Express

现在我们需要安装 [Express](http://expressjs.com/) 。转到终端，输入以下命令:

```
npm install express 
```

我们还需要安装[主体解析器](https://www.npmjs.com/package/body-parser)中间件，用于解析 Passport 用来验证用户的请求主体。我们还需要安装[快速会话](https://www.npmjs.com/package/express-session)中间件。

就这么办吧。运行以下命令:

```
npm install body-parser express-session 
```

完成后，在应用程序的根文件夹中创建一个`index.js`文件，并向其中添加以下内容:

```
/*  EXPRESS SETUP  */

const express = require('express');
const app = express();

app.use(express.static(__dirname));

const bodyParser = require('body-parser');
const expressSession = require('express-session')({
  secret: 'secret',
  resave: false,
  saveUninitialized: false
});

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
app.use(expressSession);

const port = process.env.PORT || 3000;
app.listen(port, () => console.log('App listening on port ' + port)); 
```

首先，我们`require`通过调用 [express()](http://expressjs.com/en/api.html#express) 来表达和创建我们的 Express 应用程序。然后，我们定义从哪个目录开始为静态文件提供服务。

下一行是正文解析中间件，它将帮助我们解析请求的正文。我们还添加了快速会话中间件来帮助我们保存会话 cookie。

如您所见，我们正在配置 express-session，使用一个`secret`来签署会话 ID cookie(您应该在这里选择一个唯一的值)，以及另外两个字段， [resave](https://github.com/expressjs/session#resave) 和[save initialized](https://github.com/expressjs/session#saveuninitialized)。`resave`字段强制将会话保存回会话存储，`saveUninitialized`字段强制将“未初始化”的会话保存到存储。要了解更多关于它们的信息，请查看它们的文档，但是现在知道对于我们的情况，我们想要保留它们就足够了。

然后，我们使用`process.env.PORT`将端口设置为环境端口变量，如果它存在的话。否则，我们将默认使用`3000`，这是我们将在本地使用的端口。这为您提供了足够的灵活性，可以从开发环境直接切换到生产环境，在生产环境中，端口可以由服务提供商来设置，例如， [Heroku](https://www.heroku.com/) 。就在那下面，我们调用了 [app.listen()](http://expressjs.com/en/api.html#app.listen) ，使用我们设置的端口变量和一个简单的日志让我们知道它一切正常，以及应用程序在监听哪个端口。

快速设置到此为止。现在开始设置`Passport`。

## 设置 Passport

首先，我们用以下命令安装 Passport:

```
npm install passport 
```

然后我们需要在`index.js`文件的底部添加以下几行:

```
/*  PASSPORT SETUP  */

const passport = require('passport');

app.use(passport.initialize());
app.use(passport.session()); 
```

这里，我们需要`passport`，并直接在我们的 Express 应用程序中初始化它及其会话认证中间件。

## 创建 MongoDB 数据存储

因为我们假设您已经安装了 Mongo，所以您应该能够使用以下命令启动 Mongo shell:

```
mongo 
```

在 shell 中，发出以下命令:

```
use MyDatabase; 
```

这只是创建了一个名为`MyDatabase`的数据存储。

把终端留在那里；我们稍后再来讨论。

## 用 Mongoose 将 Mongo 连接到节点

现在我们有了一个包含记录的数据库，我们需要一种从应用程序与它通信的方法。我们将使用[猫鼬](http://mongoosejs.com/)来实现这一点。为什么我们不用普通的蒙哥呢？嗯，正如猫鼬开发者喜欢说的，A href = " https://mongose js . com/docs/unstable/index . html ">在他们的网站上:

> 编写 MongoDB 验证、类型转换和业务逻辑样板文件是一件很麻烦的事情。

Mongoose 只会让我们的生活更简单，代码更优雅。

让我们继续用下面的命令安装它:

```
npm install mongoose 
```

我们还将使用[passport-local-mongose](https://www.npmjs.com/package/passport-local-mongoose)，这将简化 mongose 和 Passport 之间的本地认证集成。它将向我们的模式添加一个`hash`和`salt`字段，以便存储散列密码和 salt 值。这很好，因为[密码永远不应该以纯文本的形式存储在数据库](https://security.stackexchange.com/a/120541)中。

让我们安装软件包:

```
npm install passport-local-mongoose 
```

现在我们必须配置 Mongoose。希望您现在已经知道该如何操作了:将下面的代码添加到您的`index.js`文件的底部:

```
/* MONGOOSE SETUP */

const mongoose = require('mongoose');
const passportLocalMongoose = require('passport-local-mongoose');

mongoose.connect('mongodb://localhost/MyDatabase',
  { useNewUrlParser: true, useUnifiedTopology: true });

const Schema = mongoose.Schema;
const UserDetail = new Schema({
  username: String,
  password: String
});

UserDetail.plugin(passportLocalMongoose);
const UserDetails = mongoose.model('userInfo', UserDetail, 'userInfo'); 
```

这里我们需要以前安装的软件包。然后我们使用`mongoose.connect`连接到我们的数据库，并给它数据库的路径。接下来，我们将利用一个[模式](http://mongoosejs.com/docs/guide.html)来定义我们的数据结构。在本例中，我们创建了一个带有`username`和`password`字段的`UserDetail`模式。

最后，我们将`passportLocalMongoose`作为插件添加到我们的模式中。这将发挥我们之前讨论的部分魔力。然后，我们从这个模式中创建一个[模型](http://mongoosejs.com/docs/models.html)。第一个参数是数据库中集合的名称。第二个是对我们模式的引用，第三个是我们分配给 Mongoose 内部集合的名称。

这就是猫鼬设置。我们现在可以继续实施我们的护照战略。

## 实现本地身份验证

最后，这就是我们来这里的目的！让我们设置本地身份验证。正如您将在下面看到的，我们将只编写代码来为我们设置它:

```
/* PASSPORT LOCAL AUTHENTICATION */

passport.use(UserDetails.createStrategy());

passport.serializeUser(UserDetails.serializeUser());
passport.deserializeUser(UserDetails.deserializeUser()); 
```

这里发生了不少神奇的事情。首先，我们通过在我们的`UserDetails`模型上调用`createStrategy()`来让`passport`使用本地策略——这是由`passport-local-mongoose`提供的——它会处理所有事情，因此我们不必设置策略。非常方便。

然后我们使用`serializeUser`和`deserializeUser`回调。第一个将在身份验证时调用，它的工作是用我们传递给它的信息序列化用户实例，并通过 cookie 将其存储在会话中。第二个将在每个反序列化实例的后续请求中被调用，为它提供惟一的 cookie 标识符作为“凭证”。你可以在[护照文档](http://www.passportjs.org/docs/configure/#sessions)中了解更多。

## 路线

现在，让我们添加一些路线，将一切联系在一起。首先，我们将添加一个最终包。转到终端并运行以下命令:

```
npm install connect-ensure-login 
```

[connect-assure-log in](https://www.npmjs.com/package/connect-ensure-login)包是确保用户登录的中间件。如果收到未经身份验证的请求，该请求将被重定向到登录页面。我们用这个来保护我们的路线。

现在，将以下内容添加到`index.js`的底部:

```
/* ROUTES */

const connectEnsureLogin = require('connect-ensure-login');

app.post('/login', (req, res, next) => {
  passport.authenticate('local',
  (err, user, info) => {
    if (err) {
      return next(err);
    }

    if (!user) {
      return res.redirect('/login?info=' + info);
    }

    req.logIn(user, function(err) {
      if (err) {
        return next(err);
      }

      return res.redirect('/');
    });

  })(req, res, next);
});

app.get('/login',
  (req, res) => res.sendFile('html/login.html',
  { root: __dirname })
);

app.get('/',
  connectEnsureLogin.ensureLoggedIn(),
  (req, res) => res.sendFile('html/index.html', {root: __dirname})
);

app.get('/private',
  connectEnsureLogin.ensureLoggedIn(),
  (req, res) => res.sendFile('html/private.html', {root: __dirname})
);

app.get('/user',
  connectEnsureLogin.ensureLoggedIn(),
  (req, res) => res.send({user: req.user})
); 
```

在顶部，我们要求`connect-ensure-login`。我们稍后将回到这一点。

接下来，我们设置一个路由来处理对`/login`路径的 POST 请求。在处理程序内部，我们使用 [passport.authenticate](http://www.passportjs.org/docs/authenticate/) 方法，该方法尝试使用它接收的策略作为第一个参数进行身份验证——在本例中为`local`。如果认证失败，它会将我们重定向到`/login`，但是它会添加一个查询参数——`info`——其中会包含一条错误消息。否则，如果认证成功，它会将我们重定向到`'/'`路线。

然后我们设置`/login`路由，它会发送登录页面。为此，我们使用 [res.sendFile()](http://expressjs.com/en/api.html#res.sendFile) 并传入文件路径和我们的根目录，这是我们正在处理的目录，因此有了`__dirname`。

这条路线对任何人来说都是可行的，但我们接下来的路线就不行了。在`/`和`/private`路线中，我们将发送它们各自的 HTML 页面，您会注意到这里有些不同。在回调之前，我们将添加`connectEnsureLogin.ensureLoggedIn()`调用。这是我们的路线守卫。它的工作是验证会话，以确保您被允许查看该路线。现在你明白我之前说的“让服务器来做繁重的工作”是什么意思了吧？我们每次都在认证用户。

最后，我们需要一个`/user` route，它将返回一个包含用户信息的对象。这只是向您展示如何从服务器获取信息。我们将从客户端请求这条路线并显示结果。

说到客户，我们现在就开始吧。

## 客户

客户端应该很简单。我们将创建一些`HTML`页面和一个`CSS`文件。让我们从主页或索引开始。在项目根目录下，创建一个名为`html`的文件夹，并添加一个名为`index.html`的文件。添加以下内容:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title> Home </title>
  <link rel="stylesheet" href="css/styles.css">
</head>

<body>
  <div class="message-box">
    <h1 id="welcome-message"></h1>
    <a href="/private">Go to private area</a>
  </div>

  <script> const req = new XMLHttpRequest();
    req.onreadystatechange = function () {
      if (req.readyState == 4 && req.status == 200) {
        const user = JSON.parse(req.response).user;
        document.getElementById("welcome-message").innerText = `Welcome ${user.username}!!`;
      }
    };
    req.open("GET", "http://localhost:3000/user", true);
    req.send(); </script>
</body>
</html> 
```

这里我们有一个空的`h1`标签，用来放置我们的欢迎信息，下面是一个到`/private`的链接。这里的关键部分是底部的`script`标签，我们将在这里处理获取用户名来创建欢迎消息。

这分为四个部分:

1.  我们使用`new XMLHttpRequest()`实例化请求对象。
2.  我们用得到答案后将被调用的函数来设置`onreadystatechange`属性。在回调中，我们检查是否获得了成功的响应，如果是，我们解析响应，获得用户对象(我们在`/user`路由中发送的对象，还记得吗？)，然后我们找到`welcome-message`元素，将它的`innerText`设置为我们的`user.username`。
3.  我们将`GET`请求发送给用户`URL`，并将最后一个参数设置为`true`，使其成为`asynchronous`。
4.  最后，我们`send()`了这个请求。

现在我们将创建登录页面。像以前一样，在 HTML 文件夹中创建一个名为`login.html`的文件，并向其中添加以下内容:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <title> Login </title>
  <link rel="stylesheet" href="css/styles.css">
</head>

<body>
  <form action="/login" method="post">
    <div class="title">
      <h3>Login</h3>
    </div>
    <div class="field">
      <label>Username:</label>
      <input type="text" name="username" />
      <br />
    </div>
    <div class="field">
      <label>Password:</label>
      <input type="password" name="password" required />
    </div>
    <div class="field">
      <input class="submit-btn" type="submit" value="Submit" required />
    </div>
    <label id="error-message"></label>
  </form>

  <script> const urlParams = new URLSearchParams(window.location.search);
    const info = urlParams.get('info');

    if(info) {
      const errorMessage = document.getElementById("error-message");
      errorMessage.innerText = info;
      errorMessage.style.display = "block";
    } </script>
</body>
</html> 
```

在这个页面上，我们有一个简单的登录表单，有`username`和`password`字段，以及一个*提交*按钮。在那下面，我们有一个标签，我们将在那里显示任何错误消息。记住，这些都包含在查询字符串中。

底部的`script`标签这次简单多了。我们正在实例化一个传递`window.location.search`属性的`URLSearchParams`对象，该属性包含 URL 中的参数字符串。然后我们使用`URLSearchParams.get()`方法，传入我们正在寻找的参数名。

此时，我们要么有信息消息，要么没有。因此，如果我们这样做了，我们获得了`error-message`元素，并将其`innerText`设置为消息本身，然后将其`style.display`属性设置为`block`。这将使它可见，因为默认情况下它有一个`display: "none"`值。

让我们现在建立个人主页。同样，在 HTML 文件夹中创建一个名为`private.html`的文件，并添加以下内容:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <title> Private </title>
  <link rel="stylesheet" href="css/styles.css">
</head>

<body>
  <div class="message-box">
    <h2>This is a private area</h2>
    <h3>Only you can see it</h3>
    <a href="/">Go back</a>
  </div>
</body>
</html> 
```

超级简单。只有一条简单的消息和一个带我们回到主页的`Go back`链接。

这就是 HTML，但是你可能注意到了，我们引用了一个`CSS`文件的`head`标签。让我们现在添加该文件。在我们项目的根目录下创建一个名为`css`的文件夹，并在其中添加一个`styles.css`文件，内容如下:

```
body {
  display: flex;
  align-items: center;
  background: #37474F;
  font-family: monospace;
  color: #cfd8dc;
  justify-content: center;
  font-size: 20px;
}

.message-box {
  text-align: center;
}

a {
  color: azure;
}

.field {
  margin: 10px;
}

input {
  font-family: monospace;
  font-size: 20px;
  border: none;
  background: #1c232636;
  color: #CFD8DC;
  padding: 7px;
  border: #4c5a61 solid 2px;
  width: 300px;
}

.submit-btn {
  width: 100%
}

.title {
  margin: 10px 0px 20px 10px
}

#error-message {
  color: #E91E63;
  display: block;
  margin: 10px;
  font-size: large;
  max-width: fit-content;
} 
```

这将使我们的网页看起来足够体面。我们去看看吧！

抓取一个指向项目根目录的终端，并运行以下命令:

```
node index.js 
```

现在在浏览器中导航到 [http://localhost:3000/](http://localhost:3000/) 。您应该会被重定向到登录页面。如果你尝试去[http://localhost:3000/private](http://localhost:3000/private)，它应该会再次把你重定向到登录页面。我们的护路员在做他们的工作。

在终端窗口按`Ctrl` + `C`停止我们的服务器。然后返回到`index.js`文件，在文件的底部，添加以下几行:

```
/* REGISTER SOME USERS */

UserDetails.register({username:'paul', active: false}, 'paul');
UserDetails.register({username:'jay', active: false}, 'jay');
UserDetails.register({username:'roy', active: false}, 'roy'); 
```

它使用 passport-local-mongose`register`方法为我们加盐密码。我们只需要以纯文本的形式传递它。

现在我们运行`node index.js`。将创建用户。你现在应该注释最后几行。

还记得我们打开的 MongoDB shell 终端吗？返回并键入:

```
db.userInfo.find() 
```

这应该显示您的三个用户，正如您所看到的，salt 和 hash 现在占据了终端上很大一部分空间。

这就是我们需要的应用程序的工作。我们完了！

回到浏览器，尝试使用我们输入的凭证之一登录，您将看到包含给定用户名的登录消息。

## 后续步骤

我们只添加了该应用程序运行所需的模块——不多也不少。对于生产应用程序，您需要添加其他中间件，并将您的代码分成模块。您可以将此视为建立一个干净且可扩展的环境并将其发展成有用的东西的挑战！

您应该尝试的第一件也是最简单的事情是添加`logout`，使用 Passport 的 [req.logout()](http://www.passportjs.org/docs/logout/) 方法。

然后，您可以尝试实现一个注册流。你需要一张登记表和一条谈话路线。您应该使用我们之前添加的`UserDetails.register()`作为模板。对于电子邮件确认，你应该检查一下[节点邮件](https://www.npmjs.com/package/nodemailer)。

您可以做的另一件事是尝试将这些概念应用到单页面应用程序中。可能用的是 Vue.js 和它的路由器。你的周末泡汤了！

## 结论

嗯，我们终于到终点了。在本文中，我们学习了如何在一个`Node.js`应用程序中使用`Passport`实现本地认证。在这个过程中，我们还学习了如何使用`Mongoose`连接到`MongoDB`。

也许对你来说，这并不像我试图描绘的那样容易，但至少你可以看到，这些工具在后台发挥了一些神奇的作用，让我们只担心我们试图构建的东西。

“神奇”的工具并不总是理想的，但是有信誉的和积极维护的工具可以帮助我们编写更少的代码——你不写的代码就是你不维护的代码，你不维护的代码就是你不破坏的代码。

此外，请记住，如果一个工具是由核心团队积极维护的，那么他们很可能比我们任何人都更清楚自己在做什么。尽可能委派。

我希望你喜欢这个教程，也许对你的下一个项目有所启发。编码快乐！

## 分享这篇文章