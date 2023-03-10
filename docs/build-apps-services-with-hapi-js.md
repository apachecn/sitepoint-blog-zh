# 使用 Hapi.js 框架构建应用和服务

> 原文：<https://www.sitepoint.com/build-apps-services-with-hapi-js/>

Hapi.js 被描述为“构建应用和服务的丰富框架”。哈比神的智能默认值使创建 JSON APIs 变得轻而易举，其模块化设计和插件系统允许您轻松扩展或修改其行为。

最近发布的[版本 17.0](https://github.com/hapijs/hapi/issues/3658) 已经完全包含了`async`和`await`，所以你将编写看似同步但非阻塞的*和*代码，避免回调地狱。双赢。

## 该项目

在本文中，我们将从头开始为一个典型的博客构建以下 API:

```
# RESTful actions for fetching, creating, updating and deleting articles
GET    /articles                articles#index
GET    /articles/:id            articles#show
POST   /articles                articles#create
PUT    /articles/:id            articles#update
DELETE /articles/:id            articles#destroy

# Nested routes for creating and deleting comments
POST   /articles/:id/comments   comments#create
DELETE /articles/:id/comments   comments#destroy

# Authentication with JSON Web Tokens (JWT)
POST   /authentications         authentications#create 
```

文章将涵盖:

*   哈比神的核心 api:路由、请求和响应
*   关系数据库中的模型和持久性
*   文章和评论的路线和操作
*   用 HTTPie 测试 REST API
*   使用 JWT 进行身份验证并保护路由
*   确认
*   根路由`/`的 HTML 视图和布局。

## 起点

确保您已经安装了 Node.js 的最新版本；`node -v`应返回`8.9.0`或更高。

使用 git 从这里下载启动代码:

```
git clone https://github.com/markbrown4/hapi-api.git
cd hapi-api
npm install 
```

打开`package.json`，你会看到“开始”脚本用`nodemon`运行`server.js`。当我们更改文件时，这会帮助我们重新启动服务器。

运行`npm start`并打开`http://localhost:3000/`:

```
[{ "so": "hapi!" }] 
```

我们来看一下出处:

```
// server.js
const Hapi = require('hapi')

// Configure the server instance
const server = Hapi.server({
  host: 'localhost',
  port: 3000
})

// Add routes
server.route({
  method: 'GET',
  path: '/',
  handler: () => {
    return [{ so: 'hapi!' }]
  }
})

// Go!
server.start().then(() => {
  console.log('Server running at:', server.info.uri)
}).catch(err => {
  console.log(err)
  process.exit(1)
}) 
```

## 路由处理器

路由处理程序是这段代码中最有趣的部分。用下面的代码替换它，逐个注释掉返回行，并在浏览器中测试响应。

```
server.route({
  method: 'GET',
  path: '/',
  handler: () => {
    // return [{ so: 'hapi!' }]
    return 123
    return `HTML rules!`
    return null
    return new Error('Boom')
    return Promise.resolve({ whoa: true })
    return require('fs').createReadStream('index.html')
  }
}) 
```

要发送响应，您只需简单地输入一个值，哈比神就会发送适当的主体和头。

*   一个`Object`将用字符串化的 JSON 和`Content-Type: application/json`来响应
*   `String`值将为`Content-Type: text/html`
*   也可以返回一个`Promise`或者`Stream`。

handler 函数通常被做成`async`用于带有承诺的更干净的控制流:

```
server.route({
  method: 'GET',
  path: '/',
  handler: async () => {
    let html = await Promise.resolve(`Google`)
    html = html.replace('Google', 'Hapi')

    return html
  }
}) 
```

虽然不总是*和`async`一起*清洁。有时候，兑现承诺更简单:

```
handler: () => {
  return Promise.resolve(`Google`)
    .then(html => html.replace('Google', 'Hapi'))
} 
```

当我们开始与数据库交互时，我们将看到`async`如何帮助我们的更好的例子。

## 模型层

像流行的 [Express.js](https://expressjs.com/) 框架一样，哈比神是一个最小的框架，不提供任何关于模型层或持久性的建议。您可以选择任何您喜欢的数据库和 ORM，或者什么都不选——由您决定。在本教程中，我们将使用 [SQLite](https://www.sqlite.org/) 和 [Sequelize ORM](http://sequelizejs.com) 来提供一个与数据库交互的简洁 API。

SQLite 预装在 macOS 和大多数 Linux 发行版中。可以检查一下有没有装`sqlite -v`。如果没有，你可以在 [SQLite 网站](https://www.sqlite.org/)找到安装说明。

Sequelize 可以与许多流行的关系数据库一起工作，比如 Postgres 或 MySQL，所以您需要安装`sequelize`和`sqlite3`适配器:

```
npm install --save sequelize sqlite3 
```

让我们连接到数据库，为`articles`编写第一个表定义:

```
// models.js
const path = require('path')
const Sequelize = require('sequelize')

// configure connection to db host, user, pass - not required for SQLite
const sequelize = new Sequelize(null, null, null, {
  dialect: 'sqlite',
  storage: path.join('tmp', 'db.sqlite') // SQLite persists its data directly to file
})

// Here we define our Article model with a title attribute of type string, and a body attribute of type text. By default, all tables get columns for id, createdAt, updatedAt as well.
const Article = sequelize.define('article', {
  title: Sequelize.STRING,
  body: Sequelize.TEXT
})

// Create table
Article.sync()

module.exports = {
  Article
} 
```

让我们测试一下我们的新模型，方法是导入它并用下面的代码替换我们的路由处理程序:

```
// server.js
const { Article } = require('./models')

server.route({
  method: 'GET',
  path: '/',
  handler: () => {
    // try commenting these lines out one at a time
    return Article.findAll()
    return Article.create({ title: 'Welcome to my blog', body: 'The happiest place on earth' })
    return Article.findById(1)
    return Article.update({ title: 'Learning Hapi', body: `JSON API's a breeze.` }, { where: { id: 1 } })
    return Article.findAll()
    return Article.destroy({ where: { id: 1 } })
    return Article.findAll()
  }
}) 
```

如果你熟悉 SQL 或其他 ORM，那么 [Sequelize API](http://docs.sequelizejs.com/manual/tutorial/models-usage.html) 应该是不言自明的，它是用承诺构建的，所以它也能与哈比神的`async`处理程序一起工作。

*注意:使用`Article.sync()`创建表格，或者使用`Article.sync({ force: true })`到*拖放*并创建表格，对于本演示来说都是可以的。如果您想在生产中使用它，您应该检查一下 [sequelize-cli](https://github.com/sequelize/cli) 并为任何模式更改编写[迁移](http://docs.sequelizejs.com/manual/tutorial/migrations.html)。*

## 我们平静的行动

让我们建立以下路线:

```
GET     /articles        fetch all articles
GET     /articles/:id    fetch article by id
POST    /articles        create article with `{ title, body }` params
PUT     /articles/:id    update article with `{ title, body }` params
DELETE  /articles/:id    delete article by id 
```

添加一个新文件`routes.js`，将服务器配置与应用程序逻辑分开:

```
// routes.js
const { Article } = require('./models')

