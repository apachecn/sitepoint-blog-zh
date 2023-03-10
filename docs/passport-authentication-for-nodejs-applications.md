# Node.js 应用程序的 Passport 身份验证

> 原文：<https://www.sitepoint.com/passport-authentication-for-nodejs-applications/>

在本教程中，我们将在一个 [Node.js](http://nodejs.org/) web 应用程序中通过脸书和 GitHub 实现认证。为此，我们将使用 Node.js 的认证中间件 [Passport](http://passportjs.org/) ，Passport 支持与 [OpenId/OAuth](http://www.passportjs.org/docs/facebook/) 提供者的认证。

## 快速 Web 应用程序

在开始之前，确保您的机器上已经安装了的 [Node.js。](http://nodejs.org/)

我们将首先为我们的应用程序创建文件夹，然后在终端上访问该文件夹:

```
mkdir AuthApp
cd AuthApp 
```

要创建节点应用程序，我们将使用以下命令:

```
npm init 
```

系统会提示您为节点的`package.json`提供一些信息。只需点击`enter`直到结束，以离开默认配置。

接下来，我们需要一个 HTML 文件发送给客户端。在你的 app 的根文件夹下创建一个名为`auth.html`的文件，内容如下:

```
<html>
  <head>
    <title>Node.js OAuth</title>
  </head>
  <body>
    <a href=auth/facebook>Sign in with Facebook</a>
    <br></br>
    <a href=auth/github>Sign in with Github</a>
  </body>
</html> 
```

这就是我们在本教程中需要的所有 HTML。

你还需要 [Express](http://expressjs.com/) ，一个受 Ruby 的 Sinatra 启发的构建网络应用的框架。要安装 Express，请从终端键入以下命令:

```
npm install express --save 
```

一旦你完成了这些，就该写一些代码了。

在您的应用程序的根文件夹中创建一个文件`index.js`，并向其中添加以下内容:

```
/*  EXPRESS SETUP  */

const express = require('express');
const app = express();

app.get('/', (req, res) => res.sendFile('auth.html', { root : __dirname}));

const port = process.env.PORT || 3000;
app.listen(port , () => console.log('App listening on port ' + port)); 
```

在上面的代码中，我们需要 Express 并通过调用 [express()](http://expressjs.com/en/api.html#express) 来创建我们的 Express 应用程序。然后我们为我们的应用程序的主页声明路线。在那里，我们将创建的 HTML 文件发送给访问该路由的客户机。然后，我们使用`process.env.PORT`将端口设置为环境端口变量，如果它存在的话。否则，我们将默认使用`3000`，这是我们将在本地使用的端口。这给了你足够的灵活性，可以从开发环境直接切换到生产环境，在生产环境中，端口可以由服务提供商来设置，例如， [Heroku](https://www.heroku.com/) 。就在下面，我们用我们设置的端口变量调用 [app.listen()](http://expressjs.com/en/api.html#app.listen) ，一个简单的日志让我们知道它一切正常，以及应用程序在哪个端口监听。

现在，我们应该启动我们的应用程序，以确保一切正常工作。只需在终端上写下以下命令:

```
node index.js 
```

您应该会看到消息:`App listening on port 3000`。如果不是这样，你可能错过了一个步骤。回去再试一次。

接下来，让我们看看我们的页面是否提供给了客户端。转到您的网络浏览器并导航至`http://localhost:3000`。

如果您能看到我们在`auth.html`中创建的页面，我们就可以开始了。

回到终端，用`ctrl` + `c`停止应用。所以记住，当我说启动应用，你写`node index.js`，当我说停止应用，你做`ctrl` + `c`。清楚了吗？很好，你刚刚被编程了:-)

## 设置 Passport

您很快就会意识到，Passport 使为我们的用户提供身份验证变得轻而易举。让我们使用以下命令安装 Passport:

```
npm install passport --save 
```

现在我们必须设置护照。在`index.js`文件的底部添加以下代码:

```
/*  PASSPORT SETUP  */

const passport = require('passport');
app.use(passport.initialize());
app.use(passport.session());

app.get('/success', (req, res) => res.send("You have successfully logged in"));
app.get('/error', (req, res) => res.send("error logging in"));

passport.serializeUser(function(user, cb) {
  cb(null, user);
});

passport.deserializeUser(function(obj, cb) {
  cb(null, obj);
}); 
```

这里我们需要 Passport，并直接在我们的 Express 应用程序中初始化它及其会话认证中间件。然后，我们设置了`'/success'`和`'/error'`路由，这将呈现一条消息，告诉我们认证是如何进行的。这与我们上一条路线的语法相同，只是这次我们没有使用`[res.SendFile()](http://expressjs.com/en/api.html#res.sendFile)`，而是使用了`[res.send()](http://expressjs.com/en/api.html#res.send)`，它将给定的字符串在浏览器中显示为`text/html`。然后我们使用`serializeUser`和`deserializeUser`回调。第一个将在身份验证时调用，它的工作是序列化用户实例，并通过 cookie 将其存储在会话中。第二个将在每个反序列化实例的后续请求中被调用，为它提供惟一的 cookie 标识符作为“凭证”。你可以在 Passport [文档](http://www.passportjs.org/docs/configure/#sessions)中了解更多信息。

顺便说一句，我们这个非常简单的示例应用程序没有`deserializeUser`也可以工作得很好，但是它扼杀了保持会话的目的，而这是每个需要登录的应用程序都需要的。

这就是实际 Passport 设置的全部内容。现在我们终于可以进入正题了。

## 实施脸书身份验证

为了提供*脸书认证*，我们需要做的第一件事是安装 [passport-facebook](https://github.com/jaredhanson/passport-facebook) 包。你知道是怎么回事:

```
npm install passport-facebook --save 
```

现在一切都设置好了，添加*脸书认证*是非常容易的。在`index.js`文件的底部添加以下代码:

```
/*  FACEBOOK AUTH  */

const FacebookStrategy = require('passport-facebook').Strategy;

const FACEBOOK_APP_ID = 'your app id';
const FACEBOOK_APP_SECRET = 'your app secret';

passport.use(new FacebookStrategy({
    clientID: FACEBOOK_APP_ID,
    clientSecret: FACEBOOK_APP_SECRET,
    callbackURL: "/auth/facebook/callback"
  },
  function(accessToken, refreshToken, profile, cb) {
      return cb(null, profile);
  }
));

app.get('/auth/facebook',
  passport.authenticate('facebook'));

app.get('/auth/facebook/callback',
  passport.authenticate('facebook', { failureRedirect: '/error' }),
  function(req, res) {
    res.redirect('/success');
  }); 
```

让我们一步一步地检查这段代码。首先，我们需要`passport-facebook`模块。然后，我们声明变量，我们将在其中存储我们的*应用 id* 和*应用秘密*(我们将很快看到如何获得它们)。之后，我们告诉 Passport 给`use`一个我们需要的`FacebookStrategy`的实例。为了实例化所述策略，我们给它我们的*应用 id* 和*应用秘密*变量以及我们将用来认证用户的`callbackURL`。作为第二个参数，它采用一个函数，该函数将返回用户提供的配置文件信息。

再往下，我们设置路由来提供身份验证。正如您在`callbackURL`中看到的，我们将用户重定向到我们之前定义的`/error`和`/success`路线。我们使用的是 [passport.authenticate](http://www.passportjs.org/docs/authenticate/) ，它尝试使用给定策略的第一个参数进行身份验证，在本例中是`facebook`。你可能注意到我们做了两次。第一次，它将请求发送到我们的脸书应用程序。第二个是由回调 URL 触发的，脸书将使用它来响应登录请求。

现在，您需要创建一个脸书应用程序。关于如何做的细节，请参考脸书非常详细的指南[创建一个脸书应用](https://developers.facebook.com/docs/apps/register)，它提供了如何创建一个应用的一步一步的指导。

当你的应用程序创建完成后，进入应用程序配置页面的*设置*。在那里你会看到你的*应用 id* 和*应用秘密*。不要忘记用它们相应的值来改变你在`index.js`文件中为它们声明的变量。

接下来，在*应用程序域*字段中输入“localhost”。然后，进入页面下方的*添加平台*，选择*网站*。使用`http://localhost:3000/auth/facebook/callback`作为*站点 URL* 。

在左侧栏的*产品*部分，您应该会看到*脸书登录*。点击进入那里。

最后，将*有效 OAuth 重定向 URIs* 字段设置为`http://localhost:3000/auth/facebook/callback`。

如果你现在启动应用程序并点击*登录脸书*链接，脸书会提示你提供所需信息，在你登录后，你会被重定向到`/success`路线，在那里你会看到消息`You have successfully logged in`。

就是这样！您刚刚设置了*脸书认证*。很简单，对吧？

## 实现 GitHub 认证

添加 *GitHub 认证*的过程与我们为脸书所做的非常相似。首先，我们将安装 [passport-github](https://github.com/jaredhanson/passport-github) 模块:

```
npm install passport-github --save 
```

现在转到`index.js`文件，在底部添加以下几行:

```
/*  GITHUB AUTH  */

const GitHubStrategy = require('passport-github').Strategy;

const GITHUB_CLIENT_ID = "your app id"
const GITHUB_CLIENT_SECRET = "your app secret";

passport.use(new GitHubStrategy({
    clientID: GITHUB_CLIENT_ID,
    clientSecret: GITHUB_CLIENT_SECRET,
    callbackURL: "/auth/github/callback"
  },
  function(accessToken, refreshToken, profile, cb) {
      return cb(null, profile);
  }
));

app.get('/auth/github',
  passport.authenticate('github'));

app.get('/auth/github/callback',
  passport.authenticate('github', { failureRedirect: '/error' }),
  function(req, res) {
    res.redirect('/success');
  }); 
```

这个看着眼熟！它实际上和以前一样。唯一的不同是，我们使用的是 github 策略，而不是 T2 的 Facebook 策略。

到目前为止…一样。如果你还没想明白，下一步是创建我们的 *GitHub 应用*。GitHub 有一个非常简单的指南，[创建一个 GitHub 应用](https://developer.github.com/apps/building-github-apps/creating-a-github-app/)，它将引导你完成这个过程。

完成后，在配置面板中，你需要将*主页的 URL* 设置为`http://localhost:3000/`，将*的授权回调 URL* 设置为`http://localhost:3000/auth/github/callback`，就像我们对脸书做的那样。

现在，只需重启节点服务器，并尝试使用 GitHub 链接登录。

有用！现在你可以让你的用户用 GitHub 登录了。

## 结论

在本教程中，我们看到了 Passport 如何使身份验证任务变得非常简单。实现 Google 和 Twitter 认证遵循几乎相同的模式。我挑战你使用 [passport-google](https://github.com/jaredhanson/passport-google) 和 [passport-twitter](https://github.com/jaredhanson/passport-twitter) 模块来实现这些。同时，这款应用的代码可以在 [GitHub](https://github.com/sitepoint-editors/OpenAuthWithPassport) 上获得。

## 分享这篇文章