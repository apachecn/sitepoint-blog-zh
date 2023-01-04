# 通过谷歌和脸书将社交登录添加到你的单页应用程序中

> 原文：<https://www.sitepoint.com/spa-social-login-google-facebook/>

![SPA Social Login: Authenticate Your Users via Google and Facebook ](img/45fecebf463a722233f451ffdbe9ef0c.png)

我们越来越多地看到使用单页面架构开发的 web 应用程序，其中整个应用程序作为 JavaScript 加载到浏览器中，然后所有与服务器的交互都使用基于 HTTP 的 API 来执行，这些 API 返回 JSON 文档。通常，这些应用程序需要某种程度的用户限制的交互，例如存储用户个人资料的细节。在传统的基于 HTML 的应用程序中，这是一个相对简单的任务，但在需要验证每个 API 请求的单页面应用程序中，这就比较棘手了。

本文将展示一种使用 [Passport.js](http://passportjs.org) 库来实现社交登录的技术，该技术使用各种提供者，并由此为后面的 API 调用引入基于令牌的认证。

本文的所有源代码都可以从我们的 [GitHub 资源库](https://github.com/sitepoint-editors/social-logins-spa)下载。

## 为什么你的 SPA 要使用社交登录？

在 web 应用程序上实现登录机制时，有许多问题需要考虑。

*   你的 UI 应该如何处理认证本身？
*   你应该如何存储用户信息？
*   您应该如何最好地保护用户凭证？

在您开始编写登录门户之前，需要考虑这些以及更多的问题。但是，有更好的方法。

许多网站，主要是社交网络，允许你使用他们的平台来认证你自己的应用。这是通过使用许多不同的 API 实现的——[OAuth 1.0](https://oauth.net/core/1.0/)、 [OAuth 2.0](https://oauth.net/2/) 、 [OpenID](https://openid.net/) 、 [OpenID Connect](https://openid.net/connect/) 等。

通过使用这些社交登录技术来实现您的登录流程有很多好处。

*   您不再负责呈现用户进行身份验证的 UI。
*   您不再负责存储和保护敏感的用户信息。
*   用户能够使用单一登录来访问多个站点。
*   如果用户觉得他们的密码被泄露，他们可以重置一次，并受益于许多网站。
*   通常，提供身份验证功能的服务会提供其他详细信息。例如，这可以用于自动注册以前从未使用过您的网站的用户，或者允许您代表他们更新他们的个人资料。

## 为什么要对 API 使用基于令牌的认证？

任何时候客户需要访问您的 API，您都需要某种方法来确定他们是谁以及是否允许访问。有几种方法可以实现这一点，但主要的选择是:

*   基于会话的认证
*   基于 Cookie 的身份验证
*   基于令牌的认证

基于会话的身份验证要求您的 API 服务以某种方式将会话与客户端关联起来。这通常很容易设置，但是如果您在多台服务器上部署您的 API，可能会有问题。您还受到服务器用于会话管理和过期的机制的支配，这可能不受您的控制。

基于 Cookie 的方式是在 cookie 中存储一些标识符，用于自动识别 API 请求。这意味着您首先需要某种设置 cookie 的机制，并且您有在后续请求中泄露它的风险，因为 cookie 会自动包含在对同一主机的所有(合适的)请求中。

基于令牌的身份验证是基于 cookie 的身份验证的变体，但是将更多的控制权放在您的手中。本质上，您以与基于 cookie 的身份验证系统相同的方式生成令牌，但是您将自己在请求中包含它——通常在“Authorization”标头中，或者直接在 URL 中。这意味着您可以完全控制令牌的存储，哪些请求将包含令牌，等等。

> 注意:即使 HTTP 头被称为“授权”，我们实际上是在用它进行身份验证。这是因为我们用它来确定客户是谁，而不是客户被允许做什么。

用于生成令牌的策略也很重要。这些标记可以是引用标记，这意味着它们只不过是服务器用来查找实际细节的标识符。或者完整的令牌，这意味着令牌已经包含了所有需要的信息。

引用令牌具有显著的安全优势，因为绝对不会向客户端泄漏用户凭证。但是这有一个性能损失，因为您需要在发出每个请求时将令牌解析为实际的凭证。

完全令牌则相反。它们向任何能够理解令牌的人公开用户凭证，但是因为令牌是完整的，所以在查找时不会有性能损失。

通常，完整的令牌将使用 [JSON Web Tokens](http://jwt.io) 标准来实现，因为它允许提高令牌的安全性。具体来说，jwt 允许对令牌进行加密签名，这意味着您可以保证令牌没有被篡改。还规定对它们进行加密，这意味着如果没有加密密钥，令牌甚至不能被解码。

如果您想重温一下在 Node 中使用 jwt，请查看我们的教程:[使用 JSON Web 令牌和 Node.js](https://www.sitepoint.com/using-json-web-tokens-node-js/) 。

使用完整令牌的另一个缺点是大小。例如，可以使用长度为 36 个字符的 UUID 来实现引用令牌。相反，一个 JWT 很容易有数百个字符长。

在本文中，我们将使用 JWT 令牌来演示它们是如何工作的。然而，当您为自己实现这一点时，您将需要决定是希望使用引用令牌还是完整令牌，以及您将为此使用什么机制。

## 什么是护照？

Passport 是 Node.js 的一组模块，用于在 web 应用程序中实现身份验证。它可以非常容易地插入到许多基于节点的 web 服务器中，并以模块化的结构来实现您需要的登录机制，而只需要最小的膨胀。

Passport 是一个强大的模块套件，涵盖了大范围的身份验证要求。使用这些，我们能够有一个可插拔的设置，允许不同的端点有不同的认证要求。所使用的认证系统可以简单到检查 URL 中的特殊值，一直到依靠第三方提供商为我们完成所有工作。

对于本文，我们将使用 [passport-google-oauth](https://github.com/jaredhanson/passport-google-oauth) 、 [passport-facebook](https://github.com/jaredhanson/passport-facebook) 和 [passport-jwt](https://github.com/themikenicholson/passport-jwt) 模块，允许我们为 API 端点实现社交登录和基于 jwt 令牌的认证。

passport-jwt 模块将用于要求某些端点——我们实际的需要身份验证才能访问的 API 端点——在请求中具有有效的 jwt。passport-google-oauth 和 passport-facebook 模块将用于提供分别针对 google 和脸书进行身份验证的端点，然后生成可用于访问应用程序中其他端点的 JWT。

## 为您的单页应用程序实现社交登录

从这里开始，我们将通过一个简单的单页应用程序来实现社交登录。这个应用程序是使用 Express 编写的，带有一个简单的 API，提供一个安全的和一个不安全的端点。如果您想继续学习，可以从 https://github.com/sitepoint-editors/social-logins-spa 查看源代码。这个应用程序可以通过在下载的源代码中执行`npm install`来构建——下载所有的依赖项——然后通过执行`node src/index.js`来运行。

为了成功使用该应用程序，您需要向谷歌和脸书注册社交登录凭据，并使凭据可用于该应用程序。演示应用程序的[自述文件](https://github.com/sitepoint-editors/social-logins-spa/blob/master/README.md)中提供了完整的说明。这些是作为环境变量来访问的。因此，应用程序可以按如下方式运行:

```
# Linux / OS X
$ export GOOGLE_CLIENTID=myGoogleClientId
$ export GOOGLE_CLIENTSECRET=myGoogleClientSecret
$ export FACEBOOK_CLIENTID=myFacebookClientId
$ export FACEBOOK_CLIENTSECRET=myFacebookClientSecret
$ node src/index.js 
```

```
# Windows
> set GOOGLE_CLIENTID=myGoogleClientId
> set GOOGLE_CLIENTSECRET=myGoogleClientSecret
> set FACEBOOK_CLIENTID=myFacebookClientId
> set FACEBOOK_CLIENTSECRET=myFacebookClientSecret
> node src/index.js 
```

这个过程的最终结果是将令牌认证支持(使用 JSON Web 令牌)添加到我们的安全端点，然后添加社交登录支持(使用谷歌和脸书)，以便获得令牌供应用程序的其余部分使用。这意味着您需要向社交提供者进行一次身份验证，完成后，将生成的 JWT 用于应用程序的所有未来 API 调用。

对于我们的场景来说，jwt 是一个特别好的选择，因为它们是完全独立的，同时仍然是安全的。JWT 由一个 JSON 负载和一个加密签名组成。有效负载包含已验证用户、验证系统和令牌有效期的详细信息。然后签名确保它不能被恶意的第三方伪造——只有拥有签名密钥的人才能生产令牌。

在阅读本文时，您会经常看到对作为应用程序一部分的 [config.js 模块](https://github.com/sitepoint-editors/social-logins-spa/blob/master/src/config.js)的引用。这用于配置应用程序，并利用[节点识别](https://github.com/mozilla/node-convict)模块进行外部配置。本文中使用的配置如下:

*   `http.port`–应用程序运行的端口。默认为 3000，并使用“PORT”环境变量覆盖。
*   `authentication.google.clientId`–用于谷歌认证的谷歌客户端 ID。这是使用“GOOGLE_CLIENTID”环境变量提供给应用程序的
*   `authentication.google.clientSecret`–用于谷歌认证的谷歌客户端密码。这是使用“GOOGLE_CLIENTSECRET”环境变量提供给应用程序的。
*   `authentication.facebook.clientI`d–用于脸书认证的脸书客户端 ID。这是使用“FACEBOOK_CLIENTID”环境变量提供给应用程序的
*   `authentication.facebook.clientSecret`–用于脸书认证的脸书客户端密码。这是使用“FACEBOOK_CLIENTSECRET”环境变量提供给应用程序的。
*   用于签署我们的认证令牌所用的 JWT 的秘密。这默认为“mySuperSecretKey”。
*   `authentication.token.issuer`–存放在 JWT 内的发行人。在一个身份验证服务为多个应用程序提供服务的情况下，这表示哪个服务颁发了令牌。
*   `authentication.token.audience`–JWT 内储存的观众。在一个身份认证服务服务于多个应用程序的情况下，这表示令牌打算用于哪个服务。

### 集成护照

在应用程序中使用 Passport 之前，需要进行少量的设置。这无非是确保安装了模块，并在您的 Express 应用程序中初始化中间件。

我们在这个阶段需要的模块是`passport`模块，然后要设置中间件，我们只需要将它添加到我们的 Express 应用程序中。

```
// src/index.js
const passport = require('passport');
.....
app.use(passport.initialize()); 
```

如果你按照 [Passport 网站](http://passportjs.org/) e 的指示，那么它会让你通过使用`passport.session()`呼叫建立会话支持。我们在应用程序中没有使用任何会话支持，所以这是不必要的。这是因为我们正在实现一个无状态 API，所以我们将对每个请求提供身份验证，而不是将它保存在一个会话中。

### 为安全端点实现 JWT 令牌身份验证

使用 Passport 设置 JWT 令牌身份验证相对简单。我们将利用`passport-jwt`模块，它为我们做所有的艰苦工作。该模块查找值以“JWT”开头的“授权”报头，并将报头的其余部分视为用于身份验证的 JWT 令牌。然后，它对 JWT 进行解码，并使存储在其中的值可供您自己的代码操作，例如进行用户查找。如果 JWT 无效，例如，如果签名无效，令牌已经过期…那么请求将未经验证，而不需要您自己的代码进行任何额外的处理。

配置 JWT 令牌身份验证就是这样一种情况:

```
// src/authentication/jwt.js
const passport = require('passport');
const passportJwt = require('passport-jwt');
const config = require('../config');
const users = require('../users');

const jwtOptions = {
  // Get the JWT from the "Authorization" header.
  // By default this looks for a "JWT " prefix
  jwtFromRequest: passportJwt.ExtractJwt.fromAuthHeader(),
  // The secret that was used to sign the JWT
  secretOrKey: config.get('authentication.token.secret'),
  // The issuer stored in the JWT
  issuer: config.get('authentication.token.issuer'),
  // The audience stored in the JWT
  audience: config.get('authentication.token.audience')
};

passport.use(new passportJwt.Strategy(jwtOptions, (payload, done) => {
  const user = users.getUserById(parseInt(payload.sub));
  if (user) {
      return done(null, user, payload);
  }
  return done();
})); 
```

在上面的示例中，我们使用了几个内部模块:

*   `config.js`–这包含了整个应用程序的配置属性。可以假设这些都已经配置好了，并且这些值随时可供使用
*   `users.js`–这是应用程序的用户存储。这允许加载和创建用户——这里我们简单地通过用户的内部 ID 加载用户。

这里，我们用已知的秘密、发布者和受众配置 JWT 解码器，并且我们通知策略它应该从授权头获得 JWT。如果发行者或受众与 JWT 中存储的内容不匹配，则身份验证将失败。这给了我们另一个层次的防伪保护，虽然非常简单。

令牌解码完全由`passport-jwt`模块处理，我们需要做的就是提供与最初用于生成令牌的配置相对应的配置。因为 JWT 是一个标准，任何遵循该标准的模块都能够很好地协同工作。

当令牌被成功解码后，它将作为有效载荷传递给我们的回调函数。这里我们只是尝试从令牌中查找由“主题”标识的用户。实际上，您可能需要进行额外的检查，例如确保令牌没有被撤销。

如果找到了用户，我们将它提供给 Passport，然后 Passport 将把它作为`req.user`提供给请求处理的其余部分。如果找不到用户，那么我们不向 Passport 提供任何用户，Passport 将认为身份验证失败。

现在可以将它连接到请求处理程序中，以便请求需要身份验证才能成功:

```
// src/index.js
app.get('/api/secure',
  // This request must be authenticated using a JWT, or else we will fail
  passport.authenticate(['jwt'], { session: false }),
  (req, res) => {
    res.send('Secure response from ' + JSON.stringify(req.user));
  }
); 
```

上面的第 3 行是让 Passport 处理请求的神奇之处。这导致 Passport 运行我们刚刚针对传入请求配置的“jwt”策略，要么允许它继续，要么立即失败。

我们可以通过运行应用程序——通过执行`node src/index.js`——并尝试访问该资源来看到这一点:

```
$ curl -v http://localhost:3000/api/secure
> GET /api/secure HTTP/1.1
> Host: localhost:3000
> User-Agent: curl/7.51.0
> Accept: */*
>
< HTTP/1.1 401 Unauthorized
< X-Powered-By: Express
< Date: Tue, 13 Jun 2017 07:53:10 GMT
< Connection: keep-alive
< Content-Length: 12
<
Unauthorized 
```

我们不提供任何授权头，它不允许我们继续。
但是，如果您提供一个有效的授权头，您将得到一个成功的响应:

```
$ curl -v http://localhost:3000/api/secure -H "Authorization: JWT eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE0OTczNDAzNzgsImV4cCI6MTQ5NzM0Mzk3OCwiYXVkIjoic29jaWFsLWxvZ2lucy1zcGEiLCJpc3MiOiJzb2NpYWwtbG9naW5zLXNwYSIsInN1YiI6IjAifQ.XlVnG59dX-SykXTJqCmvz_ALvzPW-yGZKOJEGFZ5KUs"
> GET /api/secure HTTP/1.1
> Host: localhost:3000
> User-Agent: curl/7.51.0
> Accept: */*
> Authorization: JWT eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE0OTczNDAzNzgsImV4cCI6MTQ5NzM0Mzk3OCwiYXVkIjoic29jaWFsLWxvZ2lucy1zcGEiLCJpc3MiOiJzb2NpYWwtbG9naW5zLXNwYSIsInN1YiI6IjAifQ.XlVnG59dX-SykXTJqCmvz_ALvzPW-yGZKOJEGFZ5KUs
>
< HTTP/1.1 200 OK
< X-Powered-By: Express
< Content-Type: text/html; charset=utf-8
< Content-Length: 60
< ETag: W/"3c-2im1YD4hSDFtwS8eVcEUzt3l5XQ"
< Date: Tue, 13 Jun 2017 07:54:37 GMT
< Connection: keep-alive
<
Secure response from {"id":0,"name":"Graham","providers":[]} 
```

为了执行这个测试，我通过访问 https://www.jsonwebtoken.io 手工生成了一个 JWT，并在那里填写表单。我使用的“有效载荷”是

```
{
  "iat": 1497340378, // Tuesday, 13 June 2017 07:52:58 UTC
  "exp": 1497343978, // Tuesday, 13 June 2017 08:52:58 UTC
  "aud": "social-logins-spa",
  "iss": "social-logins-spa",
  "sub": "0"
} 
```

“签名密钥”是“mySuperSecretKey”，取自配置。

### 支持令牌生成

既然我们只能用有效的令牌访问资源，我们需要一种方法来实际生成令牌。这是通过使用 [jsonwebtoken](https://github.com/auth0/node-jsonwebtoken) 模块完成的，构建一个包含正确细节的 JWT，并使用与上面使用的相同的密钥进行签名。

```
// src/token.js
const jwt = require('jsonwebtoken');
const config = require('./config');

// Generate an Access Token for the given User ID
function generateAccessToken(userId) {
  // How long will the token be valid for
  const expiresIn = '1 hour';
  // Which service issued the token
  const issuer = config.get('authentication.token.issuer');
  // Which service is the token intended for
  const audience = config.get('authentication.token.audience');
  // The signing key for signing the token
  const secret = config.get('authentication.token.secret');

  const token = jwt.sign({}, secret, {
    expiresIn: expiresIn,
    audience: audience,
    issuer: issuer,
    subject: userId.toString()
  });

  return token;
} 
```

请注意，在生成 JWT 时，我们对受众、发布者和机密使用完全相同的配置设置。我们还规定 JWT 的有效期为一小时。这个时间段可以是您认为对您的应用程序来说合理的任何时间段，甚至可以从配置中删除，以便可以很容易地更改。

在这种情况下，没有指定 JWT ID，但这可以用来为令牌生成一个完全唯一的 ID，例如使用 UUID。这为您提供了一种方法来撤销令牌，并将撤销的 ID 集合存储在数据存储中，并在处理 Passport 策略中的 JWT 时检查 JWT ID 是否不在列表中。

### 社交登录提供商

既然我们已经有了生成令牌的能力，我们需要一种让用户真正登录的方法。这就是社交登录提供商的用武之地。我们将为用户添加重定向到社交登录提供商的功能，并在成功时生成一个 JWT 令牌，并将其提供给浏览器的 JavaScript 引擎，以供将来的请求使用。我们几乎已经准备好了所有的部件，我们只需要把它们连接起来。

Passport 中的社交登录提供者分为两部分。首先，需要使用适当的插件为社交登录提供商实际配置 Passport。其次，需要有用户被指引到的快速路由，以便开始认证，并且当认证成功时，用户被重定向回。

我们将在一个新的子浏览器窗口中打开这些 URL，我们将能够在完成时关闭它，并且它能够在打开它的窗口内调用 JavaScript 方法。这意味着该过程对用户来说是相对透明的——最多他们会看到一个新窗口打开，要求他们提供凭据，但除了他们现在已登录这一事实之外，他们最多可能什么也看不到。

这方面的浏览器将需要由两部分组成。弹出窗口的视图，以及在主窗口中处理它的 JavaScript。这可以很容易地与任何框架集成，但是为了简单起见，在这个例子中我们将使用普通的 JavaScript。

主页面 JavaScript 只需要这样的东西:

```
// src/public/index.html
let accessToken;

function authenticate(provider) {
  window.authenticateCallback = function(token) {
    accessToken = token;
  };

  window.open('/api/authentication/' + provider + '/start');
} 
```

这将在窗口(名为`authenticateCallback`)上注册一个全局函数对象，该对象将存储访问令牌，然后打开我们的路由以开始身份验证，我们在`/api/authentication/{provider}/start`上访问该身份验证。

然后，您可以通过任何方式触发此功能来启动身份验证。这通常是在标题区域某处的一个登录链接，但是细节完全取决于你的应用程序。

其中的第二部分是在成功认证时呈现的视图。在这种情况下，为了简单起见，我们使用 Mustache，但这将使用对您最有意义的任何视图技术。

```
<!-- src/public/authenticated.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Authenticated</title>
  </head>
  <body>
    Authenticated successfully.

    <script type="text/javascript"> window.opener.authenticateCallback('{{token}}');
      window.close(); </script>
  </body>
</html> 
```

在这里，我们简单地用一点 JavaScript 从上面调用这个窗口的打开者的`authenticateCallback`方法，即在主应用程序窗口上，然后我们关闭自己。

此时，JWT 令牌将出现在主应用程序窗口中，用于任何您想要的目的。

### 实施 Google 认证

将使用`passport-google-oauth`模块对谷歌进行认证。这需要提供三条信息:

*   客户端 ID
*   客户机密
*   重定向 URL

通过在 [Google 开发者控制台](https://console.developers.google.com/apis/credentials)注册您的应用程序，可以获得客户端 ID 和密码。重定向 URL 是您的应用程序中的 URL，当用户使用他们的 Google 凭据登录时，会被发送回该 URL。这将取决于应用程序部署的方式和位置，但现在我们将对其进行硬编码。

我们用于 Google 身份验证的 Passport 配置将如下所示:

```
// src/authentication/google.js
const passport = require('passport');
const passportGoogle = require('passport-google-oauth');
const config = require('../config');
const users = require('../users');

const passportConfig = {
  clientID: config.get('authentication.google.clientId'),
  clientSecret: config.get('authentication.google.clientSecret'),
  callbackURL: 'http://localhost:3000/api/authentication/google/redirect'
};

if (passportConfig.clientID) {
  passport.use(new passportGoogle.OAuth2Strategy(passportConfig, function (request, accessToken, refreshToken, profile, done) {
    // See if this user already exists
    let user = users.getUserByExternalId('google', profile.id);
    if (!user) {
      // They don't, so register them
      user = users.createUser(profile.displayName, 'google', profile.id);
    }
    return done(null, user);
  }));
} 
```

当用户在成功认证后被重定向回我们时，我们会得到他们在 Google 系统中的 ID 和一些个人资料信息。我们首先尝试查看这个用户以前是否登录过。如果是这样，那么我们获取他们的用户记录，就大功告成了。如果没有，我们将为他们注册一个新帐户，这个新帐户就是我们将使用的帐户。这为我们提供了一个透明的机制，用户注册在首次登录时完成。如果你愿意，我们可以用不同的方式来做，但是现在没有必要。

下一步是设置路由处理程序来管理这个登录。这些看起来像这样:

```
// src/index.js
function generateUserToken(req, res) {
  const accessToken = token.generateAccessToken(req.user.id);
  res.render('authenticated.html', {
    token: accessToken
  });
}

app.get('/api/authentication/google/start',
  passport.authenticate('google', { session: false, scope: ['openid', 'profile', 'email'] }));
app.get('/api/authentication/google/redirect',
  passport.authenticate('google', { session: false }),
  generateUserToken); 
```

注意`/api/authentication/google/start`和`/api/authentication/gogle/redirect`的路线。如上所述，`/start`变体是我们打开的 URL，而`/redirect`变体是 Google 在成功时将用户重定向回的 URL。这将呈现如上所示的经过验证的视图，提供生成的 JWT 供其使用。

### 实施脸书身份验证

现在我们已经有了第一个社交登录提供商，让我们扩展并添加第二个。这次将是脸书，使用`passport-facebook`模块。

这个模块实际上与 Google 模块一样工作，需要相同的配置和相同的设置。唯一真正的区别是，它是一个不同的模块和一个不同的访问它的 URL 结构。

为了配置脸书身份验证，您还需要一个客户端 ID、客户端密码和重定向 URL。
在[脸书开发者控制台](https://developers.facebook.com/apps/)中创建一个脸书应用，就可以获得客户端 ID 和客户端密码(脸书称之为 App ID 和 App Secret)。
您需要确保将“脸书登录”产品添加到您的应用程序中，这样才能正常工作。

我们用于脸书身份验证的 Passport 配置将是:

```
// src/authentication/facebook.js
const passport = require('passport');
const passportFacebook = require('passport-facebook');
const config = require('../config');
const users = require('../users');

const passportConfig = {
  clientID: config.get('authentication.facebook.clientId'),
  clientSecret: config.get('authentication.facebook.clientSecret'),
  callbackURL: 'http://localhost:3000/api/authentication/facebook/redirect'
};

if (passportConfig.clientID) {
  passport.use(new passportFacebook.Strategy(passportConfig, function (accessToken, refreshToken, profile, done) {
    let user = users.getUserByExternalId('facebook', profile.id);
    if (!user) {
      user = users.createUser(profile.displayName, 'facebook', profile.id);
    }
    return done(null, user);
  }));
} 
```

这与谷歌的情况几乎相同，只是用了“facebook”一词来代替。并且 URL 路由是相似的:

```
// src/index.js
app.get('/api/authentication/facebook/start',
  passport.authenticate('facebook', { session: false }));
app.get('/api/authentication/facebook/redirect',
  passport.authenticate('facebook', { session: false }),
  generateUserToken); 
```

这里我们不需要指定我们想要使用的范围，因为默认设置已经足够好了。除此之外，谷歌和脸书的配置几乎完全相同。

## 摘要

社交登录提供者的使用使得向应用程序添加用户登录和注册变得非常快速和容易。事实上，它使用浏览器重定向将用户发送到社交登录提供商，然后返回到您的应用程序，这使得它很难集成到单页面应用程序中，即使它相对容易集成到更传统的应用程序中。

本文展示了一种将这些社交登录提供者集成到您的单页面应用程序中的方法，希望这种方法既易于使用，又易于扩展，以适应您可能希望与之合作的未来提供者。Passport 有大量的模块可以与不同的提供商合作，这是一个找到正确的模块并按照我们为谷歌和脸书所做的相同方式进行配置的问题。

*这篇文章由[詹姆斯·科尔斯](https://www.sitepoint.com/author/jkolce)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好*

## 分享这篇文章