exports.configureRoutes = (server) => {
  // server.route accepts an object or an array
  return server.route([{
    method: 'GET',
    path: '/articles',
    handler: () => {
      return Article.findAll()
    }
  }, {
    method: 'GET',
    // The curly braces are how we define params (variable path segments in the URL)
    path: '/articles/{id}',
    handler: (request) => {
      return Article.findById(request.params.id)
    }
  }, {
    method: 'POST',
    path: '/articles',
    handler: (request) => {
      const article = Article.build(request.payload.article)

      return article.save()
    }
  }, {
    // method can be an array
    method: ['PUT', 'PATCH'],
    path: '/articles/{id}',
    handler: async (request) => {
      const article = await Article.findById(request.params.id)
      article.update(request.payload.article)

      return article.save()
    }
  }, {
    method: 'DELETE',
    path: '/articles/{id}',
    handler: async (request) => {
      const article = await Article.findById(request.params.id)

      return article.destroy()
    }
  }])
} 
```

在启动服务器之前，导入并配置我们的路由:

```
// server.js
const Hapi = require('hapi')
const { configureRoutes } = require('./routes')

const server = Hapi.server({
  host: 'localhost',
  port: 3000
})

// This function will allow us to easily extend it later
const main = async () => {
  await configureRoutes(server)
  await server.start()

  return server
}

