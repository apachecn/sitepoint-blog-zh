# 用 Node 和 OAuth 2.0 构建简单的 REST API

> 原文：<https://www.sitepoint.com/build-a-simple-rest-api-with-node-and-oauth-2-0/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/08/21/build-secure-rest-api-with-node)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

JavaScript 在网络上无处不在——几乎每个网页都会包含至少一些 JavaScript，即使没有，你的浏览器也可能有某种扩展将 JavaScript 代码注入页面。2018 年很难避免。

JavaScript 也可以在浏览器环境之外使用，从托管 web 服务器到控制 RC 汽车或运行成熟的操作系统。有时您希望几台服务器相互通信，无论是在本地网络上还是在互联网上。

今天，我将向您展示如何使用 Node.js 创建一个 REST API，并使用 OAuth 2.0 保护它以防止不必要的请求。REST APIs 遍布网络，但是没有合适的工具需要大量的样板代码。我将向您展示如何使用几个令人惊奇的工具来使这一切变得轻而易举，包括 Okta 来实现客户端凭证流，它在没有用户上下文的情况下安全地将两台机器连接在一起。

## 构建您的节点服务器

使用 [Express JavaScript 库](https://expressjs.com/)在 Node 中设置 web 服务器非常简单。创建一个包含您的服务器的新文件夹。

```
$ mkdir rest-api 
```

Node 使用一个`package.json`来管理依赖项和定义项目。要创建一个，使用`npm init`，它会问你一些问题来帮助你初始化项目。现在，你可以使用[标准 JS](https://standardjs.com/) 来执行一个编码标准，并把它作为测试。

```
$ cd rest-api

$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (rest-api)
version: (1.0.0)
description: A parts catalog
entry point: (index.js)
test command: standard
git repository:
keywords:
author:
license: (ISC)
About to write to /Users/Braden/code/rest-api/package.json:

{
  "name": "rest-api",
  "version": "1.0.0",
  "description": "A parts catalog",
  "main": "index.js",
  "scripts": {
    "test": "standard"
  },
  "author": "",
  "license": "ISC"
}

Is this OK? (yes) 
```

默认的入口点是`index.js`，所以您应该用这个名称创建一个新文件。下面的代码将为您提供一个真正的基本服务器，默认情况下，它不做任何事情，只监听端口 3000。

**index.js**

```
const express = require('express')
const bodyParser = require('body-parser')
const { promisify } = require('util')

const app = express()
app.use(bodyParser.json())

const startServer = async () => {
  const port = process.env.SERVER_PORT || 3000
  await promisify(app.listen).bind(app)(port)
  console.log(`Listening on port ${port}`)
}

startServer() 
```

`util`的`promisify`函数允许您获取一个期望回调的函数，而不是返回一个承诺，这是处理异步代码的新标准。这也让我们可以使用相对较新的`async` / `await`语法，让我们的代码看起来更漂亮。

为了做到这一点，您需要在文件顶部安装您`require`的依赖项。使用`npm install`添加它们。这将自动保存一些元数据到你的`package.json`文件中，并将它们安装在本地的`node_modules`文件夹中。

**注意**:你永远不应该将`node_modules`提交给源代码控制，因为它会很快变得臃肿不堪，而且`package-lock.json`文件会跟踪你曾经使用过的确切版本，如果你在另一台机器上安装它，它们会得到相同的代码。

```
$ npm install express@4.16.3 util@0.11.0 
```

对于一些快速林挺，安装`standard`作为开发依赖项，然后运行它以确保你的代码符合标准。

```
$ npm install --save-dev standard@11.0.1
$ npm test

> rest-api@1.0.0 test /Users/bmk/code/okta/apps/rest-api
> standard 
```

如果一切正常，您应该不会看到任何超过`> standard`行的输出。如果有错误，可能是这样的:

```
$ npm test

> rest-api@1.0.0 test /Users/bmk/code/okta/apps/rest-api
> standard

standard: Use JavaScript Standard Style (https://standardjs.com)
standard: Run `standard --fix` to automatically fix some problems.
  /Users/Braden/code/rest-api/index.js:3:7: Expected consistent spacing
  /Users/Braden/code/rest-api/index.js:3:18: Unexpected trailing comma.
  /Users/Braden/code/rest-api/index.js:3:18: A space is required after ','.
  /Users/Braden/code/rest-api/index.js:3:38: Extra semicolon.
npm ERR! Test failed.  See above for more details. 
```

现在您的代码已经准备好了，并且您已经安装了您的依赖项，您可以用`node .`运行您的服务器了(`.`要求查看当前目录，然后检查您的`package.json`文件，以查看该目录中使用的主文件是`index.js`):

```
$ node .

Listening on port 3000 
```

要测试它是否工作，您可以使用`curl`命令。还没有端点，所以 express 将返回一个错误:

```
$ curl localhost:3000 -i
HTTP/1.1 404 Not Found
X-Powered-By: Express
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
Content-Type: text/html; charset=utf-8
Content-Length: 139
Date: Thu, 16 Aug 2018 01:34:53 GMT
Connection: keep-alive

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>Cannot GET /</pre>
</body>
</html> 
```

即使它说这是一个错误，这很好。您还没有设置任何端点，所以 Express 唯一返回的是一个 404 错误。如果您的服务器根本没有运行，您会得到如下错误:

```
$ curl localhost:3000 -i
curl: (7) Failed to connect to localhost port 3000: Connection refused 
```

## 用 Express、Sequelize 和 Epilogue 构建您的 REST API

现在您已经有了一个工作的 Express 服务器，您可以添加一个 REST API 了。这实际上比你想象的要简单得多。我见过的最简单的方法是使用 [Sequelize](http://docs.sequelizejs.com/) 来定义你的数据库模式，使用 [Epilogue](https://github.com/dchester/epilogue) 来创建一些几乎没有样板文件的 REST API 端点。

您需要将这些依赖项添加到您的项目中。Sequelize 还需要知道如何与数据库通信。现在，使用 SQLite，因为它会让我们快速启动并运行。

```
npm install sequelize@4.38.0 epilogue@0.7.1 sqlite3@4.0.2 
```

用下面的代码创建一个新文件`database.js`。我将在下面更详细地解释每一部分。

**database.js**

```
const Sequelize = require('sequelize')
const epilogue = require('epilogue')

const database = new Sequelize({
  dialect: 'sqlite',
  storage: './test.sqlite',
  operatorsAliases: false
})

const Part = database.define('parts', {
  partNumber: Sequelize.STRING,
  modelNumber: Sequelize.STRING,
  name: Sequelize.STRING,
  description: Sequelize.TEXT
})

const initializeDatabase = async (app) => {
  epilogue.initialize({ app, sequelize: database })

  epilogue.resource({
    model: Part,
    endpoints: ['/parts', '/parts/:id']
  })

  await database.sync()
}

module.exports = initializeDatabase 
```

现在，您只需要将该文件导入您的主应用程序，并运行初始化功能。向您的`index.js`文件添加以下内容。

**index.js**

```
@@ -2,10 +2,14 @@ const express = require('express')
 const bodyParser = require('body-parser')
 const { promisify } = require('util')

+const initializeDatabase = require('./database')
+
 const app = express()
 app.use(bodyParser.json())

 const startServer = async () => {
+  await initializeDatabase(app)
+
   const port = process.env.SERVER_PORT || 3000
   await promisify(app.listen).bind(app)(port)
   console.log(`Listening on port ${port}`) 
```

现在，您可以测试语法错误，如果一切正常，就可以运行应用程序:

```
$ npm test && node .

> rest-api@1.0.0 test /Users/bmk/code/okta/apps/rest-api
> standard

Executing (default): CREATE TABLE IF NOT EXISTS `parts` (`id` INTEGER PRIMARY KEY AUTOINCREMENT, `partNumber` VARCHAR(255), `modelNu
mber` VARCHAR(255), `name` VARCHAR(255), `description` TEXT, `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL);
Executing (default): PRAGMA INDEX_LIST(`parts`)
Listening on port 3000 
```

在另一个终端中，您可以测试这是否有效(为了格式化 JSON 响应，我使用了一个 [json CLI](https://github.com/trentm/json) ，使用`npm install --global json`全局安装):

```
$ curl localhost:3000/parts
[]

$ curl localhost:3000/parts -X POST -d '{
  "partNumber": "abc-123",
  "modelNumber": "xyz-789",
  "name": "Alphabet Soup",
  "description": "Soup with letters and numbers in it"
}' -H 'content-type: application/json' -s0 | json
{
  "id": 1,
  "partNumber": "abc-123",
  "modelNumber": "xyz-789",
  "name": "Alphabet Soup",
  "description": "Soup with letters and numbers in it",
  "updatedAt": "2018-08-16T02:22:09.446Z",
  "createdAt": "2018-08-16T02:22:09.446Z"
}

$ curl localhost:3000/parts -s0 | json
[
  {
    "id": 1,
    "partNumber": "abc-123",
    "modelNumber": "xyz-789",
    "name": "Alphabet Soup",
    "description": "Soup with letters and numbers in it",
    "createdAt": "2018-08-16T02:22:09.446Z",
    "updatedAt": "2018-08-16T02:22:09.446Z"
  }
] 
```

### 这是怎么回事？

如果你遵循了所有这些，请随意跳过这一部分，但我保证会有一个解释。

`Sequelize`函数创建一个数据库。这是您配置细节的地方，比如使用哪种 SQL 方言。现在，使用 SQLite 快速启动并运行。

```
const database = new Sequelize({
  dialect: 'sqlite',
  storage: './test.sqlite',
  operatorsAliases: false
}) 
```

一旦创建了数据库，就可以使用`database.define`为每个表定义模式。创建一个名为`parts`的表，其中包含一些有用的字段来跟踪零件。默认情况下，当您创建或更新一行时，Sequelize 还会自动创建和更新`id`、`createdAt`和`updatedAt`字段。

```
const Part = database.define('parts', {
  partNumber: Sequelize.STRING,
  modelNumber: Sequelize.STRING,
  name: Sequelize.STRING,
  description: Sequelize.TEXT
}) 
```

Epilogue 需要访问您的 Express `app`才能添加端点。但是，`app`是在另一个文件中定义的。处理这个问题的一个方法是导出一个函数，该函数接受应用程序并对其进行处理。在另一个文件中，当我们导入这个脚本时，您会像`initializeDatabase(app)`一样运行它。

尾声需要用`app`和`database`来初始化。然后，您可以定义想要使用的 REST 端点。`resource`函数将包括`GET`、`POST`、`PUT`和`DELETE`动词的端点，大部分是自动完成的。

为了实际创建数据库，您需要运行`database.sync()`，它返回一个承诺。您需要等到它完成后再启动您的服务器。

`module.exports`命令表示可以从另一个文件导入`initializeDatabase`函数。

```
const initializeDatabase = async (app) => {
  epilogue.initialize({ app, sequelize: database })

  epilogue.resource({
    model: Part,
    endpoints: ['/parts', '/parts/:id']
  })

  await database.sync()
}

module.exports = initializeDatabase 
```

## 使用 OAuth 2.0 保护您的 Node + Express REST API

既然您已经有了一个 REST API 并正在运行，想象一下您希望一个特定的应用程序从远程位置使用它。如果你把它放在互联网上，那么任何人都可以随意添加、修改或删除部分内容。

为了避免这种情况，您可以使用 OAuth 2.0 客户端凭证流。这是一种让两台服务器相互通信的方式，不需要用户的上下文。两台服务器必须提前同意使用第三方授权服务器。假设有两个服务器，A 和 B，以及一个授权服务器。服务器 A 托管 REST API，服务器 B 想要访问该 API。

*   服务器 B 向授权服务器发送一个密钥来证明他们是谁，并请求一个临时令牌。
*   然后，服务器 B 像往常一样使用 REST API，但是将令牌和请求一起发送。
*   服务器 A 向授权服务器请求一些可用于验证令牌的元数据。
*   服务器 A 验证服务器 B 的请求。
    *   如果有效，就会发送一个成功的响应，服务器 B 会很高兴。
    *   如果令牌无效，则会发送一条错误消息，并且不会泄露任何敏感信息。

### 创建授权服务器

这就是 Okta 发挥作用的地方。Okta 可以作为一个授权服务器来保护你的数据。你可能会问自己“为什么是俄克拉荷马州？嗯，构建一个 REST 应用程序很酷，但构建一个安全的应用程序更酷。为此，您需要添加身份验证，以便用户在查看/修改组之前必须登录。在 Okta，我们的目标是让身份管理比您习惯的更简单、更安全、更具可扩展性。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

如果你还没有的话，[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)，让我们开始吧！

创建您的帐户后，登录到您的开发者控制台，导航到 **API** ，然后到**授权服务器**选项卡。点击您的`default`服务器的链接。

从该**设置**选项卡中，复制`Issuer`字段。您需要将它保存在 Node 应用程序可以读取的地方。在您的项目中，创建一个名为`.env`的文件，如下所示:

**。环境**

```
ISSUER=https://{yourOktaDomain}/oauth2/default 
```

`ISSUER`的值应该是设置页面的`Issuer URI`字段中的值。

![Higlighting the issuer URL.](img/9b539eb4892baf84fb12f8718f08d4d3.png)

**注意**:一般来说，你不应该将这个`.env`文件存储在源代码控制中。这允许多个项目使用相同的源代码，而不需要单独的分支。它还确保您的安全信息不是公开的(特别是如果您将代码作为开放源代码发布)。

接下来，导航到**范围**选项卡。点击**添加范围**按钮，为您的 REST API 创建一个范围。你需要给它一个名字(例如`parts_manager`)，如果你愿意，你可以给它一个描述。

![Add scope screenshot.](img/221cdf822dfd5d423c888c3715aaac45.png)

您还应该将作用域名添加到您的`.env`文件中，以便您的代码可以访问它。

**。环境**

```
ISSUER=https://{yourOktaDomain}/oauth2/default
SCOPE=parts_manager 
```

现在您需要创建一个客户端。导航到**应用**，然后点击**添加应用**。选择**服务**，然后点击**下一步**。输入服务名称(如`Parts Manager`)，然后点击**完成**。

这会将您带到一个包含您的客户端凭据的页面。这些是服务器 B(将使用 REST API 的服务器)进行身份验证所需的凭证。对于这个例子，客户机和服务器代码将在同一个存储库中，所以继续将这些数据添加到您的`.env`文件中。确保用本页中的值替换`{yourClientId}`和`{yourClientSecret}`。

```
CLIENT_ID={yourClientId}
CLIENT_SECRET={yourClientSecret} 
```

### 创建中间件来验证 Express 中的令牌

在 Express 中，您可以添加将在每个端点之前运行的中间件。然后，您可以添加元数据、设置标题、记录一些信息，甚至提前取消请求并发送错误消息。在这种情况下，您需要创建一些中间件来验证客户端发送的令牌。如果令牌有效，它将继续访问 REST API 并返回适当的响应。如果令牌无效，它将改为用一条错误消息来响应，以便只有授权的机器才能访问。

要验证令牌，可以使用 Okta 的中间件。您还需要一个名为 [dotenv](https://github.com/motdotla/dotenv) 的工具来加载环境变量:

```
npm install dotenv@6.0.0 @okta/jwt-verifier@0.0.12 
```

现在创建一个名为`auth.js`的文件，它将导出中间件:

**auth.js**

```
const OktaJwtVerifier = require('@okta/jwt-verifier')

const oktaJwtVerifier = new OktaJwtVerifier({ issuer: process.env.ISSUER })

module.exports = async (req, res, next) => {
  try {
    const { authorization } = req.headers
    if (!authorization) throw new Error('You must send an Authorization header')

    const [authType, token] = authorization.trim().split(' ')
    if (authType !== 'Bearer') throw new Error('Expected a Bearer token')

    const { claims } = await oktaJwtVerifier.verifyAccessToken(token)
    if (!claims.scp.includes(process.env.SCOPE)) {
      throw new Error('Could not verify the proper scope')
    }
    next()
  } catch (error) {
    next(error.message)
  }
} 
```

这个函数首先检查请求上的`authorization`头，否则抛出一个错误。如果它存在，它应该看起来像是`Bearer {token}`，其中`{token}`是一个 [JWT](https://www.jsonwebtoken.io/) 字符串。如果标题不是以`Bearer`开头，这将抛出另一个错误。然后我们将令牌发送给 [Okta 的 JWT 验证器](https://github.com/okta/okta-oidc-js/tree/master/packages/jwt-verifier)来验证令牌。如果令牌无效，JWT 验证器将抛出一个错误。否则，它将返回一个包含一些信息的对象。然后，您可以验证声明是否包含您期望的范围。

如果一切都成功了，它将不带任何参数地调用`next()`函数，这告诉 Express 可以继续执行链中的下一个函数(另一个中间件或最终端点)。如果您将一个字符串传递到`next`函数中，Express 会将其视为一个错误，并将其传递回客户端，而不会在链中继续。

你还是需要导入这个函数，把它作为中间件添加到你的 app 里。您还需要在索引文件的顶部加载`dotenv`，以确保来自`.env`的环境变量被加载到您的应用程序中。对`index.js`进行如下修改:

**index.js**

```
@@ -1,11 +1,14 @@
+require('dotenv').config()
 const express = require('express')
 const bodyParser = require('body-parser')
 const { promisify } = require('util')

+const authMiddleware = require('./auth')
 const initializeDatabase = require('./database')

 const app = express()
 app.use(bodyParser.json())
+app.use(authMiddleware)

 const startServer = async () => {
   await initializeDatabase(app) 
```

要测试请求是否被正确阻止，请尝试再次运行它…

```
$ npm test && node . 
```

…然后在另一个终端中运行几个`curl`命令来测试:

需要授权头:

```
$ curl localhost:3000/parts
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>You must send an Authorization header</pre>
</body>
</html> 
```

授权头中需要一个不记名令牌:

```
$ curl localhost:3000/parts -H 'Authorization: Basic asdf:1234'
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>Expected a Bearer token</pre>
</body>
</html> 
```

不记名令牌有效:

```
$ curl localhost:3000/parts -H 'Authorization: Bearer asdf'
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>Jwt cannot be parsed</pre>
</body>
</html> 
```

### 在节点中创建测试客户端

您现在已经禁止没有有效令牌的人访问该应用程序，但您如何获得令牌并使用它呢？我将向您展示如何在 Node 中编写一个简单的客户端，这也将帮助您测试一个有效的令牌是否有效。

```
npm install btoa@1.2.1 request-promise@4.2.2 
```

**client.js**

```
require('dotenv').config()
const request = require('request-promise')
const btoa = require('btoa')

const { ISSUER, CLIENT_ID, CLIENT_SECRET, SCOPE } = process.env

const [,, uri, method, body] = process.argv
if (!uri) {
  console.log('Usage: node client {url} [{method}] [{jsonData}]')
  process.exit(1)
}

const sendAPIRequest = async () => {
  const token = btoa(`${CLIENT_ID}:${CLIENT_SECRET}`)
  try {
    const auth = await request({
      uri: `${ISSUER}/v1/token`,
      json: true,
      method: 'POST',
      headers: {
        authorization: `Basic ${token}`
      },
      form: {
        grant_type: 'client_credentials',
        scope: SCOPE
      }
    })

    const response = await request({
      uri,
      method,
      body,
      headers: {
        authorization: `${auth.token_type}  ${auth.access_token}`
      }
    })

    console.log(response)
  } catch (error) {
    console.log(`Error: ${error.message}`)
  }
}

sendAPIRequest() 
```

这里的代码将变量从`.env`加载到环境中，然后从 Node 中获取它们。节点在`process.env`中存储环境变量(`process`是一个全局变量，有一堆有用的变量和函数)。

```
require('dotenv').config()
// ...
const { ISSUER, CLIENT_ID, CLIENT_SECRET, SCOPE } = process.env
// ... 
```

接下来，由于这将从命令行运行，您可以再次使用`process`来获取用`process.argv`传递的参数。这将为您提供一个包含所有传入参数的数组。前两个逗号前面没有变量名，因为在这种情况下前两个不重要；这些将只是到`node`的路径，以及脚本的名称(`client`或`client.js`)。

URL 是必需的，包括端点，但是方法和 JSON 数据是可选的。默认的方法是`GET`，所以如果你只是获取数据，你可以省略它。在这种情况下，你也不需要任何有效载荷。如果参数看起来不正确，那么这将退出程序，并显示一条错误消息和一个退出代码`1`，表示一个错误。

```
const [,, uri, method, body] = process.argv
if (!uri) {
  console.log('Usage: node client {url} [{method}] [{jsonData}]')
  process.exit(1)
} 
```

Node 目前不允许在主线程中使用`await`，所以要使用更干净的`async` / `await`语法，您必须创建一个函数，然后在之后调用它。

如果任何一个`await` ed 函数出现错误，`try` / `catch`就会被打印到屏幕上。

```
const sendAPIRequest = async () => {
  try {
    // ...
  } catch (error) {
    console.error(`Error: ${error.message}`)
  }
}

sendAPIRequest() 
```

这是客户端向授权服务器发送令牌请求的地方。对于授权服务器本身的授权，您需要使用基本的 Auth。当你得到一个要求输入用户名和密码的内置弹出窗口时，基本验证和浏览器使用的是一样的东西。假设你的用户名是`AzureDiamond`，密码是`hunter2`。然后你的浏览器会用冒号(`:`)把它们连接在一起，然后用 base64 编码(这就是`btoa`函数所做的)得到`QXp1cmVEaWFtb25kOmh1bnRlcjI=`。然后它发送一个授权报头`Basic QXp1cmVEaWFtb25kOmh1bnRlcjI=`。然后，服务器可以使用 base64 对令牌进行解码，以获得用户名和密码。

基本授权本身并不安全，因为它很容易被解码，这就是为什么`https`对于防止中间人攻击很重要。这里，客户端 ID 和客户端密码分别是用户名和密码。这也是保持你的`CLIENT_ID`和`CLIENT_SECRET`隐私的重要性。

对于 OAuth 2.0，您还需要指定授权类型，在本例中是`client_credentials`，因为您计划在两台机器之间进行对话。您还需要指定范围。这里可以添加许多其他选项，但这是我们在此演示中需要的全部内容。

```
const token = btoa(`${CLIENT_ID}:${CLIENT_SECRET}`)
const auth = await request({
  uri: `${ISSUER}/v1/token`,
  json: true,
  method: 'POST',
  headers: {
    authorization: `Basic ${token}`
  },
  form: {
    grant_type: 'client_credentials',
    scope: SCOPE
  }
}) 
```

一旦通过身份验证，您将获得一个访问令牌，您可以将它发送给 REST API，它看起来应该类似于`Bearer eyJra...HboUg`(实际的令牌要比这个长得多——可能大约 800 个字符)。令牌包含 REST API 验证您是谁、令牌何时过期所需的所有信息，以及所有其他类型的信息，如请求的范围、颁发者和用于请求令牌的客户机 ID。

然后，来自 REST API 的响应被打印到屏幕上。

```
const response = await request({
  uri,
  method,
  body,
  headers: {
    authorization: `${auth.token_type}  ${auth.access_token}`
  }
})

console.log(response) 
```

现在就开始测试吧。同样，用`npm test && node .`启动应用程序，然后尝试一些命令，如下所示:

```
$ node client http://localhost:3000/parts | json
[
  {
    "id": 1,
    "partNumber": "abc-123",
    "modelNumber": "xyz-789",
    "name": "Alphabet Soup",
    "description": "Soup with letters and numbers in it",
    "createdAt": "2018-08-16T02:22:09.446Z",
    "updatedAt": "2018-08-16T02:22:09.446Z"
  }
]

$ node client http://localhost:3000/parts post '{
  "partNumber": "ban-bd",
  "modelNumber": 1,
  "name": "Banana Bread",
  "description": "Bread made from bananas"
}' | json
{
  "id": 2,
  "partNumber": "ban-bd",
  "modelNumber": "1",
  "name": "Banana Bread",
  "description": "Bread made from bananas",
  "updatedAt": "2018-08-17T00:23:23.341Z",
  "createdAt": "2018-08-17T00:23:23.341Z"
}

$ node client http://localhost:3000/parts | json
[
  {
    "id": 1,
    "partNumber": "abc-123",
    "modelNumber": "xyz-789",
    "name": "Alphabet Soup",
    "description": "Soup with letters and numbers in it",
    "createdAt": "2018-08-16T02:22:09.446Z",
    "updatedAt": "2018-08-16T02:22:09.446Z"
  },
  {
    "id": 2,
    "partNumber": "ban-bd",
    "modelNumber": "1",
    "name": "Banana Bread",
    "description": "Bread made from bananas",
    "createdAt": "2018-08-17T00:23:23.341Z",
    "updatedAt": "2018-08-17T00:23:23.341Z"
  }
]

$ node client http://localhost:3000/parts/1 delete | json
{}

$ node client http://localhost:3000/parts | json
[
  {
    "id": 2,
    "partNumber": "ban-bd",
    "modelNumber": "1",
    "name": "Banana Bread",
    "description": "Bread made from bananas",
    "createdAt": "2018-08-17T00:23:23.341Z",
    "updatedAt": "2018-08-17T00:23:23.341Z"
  }
] 
```

## 通过 Okta 了解更多关于节点和 OAuth 2.0 客户端凭证的信息

希望您已经看到了在 Node 中创建一个 REST API 并防止未经授权的用户使用它是多么容易。你可以在 GitHub 上找到这个例子[的代码。](https://github.com/oktadeveloper/okta-node-rest-api-example)

既然您已经有机会制作自己的示例项目，那么就来看看其他一些关于 Node、OAuth 2.0 和 Okta 的优秀资源。也可以浏览 Okta 开发者博客，了解其他优秀文章。

*   [实现客户端凭证流程](https://developer.okta.com/authentication-guide/implementing-authentication/client-creds)
*   [验证访问令牌](https://developer.okta.com/authentication-guide/tokens/validating-access-tokens)
*   [定制您的授权服务器](https://developer.okta.com/authentication-guide/implementing-authentication/set-up-authz-server)
*   [教程:用 Node.js 构建一个基本的 CRUD App](https://developer.okta.com/blog/2018/06/28/tutorial-build-a-basic-crud-app-with-node)
*   [使用 OAuth 2.0 客户端证书保护节点 API](https://developer.okta.com/blog/2018/06/06/node-api-oauth-client-credentials)

和往常一样，你可以在下面的评论中提出反馈或问题，或者在 Twitter [@oktadev](https://twitter.com/OktaDev) 上联系我们。我们期待您的回复！

## 分享这篇文章