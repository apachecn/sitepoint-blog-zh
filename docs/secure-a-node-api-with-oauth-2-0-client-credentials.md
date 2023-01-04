# 使用 OAuth 2.0 客户端凭证保护节点 API

> 原文：<https://www.sitepoint.com/secure-a-node-api-with-oauth-2-0-client-credentials/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/06/06/node-api-oauth-client-credentials)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

保护服务器到服务器的 API 服务可能很棘手。OAuth 2.0 是将用户身份验证转移到另一个服务的绝佳方式，但是如果没有用户需要进行身份验证呢？在本文中，我将向您展示如何在用户上下文之外使用 OAuth 2.0，也就是所谓的客户端凭证流。

您可以使用第三方服务为您管理授权，而不是为您的客户端(其他服务器)存储和管理 API 密钥。其工作方式是，API 客户机向 OAuth 服务器发送请求，请求 API 令牌。然后，这个令牌连同请求一起从 API 客户机发送到您的 API 服务。一旦有了客户机的令牌，就可以验证它的有效性，而不需要存储任何关于客户机的信息。

![Client Credentials Flow](img/8c2395e0fdbc18857147ca9731964cec.png)

## 客户端凭据流验证的工作原理

验证 API 服务收到的令牌的一种方法是将令牌转发给 OAuth 服务器，询问它是否有效。这种方法的缺点是，发送到服务器的每个 API 请求都需要发送到 OAuth 服务器，这增加了响应客户机的时间。一种替代方法是使用被称为本地验证的东西，这是一种由 JSON Web Tokens (JWT)推广的策略。JWT 包含未加密的机器可读 JSON 格式的索赔(客户数据)。

当使用本地验证模式来验证 API 令牌(JWT)时，您可以使用数学来验证:

API 接收到的令牌没有被篡改，API 接收到的令牌没有过期，令牌中编码的某些 JSON 数据是您所期望的

这有多安全？你可能想知道。jwt 包含三个部分:头部、有效载荷和签名。标头和有效负载是简单的 base64 编码字符串，很容易被解密和读取。签名使用报头中列出的算法和私钥来创建报头和有效载荷的散列。没有私钥就不能重新创建散列，但是可以用公钥来验证它。

在某种程度上，这就像驾照或护照。这很难伪造，但很容易被人看到，看到你的名字、出生日期和其他信息。您可以扫描条形码，用黑光灯测试它，或者寻找水印来帮助验证它的有效性。

虽然在概念上相似，但一个有效的 JWT 实际上更难伪造。有足够技能的人可以制造出令人信服的驾照，但是如果没有私钥，现代计算机可能需要数年时间来暴力破解有效的 JWT 签名。令牌也应该有到期时间。虽然是可配置的，但一个固定的默认值是一小时。这意味着，如果客户端需要向您的 API 服务器发出新请求，它需要每 60 分钟请求一个新令牌。这是一个额外的安全层，以防您的令牌被泄露。谁知道呢？也许有一台量子计算机可以在几个小时内重现信号。

既然您已经理解了 OAuth 2.0 客户端凭证流工作的基础，那么让我们构建一个使用客户端凭证和 Okta 的节点 API。

## Okta 是什么？

