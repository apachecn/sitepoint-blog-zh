# 使用 Deno、Reno 和 PostgreSQL 构建微服务

> 原文：<https://www.sitepoint.com/building-microservices-with-deno-reno-postgresql/>

在本教程中，我们将向您展示如何使用 Deno 构建微服务，并向您介绍 Reno——Deno 的瘦路由库。我们将探索如何使用这个较新的 JavaScript 平台来构建一个微服务，该微服务公开了对数据库进行操作的端点。

Deno 是 Node.js 创建者 Ryan Dahl 的一个 JavaScript 和 TypeScript 运行时，旨在解决后一种技术的一些缺点，例如简化模块路径查找算法和更紧密地将核心 API 与其基于浏览器的等效 API 对齐。尽管存在这些基本差异，Deno 和 Node.js 的潜在应用程序大部分是相同的。Node 的核心优势之一在于构建 HTTP 服务，Deno 也是如此。

## 用`std/http`编写 HTTP 服务器

在我们介绍路由库或考虑我们的数据访问层之前，回过头来用 [`std/http`模块](https://deno.land/std/http/mod.ts)构建一个简单的 HTTP 服务器是有用的，它是 [Deno 的标准库](https://deno.land/manual/standard_library)的一部分。如果你还没有安装 Deno。在 Unix 类型的操作系统中，您可以运行:

```
$ curl -fsSL https://deno.land/x/install/install.sh | sh -s v1.3.0 
```

请注意，本教程是针对 1.3.0(以及我们稍后将看到的 std 0.65.0)开发的，但是您可能使用的任何更高的 1.x 版本都应该是兼容的。或者，如果您运行的是 Deno 的旧版本，您可以使用`deno upgrade`命令升级到 1.3.0:

```
deno upgrade --version 1.3.0 
```

可以用`deno --version`验证预期的 Deno 版本已经安装。

我们现在可以构建一个 HTTP 服务器了。在通常的开发目录中创建一个名为`deno-hello-http`的目录，并在编辑器中打开它。然后，创建一个名为`server.ts`的文件，并使用`std/http`中的`listenAndServe`函数来构建我们的服务器:

```
import { listenAndServe } from "https://deno.land/std@0.65.0/http/mod.ts";

const BINDING = ":8000";

console.log(`Listening on ${BINDING}...`);

await listenAndServe(BINDING, (req) => {
  req.respond({ body: "Hello world!" });
}); 
```

### 开发者体验保护

如果你使用 VS 代码，我强烈推荐[官方 Deno 扩展](https://marketplace.visualstudio.com/items?itemName=denoland.vscode-deno)，它提供了对 Deno 路径解析算法的支持。此外，您可以运行`deno cache server.ts`来安装依赖项及其 TypeScript 定义，后者在编写代码时是一个非常有价值的 API 指南。

我们可以通过在 shell 中运行`deno run --allow-net server.ts`来启动我们的服务器。注意`--allow-net` [许可标志](https://deno.land/manual/getting_started/permissions)，授予我们的程序网络访问权。一旦监听了端口`8000`，我们就可以用一个 HTTP 请求来锁定它:

```
$ curl -v http://localhost:8000/ ; echo

> GET / HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.58.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-length: 12
<

Hello world! 
```

太好了！通过几行类型脚本，我们已经能够实现一个简单的服务器。也就是说，它在这一点上并没有特别好的特性。假设我们始终从回调函数中提供`"Hello world!"`,那么对于任何端点或 HTTP 方法都将返回相同的响应。如果我们用`POST /add`点击一个服务器，我们将收到相同的头和主体:

```
$ curl -v -d '{}' http://localhost:8000/add ; echo

> POST /add HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.58.0
> Accept: */*
> Content-Length: 2
> Content-Type: application/x-www-form-urlencoded
>
< HTTP/1.1 200 OK
< content-length: 12
<

Hello world! 
```

我们可以通过有条件地检查回调的`req`参数的`url`和`method`属性，将现有的响应限制为`GET /`:

```
import {
  listenAndServe,
  ServerRequest,
} from "https://deno.land/std@0.65.0/http/mod.ts";

const BINDING = ":8000";

console.log(`Listening on ${BINDING}...`);

function notFound({ method, url }: ServerRequest) {
  return {
    status: 404,
    body: `No route found for ${method}  ${url}`,
  };
}

await listenAndServe(BINDING, (req) => {
  const res = req.method === "GET" && req.url === "/"
    ? { body: "Hello world" }
    : notFound(req);

  req.respond(res);
}); 
```

如果我们重启我们的服务器，我们应该观察到`GET /`像预期的那样工作，但是任何其他的 URL 或方法都会导致 HTTP 404:

```
$ curl -v -d '{}' http://localhost:8000/add ; echo

> POST /add HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.58.0
> Accept: */*
> Content-Length: 2
> Content-Type: application/x-www-form-urlencoded
>
< HTTP/1.1 404 Not Found
< content-length: 28
<

No route found for POST /add 
```

## `std/http`超越简单的服务

用 Deno 和`std/http`引导普通的 HTTP 服务器被证明是相对简单的。这种方法如何扩展到更复杂的服务？

让我们考虑一个接受并返回用户提交的消息的`/messages`端点。遵循 RESTful 方法，我们可以定义这个端点的行为以及我们整个服务的行为:

*   `/messages`
*   `GET`:返回存储在服务器内存中的所有消息的 JSON 序列化数组
*   `POST`:向内存数组中添加一条新消息
*   所有其他方法将返回 [HTTP 405(不允许方法)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/405)
*   所有其他 URL 将返回 HTTP 404(未找到)

让我们更新现有的`server.ts`模块，使其符合新的服务规范:

```
import {
  listenAndServe,
  ServerRequest,
} from "https://deno.land/std@0.65.0/http/mod.ts";

interface MessagePayload {
  message: string;
}

const BINDING = ":8000";

const decoder = new TextDecoder();
const messages: string[] = [];

function jsonResponse<TBody>(body: TBody, status = 200) {
  return {
    status,
    headers: new Headers({
      "Content-Type": "application/json",
    }),
    body: JSON.stringify(body),
  };
}

function textResponse(body: string, status = 200) {
  return {
    status,
    headers: new Headers({
      "Content-Type": "text/plain",
    }),
    body,
  };
}

async function addMessage({ body }: ServerRequest) {
  const { message }: MessagePayload = JSON.parse(
    decoder.decode(await Deno.readAll(body)),
  );

  messages.push(message);

  return jsonResponse({ success: true }, 201);
}

function getMessages() {
  return jsonResponse(messages);
}

function methodNotAllowed({ method, url }: ServerRequest) {
  return textResponse(
    `${method} method not allowed for resource ${url}`,
    405,
  );
}

function notFound({ url }: ServerRequest) {
  return textResponse(`No resource found for ${url}`, 404);
}

function internalServerError({ message }: Error) {
  return textResponse(message, 500);
}

console.log(`Listening on ${BINDING}...`);

await listenAndServe(BINDING, async (req) => {
  let res = notFound(req);

  try {
    if (req.url === "/messages") {
      switch (req.method) {
        case "POST":
          res = await addMessage(req);
          break;
        case "GET":
          res = getMessages();
          break;
        default:
          res = methodNotAllowed(req);
      }
    }
  } catch (e) {
    res = internalServerError(e);
  }

  req.respond(res);
}); 
```

重启服务器并验证`GET` `/messages`返回一个以空 JSON 数组为主体的`application/json`响应。然后，我们可以通过向`/messages`发出一个带有有效负载的`POST`请求，并随后检索消息，来测试添加消息是否可行:

```
$ curl -v -H "Content-Type: application/json" -d '{ "message": "Hello!" }' http://localhost:8000/messages ; echo
< HTTP/1.1 201 Created
< content-length: 16
< content-type: application/json
<

{"success":true}

$ curl -v http://localhost:8000/messages ; echo
< HTTP/1.1 200 OK
< content-length: 10
< content-type: application/json
<

["Hello!"] 
```

## 宣告雷诺的路线

考虑到我们的服务只提供了一个端点，代码保持得相当不显眼。然而，如果它跨越许多端点，那么我们的路由处理代码将很快变得难以管理:

```
if (req.url === "/messages") {
  switch (req.method) {
    case "POST":
      res = await addMessage(req);
      break;
    case "GET":
      // Route params e.g. /messages/ade25ef
      const [, id] = req.url.match(/^\/messages\/([a-z0-9]*)$/) || [];
      res = id ? getMessage(id) : getMessages();
      break;
    default:
      res = methodNotAllowed(req);
  }
} else if (req.url === "/topics") {
  switch (req.method) {
    case "GET":
      res = getTopics();
      break;
    default:
      res = methodNotAllowed(req);
  }
} else if (req.url === "/users") {
  // ...etc
} 
```

我们当然可以构建这段代码，使其更具声明性，比如定义一个与特定路径匹配的路由处理函数的`Map`,但是我们仍然必须自己处理路由实现，扩展到路由查找、路径和查询参数的解析以及嵌套路由。即使是结构最好的代码，这也是一项艰巨的任务，而且在商业环境中会消耗宝贵的开发时间。

在过去的一年里，我一直在从事 [Reno](https://github.com/reno-router/reno) 的工作，这是一个用于`std/http`服务器的路由库，它处理并抽象了大部分这种复杂性，使我们能够专注于应用程序的核心逻辑。使用它提供的路由器附带功能，让我们重新构建我们的消息服务:

```
import {
  listenAndServe,
  ServerRequest,
} from "https://deno.land/std@0.65.0/http/mod.ts";

import {
  createRouter,
  createRouteMap,
  forMethod,
  withJsonBody,
  jsonResponse,
  textResponse,
  ProcessedRequest,
  NotFoundError,
} from "https://deno.land/x/reno@v1.3.0/reno/mod.ts";

interface MessagePayload {
  message: string;
}

const BINDING = ":8000";

const messages: string[] = [];

async function addMessage(
  { body: { message } }: ProcessedRequest<MessagePayload>,
) {
  messages.push(message);
  return jsonResponse({ success: true }, {}, 201);
}

function getMessages() {
  return jsonResponse(messages);
}

function notFound({ url }: ServerRequest) {
  return textResponse(`No resource found for ${url}`, {}, 404);
}

function internalServerError({ message }: Error) {
  return textResponse(message, {}, 500);
}

const routes = createRouteMap([
  [
    "/messages",
    forMethod([
      ["GET", getMessages],
      ["POST", withJsonBody<MessagePayload>(addMessage)],
    ]),
  ],
]);

const router = createRouter(routes);

console.log(`Listening on ${BINDING}...`);

await listenAndServe(BINDING, async (req) => {
  try {
    req.respond(await router(req));
  } catch (e) {
    req.respond(
      e instanceof NotFoundError ? notFound(req) : internalServerError(e),
    );
  }
}); 
```

如果您重启服务器并向`/messages`发出同样的`GET`和`POST`请求，我们会注意到核心功能保持不变。为了重申 Reno 处理的复杂性，以下是多端点示例:

```
const routes = createRouteMap([
  [
    /^\/messages\/([a-z0-9]*)$/,
    forMethod([
      ["GET", ({ routeParams: [id] }) => id ? getMessage(id) : getMessages],
      ["POST", withJsonBody<MessagePayload>(addMessage)],
    ]),
  ],
  ["/topics", getTopics],
  ["/users", getUsers],
]); 
```

由于 Reno 提供了内置的路径解析和开箱即用的 HTTP 方法处理，以及其他特性，因此我们只需要关心端点的*声明*以及响应它们可能收到的请求的逻辑。

Reno 值得强调的一个基本原则是，它将自己呈现为一个功能为的*路由器。也就是`const response = await router(request)`。与通常负责引导 HTTP 服务器和管理其生命周期的成熟服务器框架相反，Reno 只关心请求的路由，这是通过独立的函数调用来实现的；这有助于它的采用以及与现有 Deno 服务的集成。*

## 使用 Reno 构建微服务

鉴于 Reno 的小 API，它非常适合开发[微服务](https://www.sitepoint.com/premium/books/python-microservices-development/read/1)。在这种情况下，我们将使用 deno 和 Reno 构建一个博客帖子微服务，由 PostgreSQL 数据库提供支持(我们将使用出色的 [deno-postgres](https://github.com/deno-postgres/deno-postgres) 从 Deno 查询我们的数据库)。我们的服务将公开支持许多操作的单个`/posts`端点:

*   `GET /posts`:检索数据库中所有帖子的元数据
*   `GET /posts/<UUID>`:检索给定 UUID 的帖子的元数据和内容
*   `POST /posts`:向数据库中添加新帖子
*   `PATCH /posts/<UUID>`:用给定的 UUID 替换帖子的内容

对于单个教程来说，构建一个完全成熟的微服务听起来可能是一项令人生畏的任务，但我出于礼貌提供了一个包含 [Docker Compose](https://docs.docker.com/compose/) 设置和预写数据库脚本和查询的实质性样板文件。首先，确保你已经安装了 [Docker](https://docs.docker.com/get-docker/) [和 Docker Compose](https://docs.docker.com/compose/install/) ，然后【克隆】Reno blog 微服务，**专门检查`sitepoint-boilerplate`分支**:

```
$ git clone --branch sitepoint-boilerplate https://github.com/reno-router/blog-microservice.git 
```

用您选择的编辑器打开`blog-microservice`文件夹。在我们实施第一条路线之前，我将从较高的层面讨论一些关键的目录和文件:

*   `data`:包含创建数据库容器时将运行的 SQL 脚本，定义我们的应用程序的表并用一些种子数据填充它们。
*   `service/blog_service.ts`:提供检索、创建和更新存储在数据库中的帖子的方法。
*   位于 deno-postgres 之上的通用数据库抽象，免费处理连接池和事务。
*   预定义的 Postgres 查询，用于我们的各种数据库操作；博客服务将这些传递给数据库服务，并将结果以可消费的格式转发给调用者。这些查询是[参数化的](https://www.sitepoint.com/how-to-protect-your-website-against-sql-injection-attacks/#usecommandparameters)，其值 deno-postgres 将自动统计。
*   `service/server.ts`:我们服务器的入口点。
*   包含所有外部依赖项的集中模块，允许在单点维护它们。这种做法在 Deno 项目中很常见，并且得到了官方手册的认可。
*   声明我们的生产 Docker 容器，它将在构建时安装我们项目的依赖项，大大减少冷启动时间。
*   `Dockerfile.local`:声明我们的开发 Docker 容器，使用 [Denon](https://github.com/denosaurs/denon) 在我们的源代码改变时自动重启 Deno。
*   一个 Docker Compose 配置，包括我们的开发容器和一个 Postgres 容器，我们的 SQL 脚本在这个容器上运行，极大地减少了运行我们项目的任何先决步骤。

让我们创建我们的应用程序的路线。在`service`文件夹中，创建一个名为`routes.ts`的新文件。用这些导入填充它，我们很快就会用到:

```
import {
  createRouteMap,
  jsonResponse,
  forMethod,
  DBPool,
  uuidv4,
} from "../deps.ts";

import createBlogService from "./blog_service.ts";
import createDbService from "./db_service.ts"; 
```

接下来，让我们实例化我们的数据库连接池。注意，通过使用`Object.fromEntries`，我们可以以相对简洁的方式构建 deno-postgres 所需的[选项对象:](https://github.com/deno-postgres/deno-postgres#example)

```
function createClientOpts() {
  return Object.fromEntries([
    ["hostname", "POSTGRES_HOST"],
    ["user", "POSTGRES_USER"],
    ["password", "POSTGRES_PASSWORD"],
    ["database", "POSTGRES_DB"],
  ].map(([key, envVar]) => [key, Deno.env.get(envVar)]));
}

function getPoolConnectionCount() {
  return Number.parseInt(Deno.env.get("POSTGRES_POOL_CONNECTIONS") || "1", 10);
}

const dbPool = new DBPool(createClientOpts(), getPoolConnectionCount()); 
```

使用实例化的连接池，我们可以创建数据库和博客服务:

```
const blogService = createBlogService(
  createDbService(dbPool),
  uuidv4.generate,
); 
```

现在让我们编写一个路由处理程序来检索数据库中的所有帖子:

```
async function getPosts() {
  const res = await blogService.getPosts();
  return jsonResponse(res);
} 
```

为了将我们的处理程序绑定到`GET /posts`，我们需要声明一个路线图并将其导出:

```
const routes = createRouteMap([
  ["/posts", forMethod([
    ["GET", getPosts],
  ])],
]);

export default routes; 
```

端到端，`routes.ts`应该是这样的:

```
import {
  createRouteMap,
  jsonResponse,
  forMethod,
  DBPool,
  uuidv4,
} from "../deps.ts";

import createBlogService from "./blog_service.ts";
import createDbService from "./db_service.ts";

function createClientOpts() {
  return Object.fromEntries([
    ["hostname", "POSTGRES_HOST"],
    ["user", "POSTGRES_USER"],
    ["password", "POSTGRES_PASSWORD"],
    ["database", "POSTGRES_DB"],
  ].map(([key, envVar]) => [key, Deno.env.get(envVar)]));
}

function getPoolConnectionCount() {
  return Number.parseInt(Deno.env.get("POSTGRES_POOL_CONNECTIONS") || "1", 10);
}

const dbPool = new DBPool(createClientOpts(), getPoolConnectionCount());

const blogService = createBlogService(
  createDbService(dbPool),
  uuidv4.generate,
);

async function getPosts() {
  const res = await blogService.getPosts();
  return jsonResponse(res);
}

const routes = createRouteMap([
  ["/posts", forMethod([
    ["GET", getPosts],
  ])],
]);

export default routes; 
```

为了将请求转发给我们的处理程序，我们需要更新现有的`server.ts`模块。将`createRouter`添加到从`deps.ts`导入的绑定:

```
import {
  listenAndServe,
  ServerRequest,
  textResponse,
  createRouter,
} from "../deps.ts"; 
```

在这条语句下面，我们需要导入我们的路由:

```
import routes from "./routes.ts"; 
```

为了创建我们的服务的路由器，调用服务器监听消息上面的`createRouter`函数*，传递我们的路由作为唯一的参数:*

```
const router = createRouter(routes); 
```

最后，为了将传入的请求转发到我们的路由器并返回预期的响应，让我们在服务器回调的`try`块内调用路由器:

```
try {
  const res = await router(req);
  return req.respond(res);
} 
```

我们现在可以运行我们的应用程序了，但是还有最后一步。我们需要将`.env.sample`文件重命名为`.env`。它有一个`.sample`后缀，表示它不包含任何真实世界的敏感值，但是为了开始，我们仍然可以逐字使用它们:

```
$ mv .env.sample .env 
```

通过 swift `docker-compose up`，我们应该可以看到数据库和服务容器变得活跃起来，后者最终监听端口 8000:

```
$ docker-compose up

# [...]

db_1   | 2020-08-16 22:04:50.314 UTC [1] LOG:  database system is ready to accept connections
# [...]
api_1  | Listening for requests on :8000... 
```

一旦绑定到该端口，我们应该验证我们的端点是否工作。它应该返回数据库中每个帖子的 ID、标题和标签，当前由种子数据填充:

```
# jq is like sed for JSON data:
# https://stedolan.github.io/jq/

$ curl http://localhost:8000/posts | jq 
```

```
[
  {
    "id": "006a8213-8aac-47e2-b728-b0e2c07ddaf6",
    "title": "Go's generics experimentation tool",
    "author": {
      "id": "c9e69690-9246-41bf-b912-0c6190f64f1f",
      "name": "Joe Bloggs"
    },
    "tags": [
      {
        "id": "f9076c31-69eb-45cf-b51c-d7a1b6e3fe0c",
        "name": "Go"
      }
    ]
  },
  {
    "id": "16f9d2b0-baf9-4618-a230-d9b95ab75fa8",
    "title": "Deno 1.3.0 released",
    "author": {
      "id": "91ef4450-97ff-44da-8b1d-f1560e9d10cc",
      "name": "James Wright"
    },
    "tags": [
      {
        "id": "21c1ac3a-9c1b-4be1-be50-001b44cf84d1",
        "name": "JavaScript"
      },
      {
        "id": "ac9c2f73-6f11-470f-b8a7-9930dbbf137a",
        "name": "TypeScript"
      },
      {
        "id": "c35defc4-42f1-43b9-a181-a8f12b8457f1",
        "name": "Deno"
      },
      {
        "id": "d7c2f180-18d6-423e-aeda-31c4a3a7ced1",
        "name": "Rust"
      }
    ]
  }
] 
```

## 检索帖子的内容

接下来要执行的操作是`GET /posts/<UUID>`。假设我们已经在处理`GET /posts`，我们可以做一组最小的修改来通过 ID 检索单个帖子。首先，让我们调整路由图中的`"/posts"`路径绑定，引入一个通配符路径段:

```
const routes = createRouteMap([
  ["/posts/*", forMethod([
    ["GET", getPosts],
  ])],
]); 
```

除了正则表达式，Reno 还允许字符串路径与通配符(' * ')一起使用，这些通配符将通过请求的`routeParams`属性被捕获和公开。虽然它们不像正则表达式那样具体，但是它们更容易阅读，并且通常是达到相同目的的一种手段。让我们更新`getPosts`路由处理程序，以确定 path 参数的存在，并从博客服务中检索一篇文章(如果存在的话)(`AugmentedRequest`类型可以从`deps.ts`中导入):

```
async function getPosts({ routeParams: [id] }: AugmentedRequest) {
  const res = await (id ? blogService.getPost(id) : blogService.getPosts());
  return jsonResponse(res);
} 
```

注意`routeParams`是一个线性排序的数组，每一项都按照声明的顺序引用 path 参数。在我们的例子中，我们可以确定第一个条目总是指向一个帖子 ID。在保存我们的更改后，Denon 将检测到这些更改并重启 Deno，调用`GET /posts`后接我们的一个帖子的 ID 将返回其元数据*和*内容:

```
$ curl http://localhost:8000/posts/16f9d2b0-baf9-4618-a230-d9b95ab75fa8 | jq 
```

```
{
  "id": "16f9d2b0-baf9-4618-a230-d9b95ab75fa8",
  "title": "Deno 1.3.0 released",
  "contents": "This release includes new flags to various Deno commands and implements the W3C FileReader API, amongst other enhancements and fixes.",
  "author": {
    "id": "91ef4450-97ff-44da-8b1d-f1560e9d10cc",
    "name": "James Wright"
  },
  "tags": [
    {
      "id": "21c1ac3a-9c1b-4be1-be50-001b44cf84d1",
      "name": "JavaScript"
    },
    {
      "id": "ac9c2f73-6f11-470f-b8a7-9930dbbf137a",
      "name": "TypeScript"
    },
    {
      "id": "c35defc4-42f1-43b9-a181-a8f12b8457f1",
      "name": "Deno"
    },
    {
      "id": "d7c2f180-18d6-423e-aeda-31c4a3a7ced1",
      "name": "Rust"
    }
  ]
} 
```

## 处理不存在的职位

扩展我们的`GET /posts`操作以通过 ID 检索单个帖子导致了一个 bug。让我们为一个不存在的 ID 请求一个帖子的内容:

```
$ curl -v http://localhost:8000/posts/b801087e-f1c9-4b1e-9e0c-70405b685e86

> GET /posts/b801087e-f1c9-4b1e-9e0c-70405b685e86 HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-length: 0
< content-type: application/json
< 
```

因为当找不到具有给定 ID 的帖子时,`blogService.getPost(id)`会返回`undefined`,所以我们当前的处理程序会产生一个带有空主体的 HTTP 200 响应。最好向请求者指出这个错误。为了保持`getPosts`函数的可读性，让我们将`blogService.getPost(id)`调用提升到它自己的函数中，在这个函数中，如果检索到的帖子是`undefined`，我们将抛出一个错误。`BlogService`型可以从`blog_service.ts`导入:

```
async function getPost(blogService: BlogService, id: string) {
  const res = await blogService.getPost(id);

  if (!res) {
    throw new Error(`Post not found with ID ${id}`);
  }

  return res;
}

async function getPosts({ routeParams: [id] }: AugmentedRequest) {
  const res = await (id ? getPost(blogService, id) : blogService.getPosts());
  return jsonResponse(res);
} 
```

如果我们现在请求一个不存在的帖子，我们将得到一个错误响应:

```
$ curl -v http://localhost:8000/posts/b801087e-f1c9-4b1e-9e0c-70405b685e86 ; echo

> GET /posts/b801087e-f1c9-4b1e-9e0c-70405b685e86 HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 500 Internal Server Error
< content-length: 59
< content-type: text/plain
<

Post not found with ID b801087e-f1c9-4b1e-9e0c-70405b685e86 
```

这当然是一个改进，但是可能状态代码不准确。这个响应不是应用程序错误的结果，而是用户指定了一个丢失的帖子。在这种情况下，HTTP 404 将是更好的选择。在`getPost`函数上面，我们可以定义一个自定义的错误类，在没有找到帖子时抛出:

```
export class PostNotFoundError extends Error {
  constructor(id: string) {
    super(`Post not found with ID ${id}`);
  }
} 
```

然后，在`getPost`的主体中，我们可以抛出这个而不是普通的`Error`实例:

```
async function getPost(blogService: BlogService, id: string) {
  const res = await blogService.getPost(id);

  if (!res) {
    throw new PostNotFoundError(`Post not found with ID ${id}`);
  }

  return res;
} 
```

抛出自定义错误的好处是，当它被捕获时，我们能够提供特定的响应。在`server.ts`中，让我们更新`mapToErrorResponse`函数中的`switch`语句，当我们的`PostNotFoundError`发生时，返回一个对`notFound()`的调用:

```
function mapToErrorResponse(e: Error) {
  switch (e.constructor) {
    case PostNotFoundError:
      return notFound(e);
    default:
      return serverError(e);
  }
} 
```

在重试之前的请求时，我们现在应该看到我们收到了一个 HTTP 404:

```
$ curl -v http://localhost:8000/posts/b801087e-f1c9-4b1e-9e0c-70405b685e86 ; echo

> GET /posts/b801087e-f1c9-4b1e-9e0c-70405b685e86 HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 404 Not Found
< content-length: 82
< content-type: text/plain
<

Post not found with ID Post not found with ID b801087e-f1c9-4b1e-9e0c-70405b685e86 
```

我们还应该将 Reno 的`NotFoundError`添加到这个案例中，如果请求路由不存在，这也将导致提供 HTTP 404:

```
switch (e.constructor) {
  case PostNotFoundError:
  case NotFoundError:
    return notFound(e);
  default:
    return serverError(e);
} 
```

我们可以遵循这种模式来处理应用程序中的其他类型的错误。例如，当用户*用无效的 UUID 创建*资源时，完整服务[提供 HTTP 400(错误请求)。](https://github.com/reno-router/blog-microservice/blob/45ccafec8ce2791259a4834005ad9a3edb998183/service/server.ts#L73)

## 向数据库添加新帖子

到目前为止，我们已经实现的操作从数据库中读取帖子。创建新的职位呢？我们可以为此添加一个路由处理器，但是首先我们需要将`deps.ts`中的`withJsonBody`导入到`routes.ts`中:

```
import {
  createRouteMap,
  jsonResponse,
  forMethod,
  DBPool,
  uuidv4,
  AugmentedRequest,
  withJsonBody,
} from "../deps.ts"; 
```

我们还应该从`blog_service.ts`导入`CreatePostPayload`接口，我们很快就会用到它:

```
import createBlogService, {
  BlogService,
  CreatePostPayload,
} from "./blog_service.ts"; 
```

`withJsonBody`是一个[的高阶](https://www.sitepoint.com/higher-order-functions-javascript/)路由处理器，它将假设底层请求体是一个 JSON 序列化的字符串，并为我们解析它。它还支持一个泛型参数，允许我们断言主体的类型。让我们用它来定义我们的`addPost`处理程序:

```
const addPost = withJsonBody<CreatePostPayload>(
  async function addPost({ body }) {
    const id = await blogService.createPost(body);
    return jsonResponse({ id });
  },
); 
```

然后，我们必须在我们的路线图中注册处理程序:

```
const routes = createRouteMap([
  [
    "/posts/*",
    forMethod([
      ["GET", getPosts],
      ["POST", addPost],
    ]),
  ],
]); 
```

为了测试我们的`POST /posts`操作是否工作正常，我们可以用一个有效的创建后有效负载发出这个请求:

```
$ curl -H "Content-Type: application/json" -d '{
  "authorId": "91ef4450-97ff-44da-8b1d-f1560e9d10cc",
  "title": "New post",
  "contents": "This was submitted via our new API endpoint!",
  "tagIds": ["6a7e1f4d-7fca-4573-b138-f2cba0163077", "f9076c31-69eb-45cf-b51c-d7a1b6e3fe0c"]
}' http://localhost:8000/posts | jq 
```

```
{
  "id": "586bb055-cea6-4d56-8d8d-1856e8f8e5eb"
} 
```

然后，我们可以通过由生成的 UUID 请求发布来确保这已经成功地存储在我们的数据库中:

```
$ curl http://localhost:8000/posts/586bb055-cea6-4d56-8d8d-1856e8f8e5eb | jq 
```

```
{
  "id": "586bb055-cea6-4d56-8d8d-1856e8f8e5eb",
  "title": "New post",
  "contents": "This was submitted via our new API endpoint!",
  "author": {
    "id": "91ef4450-97ff-44da-8b1d-f1560e9d10cc",
    "name": "James Wright"
  },
  "tags": [
    {
      "id": "6a7e1f4d-7fca-4573-b138-f2cba0163077",
      "name": "C#"
    },
    {
      "id": "f9076c31-69eb-45cf-b51c-d7a1b6e3fe0c",
      "name": "Go"
    }
  ]
} 
```

## 编辑现有帖子

为了结束我们的服务，我们将实现`PATCH /posts/<UUID>`路由，它使帖子的内容能够被替换。让我们从从`blog_service.ts`导入`EditPostPayload`接口开始:

```
import createBlogService, {
  BlogService,
  CreatePostPayload,
  EditPostPayload,
} from "./blog_service.ts"; 
```

接下来，我们应该添加一个名为`editPost`的路由处理函数:

```
const editPost = withJsonBody<EditPostPayload>(
  async function editPost({ body: { contents }, routeParams: [id] }) {
    const rowCount = await blogService.editPost(id, contents);

    if (rowCount === 0) {
      throw new PostNotFoundError(id);
    }

    return jsonResponse({ id });
  },
); 
```

最后，让我们将处理程序添加到我们的路由中:

```
const routes = createRouteMap([
  [
    "/posts/*",
    forMethod([
      ["GET", getPosts],
      ["POST", addPost],
      ["PATCH", editPost],
    ]),
  ],
]); 
```

我们可以通过更新我们在上一节中创建的帖子的内容来确定我们的处理程序是有效的:

```
$ curl -X PATCH -H "Content-Type: application/json" -d '{
  "contents": "This was edited via our new API endpoint!"
}' http://localhost:8000/posts/586bb055-cea6-4d56-8d8d-1856e8f8e5eb | jq 
```

```
{
  "id": "586bb055-cea6-4d56-8d8d-1856e8f8e5eb"
} 
```

```
$ curl http://localhost:8000/posts/586bb055-cea6-4d56-8d8d-1856e8f8e5eb | jq .contents 
```

```
"This was edited via our new API endpoint!" 
```

调用`GET /posts`操作还应该表明**没有**额外的帖子被存储在数据库中。

## 后续步骤

我们已经构建了一个设计良好且可维护的服务，但仍有一些额外的步骤可以提高我们服务的健壮性和安全性，比如验证传入的有效负载并授权`POST`和`PUT`请求。此外，我们可以为我们的路由处理器编写一些单元测试。假设它们是有效的纯函数(也就是说，它们对给定的输入产生确定的响应，副作用是可选的)，我们可以用相对较少的开销实现这一点:

```
Deno.test(
  "getPosts route handler should retrieve the post for the given ID from the blog service",
  async () => {
    const id = "post ID";

    const post = {
      id,
      title: "Test Post",
      author: {
        id: "author ID",
        name: "James Wright",
      },
      tags: [
        { id: "tag ID", name: "JavaScript" },
        { id: "tag ID", name: "TypeScript" },
      ],
    };

    const blogService = {
      getPost: sinon.stub().resolves(post),
      getPosts: sinon.stub().resolves(),
    };

    const getPosts = createGetPostsHandler(blogService);
    const response = await getPosts({ routeParams: [id] });

    assertResponsesAreEqual(response, jsonResponse(post));
    assertStrictEquals(blogService.getPost.callCount, 1);
    assertStrictEquals(blogService.getPosts.callCount, 0);
  },
); 
```

注意，我们使用[部分应用程序](https://www.sitepoint.com/currying-in-functional-javascript/)将存根博客服务注入到路由处理程序中，我们可以相应地更新它:

```
export function createGetPostsHandler(
  blogService: Pick<BlogService, "getPosts" | "getPost">,
) {
  return async function getPosts(
    { routeParams: [id] }: Pick<AugmentedRequest, "routeParams">,
  ) {
    const res = await (id ? getPost(blogService, id) : blogService.getPosts());
    return jsonResponse(res);
  };
} 
```

然后，实际的服务将以类似于测试的方式向处理程序提供真正的博客服务。另一个有趣的观察是，`Pick<BlogService, "getPosts" | "getPost">`允许我们提供一个仅包含其属性子集的`BlogService`的实现，这意味着我们不必定义每一个方法来测试甚至不需要它们的处理程序。

## 摘要

使用`std/http`构建小型 HTTP 服务是可以实现的，但是随着应用程序的增长，管理额外的端点、特定 HTTP 方法的专用逻辑和错误处理会变得很麻烦。Reno 向我们隐藏了这些复杂性，使我们能够专注于微服务的核心业务逻辑。给定路由处理器函数的结构，使用 Reno 路由的应用程序本质上有助于单元测试，并且可以很容易地与现有的 Deno 项目集成。

也就是说，更大或更复杂的服务可能会受益于一个完整的框架，如 [Oak](https://github.com/oakserver/oak) 。然而，对于微服务，Reno 提供了一个非常小的、不显眼的 API 表面，允许它们随着我们业务需求的增长而扩展。

## 德诺基金会

跟上德诺的步伐。我们的 Deno Foundations collection 帮助您迈出进入 Deno 世界以及更远的第一步，并且我们会不断增加它。我们将为您带来成为职业选手所需的教程。您可以随时参考我们的索引，因为它在我们对 Deno 的介绍结束时更新:

➤ [德诺基金会](https://www.sitepoint.com/deno-guide#foundations)

## 分享这篇文章