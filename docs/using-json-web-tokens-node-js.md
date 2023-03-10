# 通过 Node.js 使用 JSON Web 令牌

> 原文：<https://www.sitepoint.com/using-json-web-tokens-node-js/>

前端框架和库，如 Ember、Angular 和 Backbone，是向更丰富、更复杂的 web 应用程序客户机发展的趋势的一部分。因此，服务器端组件摆脱了许多传统的责任，本质上变得更像 API。这种 API 方法允许应用程序的传统“前端”和“后端”部分更好地分离。一组开发人员可以独立于前端工程师构建后端，还有一个好处是测试变得更简单。这种方法还使得构建一个与 web 应用程序共享相同后端的移动应用程序变得更加容易。

提供 API 的挑战之一是认证。在传统的 web 应用程序中，服务器通过做两件事来响应成功的身份验证请求。首先，它使用某种存储机制创建一个会话。每个会话都有自己的标识符——通常是一个长的半随机字符串——用于在将来的请求中检索有关会话的信息。第二，该信息通过报头的方式发送给客户机，指示它设置一个 cookie。浏览器会自动将会话 ID cookie 附加到所有后续请求，从而允许服务器通过从存储中检索适当的会话来识别用户。这就是传统的 web 应用程序如何避开 HTTP 是无状态的这一事实。

API 应该被设计成真正的无状态。这意味着没有登录或注销方法，也没有会话。API 设计者也不能依赖 cookies，因为不能保证请求会通过 web 浏览器发出。显然，我们需要一种替代机制。本文着眼于一种可能的机制来解决这个问题——JSON Web Tokens，或 jwt(发音为 jots)。本文中的例子在后端使用 Node 的 Express 框架，在客户端使用 Backbone。

## 背景

让我们简要地看一下保护 API 的几种常见方法。

一种是使用 HTTP 基本认证。在官方的 HTTP 规范中定义，这实质上涉及到在服务器响应上设置一个指示需要认证的头。客户端必须通过向每个后续请求附加其凭据(包括密码)来做出响应。如果凭据匹配，用户信息将作为变量提供给服务器应用程序。

第二种方法非常类似，但是使用应用程序自己的身份验证机制。这通常包括对照存储中的凭据检查提供的凭据。与 HTTP 基本身份验证一样，这要求每次调用都提供用户凭证。