简而言之，我们让[身份管理](https://developer.okta.com/product/user-management/)比您习惯的更简单、更安全、更可扩展。Okta 是一个 API 服务，允许您创建、编辑和安全地存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)了解更多信息

[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)，当你完成后，回来学习更多关于在 Node 中构建安全 API 的知识！

## 创建一个基本节点 API

为了开始，我将向您展示如何在 Node 中创建一个基本的 API。Node 在一个名为`package.json`的文件中保存了一个依赖关系列表和其他元数据。

假设您已经安装了 Node，为您的 API 服务器创建一个新文件夹。然后，您可以使用`npm`为您生成一个`package.json`。命令`npm init`会提示你一些信息，但是你可以一直按`Enter`来保持默认值。

```
$ mkdir client-credentials-flow
$ cd client-credentials-flow
$ git init
$ npm init 
```

在 Node 中启动并运行 API 服务器的最快方法是使用 Express。您可以使用命令`npm install express@4.16.3 --save`添加 Express 作为依赖项。这会创建一个名为`node_modules`的文件夹，express 和它所依赖的任何东西都会被下载到这个文件夹中，然后你的应用程序就可以使用它们了。为了使开发进行得更快，您还可以添加一个名为`nodemon`的开发依赖项，它将在您修改代码时重启您的服务器。要添加一个开发依赖项，可以使用`-D`标志:`npm install -D nodemon@1.17.5`。

在构建节点应用程序时，您通常希望忽略在 git repo 中存储`node_modules`文件夹。你可以通过在你的`.gitignore`文件中添加`node_modules`来实现。

```
echo node_modules >> .gitignore 
```

软件包管理器也会包含一个文件(例如`package-lock.json`或`yarn.lock`)，这样当你在另一台机器上下载`node_modules`(用`npm install`或`yarn`)时，同样的版本也会被下载。这有助于防止服务器之间的任何不一致，并使您不会怀疑为什么有些东西在您的机器上可以工作，但在生产中却不能。确保将该文件提交到您 git repo 中:

```
$ git add .
$ git commit -m "Adding package files." 
```

您也可以将脚本添加到您的`package.json`文件夹来运行这些命令。用命令`node .`创建一个`start`脚本(`.`告诉它运行在你的`package.json`中列为`main`的脚本，默认为`index.js`)。您还需要用命令`nodemon *.js node .`创建一个`dev`脚本。命令行依赖项，如`nodemon`，在节点脚本中运行时位于路径中。您现在可以使用`npm start`或`npm run dev`运行这些命令。您的`package.json`文件现在应该看起来像这样:

**package.json**

```
{
  "name": "client-credentials-flow",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "nodemon *.js node .",
    "start": "node .",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.16.3"
  },
  "devDependencies": {
    "nodemon": "^1.17.5"
  }
} 
```

现在对于最基本的“Hello World”express 应用程序:

**index.js**

```
const express = require('express')
const app = express()

app.get('/', (req, res) => res.send('Hello World!'))

const port = process.env.PORT || 3000
app.listen(port, () => console.log(`Listening on port ${port}`)) 
```

就是这样！要启动它，在终端窗口中键入`npm run dev`。我们进行更改时，您可以让它一直运行，它会自动重新启动以反映新的更改。现在在你的浏览器中进入`http://localhost:3000`(或者在命令行中使用`curl http://localhost:3000`)，你应该会看到`Hello World!`的回应。

## 向 OAuth 2.0 提供者注册您的节点 API

现在来保护应用程序。这是您需要设置 OAuth 2.0 服务的地方。Okta 是一种基于云的服务，允许开发人员轻松安全地存储 OAuth 2.0 令牌、用户帐户和用户数据，然后将它们与一个或多个应用程序连接起来。Okta 还提供了许多语言的库，包括 Node，使得开发者可以很容易地将它们的 API 集成到各种各样的应用中。

您可以使用 Okta 快速轻松地设置服务器到服务器的身份验证。如果你还没有账户，[注册一个免费的 Okta 开发者账户](https://developer.okta.com/signup/)。注册后，您将获得一个唯一的 Okta Org URL(例如`https://{yourOktaDomain}`)和一封电子邮件来激活您的新帐户。

为了使客户端到服务器的认证工作，您需要两个部分:授权服务器和测试客户端/应用程序。

### 创建授权服务器

授权服务器是客户端可以请求在 API 服务器上使用的令牌的地方。在 Okta 仪表板中，点击标题中的 **API** 选项卡，然后选择**授权服务器**选项卡。点击**添加授权服务器**，然后给你的服务器起一个有用的名字和描述。`Audience`应该是消费令牌的服务器的绝对路径。

![Add Authorization Server](img/bc61fec5454520cdc7d04af307c721ff.png)

一旦创建了授权服务器，您将需要一个供客户端访问的范围。点击**范围**选项卡，添加一个范围。您可以拥有许多这样的工具，它们可以帮助定义 API 的哪些部分正在被使用，或者甚至是谁正在使用它。

![Add Scope](img/135a5ab9b5fda2eeaded911e81c0791e.png)

现在您有了一个范围，您还需要指定一些规则来说明谁可以访问它。点击**访问策略**选项卡，创建一个新策略。目前，只允许访问`All clients`。然后点击**添加规则**并为其命名。由于这仅适用于客户端凭证，因此删除代表用户的其他授权类型(`Authorization Code`、`Implicit`和`Resource Owner Password`)，这样唯一的授权类型就是`Client Credentials`。除此之外，现在使用默认设置。

![Add Rule](img/e36b3334f0108cd1d9bd7567b516d9c3.png)

回到**设置**选项卡，记下**发行人**。这是客户端将用来请求令牌的地址，也是 API 服务器将用来验证这些令牌是否有效的地址。

### 创建测试客户端

在您的 Okta 仪表盘中，点击顶部标题中的**应用程序**。应用程序也称为客户端，因此这是您可以创建测试客户端的地方。点击**添加应用**，选择**服务**(机器对机器)。它需要的唯一信息是一个名称，所以您可以使用类似于`Test Client`的东西。这将为您的客户端(在这个测试案例中，就是您)提供凭证。

![Client Credentials](img/5194ab3d94b9591f7f12b02817143752.png)

## 保护您的节点 API

现在，您已经完成了拼图的所有部分，因此只有经过身份验证的用户才能收到受人喜爱的“Hello World”欢迎消息，其他人都会收到错误消息。

### 安全存储您的凭据

您会希望安全地存储您的凭据。一种方法是在本地保存一个不存储在 git 中的文件(如果您的代码是开源的，这尤其有用，但不管怎样，这仍然是一件好事)。这也让您可以对多个应用程序(例如开发和生产环境)使用相同的代码。

继续创建一个`.env`文件，包含来自您的授权服务器的发行者和来自您的测试应用程序的客户机凭证。确保将它添加到您的`.gitignore`文件中，这样它就不会被添加到您的 git repo: `echo .env >> .gitignore`中。您的`.env`文件应该是这样的:

**。环境**

```
ISSUER=https://{yourOktaDomain}/oauth2/abcdefg1234567
DEFAULT_SCOPE=such_scope
TEST_CLIENT_ID=client-id
TEST_CLIENT_SECRET=client-secret 
```

代码读取`.env`文件的一个快速方法是使用一个名为`dotenv`的库。可以用`npm install dotenv`安装。然后在`index.js`的第一行加上`require('dotenv').config()`。您希望它是第一个运行的对象，以便您的代码的其余部分可以访问这些环境变量。

### 验证客户端请求

Okta 提供了一个节点库来帮助验证 JSON Web 令牌。当它第一次看到验证令牌的请求时，它将通过您的授权服务器获取公钥 Okta。默认情况下，它将保持这些键一个小时，尽管这是可配置的。如果有一个令牌无法被验证，它将与 Okta 一起检查是否有新的密钥可以使用。如果它仍然不能验证它，库将抛出一个错误。可以用`npm install @okta/jwt-verifier@0.0.12`安装包。

你需要提供带有 JWT 的包裹。由您来告诉您的客户端如何提供令牌，这可以通过多种方式来完成。常见的做法是在 HTTP(s)请求中使用类似于`Bearer MG9h...NhOq==`的`Authorization`头。修改您的`/`端点来寻找令牌，并用 Okta 验证它。

**index.js**

```
const OktaJwtVerifier = require('@okta/jwt-verifier')
const oktaJwtVerifier = new OktaJwtVerifier({
  issuer: process.env.ISSUER,
})

app.get('/', async (req, res) => {
  try {
    const { authorization } = req.headers
    if (!authorization) throw new Error('You must send an Authorization header')

    const [authType, token] = authorization.split(' ')
    if (authType !== 'Bearer') throw new Error('Expected a Bearer token')

    await oktaJwtVerifier.verifyAccessToken(token)
    res.json('Hello World!')
  } catch (error) {
    res.json({ error: error.message })
  }
}) 
```

再次尝试前往`http://localhost:3000`。这一次您将得到一条错误消息，因为您没有通过身份验证。

如果你不熟悉相对较新的`async/await`语法，你可能会觉得有点奇怪。这里发生的是函数被标记为`async`，所以它总是返回一个`Promise`。当它看到`await`关键字时，函数的其余部分将暂停，直到响应返回。同时，主线程被释放出来供其他 JavaScript 代码执行。

在这个例子中，`verifyAccessToken`如果不能立即验证令牌，就向 Okta 发送一个请求。如果在代码的其他地方有一个`setInterval`,在等待 Okta 的响应时，代码仍然可以执行。

当`verifyAccessToken`完成时，如果令牌无效，它将抛出一个错误。因此，如果它通过了那一行而没有抛出一个错误，就可以安全地假定客户端已经过验证，并且您可以发送“Hello World”消息。如果您想要关于客户端的更多信息，您可以使用`const jwt = await oktaJwtVerifier.verifyAccessToken(token)`从验证器获得响应。

## 测试您的安全 API

您现在可以看到，在没有正确认证的情况下，您在浏览器中得到一个错误，但是我没有向您展示您仍然可以正确地验证自己。为了从授权服务器获得令牌，您可以编写一个简单的节点脚本。使用原生节点`request`有点乏味，所以您可以使用`request-promise`库，它将允许您继续使用承诺和漂亮的`async/await`语法。您还需要`btoa`，它将一个字符串转换成 base64。

```
npm install request-promise@4.2.2 btoa@1.2.1 
```

**test.js**

```
require('dotenv').config()
const request = require('request-promise')
const btoa = require('btoa')
const { ISSUER, TEST_CLIENT_ID, TEST_CLIENT_SECRET, DEFAULT_SCOPE } = process.env

const test = async () => {
  const token = btoa(`${TEST_CLIENT_ID}:${TEST_CLIENT_SECRET}`)
  try {
    const { token_type, access_token } = await request({
      uri: `${ISSUER}/v1/token`,
      json: true,
      method: 'POST',
      headers: {
        authorization: `Basic ${token}`,
      },
      form: {
        grant_type: 'client_credentials',
        scope: DEFAULT_SCOPE,
      },
    })

    const response = await request({
      uri: 'http://localhost:3000',
      json: true,
      headers: {
        authorization: [token_type, access_token].join(' '),
      },
    })

    console.log(response)
  } catch (error) {
    console.log(`Error: ${error.message}`)
  }
}

test() 
```

现在，您的应用程序仍在端口 3000 上运行，使用`node test.js`运行测试。这将向 Okta 发送一个获取令牌的请求，然后将该令牌转发给 API 服务器并打印结果。你应该得到一个很好的“你好，世界”问候！

## 即时注册客户端

您有一个测试客户端，但是在现实世界中，您可能希望让人们注册您的 API，而不是让您登录 Okta 并手动为他们创建一个客户端。您可能也不希望每个人都共享同一个客户端 id 和密码，这样您就可以跟踪谁发出了什么请求，例如，多长时间一次。

Okta 提供了一个 API，允许你自动完成各种任务。其中之一就是创建新的应用程序。Okta 还有一个节点库，让它变得非常简单。为了让你的应用通过 Okta 认证，你需要一个 API 令牌。登录您的仪表板，然后从标题中的 **API** 下拉列表中选择**令牌**。点击**创建令牌**并给它一个有意义的名称。这一次它会给你一个令牌——如果你丢失了它，你需要重新创建一个。继续将它作为`TOKEN`添加到您的`.env`文件中。

用`npm install @okta/okta-sdk-nodejs@1.1.0`安装 Okta 节点 SDK。它需要您的组织 URL，所以您也应该将它添加到您的`.env`文件中。然后在`index.js`创建一条新路线来注册新客户。

**index.js**

```
const okta = require('@okta/okta-sdk-nodejs')
const oktaClient = new okta.Client({
  orgUrl: process.env.ORG_URL,
  token: process.env.TOKEN,
})

app.get('/register/:label', async (req, res) => {
  try {
    const application = await oktaClient.createApplication({
      name: 'oidc_client',
      label: req.params.label,
      signOnMode: 'OPENID_CONNECT',
      credentials: {
        oauthClient: {},
      },
      settings: {
        oauthClient: {
          grant_types: ['client_credentials'],
          application_type: 'service',
        },
      },
    })

    const { client_id, client_secret } = application.credentials.oauthClient

    res.json({
      client_id,
      client_secret,
      request_token_url: `${process.env.ISSUER}/v1/token`,
    })
  } catch (error) {
    res.json({ error: error.message })
  }
}) 
```

您现在可以转到`http://localhost:3000/register/Awesome+App+Name`(在您的浏览器中即可)来创建新的客户端。当你第一次去那里的时候，它会给你一个客户端 id 和密码，并提醒你在哪里申请令牌。您可以在`.env`中用这个新的客户端 id 和密码替换之前的客户端 id 和密码，然后重新运行`test.js`来查看这个客户端现在还能工作。

如果你重新登录你的 Okta 开发者控制台，你会看到“Awesome App Name”已经被添加为一个应用程序。

记住，这是一个测试…这只是一个测试。您可能不希望任何人在没有任何验证的情况下公开注册 API 密钥。这可能是实现某种 CAPTCHA 或要求用户认证以获得 API 密钥的好地方。一旦他们有了 API 密钥，他们就可以在应用程序中使用它，而不需要进一步的用户验证。

## 通过 Okta 了解更多关于节点和 OAuth 2.0 客户端凭证的信息

我希望我已经向您展示了为您的节点 API 提供出色的安全性是非常容易的。Okta 在保护您的应用程序方面提供了更多。如果您想了解更多关于 Node、Okta 和 OAuth 2.0 客户端凭证的信息，请查看其他资源或浏览 Okta 开发人员博客的其余部分:

*   [Okta 节点 SDK](https://github.com/okta/okta-sdk-nodejs)
*   [实现客户端凭证流程](https://developer.okta.com/authentication-guide/implementing-authentication/client-creds)
*   [验证访问令牌](https://developer.okta.com/authentication-guide/tokens/validating-access-tokens)
*   [通过 Spring Boot 和 OAuth 2.0 实现安全的服务器到服务器通信](https://developer.okta.com/blog/2018/04/02/client-creds-with-spring-boot)
*   如何保护你的？带有令牌认证的. NET Web API

和往常一样，你可以在下面的评论中提出反馈或问题，或者在 Twitter [@oktadev](https://twitter.com/OktaDev) 上联系我们。我们期待您的回复！

## 分享这篇文章