main().then(server => {
  console.log('Server running at:', server.info.uri)
}).catch(err => {
  console.log(err)
  process.exit(1)
}) 
```

## 测试我们的 API 非常简单

HTTPie 是一个伟大的小命令行 HTTP 客户端，可以在所有操作系统上工作。遵循[文档](https://httpie.org/doc#installation)中的安装说明，然后尝试从终端点击 API:

```
http GET http://localhost:3000/articles
http POST http://localhost:3000/articles article:='{"title": "Welcome to my blog", "body": "The greatest place on earth"}'
http POST http://localhost:3000/articles article:='{"title": "Learning Hapi", "body": "JSON APIs a breeze."}'
http GET http://localhost:3000/articles
http GET http://localhost:3000/articles/2
http PUT http://localhost:3000/articles/2 article:='{"title": "True happiness, is an inner quality"}'
http GET http://localhost:3000/articles/2
http DELETE http://localhost:3000/articles/2
http GET http://localhost:3000/articles 
```

好的，一切似乎都很好。让我们再尝试几个:

```
http GET http://localhost:3000/articles/12345
http DELETE http://localhost:3000/articles/12345 
```

呀呀呀！当我们试图获取一个不存在的文章时，我们得到一个带有空主体的`200`，我们的销毁处理程序抛出一个`Error`，导致一个`500`。这是因为当`findById`找不到记录时，默认情况下会返回`null`。我们希望我们的 API 在这两种情况下都用一个`404`来响应。有几种方法可以实现这一点。

### 防御性地检查`null`值并返回错误

有一个名为`boom`的包可以帮助创建标准的错误响应对象:

```
npm install --save boom 
```

导入并修改`GET /articles/:id`路线:

```
// routes.js
const Boom = require('boom')

{
  method: 'GET',
  path: '/articles/{id}',
  handler: async (request) => {
    const article = await Article.findById(request.params.id)
    if (article === null) return Boom.notFound()

    return article
  }
} 
```

### 扩展序列。抛出错误的模型

`Sequelize.Model`是对原型的引用，我们所有的模型都继承自它，所以我们可以很容易地向`findById`添加一个新方法`find`，如果它返回`null`就会抛出一个错误:

```
// models.js
const Boom = require('boom')

Sequelize.Model.find = async function (...args) {
  const obj = await this.findById(...args)
  if (obj === null) throw Boom.notFound()

  return obj
} 
```

然后，我们可以将处理程序恢复到以前的荣耀状态，并用`find`替换出现的`findById`:

```
{
  method: 'GET',
  path: '/articles/{id}',
  handler: (request) => {
    return Article.find(request.params.id)
  }
} 
```

```
http GET http://localhost:3000/articles/12345
http DELETE http://localhost:3000/articles/12345 
```

*轰*。我们现在得到一个`404` *未找到*错误，每当我们试图从数据库中获取不存在的东西。我们已经用一个易于理解的约定取代了我们的自定义错误检查，使我们的代码保持干净。

*注意:另一个流行的请求 REST APIs 的工具是 [Postman](https://www.getpostman.com/) 。如果您喜欢 UI 和保存常见请求的能力，这是一个很好的选择。*

## 路径参数

哈比神的路由与其他框架略有不同。路线是根据路径的*特性*选择的，因此定义它们的顺序并不重要。

*   `/hello/{name}`匹配`/hello/bob`并通过`'bob'`作为*名*参数
*   `/hello/{name?}`—`?`使名称可选，并且匹配`/hello`和`/hello/bob`
*   `/hello/{name*2}`—`*`表示多个段，通过将`'bob/marley'`作为*名*参数传递来匹配`/hello/bob/marley`
*   `/{args*}`匹配`/any/route/imaginable`，特异性最低。

## 请求对象

传递给路由处理器的[请求对象](https://hapijs.com/api#request-properties)具有以下有用的属性:

*   `request.params` —路径参数
*   `request.query` —查询字符串参数
*   `request.payload` —请求 JSON 或表单参数的主体
*   `request.state` —饼干
*   `request.headers`
*   `request.url`

## 添加第二个模型

我们的第二个模型将处理对文章的评论。以下是完整的文件:

```
// models.js
const path = require('path')
const Sequelize = require('sequelize')
const Boom = require('boom')

