# 如何用节点创建和验证 jwt

> 原文：<https://www.sitepoint.com/how-to-create-and-verify-jwts-with-node/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/11/13/create-and-verify-jwts-with-node)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

这些年来，互联网上的身份认证有了很大的发展。有很多方法可以做到这一点，但在 90 年代行之有效的方法在今天并不完全适用。在本教程中，我将简要介绍一些更古老、更简单的身份验证形式，然后向您展示一种更现代、更安全的方法。在本文结束时，您将能够在 Node 中自己创建和验证 jwt。我还将向您展示如何利用 Okta 在幕后为您完成这一切。

传统上，最简单的授权方式是使用用户名和密码。这被称为基本授权，只需将`username:password`作为一个编码字符串发送，任何人都可以解码。您可以将该字符串视为一个“令牌”。问题是，你每次请求都要发送密码。您也可以一次性发送您的用户名和密码，让服务器为您创建一个会话 ID。然后，客户端会将 ID 与每个请求一起发送，而不是用户名和密码。这种方法也很有效，但是对于客户端来说，存储和维护会话可能很麻烦，尤其是对于大量用户。

管理授权的第三种方法是通过 JSON Web 令牌，或 jwt。在过去几年中，jwt 已经成为事实上的标准。JWT 提出了一组可以被验证的声明(例如，“我是亚伯·弗罗曼，芝加哥的香肠大王”)。像基本授权一样，声明可以被任何人阅读。然而，与基本身份验证不同，你不会与任何监听你的人分享你的密码。相反，这完全是信任的问题。

## 信任，但要核实…你的 jwt

![it must be true](img/9e6bbb43a3e40eb13868c14fbdb4ece2.png)

好吧，也许不要相信网上看到的一切。你可能想知道为什么有人会做出一些声明并期望服务器相信他们。当你使用 JWT 进行声明时，它会被一个有密钥的服务器签署。读取密钥的服务器可以很容易地验证声明是否有效，即使不知道所使用的秘密。然而，如果没有密钥，要修改声明并确保签名有效几乎是不可能的。

## 为什么要用 JWT？

使用 JWT 允许服务器将认证卸载给他们信任的第三方。只要你信任第三方，你可以让他们确保用户就是他们所说的那个人。然后，第三方将创建一个 JWT 传递到您的服务器，其中包含任何必要的信息。通常这至少包括用户的用户 id(标准地称为“主题”的`sub`)、令牌的“发行者”(`iss`)和“到期时间”(`exp`)。有相当多的标准化声明，但是你真的可以在声明中放入任何你想要的 JSON。请记住，您包含的信息越多，令牌就越长。

## 构建一个简单的节点应用程序

要创建和验证您自己的 jwt，您首先需要设置一个节点服务器(嗯，您不需要*必须*这样做，但这是我今天要教您的)。首先，运行以下命令来设置一个新项目:

```
mkdir fun-with-jwts
cd fun-with-jwts
npm init -y
npm install express@4.16.4
npm install -D nodemon@1.18.6 
```

接下来，创建一个新文件`index.js`，它将包含一个超级简单的节点服务器。这里有三个端点，只是用`TODO` s 作为要实现什么的注释。

`/create`端点需要基本授权才能让您登录。如果您正在编写一个真正的 OAuth 服务器，您可能会使用基本 Auth 之外的东西。您还需要在数据库中查找用户，并确保他们提供了正确的密码。为了简化演示，我在这里只硬编码了一个用户名和密码，因此我们可以专注于 JWT 功能。

`/verify`端点将 JWT 作为待解码的参数。

```
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/create', (req, res) => {
  if (req.headers.authorization !== 'Basic QXp1cmVEaWFtb25kOmh1bnRlcjI=') {
    res.set('WWW-Authenticate', 'Basic realm="401"')
    res.status(401).send('Try user: AzureDiamond, password: hunter2')
    return
  }

  res.send('TODO: create a JWT')
})

app.get('/verify/:token', (req, res) => {
  res.send(`TODO: verify this JWT: ${req.params.token}`)
})

app.get('/', (req, res) => res.send('TODO: use Okta for auth'))

app.listen(port, () => console.log(`JWT server listening on port ${port}!`)) 
```

您现在可以通过键入`node_modules/.bin/nodemon .`来运行服务器。这将在端口 3000 上启动一个服务器，并在您更改源代码时自动重启。您可以通过浏览器中的`http://localhost:3000`进行访问。要访问不同的端点，您需要将 URL 改为`http://localhost:3000/create`或`http://localhost:3000/verify/asdf`。如果您喜欢在命令行中工作，您可以使用`curl`来访问所有这些端点:

