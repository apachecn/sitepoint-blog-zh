# Express、Koa 和 Hapi.js 的并列比较

> 原文：<https://www.sitepoint.com/express-koa-hapi/>

如果你是 Node.js 开发人员，你可能在某个时候使用过 Express.js 来创建你的应用程序或 API。Express.js 是一个非常流行的 Node.js 框架，甚至有一些其他框架建立在它的基础上，如 [Sails.js](https://sailsjs.com/) 、 [kraken.js](http://krakenjs.com/) 、 [KeystoneJS](http://keystonejs.com/) 和[许多其他的](https://expressjs.com/en/resources/frameworks.html)。然而，在这种流行中，其他一些框架也在 JavaScript 世界中获得了关注，比如 Koa 和 hapi。

在本文中，我们将研究 Express.js、Koa 和 hapi . js——它们的相似性、差异和用例。

## 背景

让我们首先分别介绍这些框架。

### 快递. js

[Express.js](https://expressjs.com/) 被描述为 Node.js 的标准服务器框架，由 [TJ Holowaychuk](https://twitter.com/tjholowaychuk) 创建，2014 年被 StrongLoop 收购，目前由 [Node.js 基金会孵化器](https://nodejs.org/en/blog/announcements/foundation-express-news/)维护。随着去年大约[1 . 7 亿次以上的下载，它无疑是目前最受欢迎的 Node.js 框架。](https://npm-stat.com/charts.html?package=express)

### 寇阿相思树

Express 的同一批人于 2013 年末在 Koa 开始开发。它被称为快递的未来。Koa 也被描述为 Express 框架的一个更加现代化、模块化和简约化的版本。

### Hapi.js

Hapi.js 是由沃尔玛实验室的团队(由 [Eran Hammer](https://twitter.com/eranhammer) 领导)在尝试了 Express 并发现它不能满足他们的要求后开发的。它最初是在 Express 之上开发的，但是随着时间的推移，它成长为一个成熟的框架。

*有趣的事实:hapi 是 Http API server 的简称。*

## 哲学

现在我们已经有了一些框架的背景知识以及它们是如何创建的，让我们基于重要的概念来比较它们，比如它们的哲学、路由等等。

*注意:所有代码示例都在 ES6 中，并使用 Express.js 的版本 4、Koa 的版本 2.4 和 hapi.js 的版本 17*

### 快递. js

Express 是一个简单的、非个性化的 web 框架。来自其 [GitHub 自述](https://github.com/expressjs/express):

> Express 的理念是为 HTTP 服务器提供小型、健壮的工具，使其成为单页面应用程序、网站、混合或公共 HTTP APIs 的优秀解决方案。

Express.js 是最小的，并且没有很多现成的特性。它不强制文件结构、ORM 或模板引擎之类的东西。

### 寇阿相思树

虽然 Express.js 很小，但 Koa 可以拥有更少的代码占用——大约 2k <abbr title="lines of code">LOC</abbr> 。它的目的是让开发者更有表现力。像 Express.js 一样，它可以通过使用现有的或定制的插件和中间件来轻松扩展。它的方法更具未来性，因为它严重依赖于相对较新的 JavaScript 特性，如[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)和[异步/等待](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)。

### Hapi.js

Hapi.js 比 Koa 和 Express.js 更注重配置，并提供了更多现成的功能。[hapi 的创始人之一 Eran Hammer](https://github.com/hueniverse) 在[的博客文章](https://hueniverse.com/hapi-a-prologue-842e166fdd08)中描述了正确构建框架的原因:

> hapi 是围绕这样的理念创建的，即配置比代码更好，业务逻辑必须与传输层隔离，以及像缓冲区和流这样的本地节点结构应该作为第一类对象来支持。

## 启动服务器

启动服务器是我们在项目中需要做的基本事情之一。让我们来看看如何在不同的框架中做到这一点。在每个示例中，我们将启动一个服务器并监听端口 3000。

### 快递. js

```
const express = require('express');
const app = express();

app.listen(3000, () => console.log('App is listening on port 3000!')); 
```

在 Express.js 中启动一个服务器就像需要`express`包一样简单，将`express` app 初始化为`app`变量并调用`app.listen()`方法，这只是对 native node . js[http . createserver()](https://nodejs.org/api/http.html#http_http_createserver_requestlistener)方法的包装。

### 寇阿相思树

在 Koa 中启动服务器与 Express.js 非常相似:

```
const Koa = require('koa');
const app = new Koa();

app.listen(3000, () => console.log('App is listening on port 3000!')); 
```

Koa 中的`app.listen()`方法也是对`http.createServer()`方法的包装。

### Hapi.js

在 hapi.js 中启动服务器与我们许多人可能习惯的 Express 完全不同:

```
const Hapi = require('hapi');

const server = Hapi.server({
    host: 'localhost',
    port: 3000
});

async function start() {
  try {
    await server.start();
  }
  catch (err) {
    console.log(err);
    process.exit(1);
  }
  console.log('Server running at:', server.info.uri);
};

start(); 
```

在上面的代码块中，首先我们需要`hapi`包，然后用`Hapi.server()`实例化一个服务器，它有一个包含主机和端口参数的配置对象参数。然后我们用异步`server.start()`函数启动服务器。

与 Express.js 和 Koa 不同，hapi 中的`server.start()`函数不是本机`http.createServer()`方法的包装器。相反，它实现自己的自定义逻辑。

上面的代码示例来自 hapi.js 网站，显示了 hapi.js 的创建者对配置和错误处理的重视。

## 按指定路线发送

路由是现代 web 应用程序的另一个关键方面。让我们在每个框架中为一个简单的 Hello World 应用程序定义一个`/hello`路由，来感受一下路由是如何为它们工作的。

### 快递. js

```
app.get('/hello', (req, res) => res.send('Hello World!')); 
```

在 Express 中创建路由就像用所需的 HTTP 方法调用`app`对象一样简单。语法是`app.METHOD(PATH, HANDLER)`，其中 PATH 是服务器上的路径，HANDLER 是路径匹配时调用的函数。

### 寇阿相思树

Koa 没有捆绑自己的路由器，所以我们必须使用路由器中间件来处理 Koa 应用程序上的路由。两个常见的路由选项是 [koa-route](https://github.com/koajs/route) 和 [koa-router](https://github.com/alexmingoia/koa-router) 。下面是一个使用 koa-route 的示例:

```
const route = require('koa-route');

app.use(route.get('/hello', ctx => {
    ctx.body = 'Hello World!';
})); 
```

我们可以立即看到，Koa 需要将每条路线定义为应用程序上的中间件。`ctx`是一个包含节点的`request`和`response`对象的上下文对象。`ctx.body`是`response`对象中的一个方法，可用于将响应体设置为`string`、`Buffer`、`Stream`、`Object`或`null`。route 方法的第二个参数可以是异步函数或生成器函数，因此减少了回调的使用。

### Hapi.js

```
server.route({
  method: 'GET',
  path:'/hello',
  handler: function (request, h) {
    return 'Hello world!';
  }
}); 
```

hapi 中的`server.route()`方法接受一个带有以下参数的配置对象:`method`、`path`和`handler`。你可以在这里看到关于 hapi [路由的文档。](https://hapijs.com/tutorials/routing)

处理函数中的`request`参数是一个包含用户请求细节的对象，而`h`参数被描述为一个响应工具包。

## 中间件

节点开发人员习惯的一个主要概念是使用中间件。**中间件功能**是介于请求和响应之间的功能。它们可以访问`request`和`response`对象，并可以在它们被处理后运行下一个中间件。让我们通过实现一个简单的函数来记录向服务器发出请求的时间，看看它们在不同的框架中是如何定义的。

### 快递. js

```
app.use((req, res, next) => {
  console.log(`Time: ${Date.now()}`);
  next();
}) 
```

在 Express.js 中注册中间件就像使用`app.use()`函数将中间件绑定到 [app 对象](http://expressjs.com/en/4x/api.html#app)一样简单。你可以在 Express.js [这里](http://expressjs.com/en/guide/using-middleware.html)阅读更多关于中间件的内容。

#### 寇阿相思树

```
app.use(async (ctx, next) => {
  console.log(`Time: ${Date.now()}`);
  await next();
}); 
```

Koa 中的中间件注册类似于 Express.js。主要区别在于，使用了[上下文对象](http://koajs.com/#cascading) ( `ctx`)来代替 Express.js 中的`request`和`response`对象，Koa 采用了现代的 async/await 范例来定义中间件功能。

### Hapi.js

```
server.ext('onRequest', (request, h) => {
  console.log(`Time: ${Date.now()}`);
  return h.continue;
}); 
```

在 hapi.js 中，在[请求生命周期](https://hapijs.com/api#request-lifecycle)中有一些扩展点。`server.ext()`方法注册了一个扩展函数，在请求生命周期中的某个时刻调用这个函数。你可以在这里了解更多关于[的信息。我们利用上面例子中的`onRequest`扩展点来注册一个中间件(或扩展)函数。](https://hapijs.com/api#-serverextevents)

## 使用

从上面我们看到的比较和代码示例来看，很明显 Express 和 Koa 是最相似的，hapi.js 是偏离 Node.js 开发人员习惯的规范的框架。因此，当试图构建一个快速简单的应用程序时，hapi.js 可能不是最佳选择，因为它需要一点时间来适应。

在我看来，在构建中小型应用程序时，Express 仍然是一个很好的选择。对于非常大的应用程序来说，管理起来可能会变得有点复杂，因为它不具备 hapi.js 内置的模块性，不支持[定制插件](https://hapijs.com/tutorials/plugins)及其独特的[路由方法](https://hapijs.com/tutorials/routing)。然而，最近有一些关于 Express.js [的未来的猜测，因为 TJ 宣布他不再为它工作](https://medium.com/@tjholowaychuk/farewell-node-js-4ba9e7f3e52b)以及[更新发货速度的降低](https://github.com/expressjs/express/issues/2964)。但是它很稳定，不会很快消失。它还有一个庞大的开发者社区，为它构建各种扩展和插件。

像 Express.js 一样，Koa 非常适合许多简单的 Node.js 项目。它只包含最少的内容(它没有内置的中间件),并鼓励开发人员通过构建或利用可用的外部中间件来添加他们需要的内容。它使用了现代的 JavaScript 生成器函数和 async/await，这使得它在方法上有点超前。它的[中间件级联模式](http://koajs.com/#cascading)也很棒，因为它使得在应用程序中实现和理解中间件流程变得非常容易。如果您还没有准备好接受新的闪亮的东西，比如生成器函数，或者如果您不愿意花一些时间来构建您需要的所有中间件，那么 Koa 可能不是您的最佳选择。社区对 Koa 的支持正在快速增长，因为它已经为路由、日志等常见任务构建了大量的外部中间件(有些是由核心 Koa 团队构建的)。

如果您和您的团队喜欢花更多的时间进行配置，而不是实际编写特性代码，那么 Hapi.js 是明确的选择。它是模块化的，适用于大型团队的大型应用程序。它鼓励微服务架构，因为你的应用程序的各个部分可以作为插件构建，并在启动之前在你的服务器中注册。Hapi.js 得到了 Auth0 和 Lob 等大公司的支持，因此它有着非常好的未来，并且不会很快消失。它也受到一些知名人士的信任，正如在他们的社区页面上看到的那样。

Hapi.js 比 Koa 和 Express.js 有更多现成的特性，比如支持认证、缓存、日志、验证等等，这让它看起来更像一个成熟的框架。你可以查看他们的[教程页面](https://hapijs.com/tutorials/)来感受一下他们提供的功能。基于 hapi.js 构建的开源项目和插件还不多，所以如果开发人员打算扩展它的核心功能，他们可能需要做大量的工作。

## 结论

在开始新项目时，这三个框架都是很好的选择，但最终你的选择将基于项目需求、你的团队成员和你所寻求的灵活性水平。

## 分享这篇文章