Sequelize.Model.find = async function (...args) {
  const obj = await this.findById(...args)
  if (obj === null) throw Boom.notFound()

  return obj
}

const sequelize = new Sequelize(null, null, null, {
  dialect: 'sqlite',
  storage: path.join('tmp', 'db.sqlite')
})

const Article = sequelize.define('article', {
  title: Sequelize.STRING,
  body: Sequelize.TEXT
})

const Comment = sequelize.define('comment', {
  commenter: Sequelize.STRING,
  body: Sequelize.TEXT
})

// These associations add an articleId foreign key to our comments table
// They add helpful methods like article.getComments() and article.createComment()
Article.hasMany(Comment)
Comment.belongsTo(Article)

// Create tables
Article.sync()
Comment.sync()

module.exports = {
  Article,
  Comment
} 
```

为了创建和删除评论，我们可以在文章路径下添加嵌套路由:

```
// routes.js
const { Article, Comment } = require('./models')

{
  method: 'POST',
  path: '/articles/{id}/comments',
  handler: async (request) => {
    const article = await Article.find(request.params.id)

    return article.createComment(request.payload.comment)
  }
}, {
  method: 'DELETE',
  path: '/articles/{articleId}/comments/{id}',
  handler: async (request) => {
    const { id, articleId } = request.params
    // You can pass options to findById as a second argument
    const comment = await Comment.find(id, { where: { articleId } })

    return comment.destroy()
  }
} 
```

最后，我们可以扩展`GET /articles/:id`来返回文章*和*的评论:

```
{
  method: 'GET',
  path: '/articles/{id}',
  handler: async (request) => {
    const article = await Article.find(request.params.id)
    const comments = await article.getComments()

    return { ...article.get(), comments }
  }
} 
```

`article`这里是*模型*对象；`article.get()`返回一个带有模型值的普通对象，在这个对象上我们可以使用 spread 操作符来组合我们的注释。让我们来测试一下:

```
http POST http://localhost:3000/articles/3/comments comment:='{ "commenter": "mb4", "body": "Agreed, this blog rules!" }'
http POST http://localhost:3000/articles/3/comments comment:='{ "commenter": "Nigerian prince", "body": "You are the beneficiary of a Nigerian prince’s $4,000,000 fortune." }'
http GET http://localhost:3000/articles/3
http DELETE http://localhost:3000/articles/3/comments/2
http GET http://localhost:3000/articles/3 
```

我们的博客 API 几乎可以交付生产了，只需要一些最后的润色。

## 向 JWT 认证

[JSON Web 令牌](https://jwt.io/)是 API 的一种通用认证机制。有一个插件`hapi-auth-jwt2`可以设置它，但它还没有更新到哈比神 17.0，所以我们现在需要安装一个分支:

```
npm install --save salzhrani/hapi-auth-jwt2#v-17 
```

下面的代码注册了`hapi-auth-jwt2`插件，并使用`jwt` *方案*设置了一个名为`admin`的*策略*。如果在头、查询字符串或 cookie 中发送了有效的 JWT 令牌，它将调用我们的`validate`函数来验证我们是否乐意授予这些凭证访问权限:

```
// auth.js
const jwtPlugin = require('hapi-auth-jwt2').plugin
// This would be in an environment variable in production
const JWT_KEY = 'NeverShareYourSecret'

var validate = function (credentials) {
  // Run any checks here to confirm we want to grant these credentials access
  return {
    isValid: true,
    credentials // request.auth.credentials
  }
}

exports.configureAuth = async (server) => {
  await server.register(jwtPlugin)
  server.auth.strategy('admin', 'jwt', {
    key: JWT_KEY,
    validate,
    verifyOptions: { algorithms: [ 'HS256' ] }
  })

  // Default all routes to require JWT and opt out for public routes
  server.auth.default('admin')
} 
```

接下来，在启动服务器之前导入并配置我们的身份验证策略:

```
// server.js
const { configureAuth } = require('./auth')