```
$ curl localhost:3000
TODO: use Okta for auth

$ curl localhost:3000/create
Try user: AzureDiamond, password: hunter2

$ curl AzureDiamond:hunter2@localhost:3000/create
TODO: create a JWT

$ curl localhost:3000/verify/asdf
TODO: verify this JWT: asdf 
```

## 在节点应用程序中创建 JSON Web 令牌

JSON Web 令牌有三个部分。由`.` s 分隔的**报头**、**有效载荷**和**签名**

**头**是一个 base64 编码的 JSON 对象，指定使用哪种算法和令牌的类型。

**有效负载**也是一个 base64 编码的 JSON 对象，包含了几乎所有您想要的东西。通常，它至少包含一个过期时间戳和一些标识信息。

**签名**使用报头中指定的算法将报头、有效载荷和密钥散列在一起。

有很多工具可以为各种语言创建 jwt。对于 Node，一个简单的是`njwt`。要将其添加到您的项目中，请运行

```
npm install njwt@0.4.0 
```

现在用以下内容替换`index.js`中的`res.send('TODO: create a JWT')`行:

```
 const jwt = require('njwt')
  const claims = { iss: 'fun-with-jwts', sub: 'AzureDiamond' }
  const token = jwt.create(claims, 'top-secret-phrase')
  token.setExpiration(new Date().getTime() + 60*1000)
  res.send(token.compact()) 
```

请随意摆弄有效载荷。通过上面的`setExpiration()`功能，令牌会在一分钟内到期，这可以让你看到到期后会发生什么，而不必等待太久。

要对此进行测试并获得令牌，请通过`/create`端点登录。同样，您可以在`http://localhost:3000/create`进入浏览器，或者使用 curl:

```
$ curl AzureDiamond:hunter2@localhost:3000/create
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJoZWxsbyI6IndvcmxkISIsIm51bWJlciI6MC41MzgyNzE0MTk3Nzg5NDc4LCJpYXQiOjE1NDIxMDQ0NDgsImV4cCI6MTU0MjEwNDUwOCwiaXNzIjoiZnVuLXdpdGgtand0cyIsInN1YiI6IkF6dXJlRGlhbW9uZCJ9.LRVmeIzAYk5WbDoKfSTYwPx5iW0omuB76Qud-xR8We4 
```

## 验证节点应用程序中的 JSON Web 令牌

嗯，这看起来有点像胡言乱语。你可以看到在 JWT 中有两个`.`分隔头部、有效载荷和签名，但是它不是人类可读的。下一步是写一些东西来把字符串解码成更易读的东西。

用以下内容替换包含`TODO: verify this JWT`的行:

```
 const jwt = require('njwt')
  const { token } = req.params
  jwt.verify(token, 'top-secret-phrase', (err, verifiedJwt) => {
    if(err){
      res.send(err.message)
    }else{
      res.send(verifiedJwt)
    }
  }) 
```

在 route `/verify/:token`中，`:token`部分告诉 express 你想把 URL 的这一部分作为参数读入，这样你就可以在`req.params.token`上得到它。然后，您可以使用`njwt`来尝试验证令牌。如果失败，这可能意味着很多事情，如令牌格式错误或已过期。

回到您的网站，或者在 curl 中，使用`http://localhost:3000/create`创建另一个令牌。然后将其复制并粘贴到 URL 中，这样就有了`http://localhost:3000/verify/eyJhb...R8We4`。您应该会得到如下所示的内容:

```
{
  "header": { "typ": "JWT", "alg": "HS256" },
  "body": {
    "iss": "fun-with-jwts",
    "sub": "AzureDiamond",
    "jti": "3668a38b-d25d-47ee-8da2-19a36d51e3da",
    "iat": 1542146783,
    "exp": 1542146843
  }
} 
```

如果你等一分钟再试一次，你会得到`jwt expired`。

## 将 OIDC 中间件添加到您的节点应用程序中，以处理 JWT 功能

嗯，还不算太糟。但是我确实掩盖了很多细节。那并不是什么高度机密。怎么保证自己有一个安全的，不容易被发现？JWT 的其他选择呢？你如何在浏览器中存储它呢？令牌的最佳过期时间是多久？

这就是 Okta 发挥作用的地方。你可以利用 Okta 的云服务来为你处理这一切，而不是自己处理这一切。经过几分钟的设置后，你可以停止考虑如何使你的应用程序安全，而只关注它的独特之处。

### 为什么用 Okta Auth？

Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

如果你还没有，那么[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)。