第三种方法是 [OAuth](http://oauth.net/) (或 OAuth2)。这在很大程度上是为了针对第三方服务进行身份验证而设计的，实现起来相当困难，至少在服务器端是如此。

第四种方法是使用令牌。这就是我们在本文中要探讨的内容。我们将看到一个在前端和后端都使用 JavaScript 的实现。

## 令牌方法

我们可以允许客户端用有效凭证交换令牌，而不是为每个请求提供用户名和密码等凭证。此令牌允许客户端访问服务器上的资源。令牌通常比密码长得多，也更令人困惑。例如，我们将要处理的 jwt 大约有 150 个字符。一旦获得了令牌，就必须在每次 API 调用时发送它。然而，这仍然比每次请求都发送用户名和密码更安全，即使是通过 HTTPS。

把令牌想象成安全通行证。到达时，你在受限建筑的前台表明自己的身份(提供你的用户名和密码)，如果你能被成功识别，你就能获得安全通行证。当您在大楼内走动时(尝试通过调用 API 来访问资源)，您需要出示您的通行证，而不是从头再来一遍初始身份识别过程。

## 关于 jwt

jwt 是一个[草案规范](http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-19)，尽管本质上它们只是一个已经很常见的认证和授权机制的更具体的实现；也就是交换代币。JWT 分为三部分，由句点分隔。jwt 是 URL 安全的，这意味着它们可以在查询字符串参数中使用。

JWT 的第一部分是一个简单 JavaScript 对象的编码字符串表示，它描述了令牌和使用的哈希算法。以下示例说明了使用 HMAC·SHA-256 的 JWT。

```
{
"typ" : "JWT",
"alg" : "HS256"
}
```

编码后，对象变成以下字符串:

```
eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9
```

JWT 的第二部分构成了令牌的核心。它也代表一个 JavaScript 对象，包含一些信息。其中有些字段是必填的，有些是可选的。下面显示了一个取自规范草案的示例。

```
{
"iss": "joe",
"exp": 1300819380,
"http://example.com/is_root": true
}
```

这被称为 JWT 索赔集。出于本文的目的，我们将忽略第三个参数，但是你可以在[规范](http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-19)中读到更多。`iss`属性是`issuer`的缩写，指定发出请求的人或实体。通常，这是访问 API 的用户。`exp`字段是`expires`的缩写，用于限制令牌的生命周期。编码后，JSON 令牌看起来像这样:

```
eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ
```

JWT 的第三部分，也是最后一部分，是根据报头(第一部分)和正文(第二部分)生成的签名。我们的示例 JWT 的签名如下所示。

```
dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

生成的完整 JWT 如下所示:

```
eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ.dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

规范中支持许多附加的可选属性。其中的`iat`表示令牌发出的时间，`nbf`(不是之前)表示令牌在某个时间之前不应该被接受，`aud`(观众)表示令牌所针对的接收者。

## 处理令牌

我们将使用 [JWT 简单](https://github.com/hokaccha/node-jwt-simple)模块来处理令牌，这样我们就不必深入研究编码和解码的细节。如果你真的感兴趣，你可以在[的规范](http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-19)中找到更多信息，或者通读回购的源代码。

使用以下命令开始安装库。请记住，通过在命令中包含`--save`标志，您可以自动将它添加到项目的`package.json`文件中。

```
npm install jwt-simple
```

在应用程序的初始化部分，添加以下代码。这段代码导入 Express 和 JWT Simple，并创建一个新的 Express 应用程序。示例的最后一行将名为`jwtTokenSecret`的应用程序变量设置为值`YOUR_SECRET_STRING`(确保将该值更改为其他值)。

```
var express = require('express');
var jwt = require('jwt-simple');
var app = express();

app.set('jwtTokenSecret', 'YOUR_SECRET_STRING');
```

## 获取令牌

我们需要做的第一件事是让客户端能够将他们的用户名和密码交换为一个令牌。在 RESTful API 中有两种可能的方法。第一种方法是向身份验证端点发出一个`POST`请求，服务器用一个令牌响应成功的请求。或者，您可以实现一个端点，客户端可以从这个端点`GET`获取令牌，这要求客户端提供它们的凭证，或者作为查询参数，或者更好的是，通过消息头。

本文的目的是解释认证令牌，而不是基本的用户名/密码认证机制，所以让我们假设我们已经有了以下内容，并且我们已经从请求中获得了`username`和`password`:

```
User.findOne({ username: username }, function(err, user) {
if (err) {
// user not found
return res.send(401);
}

if (!user) {
// incorrect username
return res.send(401);
}

if (!user.validPassword(password)) {
// incorrect password
return res.send(401);
}

// User has authenticated OK
res.send(200);
});
```

接下来，我们需要使用 JWT 令牌来响应成功的身份验证尝试:

```
var expires = moment().add('days', 7).valueOf();
var token = jwt.encode({
iss: user.id,
exp: expires
}, app.get('jwtTokenSecret'));

res.json({
token : token,
expires: expires,
user: user.toJSON()
});
```

您会注意到`jwt.encode()`函数有两个参数。第一个是将构成令牌主体的对象。第二个是我们之前定义的秘密字符串。令牌是使用前面描述的`iss`和`exp`字段构建的。请注意， [Moment.js](http://momentjs.com/) 用于将到期时间设置为从现在起 7 天。`res.json()`方法用于将令牌的 JSON 表示返回给客户机。

## 验证令牌

为了验证 JWT，我们需要编写一些中间件，它们将:

1.  检查附加的令牌。
2.  尝试解码。
3.  检查令牌的有效性。
4.  如果令牌有效，则检索相应的用户记录并将其附加到请求对象。

让我们从创建中间件的基本框架开始:

```
// @file jwtauth.js

var UserModel = require('../models/user');
var jwt = require('jwt-simple');

module.exports = function(req, res, next) {
// code goes here
};
```

为了获得最大的灵活性，我们将允许客户端以三种方式之一附加令牌——作为查询字符串参数、表单主体参数或在 HTTP 头中。对于后者，我们将使用头`x-access-token`。

下面是我们的中间件中的代码，它试图检索令牌:

```
var token = (req.body && req.body.access_token) || (req.query && req.query.access_token) || req.headers['x-access-token'];
```

注意，为了访问`req.body`，我们需要先连接`express.bodyParser()`中间件。

接下来，让我们试着解码 JWT:

```
if (token) {
try {
var decoded = jwt.decode(token, app.get('jwtTokenSecret'));

// handle token here

} catch (err) {
return next();
}
} else {
next();
}
```

如果解码过程失败，JWT 简单包将抛出一个异常。如果发生这种情况，或者没有提供令牌，我们只需调用`next()`继续处理请求——这仅仅意味着我们还没有识别用户。如果一个有效的令牌存在并被解码，我们应该得到一个具有两个属性的对象——`iss`包含用户 id，而`exp`包含过期时间戳。让我们首先检查后者，如果令牌已过期，则拒绝它:

```
if (decoded.exp <= Date.now()) {
res.end('Access token has expired', 400);
}
```

如果令牌仍然有效，我们可以检索用户并将其附加到请求对象，如下所示。

```
User.findOne({ _id: decoded.iss }, function(err, user) {
req.user = user;
});
```

最后，将中间件附加到路由:

```
var jwtauth = require('./jwtauth.js');

app.get('/something', [express.bodyParser(), jwtauth], function(req, res){
// do something
});
```

或者，也许将它附加到一组路线上:

```
app.all('/api/*', [express.bodyParser(), jwtauth]);
```

我们的中间件现在检查请求，寻找一个有效的令牌，如果存在，将一个用户对象附加到请求上。现在构建一些简单的中间件来拒绝没有有效令牌的请求应该是相当简单的，尽管您可能希望将它构建到同一个中间件中。

这是令牌方法的服务器端元素。在下一节中，我们将看看令牌在客户端是如何工作的。

## 客户

我们提供了一个简单的`GET`端点来获取访问令牌。这很简单，我们可能不需要查看细节——只需发出一个调用，传递用户名和密码(可能来自一个表单),如果请求成功，将生成的令牌存储在某个地方供以后使用。

我们将更详细地研究将令牌附加到后续调用。一种方法是使用 jQuery 的`ajaxSetup()`方法。这可以用于直接的 Ajax 调用，或者用于前端框架，前端框架在幕后使用 Ajax 与服务器通信。例如，假设我们使用`window.localStorage.setItem('token', 'the-long-access-token')`将访问令牌放在本地存储中；我们可以通过这样的头将令牌附加到所有调用:

```
var token = window.localStorage.getItem('token');

if (token) {
$.ajaxSetup({
headers: {
'x-access-token': token
}
});
}
```

简单地说，这将“劫持”所有 Ajax 请求，如果本地存储中有令牌，它将使用`x-access-token`头将其附加到请求上。

这不处理令牌过期，但应该相对简单。您会记得我们返回了一个带有令牌的过期时间戳。此外，您可能希望让服务器使用指示客户端必须重新进行身份验证的标头来通知客户端令牌已过期。

### 与主干一起使用

让我们将前一节中的方法应用于主干应用程序。最简单的方法是全局覆盖`Backbone.sync()`，如下所示。

```
// Store "old" sync function
var backboneSync = Backbone.sync

// Now override
Backbone.sync = function (method, model, options) {

/*
* "options" represents the options passed to the underlying $.ajax call
*/
var token = window.localStorage.getItem('token');

if (token) {
options.headers = {
'x-access-token': token
}
}

// call the original function
backboneSync(method, model, options);
};
```

## 附加安全性

您可以通过在服务器上存储已颁发令牌的记录，然后在每个后续请求中对照该记录来验证它们，从而增加额外的安全层。这将防止第三方“欺骗”令牌，并允许服务器使令牌无效。我不会在这里讨论这个问题，但是它应该相对容易实现。

## 摘要

在本文中，我们研究了在 API 上进行身份验证的一些方法，特别是 JSON Web 令牌。我们已经使用 Node with Express 编写了该技术的基本工作实现，并以 Backbone 为例研究了如何在客户端使用它。这篇文章的代码可以在 [GitHub](https://github.com/lukaswhite/jwt-node-express) 上找到。

还有更多我们没有完全实现的规范，例如对资源的“声明”,但我们已经使用基本的建议建立了一种机制，用于在 JavaScript 应用程序的客户端和服务器之间交换访问令牌的凭证。

当然，您可以将这种方法应用到其他技术上——例如 Ruby 或 PHP 后端，或者 Ember 或 AngularJS 应用程序。或者，您可以将它用于移动应用程序。例如，将 web 技术与 PhoneGap 之类的东西结合使用，使用 Sencha 之类的工具，或者作为完全的本地应用程序。

## 分享这篇文章