const main = async () => {
  await configureAuth(server)
  await configureRoutes(server)
  await server.start()

  return server
} 
```

现在所有的路线都需要我们的`admin`认证策略。试试这三个:

```
http GET localhost:3000/articles
http GET localhost:3000/articles Authorization:yep
http GET localhost:3000/articles Authorization:eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwibmFtZSI6IkFudGhvbnkgVmFsaWQgVXNlciIsImlhdCI6MTQyNTQ3MzUzNX0.KA68l60mjiC8EXaC2odnjFwdIDxE__iDu5RwLdN1F2A 
```

最后一个应该包含一个有效的令牌，并从数据库返回文章。要公开一条路由，我们只需要将`config: { auth: false }`添加到 route 对象中。例如:

```
{
  method: 'GET',
  path: '/articles',
  handler: (request) => {
    return Article.findAll()
  },
  config: { auth: false }
} 
```

将这三条路线公开，以便任何人都可以阅读文章和发表评论:

```
GET    /articles                articles#index
GET    /articles/:id            articles#show
POST   /articles/:id/comments   comments#create 
```

## 生成 JWT

有一个名为`jsonwebtoken`的包用于签名和验证 JWT:

```
npm install --save jsonwebtoken 
```

我们的最终路线将采用一个电子邮件/密码，并生成一个 JWT。让我们在`auth.js`中定义我们的登录函数，将所有的认证逻辑放在一个地方:

```
// auth.js
const jwt = require('jsonwebtoken')
const Boom = require('boom')

exports.login = (email, password) => {
  if (!(email === 'mb4@gmail.com' &amp;&amp; password === 'bears')) return Boom.notAcceptable()

  const credentials = { email }
  const token = jwt.sign(credentials, JWT_KEY, { algorithm: 'HS256', expiresIn: '1h' })

  return { token }
} 
```

```
// routes.js
const { login } = require('./auth')

{
  method: 'POST',
  path: '/authentications',
  handler: async (request) => {
    const { email, password } = request.payload.login

    return login(email, password)
  },
  config: { auth: false }
} 
```

```
http POST localhost:3000/authentications login:='{"email": "mb4@gmail.com", "password": "bears"}' 
```

尝试在安全路由请求中使用返回的`token`!

## 用`joi`确认

您可以通过将 config 添加到 route 对象来验证请求参数。下面的代码确保提交的`article`有一个长度在 3 到 10 个字符之间的`body`和`title`。如果验证失败，哈比神将用一个`400`错误进行响应:

```
const Joi = require('joi')

{
    method: 'POST',
    path: '/articles',
    handler: (request) => {
      const article = Article.build(request.payload.article)

      return article.save()
    },
    config: {
      validate: {
        payload: {
          article: {
            title: Joi.string().min(3).max(10),
            body: Joi.string().required()
          }
        }
      }
    }
  }
} 
```

除了`payload`之外，您还可以向`path`、`query`和`headers`添加验证。在[文档](https://hapijs.com/tutorials/validation)中了解更多关于验证的信息。

## 谁在使用这个 API？

我们可以从`/`开始提供单页应用。在本教程的开始，我们已经看到了一个如何用流提供 HTML 文件的例子。不过，在哈比神有更好的方式来处理视图和布局。关于如何渲染动态视图的更多信息，请参见[提供静态内容](https://hapijs.com/tutorials/serving-files)和[视图和布局](https://hapijs.com/tutorials/views):

```
{
  method: 'GET',
  path: '/',
  handler: () => {
    return require('fs').createReadStream('index.html')
  },
  config: { auth: false }
} 
```

如果前端和 API 在同一个域上，你就可以毫无问题地发出请求:`client`->-。

如果你从一个不同的域服务前端，并且想直接从客户端向 API 发出请求，你需要启用 CORS。这在哈比神非常简单:

```
const server = Hapi.server({
  host: 'localhost',
  port: 3000,
  routes: {
    cors: {
      credentials: true
      // See options at https://hapijs.com/api/17.0.0#-routeoptionscors
    }
  }
}) 
```

你也可以在两者之间创建一个新的应用程序。如果你走这条路，你就不需要为 CORS 费心了，因为客户端只会向前端应用发出请求，然后它可以向服务器上的 API 发出请求，而没有任何跨域限制:`client`->-`hapi-front-end`->-`hapi-api`。

这个前端是另一个哈比神应用程序，还是 Next，还是 Nuxt …我会让你来决定！

## 分享这篇文章