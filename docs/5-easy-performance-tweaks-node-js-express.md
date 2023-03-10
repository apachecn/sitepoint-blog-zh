# Node.js Express 的 5 个简单性能调整

> 原文：<https://www.sitepoint.com/5-easy-performance-tweaks-node-js-express/>

Node.js 革新了 web 开发，而 [Express framework](http://expressjs.com/) 必须分享大部分功劳。Express 可能不是最快或最先进的服务器选项，但几乎肯定是最常用的，每月下载量超过 300 万次。

如果你什么都不做，Node.js 和 Express 会运行得非常快。但是，有一些简单的方法可以让 Express 4.x 运行得更快…

## 1.切换到生产模式

Express 可以在几种模式下运行。默认情况下，它假定开发模式提供异常堆栈跟踪和其他日志记录任务。还有一种将消息记录到控制台的调试模式，例如

```
DEBUG=express:* node ./app.js
```

在实时服务器上，通过切换到生产模式，可以显著提高性能。这是通过将`NODE_ENV`环境变量设置为`production`来实现的。可以在启动 Express 应用程序之前，在 Windows 的当前会话中进行设置:

```
set NODE_ENV=production
```

或 Mac/Linux:

```
export NODE_ENV=production
```

Linux 用户也可以在启动时在一行中设置`NODE_ENV`:

```
NODE_ENV=production node ./app.js
```

理想情况下，您应该通过向您的`~/.bash_profile`或适当的启动脚本添加`export NODE_ENV=production`来配置您的环境。

## 2.启用 GZIP

Express 4.x 提供了可以通过中间件增强的最小功能。一个不太明显的缺失特性是 GZIP 压缩，它缩小了 HTTP 有效负载，因此可以在收到时被浏览器扩展。要添加 GZIP，使用 npm 安装[压缩模块](https://www.npmjs.com/package/compression):

```
npm install compression --save
```

*(根据你的设置，你可能需要在 Mac/Linux 上使用`sudo`——有各种[方法](https://docs.npmjs.com/getting-started/fixing-npm-permissions)来解决这个问题。)*

在主应用程序启动文件中，包含压缩模块:

```
var compression = require('compression');
```

然后将它作为第一个中间件函数挂载(优先于其他 Express `.use`方法):

```
// GZIP all assets
app.use(compression());
```

## 3.移除不必要的中间件

如果您使用 [Express Generator](http://expressjs.com/starter/generator.html) 或类似的流程生成了一个预构建的应用程序，那么移除您没有使用的任何中间件模块。

例如，如果你没有使用 cookie，你可以移除 [cookie 解析器](https://www.npmjs.com/package/cookie-parser)模块，或者使用[req . cookie](http://expressjs.com/4x/api.html#req.cookies)和 [res.cookie](http://expressjs.com/4x/api.html#res.cookie) 实现一个更简单的替代方案。

有可能删除某些进程，例如在生产模式下调试输出

```
if (app.get('env') !== 'production') {
  // debugging middleware in development only
  app.use(debuggerModule);
}
```

也就是说，保留[服务图标](https://github.com/expressjs/serve-favicon)可能是最好的。浏览器经常请求 favicon.ico 文件，该模块改进了缓存。

## 4.考虑一个更简单的模板引擎

Jade 是许多快速安装的默认模板引擎，这是一个很好的选择。然而，如果你乐于编写自己的 HTML，并且只需要基本的插值和评估，那么考虑一个轻量级的替代方案，比如可以使用 [express-dot-engine](https://www.npmjs.com/package/express-dot-engine) 模块作为 Express 中间件添加的 [doT](http://olado.github.io/doT/) 。

或者，您可以绕过模板引擎获得更简单的响应，例如

```
app.get('/hello', function (req, res) {
  res.write('Hello!');
  res.end();
});
```

无论使用哪种模板引擎，都要确保视图缓存处于启用状态:

```
app.set('view cache', true);
```

## 5.记得缓存！

与大多数基于服务器的应用程序不同，Node.js 应用程序永久运行。因此，您可以设置数据库连接对象等常用变量一次，并在每个用户的每个请求中重用它们。

这个概念可以根据需要进行扩展。一般来说，您可以缓存的高使用率项目越多，性能就越好。例如，假设您的基于 Express 的 CMS 应用程序在主页上显示了您的五篇最新文章的链接。该列表可以生成并存储在一个变量中，只有在发布新文章时才会更新。

## 额外快速提示

想再来几个吗？…

*   考虑[使用 nginx 服务静态内容](https://www.sitepoint.com/configuring-nginx-ssl-node-js/)，减轻 Node.js 和 Express 的负荷。
*   使用构建工具如 [Gulp](https://www.sitepoint.com/introduction-gulp-js/) 来缩小和连接你的 HTML 模板、CSS 和 JavaScript 文件。
*   限制会话变量的使用。用户会话数据通常存储在内存中，随着使用量的增加，这会导致性能问题。考虑只使用 ID，然后在请求期间从数据库中获取用户信息。
*   将 HTTP 套接字的最大数量从五个增加到一个更大的数字，例如

    ```
     // use http/https as necessary
      http.globalAgent.maxSockets = 50; 
    ```

*   尽可能并行调用阻塞操作序列，而不是按顺序调用。
*   除了应用程序初始化之外，避免在任何情况下使用同步阻塞调用。
*   请始终注意，为一个用户运行的代码会阻塞为其他所有用户运行的代码。

对于 Node.js 或 Express，您是否有进一步的性能提示？我很高兴在评论中听到他们。

## 分享这篇文章