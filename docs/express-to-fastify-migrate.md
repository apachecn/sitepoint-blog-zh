# 如何将你的应用从 Express 迁移到 Fastify

> 原文：<https://www.sitepoint.com/express-to-fastify-migrate/>

**[Express](https://expressjs.com/) 一直以来都是用 Node.js 开发 web 应用最流行的框架，不幸的是，这个框架近年来并没有太多的积极发展。这意味着它不支持现代 JavaScript 特性。与此同时，出现了许多新的框架，它们采用不同的方法来开发 Node.js 应用程序。其中一个框架是 [Fastify](https://www.fastify.io/) 。**

在本文中，我们将了解是什么使 Fastify 成为使用 Node.js 开发 web 应用程序的有吸引力的替代方法。我们将了解如何避免从头重写现有的 Express 应用程序，而是分阶段迁移到使用 Fastify。到我们完成时，您将能够自信地迁移您现有的 Express 应用程序，并开始利用 Fastify 框架的优势。

遵循本文有一些要求:

*   您需要熟悉创建基本的 Express 应用程序、定义路线和配置中间件。
*   您将需要在终端中舒适地运行命令。
*   你需要安装 [Node.js](https://nodejs.org/en/download/) > = v14.13.0。这为我们提供了对 [ECMAScript (ES)模块](https://nodejs.org/docs/latest-v14.x/api/esm.html)的良好支持，并允许我们使用[顶级 await](https://nodejs.org/docs/latest-v14.x/api/esm.html#esm_top_level_await) 。本文中的代码示例使用 es 模块语法(`import` / `export`)。

本文中的所有示例代码都可以在 GitHub 上找到，供您浏览、下载和试验。

我的网站上还有这篇文章的视频版本。

## 从 Express 迁移到 Fastify 有什么好处？

如果您习惯于使用 Express 构建 Node.js 应用程序，您可能会想知道将现有的 Express 应用程序迁移到 Fastify 有什么好处。以下是一些考虑搬家的好理由:

*   **验证和开箱登录**。构建 web 应用程序时通常需要这些特性。当使用 Fastify 时，没有必要为这些任务选择和集成库，因为它为我们提供了它们。我们将在本文后面了解更多关于这些特性的内容。

*   **对异步代码的本地支持**。Fastify 原生处理承诺，支持`async` / `await`。这意味着路由将为我们捕捉未被捕获的拒绝承诺。这允许我们安全地编写异步代码。它还允许我们做一些简单的事情，比如自动发送路由处理函数的返回值作为响应体:

    ```
    app.get("/user/:id", async (request) => await getUser(request.params.id)); 
    ```

*   **JSON 的自动解析和序列化**。我们不需要配置 Fastify 来解析 JSON 请求体，或者将对象序列化为 JSON 进行响应。它会自动为我们处理所有这一切:

    ```
    app.get("/user/:id", async (request, reply) => {
      const name = request.body.name;

      reply.send({ user: { name } });
    }); 
    ```

*   **开发者友好**。凭借显式和富于表现力的 API，以及对 TypeScript 的出色的[支持，Fastify 的设计考虑了开发人员的体验。](https://www.fastify.io/docs/latest/TypeScript/)

*   **快了**。我们从来不希望框架成为我们应用程序中性能瓶颈的来源。好消息是 Fastify 已经被构建为高性能。Fastify [基准测试](https://www.fastify.io/benchmarks/)展示了它与其他 Node.js web 框架的比较。

*   **正在开发中**。Fastify 框架正在由[积极开发](https://github.com/fastify/fastify/graphs/code-frequency)。有[定期发布](https://www.npmjs.com/package/fastify?activeTab=versions)的改进和错误/安全修复。

## 如何放心地迁移 API

我们希望确信我们的应用程序在迁移到 Fastify 后仍能按预期工作。API 集成测试可以帮助我们捕捉 bug 或识别非预期的变化。

集成测试以不同于单元测试的方式测试应用程序的组件。单元测试独立执行单个组件的功能。集成测试允许我们验证多个组件协同工作的行为。

如果我们为 Express 应用程序编写 API 集成测试，我们希望在将应用程序迁移到 Fastify 后，能够运行相同的测试。在为 API 编写集成测试时，有一些关键的事情需要考虑:

*   他们不应该被束缚在一个特定的框架上。我们希望能够在迁移前后运行相同的测试，而不需要改变测试或我们为它们使用的任何库。

*   保持简单。至少，集成测试应该向 API 公开的端点发出请求，并验证是否返回了响应，但通常不会更多。我们可能希望检查特定的 HTTP 状态代码或响应头，但是我们应该尽量使测试简单。

*   **挑选你觉得合适的工具**。有很多不同的工具可以帮助我们创建和运行 API 测试，但是使用我们熟悉的工具很重要。为了编写有效的集成测试，我们需要能够发出 HTTP 请求，并针对来自 API 的响应做出断言。一般来说，我们不需要很多库或工具来完成这项工作。

在本文中，我们不会深入探讨如何实现 API 集成测试的细节，但是在进行框架迁移之前，您应该考虑编写这些内容。

## 使用 fastify-express 从 Express 过渡到 Fastify

将一个现有的 Express 应用程序迁移到一个完全不同的框架中的想法看起来相当令人畏惧。幸运的是，Fastify 团队已经创建了一个插件——[Fastify-express](https://github.com/fastify/fastify-express)——可以帮助简化迁移过程。

`fastify-express`插件为 Fastify 增加了完全的 Express 兼容性。它提供了一个`use()`方法，我们可以用它来添加 Express 中间件并路由到我们的 Fastify 服务器。这让我们可以选择逐渐将现有 Express 应用程序的一部分迁移到 Fastify。

这里有一个[快速路由器](https://expressjs.com/en/api.html#router)的例子:

```
// src/routes.js
const router = express.Router();

router.get("/:user_id", function getUser(request, response, next) {
  response.json({});
});

export default router; 
```

然后，我们可以使用`fastify-express`将现有的 Express 路由器添加到 Fastify 服务器实例中:

```
// src/server.js

import Fastify from "fastify";
import ExpressPlugin from "fastify-express";

import routes from "./routes.js";

const fastify = Fastify();

await fastify.register(ExpressPlugin);

fastify.use("/user", routes);

await fastify.listen(3000); 
```

稍后，当我们开始将应用程序迁移到 Fastify 时，我们将探究这一切是如何工作的细节。

重要的是要意识到使用`fastify-express`插件不是一个长期的解决方案。如果我们想获得 Fastify 的全部好处，我们需要在某个时候迁移我们特定于 Express 的应用程序代码。然而，`fastify-express`插件为我们提供了分阶段迁移到 Fastify 的机会。

## 我们的示例 Express 应用程序

我们将构建一个示例 Express 应用程序，然后迁移它以使用 Fastify 框架。现在让我们来看看它的代码。

### 必需的依赖关系

首先，让我们创建一个新项目:

```
mkdir express-to-fastify-migration
cd express-to-fastify-migration
npm init -y 
```

然后，我们将在终端中运行这个命令来安装我们的 Express 应用程序将需要的依赖项:

```
npm install express cors 
```

最后，打开`package.json`并在`scripts`部分上方添加以下行:

```
"type": "module", 
```

这将允许我们在应用程序中加载 ES 模块。

### 路由器模块

我们将创建一个[快速路由器](https://expressjs.com/en/api.html#router)实例来帮助我们封装我们的路由和中间件。Express 中的路由器可以帮助我们将应用程序组织成独立的模块。例如，我们可能有一个路由器用于`/user`路由，另一个路由器用于`/address`路由。稍后我们将看到这如何帮助我们分阶段将 Express 应用程序迁移到 Fastify。

让我们创建一个路由器实例，并添加一些中间件:

```
// src/routes.js

import express from "express";
import cors from "cors";

const router = express.Router();

router.use(express.json());

router.use(cors({ origin: true })); 
```

在上面的代码中，我们已经配置了两个快速中间件示例:

*   [express.json()](https://expressjs.com/en/api.html#express.json) 。这个中间件功能内置于表示。它处理 JSON 请求体的解析。
*   [cors](https://www.npmjs.com/package/cors) 。这个中间件帮助我们将 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 头添加到我们的 API 响应中。它将允许我们的 API 从一个网页被调用。

这些中间件工具将针对我们在此路由器上定义的路由的任何请求而运行。

现在我们已经配置了中间件，我们可以向路由器添加第一条路由:

```
// src/routes.js

router.post("/", function createUser(request, response, next) {
  const newUser = request.body;

  if (!newUser) {
    return next(new Error("Error creating user"));
  }

  response.status(201).json(newUser);
}); 
```

在实际的应用程序中，上面的路由处理器函数将验证它接收到的数据，然后调用数据库来创建新的用户记录。在本例中，我们将收到的数据作为响应体发送。

现在，我们将添加检索用户的路径:

```
// src/routes.js

router.get("/:user_id", function getUser(request, response, next) {
  const user = {
    id: request.params.user_id,
    first_name: "Bobinsky",
    last_name: "Oso",
  };

  response.json(user);
}); 
```

与`POST`路由一样，上面的路由处理程序通常会调用数据库来检索用户数据，但是对于这个例子，我们硬编码了一个对象，以便在响应体中发送。

最后，我们将导出`router`对象，这样我们就可以在另一个模块中导入它:

```
// src/routes.js

export default router; 
```

### 应用程序模块

现在，我们将创建一个应用模块:

```
// src/app.js

import express from "express";

import routes from "./routes.js";

export default function buildApp() {
  const app = express();

  app.use("/user", routes);

  return app;
} 
```

在本模块中，我们将定义一个创建新 Express 服务器实例的函数。然后，我们将路由器对象添加到服务器实例中。

### 服务器模块

最后，我们将创建一个服务器模块。这个模块使用我们在 app 模块中定义的`buildApp()`函数来创建一个新的 Express 服务器实例。然后，它启动我们的 Express 服务器，将其配置为监听端口`3000`:

```
// src/server.js

import buildApp from "./app.js";

const express = buildApp();

express.listen(3000, () => {
  console.log("Example app listening at http://localhost:3000");
}); 
```

### 运行我们的应用

我们现在有了一个可以在终端上运行的完整的 Express 应用程序:

```
node src/server.js 
```

在一个单独的终端中，我们可以用 cURL 向 API 发出请求，以确认它正在工作:

```
curl --verbose --request GET \
  --url http://localhost:3000/user/3d395cb4-531c-4989-b8ed-9cc75198187e \
  --header 'Origin: http://example-origin.com' 
```

我们应该会收到类似这样的响应:

```
< HTTP/1.1 200 OK
< X-Powered-By: Express
< Access-Control-Allow-Origin: http://example-origin.com
< Vary: Origin
< Content-Type: application/json; charset=utf-8
< 

{"id":"3d395cb4-531c-4989-b8ed-9cc75198187e","first_name":"Bobinsky","last_name":"Oso"} 
```

## 将我们的应用程序从 express 迁移到证伪

现在我们已经有了一个功能齐全的 Express 应用程序，我们将把它迁移到使用 Fastify 框架。

### 必需的依赖关系

我们需要安装三个依赖项:

*   Fastify 框架
*   fastify-express 插件
*   [fastify-cors](https://www.npmjs.com/package/fastify-cors) 插件——这是我们的应用程序已经在使用的 Express `cors`中间件的一个端口

让我们在终端中运行这个命令来安装它们:

```
npm install fastify fastify-express fastify-cors 
```

你可以[在 GitHub](https://github.com/simonplend/express-to-fastify-migration/commit/480452df13076b8de76313ac126841f467665c94) 上查看这些代码变化的差异。

### 重构我们的应用模块

既然我们已经安装了依赖项，我们需要重构我们的应用程序模块。我们将把它改为:

*   导入`fastify`和`fastify-express`而不是`express`
*   创建 Fastify 服务器实例，而不是 Express 服务器实例
*   使用`fastify-express`插件将我们的快速路由器对象添加到服务器

这是我们做了这些更改后的样子:

```
// src/app.js

import Fastify from "fastify";
import ExpressPlugin from "fastify-express";

import routes from "./routes.js";

export default async function buildApp() {
  const fastify = Fastify({
    logger: true,
  });

  await fastify.register(ExpressPlugin);

  fastify.use("/user", routes);

  return fastify;
} 
```

你可以[在 GitHub](https://github.com/simonplend/express-to-fastify-migration/commit/a3a1575724789b1948205e0da9f3f5a5067da95f) 上查看这些代码变化的差异。

您会注意到，在上面的代码中，我们在创建 Fastify 服务器实例时传递了 [logger](https://www.fastify.io/docs/latest/Server/#logger) 选项。这启用了 Fastify 的内置日志功能。稍后我们将了解更多这方面的内容。

### 重构我们的服务器模块

我们现在需要更改我们的服务器模块，以便使用 Fastify 服务器实例:

```
// src/server.js

import buildApp from "./app.js";

const fastify = await buildApp();

try {
  await fastify.listen(3000);
} catch (error) {
  fastify.log.error(error);
  process.exit(1);
} 
```

你可以[在 GitHub](https://github.com/simonplend/express-to-fastify-migration/commit/53b054727839f5d13975ba944625447a247fd9aa) 上查看这些代码变化的差异。

由于 Fastify 具有对承诺的本地支持，在上面的代码中，我们能够使用`await`，然后通过 Fastify 的内置日志记录功能捕捉并记录任何错误。

### 后续步骤

我们的应用程序现在使用 Fastify 来路由请求和发送响应。它功能齐全，但我们的航线仍在使用 Express。为了完全从 Express 中迁移出来，我们需要将我们的路由也迁移到使用 Fastify。

### 重构我们的路线模块

我们的 Express 应用程序中的路由封装在一个 Express 路由器中。我们将把这个路由器重构为一个 Fastify 插件。插件是 Fastify 的一个特性，它允许我们封装路由和任何相关的功能。

我们将开始重构我们的 routes 模块(`src/routes.js`)，删除一些特定于 Express 的行:

```
- import express from "express" 
- const router = express.Router(); 
- router.use(express.json()); 
```

然后，我们需要将默认模块导出更改为一个接受 Fastify 服务器实例的`async`函数。这是 Fastify 插件的基础。routes 模块中的剩余代码将被移到这个插件函数中:

```
export default async function routes(fastify) {
  // Configure routes
} 
```

为了让我们的中间件和路由与 Fastify 一起工作，我们需要改变:

*   `router`对`fastify`的引用
*   路由处理器功能为`async`
*   将处理器函数参数从`(request, response, next)`路由到`(request, reply)`
*   `response`对`reply`的引用
*   对`response.json()`到`reply.send()`的调用
*   `next(error)`到`throw error`的实例

完成所有这些更改后，我们的 routes 模块现在是一个包含 Fastify routes 的 Fastify 插件:

```
// src/routes.js

import cors from "cors";

export default async function routes(fastify) {
  fastify.use(cors({ origin: true }));

  fastify.post("/", async function createUser(request, reply) {
    const newUser = request.body;

    if (!newUser) {
      throw new Error("Error creating user");
    }

    reply.status(201).send(newUser);
  });

  fastify.get("/:user_id", async function getUser(request, reply) {
    const user = {
      id: request.params.user_id,
      first_name: "Bobinsky",
      last_name: "Oso",
    };

    reply.send(user);
  });
} 
```

我们现在需要更改我们的应用程序模块(`src/app.js`)来使用我们从 routes 模块导出的插件。这意味着用对`fastify.register()`的呼叫替换`fastify.use()`呼叫:

```
- fastify.use("/user", routes); + fastify.register(routes, { prefix: "/user" }); 
```

你可以[在 GitHub](https://github.com/simonplend/express-to-fastify-migration/commit/da271cefefdfb616df1c3ba052424dd6a92638a7?w=1) 上查看这些代码变化的差异。

我们的示例 Express 应用程序只有一个路由器，因此我们能够一次性迁移应用程序中的所有路由来使用 Fastify。然而，如果我们有一个包含多个路由器的大型 Express 应用程序，我们可以一次一个地将每个路由器迁移到 Fastify。

### 用插件取代中间件

我们的应用程序状态良好，我们几乎已经完全将它从 Express 迁移到 Fastify。还有一件事需要迁移:我们对`cors` Express 中间件包的使用。我们之前安装了`fastify-cors`插件，现在我们需要将它添加到我们的应用程序中来替换`cors`中间件。

在我们的 routes 模块(`src/routes.js`)中，我们需要替换`cors`中间件的`import`:

```
- import cors from "cors"; + import CorsPlugin from "fastify-cors"; 
```

然后我们需要将对`fastify.use()`的调用替换为对`fastify.register()`的调用:

```
- fastify.use(cors({ origin: true })); + fastify.register(CorsPlugin, { origin: true }); 
```

请注意，当我们向 Fastify 注册插件时，我们需要将插件函数和选项对象作为单独的参数传入。

由于我们不再使用`fastify-express`插件提供的`use()`函数，我们可以将它从我们的应用程序中完全移除。为此，让我们从我们的应用程序模块(`src/app.js`)中删除以下代码行:

```
- import ExpressPlugin from "fastify-express"; 
- await fastify.register(ExpressPlugin); 
```

你可以[在 GitHub](https://github.com/simonplend/express-to-fastify-migration/commit/e5b3dfd66ba56aa25dde39aee2ba4396a39d721b) 上查看这些代码变化的差异。

### 删除快速依赖关系

我们的应用程序从 Express 到 Fastify 的迁移已经完成！现在，我们可以通过在终端中运行以下命令来删除与 Express 相关的依赖项:

```
npm uninstall express cors fastify-express 
```

你可以[在 GitHub](https://github.com/simonplend/express-to-fastify-migration/commit/ee2073b38e7c9faaaf861bb1882c81e86841f21b) 上查看这些代码变化的差异。

### 运行我们迁移的应用程序

既然我们已经将应用程序完全迁移到了 Fastify，现在是时候检查一下是否一切都如我们所期望的那样工作了。让我们运行之前应用程序使用 Express 时运行的相同命令。

首先，我们将在终端上运行应用程序:

```
node src/server.js 
```

然后，在一个单独的终端中，我们将使用 cURL 向 API 发出请求，以确认它是否按预期工作:

```
curl --verbose --request GET \
  --url http://localhost:3000/user/3d395cb4-531c-4989-b8ed-9cc75198187e \
  --header 'Origin: http://example-origin.com' 
```

我们应该会收到类似这样的响应:

```
< HTTP/1.1 200 OK
< vary: Origin
< access-control-allow-origin: http://example-origin.com
< content-type: application/json; charset=utf-8
< 

{"id":"3d395cb4-531c-4989-b8ed-9cc75198187e","first_name":"Bobinsky","last_name":"Oso"} 
```

## 远离中间件

我们的示例 Express 应用程序只使用了几个中间件功能，但是我们现实世界中的 Express 应用程序可能会使用更多的功能。正如我们所见，如果需要的话,`fastify-express`插件允许我们继续使用 Express 中间件。这允许我们推迟将我们自己的定制 Express 中间件重写到 Fastify 插件中。但是对于替代第三方快递中间件，我们能做些什么呢？

幸运的是，Fastify 有一个健康的插件生态系统。以下是一些我们可以用 Fastify 插件替换的流行的 Express 中间件包:

*   [cors](https://github.com/expressjs/cors) ➜ [fastify-cors](https://github.com/fastify/fastify-cors)
*   [头盔](https://github.com/helmetjs/helmet) ➜ [头盔](https://github.com/fastify/fastify-helmet)
*   [csurf](https://github.com/expressjs/csurf)➜[fastify-csrf](https://github.com/fastify/fastify-csrf)
*   [快速会话](https://github.com/expressjs/session) ➜ [快速服务器会话](https://github.com/jsumners/fastify-server-session)
*   [express-jwt](https://github.com/auth0/express-jwt)➜[fastify-jwt](https://github.com/fastify/fastify-jwt)
*   [http-errors](https://github.com/jshttp/http-errors)➜[fastify-sensible](https://github.com/fastify/fastify-sensible)
*   [发球-静止](https://github.com/expressjs/serve-static) ➜ [发球-静止](https://github.com/fastify/fastify-static)
*   [multer](https://github.com/expressjs/multer)➜[fastify-multer](https://github.com/fox1t/fastify-multer)

一些 Fastify 插件是 Express 插件的直接移植或者包装。这意味着我们通常不需要改变传递给 Fastify 插件的配置选项。

你可以在 [Fastify 生态系统页面](https://www.fastify.io/ecosystem/)上找到插件的完整列表。

## 充分利用伪造

既然我们已经开始通过迁移一个 Express 应用程序来熟悉 Fastify，那么现在是时候开始研究我们可以从中受益的其他 Fastify 特性了。

### 确认

Fastify 提供了请求验证功能。它使用了 [Ajv](https://ajv.js.org/) (另一个 JSON 模式验证器)，允许我们用 [JSON 模式](https://json-schema.org/)定义验证规则。

下面是一个使用 JSON 模式在`POST`路由上验证请求体的例子:

```
const schema = {
  body: {
    type: "object",
    required: ["first_name"],
    properties: {
      first_name: { type: "string", minLength: 1 },
    },
  },
};

app.post("/user", { schema }, async (request, reply) => {
  reply.send(request.body);
}); 
```

验证错误被自动格式化并作为 JSON 响应发送:

```
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "body should have required property 'first_name'"
} 
```

在 Fastify [验证和序列化文档](https://www.fastify.io/docs/latest/Validation-and-Serialization/)中了解更多信息。

### 记录

在 Node.js 应用程序中登录可能会对生产中的性能产生负面影响。这是因为将日志数据序列化并传输到其他地方需要很多步骤(例如，到 [Elasticsearch](https://www.elastic.co/elasticsearch/) )。重要的是，我们应用程序的这一方面得到了高度优化。

日志完全集成在 Fastify 中，这意味着我们不需要花费时间来选择和集成日志记录器。Fastify 使用快速灵活的记录器: [pino](https://getpino.io/) 。它生成 JSON 格式的日志:

```
{"level":30,"time":1615881822269,"pid":14323,"hostname":"localhost","msg":"Server listening at http://127.0.0.1:3000"}
{"level":30,"time":1615881829697,"pid":14323,"hostname":"localhost","reqId":"req-1","req":{"method":"GET","url":"/user/abc123","hostname":"localhost:3000","remoteAddress":"127.0.0.1","remotePort":38238},"msg":"incoming request"}
{"level":30,"time":1615881829704,"pid":14323,"hostname":"localhost","reqId":"req-1","res":{"statusCode":200},"responseTime":6.576989000663161,"msg":"request completed"} 
```

当我们创建 Fastify 服务器实例时，我们可以启用日志记录并定制传递给`pino`的选项。Fastify 将自动输出如上所示的日志消息。记录器实例在 Fastify 服务器实例(如`fastify.log.info("...")`)和所有[请求](https://www.fastify.io/docs/latest/Request/)对象(如`request.log.info("...")`)上都是可用的。

在 Fastify [日志文档](https://www.fastify.io/docs/latest/Logging/)中了解更多信息。

### 错误处理

Fastify 提供了一个 [setErrorHandler()](https://www.fastify.io/docs/latest/Server/#seterrorhandler) 方法，它允许我们显式地指定一个错误处理函数。这与 Express 不同，在 Express 中，错误处理中间件只能通过它接受的参数(`err, req, res, next`)来区分，并且必须按照特定的顺序添加。

为了充分的灵活性，我们可以在不同的插件中指定不同的 Fastify 错误处理程序。在 Fastify [错误文档](https://www.fastify.io/docs/latest/Errors/)中了解更多信息。

### 装修工

装饰器是 Fastify 中的一个强大特性，它允许我们定制核心 Fastify 对象——比如我们的 Fastify 服务器实例——以及请求和回复对象。下面是一个基本装饰的例子:

```
fastify.register(async (fastify, options) => {

  fastify.decorate("yolo", () => {
    return { yo: "lo" };
  });

  fastify.get("/yolo", async function(request, reply) {
    // Our Fastify server instance is bound to `this`
    reply.send(this.yolo());
  });

}); 
```

Decorators 允许我们在整个 Fastify 应用程序中使用数据库连接或视图引擎。在 Fastify [Decorators 文档](https://www.fastify.io/docs/latest/Decorators/)中了解更多信息。

## 结论

在本文中，我们学习了如何将现有的 Node.js 应用程序从 Express 迁移到 Fastify。我们已经看到了`fastify-express`插件如何帮助我们逐步迁移现有的应用程序。这允许我们开始受益于 Fastify 提供的特性，即使我们的应用程序的一部分仍在使用 Express。

当您从 Express 迁移到 Fastify 时，以下资源可能会对您有所帮助:

*   **[本文示例代码来自](https://github.com/simonplend/express-to-fastify-migration)** 。探索代码并运行我们在本文中构建的应用程序。
*   **[Fastify 文档](https://www.fastify.io/docs/latest/)** 。Fastify 框架的全面文档。
*   **[Fastify 生态系统](https://www.fastify.io/ecosystem/)** 。Fastify 的插件目录。方便查找插件来替换 Express 中间件。
*   **[Fastify 示例应用](https://github.com/delvedor/fastify-example)** 。由 Fastify 的主要维护者之一创建的示例应用程序。它展示了 Fastify 的核心概念、最佳实践和建议。
*   **[Fastify 社区不和服务器](https://discord.gg/fastify)** 。获得有关使用 Fastify 开发应用程序的帮助和建议的好地方。

## 分享这篇文章