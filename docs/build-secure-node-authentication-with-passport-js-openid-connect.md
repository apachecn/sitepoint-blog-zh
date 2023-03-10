# 使用 Passport.js & OpenID Connect 构建安全节点认证

> 原文：<https://www.sitepoint.com/build-secure-node-authentication-with-passport-js-openid-connect/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/05/18/node-authentication-with-passport-and-oidc)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

使用 Passport.js 可以在 Node 中轻松构建本地或社交登录。已经构建了 500 多种策略，可以轻松连接身份提供者。但是，如果您的身份提供商没有预先构建的策略，您该怎么办呢？你必须自己建造所有的东西吗？绝对不行！您可以对 Passport.js 使用通用策略，这样就可以轻松使用您选择的提供者，而不必自己编写所有的管道。在本教程中，我们将介绍如何使用我选择的身份提供者(Okta)和通用的`passport-openidconnect`包来构建安全的节点认证和用户管理！

在我们开始之前，让我告诉你 Okta 是什么，以及为什么我认为 Okta 是你的下一个节点项目的显而易见的选择。

## Okta 是什么？

Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

简而言之:我们让[用户账户管理](https://developer.okta.com/product/user-management/)比你可能习惯的更容易、更安全、更可扩展。

听起来很棒？[注册一个免费的开发人员帐户](https://developer.okta.com/signup/)，完成后，请回来，这样我们可以了解更多关于在 Node 中构建安全认证的信息。

现在，让我们开始吧！

## 使用 Express 搭建基本节点认证项目

如果您还没有安装[Express application generator](https://expressjs.com/en/starter/generator.html)，请从安装它开始。

```
npm install express-generator -g 
```

然后使用`express`命令搭建一个基础节点和 Express 应用程序。

```
express -e --git passport-oidc-example 
```

生成器将在 **passport-oidc-example** 文件夹中快速创建一个新的应用程序。它为视图模板使用嵌入式 JavaScript 语法，并将生成一个基本的**。gitignore** 文件。在输出的底部会有说明告诉你如何进行。

```
change directory:
  $ cd passport-oidc-example

install dependencies:
  $ npm install

run the app:
  $ DEBUG=passport-oidc-example:* npm start 
```

继续转到新目录，并安装依赖项。我使用 [Visual Studio 代码](https://code.visualstudio.com/)进行我的节点开发，它对编写和调试节点应用程序有很大的支持。它可以在所有平台上运行，并且完全免费。运行带有调试器的应用程序就像点击`F5`键一样简单！

一旦安装了 VS 代码，就可以使用`code`命令从命令行打开项目。

```
code . 
```

现在，点击`F5`键运行应用程序，它将在输出窗口中启动节点调试器。打开浏览器到 http://localhost:3000，并确保您的基本应用程序正在运行。

![The initial application running.](img/264e9bf4296eae248704d13c720ffefb.png)

## 将 Passport.js 添加到节点应用程序中

首先，您需要三个 npm 包:

*   护照
*   passport-openidconnect
*   快速会话

```
npm install passport@0.4.0 passport-openidconnect@0.0.2 express-session@1.15.6 --save 
```

一旦安装好，打开应用程序根文件夹中的`app.js`文件，并将 Passport.js 添加到需求中，这样文件的顶部看起来就像:

```
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan');
var session = require('express-session');
var passport = require('passport');
var OidcStrategy = require('passport-openidconnect').Strategy; 
```

## 将 Express 配置为使用 Passport.js

一旦用户登录，Passport 就依靠`express-session`来存储用户信息。要配置它，就在下面一行:

```
app.use(express.static(path.join(__dirname, 'public'))); 
```

添加配置。

```
app.use(session({
  secret: 'MyVoiceIsMyPassportVerifyMe',
  resave: false,
  saveUninitialized: true
})); 
```

在它的正下方，添加配置，告诉 Express 使用 Passport 进行会话。

```
app.use(passport.initialize());
app.use(passport.session()); 
```

## 创建一个 Okta 应用程序来支持节点认证

如果您还没有帐户(并且在本教程开始时还没有创建)，是时候注册一个帐户了！登录 Okta 仪表板后，点击**应用**菜单项，然后点击**添加应用**。从向导中选择 **Web** 并点击**下一步**。

![Create web application](img/c9299503a9a0849eb3f42d5e497f1761.png)

在**应用程序设置**屏幕上，命名应用程序(我将我的命名为“PassportOidc”)，并更改**基地 URIs** 和**登录重定向 URIs** 设置的端口。然后点击**完成**。

![The application settings](img/b1a9728263e8fcf02c81492c34db7039.png)

## 为 OpenID 连接配置 Passport.js

现在您将配置 Passport.js 以使用 Okta 作为您的身份提供者(IdP)。为此，在上一节的 Passport.js 配置下面，告诉 Passport 使用需求中创建的`OidcStrategy`。

```
// set up passport
passport.use('oidc', new OidcStrategy({
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  authorizationURL: 'https://{yourOktaDomain}/oauth2/default/v1/authorize',
  tokenURL: 'https://{yourOktaDomain}/oauth2/default/v1/token',
  userInfoURL: 'https://{yourOktaDomain}/oauth2/default/v1/userinfo',
  clientID: '{ClientID}',
  clientSecret: '{ClientSecret}',
  callbackURL: 'http://localhost:3000/authorization-code/callback',
  scope: 'openid profile'
}, (issuer, sub, profile, accessToken, refreshToken, done) => {
  return done(null, profile);
})); 
```

上面的代码将策略的名称设置为‘oidc ’,并设置了策略需要知道的所有 URL，以完成 OpenID Connect 的授权代码流。颁发者是您注册 Okta 开发者帐户时为您创建的授权服务器的 URL。您可以通过点击 Okta 仪表板中的 **API** 并选择**授权服务器**选项卡来查看它。要找到`authorizationURL`、`tokenURL`和`userInfoURL`设置，您可以点击默认授权服务器并查看其设置。有一个**元数据 URI** 设置，通过点击链接，将向您显示`.well-known`文档。这个文档告诉任何使用这个授权服务器的人它可以提供的信息和端点。

![The default authorization server settings](img/8f330812c9722381cd05b4dfc48abada.png)

最后一个参数是一个函数，它将身份验证调用返回的配置文件对象推入到`req.user`中，以便您可以在路由处理程序中使用它。您可以操作传入的对象，使其包含其他信息，或者保存/更新数据库中的用户。

您还需要告诉 Passport.js 如何将用户信息序列化到会话中。为此，您将在刚刚设置的配置下添加方法。

```
passport.serializeUser((user, next) => {
  next(null, user);
});

passport.deserializeUser((obj, next) => {
  next(null, obj);
}); 
```

## 调用 Passport.js

Passport.js 最不需要的是应用程序中的两个端点:一个启动登录流，另一个处理来自 OpenID Connect 提供者的回调。您可以将这两条路由放在索引路由器和用户路由器的`app.use()`方法的正下方。

```
app.use('/login', passport.authenticate('oidc'));

app.use('/authorization-code/callback',
  passport.authenticate('oidc', { failureRedirect: '/error' }),
  (req, res) => {
    res.redirect('/');
  }
); 
```

现在，您可以运行这个应用程序并导航到登录路径，它将带您浏览登录流程并返回到您的主页。但是没有任何东西可以直观地证明登录成功，并且在请求参数上有一个可用的用户对象。

为此，创建一个显示登录用户名的配置文件页面。从侧写路线开始。

```
app.use('/profile', (req, res) => {
  res.render('profile', { title: 'Express', user: req.user });
}); 
```

然后在**视图**文件夹中添加一个 **profile.ejs** 文件。

```
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1><%= title %></h1>
    <p>Welcome <%= user.displayName %>!</p>
  </body>
</html> 
```

然后，为了使事情变得简单一点，在主页上添加一个登录链接。

```
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1><%= title %></h1>
    <p>Welcome to <%= title %></p>
    <a href="/login">Log In</a>
  </body>
</html> 
```

现在，当您运行应用程序时，您可以单击**登录**链接，启动登录流程，并看到显示用户名的个人资料页面！

应用程序仍有问题。任何人都可以进入配置文件路径并导致错误发生。如果请求会话中没有用户，则视图中没有要传递和显示的内容。

为了确保只有登录的用户才能访问配置文件页面，需要添加一个中间件功能。

```
function ensureLoggedIn(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }

  res.redirect('/login')
} 
```

该函数检查请求的`isAuthenticated()`方法，并在用户登录的情况下将请求传递给下一个处理程序。如果没有，它会将用户重定向到登录页面，然后开始登录过程。

现在，将中间件添加到您需要保护的路由中。在这种情况下，现在只有侧面路线。

```
app.use('/profile', ensureLoggedIn, (req, res) => {
  res.render('profile', { title: 'Express', user: req.user });
}); 
```

现在，如果您手动尝试转到个人资料页面，您将被转到登录流程，然后在登录后返回到个人资料页面。但是仍然缺少一些东西。

## 注销 Passport.js 会话

最后一点是能够终止登录会话，并将用户重定向回主页。首先，创建一个路由来处理注销路由。在授权回调路由的正下方，添加一个新路由。

```
app.get('/logout', (req, res) => {
  req.logout();
  req.session.destroy();
  res.redirect('/');
}); 
```

就是这么简单。这个路由处理程序对传入的请求调用`logout()`方法，销毁会话，并将用户重定向到主页。

这就是让 Passport.js 处理 OpenID Connect 身份验证提供者的所有基础，该提供者在 Passport.js 库中还没有特定的策略！

## 了解关于节点、身份验证和 Okta 的更多信息

无法获得足够的节点？查看我们关于 Node 的[快速入门和 Okta 开发者博客上的其他很酷的帖子，比如我们关于](https://developer.okta.com/code/nodejs/)[简单节点认证](https://developer.okta.com/blog/2018/04/24/simple-node-authentication)的帖子，以及我关于[用户注册 Node 和 React](https://developer.okta.com/blog/2018/02/06/build-user-registration-with-node-react-and-okta) 的帖子。

一如既往，欢迎在 Twitter [@oktadev](https://twitter.com/oktadev) 上联系我们或在下面留下评论，别忘了看看我们的 [YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)！

## 分享这篇文章