### 创建一个 Okta 服务器

你需要保存一些信息，以便在你的应用中使用。创建一个名为`.env`的新文件。在其中，输入您的 Okta 组织的网址。

```
HOST_URL=http://localhost:3000
OKTA_ORG_URL=https://{yourOktaOrgUrl} 
```

您还需要一个随机字符串作为会话的应用程序密码。您可以使用以下命令生成该文件:

```
npm install -g uuid-cli
echo "APP_SECRET=`uuid`" >> .env 
```

接下来，登录你的开发者控制台，导航到**应用**，然后点击**添加应用**。选择**网**，然后点击**下一个**。给你的应用起一个名字，比如“JWTs 的乐趣”。将**基地 URI** 更改为`http://localhost:3000/`，将**登录重定向 URI** 为`http://localhost:3000/implicit/callback`，然后点击**完成**

点击**编辑**并添加`http://localhost:3000/`的**注销重定向 URI** ，然后点击**保存**。

![okta app settings](img/311916d371e363251d447912ed9737c0.png)

创建应用程序后，您进入的页面有更多信息需要保存到您的`.env`文件中。复制客户端 ID 和客户端密码。

```
OKTA_CLIENT_ID={yourClientId}
OKTA_CLIENT_SECRET={yourClientSecret} 
```

现在回到代码。您需要添加 Okta 的 OIDC 中间件来控制认证。它也依赖于使用会话。您需要使用`dotenv`从`.env`文件中读入变量。要安装您需要的依赖项，请运行以下命令:

```
npm install @okta/oidc-middleware@1.0.2 dotenv@6.1.0 express-session@1.15.6 
```

在您的`index.js`文件的最顶端，您需要包含`dotenv`。这将使得你的`.env`文件中的秘密可以被你的程序读取。在其他内容之前添加此行:

```
require('dotenv').config() 
```

为了安全地设置 Okta，您需要告诉 Express 使用 Okta 的 OIDC 中间件，这也需要会话。在您的`index.js`文件中查找包含`TODO: use Okta for auth`的行，然后在它上面输入以下内容，用您的所有环境变量初始化 Okta:

```
const session = require('express-session')
const { ExpressOIDC } = require('@okta/oidc-middleware')

app.use(session({
  secret: process.env.APP_SECRET,
  resave: true,
  saveUninitialized: false
}))

const oidc = new ExpressOIDC({
  issuer: `${process.env.OKTA_ORG_URL}/oauth2/default`,
  client_id: process.env.OKTA_CLIENT_ID,
  client_secret: process.env.OKTA_CLIENT_SECRET,
  redirect_uri: `${process.env.HOST_URL}/authorization-code/callback`,
  scope: 'openid profile'
})

app.use(oidc.router) 
```

现在您已经设置好了，创建安全路线将变得轻而易举！为了进行测试，用如下路径替换剩余的`TODO: use Okta for auth`行:

```
app.get('/', oidc.ensureAuthenticated(), (req, res) => res.send('Peekaboo!')) 
```

现在，当您转到`http://localhost:3000`时，您将被重定向到一个安全登录页面。由于您可能仍然从管理面板登录 Okta，您可能需要使用不同的浏览器或匿名窗口来查看登录屏幕，就像您网站的其他访问者一样。

![okta sign in page](img/889688b5508343ff790094d962eb29c6.png)

登录后，您将收到隐藏的消息！

## 了解关于节点、jwt 和安全用户管理的更多信息

当然，除了打印`Peekaboo!`之外，您还可以做很多更有意义的事情，但是这里的要点是，在快速设置之后，您可以通过添加一个简单的`oidc.ensureAuthenticated()`来为 Express 服务器中的任何路由添加身份验证。Okta 负责管理用户、存储会话、创建和验证 jwt，所以您不必这么做！

如果您想了解更多关于 jwt 或 Node 的知识，请查看 Okta 开发人员博客上的其他一些帖子:

*   如果你的 JWT 被偷了会怎么样？
*   [使用 OAuth 2.0 客户端证书保护节点 API](https://developer.okta.com/blog/2018/04/24/simple-node-authentication)
*   [教程:用 Java 创建和验证 JWTs】](https://developer.okta.com/blog/2018/10/31/jwts-with-java)
*   [简单节点认证](https://developer.okta.com/blog/2018/04/24/simple-node-authentication)
*   [为什么 jwt 不适合作为会话令牌](https://developer.okta.com/blog/2017/08/17/why-jwts-suck-as-session-tokens)

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，就像我们在脸书关注[，或者订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。

## 分享这篇文章