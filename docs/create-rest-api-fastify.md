# 如何用伪造创建你的第一个 rest api

> 原文：<https://www.sitepoint.com/create-rest-api-fastify/>

**[Fastify](https://www.fastify.io/) 是一个为后端 web 开发设计的框架。它为较重的 Node.js API 框架(如哈比神和 Express)提供了一个更轻量级的替代方案。从 2020 年 7 月起，Fastify 发布了第三版框架。**

第三个版本提供了改进的验证功能，可以作为请求参数来验证传入和传出的请求。此外，与 Koa、Resitfy、哈比神和 Express 相比，该框架的第三个版本巩固了其作为最快 Node.js 框架的吞吐量声明。更多信息可在[性能指标评测页面](https://www.fastify.io/benchmarks/)上找到。

Fastify 由于其轻量级的设计获得了很大的人气。然而，很多注意力都集中在它的插件生态系统上。Fastify 采用了一切都是插件的理念，而对于 JavaScript，一切都是对象。这允许您将项目的功能快速封装为插件，并分发它，以便其他项目可以使用您的代码。

让我们从这个教程开始吧。您将了解 Fastify 的以下方面:

*   如何设置你的第一个伪造 api
*   如何定义 Fastify API 路线
*   如何向请求添加架构验证
*   如何加载和使用 Fastify 插件
*   如何定义 Fastify 挂钩

## 要求和安装

要学习本教程，您需要:

1.  最新的 Node.js 版本
2.  发送请求的工具，如 [cURL](https://curl.haxx.se/) 或 [Postman](https://www.postman.com/)

接下来，确保创建一个空的 Node.js 项目。如果您还没有，您可以使用下面的命令来设置您的项目:

```
npm init -y 
```

最后，我们希望将这个 Fastify 依赖项添加到我们的项目中:

```
npm i fastify --save 
```

一切都好吗？让我们在下一步创建基本的 API 设置。

## 步骤 1:基本 API 设置

首先，让我们创建基本的 API 设置。首先，我们需要在项目根目录下创建一个名为`index.js`的新文件:

```
touch index.js 
```

接下来，让我们添加基本的服务器设置。复制下面的代码:

```
// Require the framework and instantiate it
const app = require('fastify')({
    logger: true
})

// Declare a route
app.get('/', function (req, reply) {
    reply.send({ hello: 'world' })
})

// Run the server!
app.listen(3000, (err, address) => {
    if (err) {
        app.log.error(err)
        process.exit(1)
    }
    app.log.info(`server listening on ${address}`)
}) 
```

这里发生了一些事情。我们首先加载 Fastify 应用程序对象并启用日志记录。接下来，我们声明一个用 JSON 响应回复的根路由。代码片段的最后一部分显示，我们在端口 3000 上监听应用程序接收请求。

让我们验证一下您的基本服务器设置是否有效。首先，我们需要通过运行`index.js`文件来启动服务器:

```
node index.js 
```

此后，在浏览器中导航至`http://localhost:3000`。您应该会看到以下响应:

```
{
    "hello": "world"
} 
```

成功？让我们转到步骤 2 来定义不同的 CRUD 路径。

## 步骤 2:定义 CRUD 路线

API 对于仅获取路由是无用的。让我们定义更多处理博客的路线。因此，让我们创建以下路线:

*   从/api/blogs 获取所有博客
*   在/api/blogs/:id 获取一个博客
*   在/api/blogs 上发布添加博客
*   将更新博客放在/api/blogs/:id
*   删除删除位于/api/blogs/:id 的博客

首先要做的是创建一个博客控制器。

### 步骤 2.1:创建博客控制器

为了保持代码的整洁，让我们在项目根目录中定义一个`controller`文件夹。在这里，我们创建一个名为`blogs.js`的文件。

该文件包含一些演示数据，以避免数据库集成使本教程变得复杂。因此，我们使用一个包含 blog 对象的数组，每个对象包含一个 ID 和 title 字段。

此外，我们在这个文件中为所有上述路由定义了不同的处理程序。处理程序总是接受一个`req`(请求)和`reply`参数。请求参数对于访问请求参数或请求正文数据很有用。

将以下代码添加到您的`/controller/blogs.js`文件中:

```
// Demo data
let blogs = [
    {
        id: 1,
        title: 'This is an experiment'
    },
    {
        id: 2,
        title: 'Fastify is pretty cool'
    },
    {
        id: 3,
        title: 'Just another blog, yea!'
    }
]

// Handlers
const getAllBlogs = async (req, reply) => {
    return blogs
}

const getBlog = async (req, reply) => {
    const id = Number(req.params.id) // blog ID
    const blog = blogs.find(blog => blog.id === id)
    return blog
}

const addBlog = async (req, reply) => {
    const id = blogs.length + 1 // generate new ID
    const newBlog = {
        id,
        title: req.body.title
    }

    blogs.push(newBlog)
    return newBlog
}

const updateBlog = async (req, reply) => {
    const id = Number(req.params.id)
    blogs = blogs.map(blog => {
        if (blog.id === id) {
            return {
                id,
                title: req.body.title
            }
        }
    })

    return {
        id,
        title: req.body.title
    }
}

const deleteBlog = async (req, reply) => {
    const id = Number(req.params.id)

    blogs = blogs.filter(blog => blog.id !== id)
    return { msg: `Blog with ID ${id} is deleted` }
}

module.exports = {
    getAllBlogs,
    getBlog,
    addBlog,
    updateBlog,
    deleteBlog
} 
```

注意我们如何通过`req.params.id`访问路由的请求参数，比如`/api/blogs/:id`。对于 POST 和 PUT 路由，我们可以通过`req.body`访问请求的主体。

在步骤 2.2 中，我们将把路由处理器连接到路由对象。

### 步骤 2.2:定义博客路径并耦合博客控制器

同样，为了保持代码的整洁，让我们在项目根目录中定义一个`routes`文件夹。在这里，我们创建一个名为`blogs.js`的文件。这个文件保存了我们博客路由的 routes 对象:

```
mkdir routes
cd routes
touch blogs.js 
```

幸运的是，Fastify 允许我们定义一个包含路由对象的数组。在这里，我们可以将之前定义的处理程序耦合到不同的路由。不要忘记需要博客控制器。让我们来看看:

```
const blogController = require('../controller/blogs');

const routes = [{
        method: 'GET',
        url: '/api/blogs',
        handler: blogController.getAllBlogs
    },
    {
        method: 'GET',
        url: '/api/blogs/:id',
        handler: blogController.getBlog
    },
    {
        method: 'POST',
        url: '/api/blogs',
        handler: blogController.addBlog
    },
    {
        method: 'PUT',
        url: '/api/blogs/:id',
        handler: blogController.updateBlog
    },
    {
        method: 'DELETE',
        url: '/api/blogs/:id',
        handler: blogController.deleteBlog
    }
]
module.exports = routes 
```

现在我们已经定义了所有的路线。但是，Fastify 并不知道这些路线。下一步将展示如何向 Fastify 应用程序对象注册路线。

### 步骤 2.3:注册 Fastify 路线

在这一步中，我们将向 app 对象注册 Fastify 路线。首先，我们加载所有的博客路径。接下来，我们循环遍历所有路由，逐一注册它们:

```
// Require the framework and instantiate it
const app = require('fastify')({
    logger: true
})

// Declare a route
app.get('/', function (req, reply) {
    reply.send({ hello: 'world' })
})

// Register routes to handle blog posts
const blogRoutes = require('./routes/blogs')
blogRoutes.forEach((route, index) => {
    app.route(route)
})

// Run the server!
app.listen(3000, (err, address) => {
    if (err) {
        app.log.error(err)
        process.exit(1)
    }
    app.log.info(`server listening on ${address}`)
}) 
```

完成了吗？是时候验证博客路线是否有效了。使用`node index.js`启动服务器并访问`http://localhost:3000/blogs/1`从演示数据中获取第一个博客。您应该会看到以下结果:

```
{
    "id": 1,
    "title": "This is an experiment"
} 
```

一切都好吗？让我们在步骤 3 中学习如何向请求和响应添加模式验证。

## 步骤 3:添加模式验证

这一步将教您如何向项目中添加模式验证。我们可以利用我们的`routes`定义中的`schema`键将验证模式传递给特定的路由。

让我们从定义路由`/api/blogs/:id`的模式开始，以验证请求参数和响应。要求？

1.  `:id`参数必须是字符串类型
2.  响应必须包含具有两个属性`id`(整数)和`title`(字符串)的对象

将以下验证对象添加到您的`routes/blogs.js`文件中:

```
const getBlogValidation = {
        params: {
            id: { type: 'string' }
        },
        response: {
            200: {
                type: 'object',
                properties: {
                    id: { type: 'integer' },
                    title: { type: 'string' }
                }
            }
        }
} 
```

为了将验证对象连接到我们的路由，我们必须定义模式键。在`routes`数组中寻找`/api/blogs/:id`路线，并相应地改变对象:

```
...
{
    method: 'GET',
    url: '/api/blogs/:id',
    schema: getBlogValidation, // add validation
    handler: blogController.getBlog
},
... 
```

让我们为添加博客`POST /api/blogs`做同样的事情。这里，我们想要验证`req.body`对象是否包含一个`title`参数。让我们来看看:

```
const addBlogValidation = {
    body: {
        type: 'object',
        required: [
            'title'
        ],
        properties: {
            title: { type: 'string' }
        }
    },
    response: {
        200: {
            type: 'object',
            properties: {
                id: { type: 'integer' },
                title: { type: 'string' }
            }
        }
    }
} 
```

接下来，我们必须将验证对象再次连接到正确的路由:

```
...
{
    method: 'POST',
    url: '/api/blogs',
    schema: addBlogValidation, // add validation
    handler: blogController.addBlog
},
... 
```

为了验证我们的验证，让我们检索 ID 为 3 的博客。在`http://localhost:3000/api/blogs/3`打开浏览器。您应该会看到以下响应:

```
{
    "id": 3,
    "title": "Just another blog, yea!"
} 
```

现在，让我们犯一个错误，将`id`字段的`params`验证从`sting`更改为`object`，如下所示:

```
const getBlogValidation = {
        params: {
            id: { type: 'object' } // Try changing to object to see error
        },
        response: {
            200: {
                type: 'object',
                properties: {
                    id: { type: 'integer' },
                    title: { type: 'string' }
                }
            }
        }
} 
```

当从 API 请求相同的资源时，您会收到下面的错误消息。

```
{
    "statusCode": 400,
    "error": "Bad Request",
    "message": "params.id should be object"
} 
```

你看到错误了吗？很好！让我们将更改恢复到`string`以避免将来的错误，并进入下一步。

## 步骤 4:加载 Fastify 插件

在这里，让我们利用 Fastify 丰富的[插件生态系统](https://www.fastify.io/ecosystem/)。您可以找到帮助您完成各种任务的插件，例如数据库集成或授权设置。当您可以使用 Fastify 插件时，为什么要花时间从头开始编写授权呢？通常，您会希望在 Fastify 的生态系统之外寻找能够帮助您解决某些问题或完成某些任务的软件包。然而，通过提供丰富的插件生态系统，Fastify 成为一站式解决方案，肯定会改善开发人员的体验！

关于插件的快速提示:你可以创建自己的插件来封装功能。此外，您可以将这些插件加载到您的 Fastify 应用程序对象中。默认情况下，Fastify 将首先从 Fastify 生态系统加载插件。然后，加载自定义插件。

好了，说点实际的吧！我想使用 [fastify-env](https://github.com/fastify/fastify-env) 插件，它可以帮助你加载环境变量并为每个变量设置默认值。因此，让我们将这种依赖性添加到我们的项目中:

```
npm install --save fastify-env 
```

接下来，我们可以在`index.js`文件中加载 Fastify 应用程序对象后加载依赖项。您的`index.js`文件如下所示:

```
// Require the framework and instantiate it
const app = require('fastify')({
    logger: true
})

// Use Fastify Env plugin: https://github.com/fastify/fastify-env
const fastifyEnv = require('fastify-env') // load plugin

const options = {
    confKey: 'config', // optional, default: 'config'
    schema: {
        type: 'object',
        required: ['PORT'],
        properties: {
            PORT: {
                type: 'string',
                default: 1000
            }
        }
    }
}

app
    .register(fastifyEnv, options)
    .ready((err) => {
        if (err) console.error(err)

        console.log(app.config)
        // output: { PORT: 1000 }
    })

// Declare a route
app.get('/', function (req, reply) {
    reply.send({ hello: 'world' })
})

// Register routes to handle blog posts
const blogRoutes = require('./routes/blogs')
blogRoutes.forEach((route, index) => {
    app.route(route)
})

// Run the server!
app.listen(app.config.PORT, (err, address) => {
    if (err) {
        app.log.error(err)
        process.exit(1)
    }
    app.log.info(`server listening on ${address}`)
}) 
```

注意，我们必须定义一个`options`对象来告诉 fastify-env 插件要查找哪些 env 变量，以及要设置哪些默认值。这里，我想加载一个默认值为`1000`的`PORT`变量。

默认情况下，fastify-env 插件将通过 fastify 应用程序对象提供所有环境变量，如下所示:`app.config.PORT`。为什么？fastify-env 插件将加载的配置附加到`confKey`，默认设置为`config`。但是，如果您愿意，可以将其更改为另一个键。

用`node index.js`启动项目并监控输出。您应该看到在您的终端中打印出了`PORT`变量。

其他有趣的插件可以使用吗？

1.  [fastify-auth](https://github.com/fastify/fastify-auth) :在 fastify 中运行多个 auth 函数
2.  [Fastify-bearer-auth](https://github.com/fastify/fastify-bearer-auth):Fastify 的载体认证插件
3.  [fastify-caching](https://github.com/fastify/fastify-caching) :通用服务器端缓存和 etag 支持
4.  [fastify-cors](https://github.com/fastify/fastify-cors) :支持在 fastify 应用程序中使用 cors

## 步骤 5:定义挂钩

最后，让我们定义一些钩子。从 [Fastify hooks 文档](https://www.fastify.io/docs/v3.2.x/Hooks/)中，我们可以读到以下内容。“钩子通过 fastify.addHook 方法注册，允许您在应用程序或请求/响应生命周期中监听特定事件。你必须在事件触发前注册一个钩子，否则事件就丢失了。”

确保在定义任何路线之前定义挂钩:

```
// hooks
app.addHook('onRoute', (routeOptions) => {
    console.log(`Registered route: ${routeOptions.url}`)
})

// Declare a route
app.get('/', function (req, reply) {
    reply.send({ hello: 'world' })
}) 
```

如您所见，`addHook`函数首先接受您想要监听的钩子。在我们的示例中，我们希望监听向应用程序注册的新路由。接下来，回调函数接受一个包含大量信息的`routeOptions`参数，比如路由 URL 或路由方法。

在文档中可以找到 [`onRoute`挂钩](https://www.fastify.io/docs/v3.2.x/Hooks/#onroute)的具体细节。

让我们用`node index.js`启动 API，看看哪些路由已经被注册。您的终端输出应该如下所示:

```
Registered route: /
Registered route: /api/blogs
Registered route: /api/blogs/:id
Registered route: /api/blogs
Registered route: /api/blogs/:id
Registered route: /api/blogs/:id 
```

得到相同的输出？成功！同时，这也是 Fastify 教程的结尾。让我们用一个简短的结论来结束这个项目。

## 包扎

Fastify 是一个很棒的轻量级项目，允许您利用其丰富的插件生态系统。您可以利用现有的插件，而不是从头开始创建功能。换句话说，Fastify 充当了开发者的一站式商店，无疑改善了开发者的体验。

就个人而言，我喜欢 Fastify hooks 的功能，因为你可以监听应用程序中的各种生命周期事件。

要了解有关 Fastify 的更多信息，请查看以下文档页面:

*   [如何创建自定义插件？](https://www.fastify.io/docs/master/Plugins/#create-a-plugin)
*   [如何添加 Typescript 支持？](https://www.fastify.io/docs/master/TypeScript/)
*   [如何使用 CORS 等中间件？](https://www.fastify.io/docs/master/Middleware/)

你也可以在 GitHub 上查看这个介绍的[回购。](https://github.com/michielmulders/sitepoint-fastify)

## 分享这篇文章