# 用哈比神和 TypeScript 为 Jamstack 构建 Rest API

> 原文：<https://www.sitepoint.com/jamstack-rest-api-hapi/>

**[jam stack](https://www.sitepoint.com/learn-jamstack/)有一种很好的方式将前端和后端分开，这样整个解决方案就不必在同一时间以单一的整体形式交付。当 Jamstack 与 REST API 配对时，客户端和 API 可以独立发展*。这意味着前端和后端不是紧密耦合的，改变一个并不一定意味着改变另一个。***

 *在本文中，我将从 Jamstack 的角度来看一看 REST API。我将展示如何在不破坏现有客户端的情况下发展 API，并遵守 REST 标准。我将选择哈比神作为构建 api 的工具，Joi 用于端点验证。数据库持久层将通过 Mongoose 进入 MongoDB 来访问数据。测试驱动的开发将帮助我迭代变更，并提供一种快速获得反馈的方法，减少认知负荷。最后，我们的目标是让您看到 REST 和 Jamstack 如何在软件模块之间提供高内聚和低耦合的解决方案。这种类型的体系结构最适合于分布式系统，这些系统中有许多微服务，每个微服务都位于各自独立的域中。我假设您对 NPM、ES6+有一定的了解，并且对 API 端点有一定的了解。

该 API 将处理作者数据，包括姓名、电子邮件和可选的 1:N ( [一对多，通过文档嵌入](https://www.mongodb.com/blog/post/6-rules-of-thumb-for-mongodb-schema-design-part-1))关系。我将编写一个 GET、PUT(带有 upsert)和 DELETE 端点。为了测试 API，任何支持`fetch()`的客户端都可以，所以我将选择 [Hoppscotch](https://hoppscotch.io/) 和 CURL。

我将保持这篇文章的阅读流程，就像一个教程，你可以从头到尾跟着读。对于那些想直接跳到代码的人来说，GitHub 上有[供你观看。本教程假设节点](https://github.com/sitepoint-editors/hapi-authors-rest-api)的[工作版本(最好是最新的 LTS)和](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/) [MongoDB 已经安装](https://www.sitepoint.com/an-introduction-to-mongodb/)。

## 初始设置

要从头开始项目，创建一个文件夹并`cd`到其中:

```
mkdir hapi-authors-rest-api
cd hapi-authors-rest-api 
```

一旦进入项目文件夹，启动`npm init`并按照提示操作。这将在文件夹的根目录下创建一个`package.json`。

每个节点项目都有依赖项。我需要哈比神，乔伊和猫鼬来开始:

```
npm i @hapi/hapi joi mongoose --save-exact 
```

*   [@hapi/hapi](https://www.npmjs.com/package/@hapi/hapi) : HTTP REST 服务器框架
*   Joi :强大的对象模式验证器
*   [mongose](https://www.npmjs.com/package/mongoose):MongoDB 对象文档建模

检查`package.json`以确保所有依赖项和项目设置都到位。然后，向该项目添加一个入口点:

```
"scripts": {
  "start": "node index.js"
}, 
```

## 带有版本控制的 MVC 文件夹结构

对于这个 REST API，我将使用一个典型的 MVC 文件夹结构，包括控制器、路由和一个数据库模型。控制器将有一个类似于`AuthorV1Controller`的版本，允许 API 在模型有重大变化时进化。哈比神将有一个`server.js`和`index.js`通过测试驱动开发使这个项目可测试。`test`文件夹将包含单元测试。

以下是整体文件夹结构:

```
┳
┣━┓ config
┃ ┣━━ dev.json
┃ ┗━━ index.js
┣━┓ controllers
┃ ┗━━ AuthorV1Controller.js
┣━┓ model
┃ ┣━━ Author.js
┃ ┗━━ index.js
┣━┓ routes
┃ ┣━━ authors.js
┃ ┗━━ index.js
┣━┓ test
┃ ┗━━ Author.js
┣━━ index.js
┣━━ package.json
┗━━ server.js 
```

现在，继续创建文件夹和每个文件夹中各自的文件。

```
mkdir config controllers model routes test
touch config/dev.json config/index.js controllers/AuthorV1Controller.js model/Author.js model/index.js routes/authors.js routes/index.js test/Authors.js index.js server.js 
```

这是每个文件夹的用途:

*   `config`:插入 Mongoose 连接和哈比神服务器的配置信息。
*   这些是处理请求/响应对象的哈比神处理程序。版本控制允许每个版本号有多个端点，即`/v1/authors`、`/v2/authors`等。
*   `model`:连接到 MongoDB 数据库并定义 Mongoose 模式。
*   `routes`:通过 Joi 验证为 REST 纯化论者定义终点。
*   通过哈比神的实验室工具进行单元测试。(稍后将详细介绍这一点。)

在一个真实的项目中，您可能会发现将常见的业务逻辑抽象到一个单独的文件夹中很有用，比如说`utils`。我建议用纯功能代码创建一个`AuthorUtil.js`模块，使其可以跨端点重用，并且易于单元测试。因为这个解决方案没有任何复杂的业务逻辑，所以我将选择跳过这个文件夹。

添加更多文件夹的一个问题是在进行更改时有更多的抽象层和更多的认知负荷。对于异常庞大的代码库，很容易迷失在层层误导的混乱中。有时候，保持文件夹结构尽可能简单和扁平会更好。

## 以打字打的文件

为了改善开发人员的体验，我现在将添加 TypeScript 类型声明。因为 Mongoose 和 Joi 在运行时定义模型，所以在编译时添加类型检查器没有什么价值。在 TypeScript 中，可以将类型定义添加到普通的 JavaScript 项目中，并且仍然可以获得代码编辑器中类型检查器的好处。像 WebStorm 或 VS Code 这样的工具将获取类型定义，并允许程序员“点”入代码。这种技术通常被称为 *IntelliSense* ，当 IDE 拥有可用的类型时，它就会被启用。你得到的是一种定义编程接口的好方法，这样开发人员就可以不用看文档就能进入对象。当开发人员点错对象时，编辑器有时也会显示警告。

这是智能感知在 VS 代码中的样子:

![VSCode IntelliSense](img/6ac215464ec31ecfc02f3a089d0c54f4.png)

在 WebStorm 中，这被称为代码完成，但本质上是一回事。请随意选择您喜欢的 IDE 来编写代码。我用的是 Vim 和 WebStorm，但你可能会有不同的选择。

要在此项目中启用 TypeScript 类型声明，请启动 NPM 并保存这些开发人员依赖项:

```
npm i @types/hapi @types/mongoose --save-dev 
```

我建议将开发者依赖和应用依赖分开。这样，组织中的其他开发人员就很清楚这些包的意义了。当构建服务器下载 repo 时，它还可以选择跳过项目在运行时不需要的包。

所有开发人员的细节都准备好了，现在是开始编写代码的时候了。打开哈比神`server.js`文件并放置主服务器:

```
const config = require('./config')
const routes = require('./routes')
const db = require('./model')
const Hapi = require('@hapi/hapi')

const server = Hapi.server({
  port: config.APP_PORT,
  host: config.APP_HOST,
  routes: {
    cors: true
  }
})

server.route(routes)

exports.init = async () => {
  await server.initialize()
  await db.connect()
  return server
}

exports.start = async () => {
  await server.start()
  await db.connect()
  console.log(`Server running at: ${server.info.uri}`)
  return server
}

process.on('unhandledRejection', (err) => {
  console.error(err)
  process.exit(1)
}) 
```

我已经通过将`cors`设置为 true 启用了 CORS，因此这个 REST API 可以与 Hoppscotch 一起工作。

为了简单起见，我将在这个项目中放弃分号。跳过这个项目中的 [TypeScript 构建并键入额外的字符有点自由。这遵循了哈比神的口头禅，因为无论如何这都是关于开发人员的快乐。](https://www.sitepoint.com/introduction-to-typescript/)

在`config/index.js`下，确保导出`dev.json`信息:

```
module.exports = require('./dev') 
```

为了充实服务器的配置，将它放入`dev.json`:

```
{
  "APP_PORT": 3000,
  "APP_HOST": "127.0.0.1"
} 
```

## REST 验证

为了让 REST 端点遵循 HTTP 标准，我将添加 Joi 验证。这些验证有助于将 API 从客户机中分离出来，因为它们加强了资源的完整性。对于 Jamstack，这意味着客户端不再关心每个资源背后的实现细节。独立对待每个端点是自由的，因为验证将确保对资源的有效请求。遵循严格的 HTTP 标准使得客户端基于位于 HTTP 边界后面的目标资源进行发展，这加强了分离。实际上，目标是使用版本控制和验证来保持 Jamstack 中清晰的边界。

使用 REST，主要目标是用 GET、PUT 和 DELETE 方法维护的幂等性。这些是安全的请求方法，因为对同一资源的后续请求没有任何副作用。即使客户端无法建立连接，同样的预期效果也会重复出现。

我将选择跳过 POST 和 PATCH，因为这些方法不安全。这是为了简洁和幂等性，而不是因为这些方法以任何方式紧密耦合客户端。同样严格的 HTTP 标准也适用于这些方法，只是它们不保证幂等性。

在`routes/authors.js`中，添加以下 Joi 验证:

```
const Joi = require('joi')

const authorV1Params = Joi.object({
  id: Joi.string().required()
})

const authorV1Schema = Joi.object({
  name: Joi.string().required(),
  email: Joi.string().email().required(),
  topics: Joi.array().items(Joi.string()), // optional
  createdAt: Joi.date().required()
}) 
```

请注意，对版本化模型的任何更改都可能需要一个新版本，比如`v2`。这保证了现有客户端的向后兼容性，并允许 API 独立发展。当缺少字段时，必填字段将通过 400(错误请求)响应使请求失败。

参数和模式验证就绪后，将实际路由添加到该资源:

```
// routes/authors.js
const v1Endpoint = require('../controllers/AuthorV1Controller')

module.exports = [{
  method: 'GET',
  path: '/v1/authors/{id}',
  handler: v1Endpoint.details,
  options: {
    validate: {
      params: authorV1Params
    },
    response: {
      schema: authorV1Schema
    }
  }
}, {
  method: 'PUT',
  path: '/v1/authors/{id}',
  handler: v1Endpoint.upsert,
  options: {
    validate: {
      params: authorV1Params,
      payload: authorV1Schema
    },
    response: {
      schema: authorV1Schema
    }
  }
}, {
  method: 'DELETE',
  path: '/v1/authors/{id}',
  handler: v1Endpoint.delete,
  options: {
    validate: {
      params: authorV1Params
    }
  }
}] 
```

为了使这些路线对`server.js`可用，将其添加到`routes/index.js`:

```
module.exports = [
  ...require('./authors')
] 
```

Joi 验证放在 routes 数组的`options`字段中。每个请求路径接受一个与 MongoDB 中的`ObjectId`匹配的字符串 ID 参数。这个`id`是版本化路径的一部分，因为它是客户端需要使用的目标资源。对于 PUT，有一个有效载荷验证来匹配 GET 的响应。这是为了遵守 REST 标准，其中 [PUT 响应必须与后续的 GET](https://tools.ietf.org/html/rfc7231#section-4.3.4) 相匹配。

标准中是这样写的:

> 一个给定表示的成功 PUT 将表明，对同一目标资源的后续 GET 将导致在 200 (OK)响应中发送一个等效的表示。

这使得 PUT 不适合支持部分更新，因为后续的 GET 与 PUT 不匹配。对于 Jamstack 来说，遵守 HTTP 标准以确保客户端的可预测性和解耦非常重要。

`AuthorV1Controller`通过`v1Endpoint`中的方法处理器处理请求。每个版本都有一个控制器是个好主意，因为这是将响应发送回客户端的部分。这使得在不破坏现有客户端的情况下，通过新的版本化控制器来发展 API 变得更加容易。

## 作者的数据库集合

节点的 Mongoose 对象建模首先需要安装一个 MongoDB 数据库。我建议在您的本地 dev box 上设置一个来[使用 MongoDB](https://www.sitepoint.com/an-introduction-to-mongodb/) 。最小安装只需要两个可执行文件，您可以在大约 50 MB 内启动并运行服务器。这是 MongoDB 的真正强大之处，因为完整的数据库可以在像 Raspberry PI 这样非常便宜的硬件上运行，并且可以根据需要水平扩展到任意数量的机器。该数据库还支持一种混合模式，在这种模式下，服务器既可以在云中运行，也可以在本地运行。所以，不要找借口！

在`model`文件夹中，打开`index.js`设置数据库连接:

```
const config = require('../config')
const mongoose = require('mongoose')

module.exports = {
  connect: async function() {
    await mongoose.connect(
      config.DB_HOST + '/' + config.DB_NAME,
      config.DB_OPTS)
  },
  connection: mongoose.connection,
  Author: require('./Author')
} 
```

请注意，`Author`集合在同一个文件夹的`Author.js`中定义:

```
const mongoose = require('mongoose')

const authorSchema = new mongoose.Schema({
  name: String,
  email: String,
  topics: [String],
  createdAt: Date
})

if (!authorSchema.options.toObject) authorSchema.options.toObject = {}
authorSchema.options.toObject.transform = function(doc, ret) {
  delete ret._id
  delete ret.__v
  if (ret.topics && ret.topics.length === 0) delete ret.topics
  return ret
}

module.exports = mongoose.model('Author', authorSchema) 
```

请记住，Mongoose 模式并不反映与 Joi 验证相同的需求。这增加了数据的灵活性，以支持多个版本，以防有人需要跨多个端点的向后兼容性。

[`toObject`转换](https://mongoosejs.com/docs/api.html#document_Document-toObject)净化 JSON 输出，所以 Joi 验证器不会抛出异常。如果 Mongoose 文档中有任何额外的字段，比如`_id`，服务器会发送一个 500(内部服务器错误)响应。可选字段`topics`在为空数组时会被销毁，因为 GET 必须匹配 PUT 响应。

最后，在`config/dev.json`中设置数据库配置:

```
{
  "APP_PORT": 3000,
  "APP_HOST": "127.0.0.1",
  "DB_HOST": "mongodb://127.0.0.1:27017",
  "DB_NAME": "hapiAuthor",
  "DB_OPTS": {
    "useNewUrlParser": true,
    "useUnifiedTopology": true,
    "poolSize": 1
  }
} 
```

## 行为驱动开发

在充实控制器中每个方法的端点之前，我喜欢从编写单元测试开始。这有助于我将手头的问题概念化，以获得最佳代码。我将做红/绿，但跳过重构，把它留给你做练习，以免重复这一点。

我将选择[哈比神的实验室工具](https://www.npmjs.com/package/@hapi/lab)和他们的 [BDD 断言库](https://www.npmjs.com/package/@hapi/code)来测试我编写的代码:

```
npm i @hapi/lab @hapi/code --save-dev 
```

在`test/Author.js`中，将这个基本的脚手架添加到测试代码中。我将选择行为驱动开发(BDD)风格来使其更加流畅:

```
const Lab = require('@hapi/lab')
const { expect } = require('@hapi/code')
const { after, before, describe, it } = exports.lab = Lab.script()
const { init } = require('../server')
const { connection } = require('../model')

const id = '5ff8ea833609e90fc87fee52'

const payload = {
  name: 'C R',
  email: 'xyz@abc.net',
  createdAt: '2021-01-08T06:00:00.000Z'
}

describe('/v1/authors', () => {
  let server

  before(async () => {
    server = await init()
  })

  after(async () => {
    await server.stop()
    await connection.close()
  })
}) 
```

随着您构建更多的模型和端点，我建议在每个测试文件中重复同样的支架代码。单元测试并不枯燥(“不要重复自己”)，启动/停止服务器和数据库连接完全没问题。MongoDB 连接和哈比神服务器可以处理这个问题，同时保持测试快速。

测试几乎已经准备好运行了，除了`AuthorV1Controller1`中的一个小问题，因为它是空的。打开`controllers/AuthorV1Controller.js`并加上这个:

```
exports.details = () => {}
exports.upsert = () => {}
exports.delete = () => {} 
```

测试通过终端中的`npm t`运行。务必在`package.json`中进行设置:

```
"scripts": {
  "test": "lab"
}, 
```

继续启动单元测试。应该还没有什么失败。要使单元测试失败，请在`describe()`中添加以下内容:

```
it('PUT responds with 201', async () => {
  const { statusCode } = await server.inject({
    method: 'PUT',
    url: `/v1/authors/${id}`,
    payload: {...payload}
  })
  expect(statusCode).to.equal(201)
})

it('PUT responds with 200', async () => {
  const { statusCode } = await server.inject({
    method: 'PUT',
    url: `/v1/authors/${id}`,
    payload: {
      ...payload,
      topics: ['JavaScript', 'MongoDB']}
  })
  expect(statusCode).to.equal(200)
})

it('GET responds with 200', async () => {
  const { statusCode } = await server.inject({
    method: 'GET',
    url: `/v1/authors/${id}`
  })
  expect(statusCode).to.equal(200)
})

it('DELETE responds with 204', async () => {
  const { statusCode } = await server.inject({
    method: 'DELETE',
    url: `/v1/authors/${id}`
  })
  expect(statusCode).to.equal(204)
}) 
```

要开始通过单元测试，将它放入`controllers/AuthorV1Controller.js`:

```
const db = require('../model')

exports.details = async (request, h) => {
  const author = await db.Author.findById(request.params.id).exec()
  request.log(['implementation'], `GET 200 /v1/authors ${author}`)
  return h.response(author.toObject())
}

exports.upsert = async (request, h) => {
  const author = await db.Author.findById(request.params.id).exec()

  if (!author) {
    const newAuthor = new db.Author(request.payload)
    newAuthor._id = request.params.id
    await newAuthor.save()
    request.log(['implementation'], `PUT 201 /v1/authors ${newAuthor}`)
    return h
      .response(newAuthor.toObject())
      .created(`/v1/authors/${request.params.id}`)
  }

  author.name = request.payload.name
  author.email = request.payload.email
  author.topics = request.payload.topics
  request.log(['implementation'], `PUT 200 /v1/authors ${author}`)
  await author.save()
  return h.response(author.toObject())
}

exports.delete = async (request, h) => {
  await db.Author.findByIdAndDelete(request.params.id)
  request.log(
    ['implementation'],
    `DELETE 204 /v1/authors ${request.params.id}`)
  return h.response().code(204)
} 
```

这里需要注意一些事情。`exec()`方法实现了查询并返回一个 Mongoose 文档。因为这个文档有哈比神服务器不关心的额外字段，所以在调用`response()`之前应用一个`toObject`。API 的默认状态代码是 200，但这可以通过`code()`或`created()`更改。

对于红绿重构测试驱动开发，我只写了最少的代码来通过测试。我将把编写更多的单元测试和更多的用例留给你。例如，当目标资源没有作者时，GET 和 DELETE 应该返回 404(未找到)。

哈比神支持其他细节，比如在`request`对象内部的记录器。默认情况下，当服务器运行时,`implementation`标记将调试日志发送到控制台，这也适用于单元测试。这是查看请求在请求管道中传递的过程中发生了什么的一种非常简洁的方式。

## 测试

最后，在我们启动主服务器之前，将它放入`index.js`:

```
const { start } = require('./server')

start() 
```

一个`npm start`应该可以让你在哈比神运行和工作 REST API。我现在将使用 Hoppscotch 向所有端点发出请求。你所要做的就是点击下面的链接来测试你的 API。请务必从上到下点击链接:

*   [放 201/v1/作者](https://hoppscotch.io/?method=PUT&url=http%3A%2F%2Flocalhost%3A3000&path=%2Fv1%2Fauthors%2F5ff8ea833609e90fc87fee52&contentType=application%2Fjson&bodyParams=%5B%7B%22key%22%3A%22name%22,%22value%22%3A%22C%20R%22,%22active%22%3Atrue%7D,%7B%22key%22%3A%22email%22,%22value%22%3A%22xyz%40abc.net%22,%22active%22%3Atrue%7D,%7B%22key%22%3A%22createdAt%22,%22value%22%3A%222021-01-08T06%3A00%3A00.000Z%22,%22active%22%3Atrue%7D%5D)
*   [放 200/v1/作者](https://hoppscotch.io/?method=PUT&url=http://localhost:3000&path=/v1/authors/5ff8ea833609e90fc87fee52&contentType=application/json&rawParams=%7B%22name%22:%22C%20R%22,%22email%22:%22xyz@abc.net%22,%22createdAt%22:%222021-01-08T06:00:00.000Z%22,%22topics%22:%5B%22JavaScript%22,%22Mongoose%22%5D%7D)
*   [获得 200/v1/作者](https://hoppscotch.io/?method=GET&url=http://localhost:3000&path=/v1/authors/5ff8ea833609e90fc87fee52)
*   [删除 204/v1/作者](https://hoppscotch.io/?method=DELETE&url=http://localhost:3000&path=/v1/authors/5ff8ea833609e90fc87fee52&contentType=application/json)

或者，在 cURL 中也可以这样做:

```
curl -i -X PUT -H "Content-Type:application/json" -d "{\"name\":\"C R\",\"email\":\"xyz@abc.net\",\"createdAt\":\"2021-01-08T06:00:00.000Z\"}" http://localhost:3000/v1/authors/5ff8ea833609e90fc87fee52
201 Created {"name":"C R","email":"xyz@abc.net","createdAt":"2021-01-08T06:00:00.000Z"}

curl -i -X PUT -H "Content-Type:application/json" -d "{\"name\":\"C R\",\"email\":\"xyz@abc.net\",\"createdAt\":\"2021-01-08T06:00:00.000Z\",\"topics\":[\"JavaScript\",\"MongoDB\"]}" http://localhost:3000/v1/authors/5ff8ea833609e90fc87fee52
200 OK {"topics":["JavaScript","MongoDB"],"name":"C R","email":"xyz@abc.net","createdAt":"2021-01-08T06:00:00.000Z"}

curl -i -H "Content-Type:application/json" http://localhost:3000/v1/authors/5ff8ea833609e90fc87fee52
200 OK {"topics":["JavaScript","MongoDB"],"name":"C R","email":"xyz@abc.net","createdAt":"2021-01-08T06:00:00.000Z"}

curl -i -X DELETE -H "Content-Type:application/json" http://localhost:3000/v1/authors/5ff8ea833609e90fc87fee52
204 No Content 
```

在 Jamstack 中，JavaScript 客户端可以通过`fetch()`进行这些调用。REST API 的好处在于它不一定是浏览器，因为任何支持 HTTP 的客户端都可以。这对于分布式系统来说是完美的，在分布式系统中，多个客户端可以通过 HTTP 调用 API。API 可以保持独立，有自己的部署时间表，并允许自由发展。

## 结论

JamStack 有一个很好的方法，通过版本化的端点和模型验证来分离软件模块。哈比神服务器支持这一点和其他细节，比如类型声明，让您的工作更加愉快。

## 分享这篇文章*