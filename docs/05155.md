# 提高 Node.js Web 应用速度的 10 个技巧

> 原文：<https://www.sitepoint.com/10-tips-make-node-js-web-app-faster/>

由于其事件驱动和异步的特性，Node.js 已经非常快了。但是，在现代网络中，仅仅快是不够的。如果你计划使用 Node.js 开发下一个 web 应用程序，你必须采取一切可能的措施来确保你的应用程序比平常更快。这篇文章介绍了 10 个技巧，它们可以极大地提高基于节点的 web 应用程序的速度。所以，让我们一个一个来看。

## 1.并行运行

在构建 web 应用程序时，有时您需要进行多个内部 API 调用来获取各种数据。例如，考虑一个用户仪表板。在显示仪表板时，您可以执行以下假设调用:

*   用户资料-`getUserProfile()`。
*   最近的活动——`getRecentActivity()`。
*   订阅—`getSubscriptions()`。
*   通知-`getNotifications()`。

为了检索这些细节，您可以为每个功能创建一个单独的中间件，并附加到仪表板路由。但是这种方法的问题是一个函数必须等待前一个函数完成。另一种选择是并行执行这些调用。

众所周知，由于 Node.js 的异步特性，它在并行运行多个函数时非常高效。我们应该利用这一点。由于我上面提到的功能并不相互依赖，我们可以并行运行它们。这将减少中间件的数量，大大提高速度。

为了实现并行化，我们可以使用 async.js，这是一个帮助驯服异步 JavaScript 的节点模块。下面的代码片段展示了如何使用 async.js 并行运行不同的函数:

```
function runInParallel() {
  async.parallel([
    getUserProfile,
    getRecentActivity,
    getSubscriptions,
    getNotifications
  ], function(err, results) {
    //This callback runs when all the functions complete
  });
}
```

如果你想了解更多关于 async.js 的知识，一定要查看该项目的 [GitHub](https://github.com/caolan/async) 页面。

## 2.异步运行

根据设计，Node.js 是单线程的。由于这个事实，同步代码可能会锁定整个应用程序。例如，大多数文件系统 API 都有它们的同步对应物。以下代码片段显示了如何同步和异步完成文件读取操作:

```
// Asynchronous
fs.readFile('file.txt', function(err, buffer) {
  var content = buffer.toString();
});

// Synchronous
var content = fs.readFileSync('file.txt').toString();
```

但是如果您执行长时间运行和阻塞操作，您的主线程将被阻塞，直到操作完成。这大大降低了你的应用程序的性能。因此，确保在代码中始终使用异步 API，至少在性能关键部分。选择第三方模块时也要小心。即使您采取了所有预防措施来避免同步代码，外部库也可能会进行同步调用，从而影响应用程序的性能。

## 3.使用缓存

如果您正在获取一些不经常更改的数据，您可以缓存它以提高性能。例如，以下面的代码片段为例，它获取最新的帖子并显示在视图上:

```
var router = express.Router();

router.route('/latestPosts').get(function(req, res) {
  Post.getLatest(function(err, posts) {
    if (err) {
      throw err;
    }

    res.render('posts', { posts: posts });
  });
});
```

如果发布博文不太频繁，可以缓存`posts`数组，间隔一段时间后清空缓存。例如，我们可以使用`redis`模块来实现这一点。为此，您需要在服务器上安装 Redis。然后，您可以使用一个名为 [`node_redis`](https://github.com/mranney/node_redis) 的客户端来存储键/值对。下面的代码片段显示了我们如何缓存帖子:

```
var redis = require('redis'),
    client = redis.createClient(null, null, { detect_buffers: true }),
    router = express.Router();

router.route('/latestPosts').get(function(req,res){
  client.get('posts', function (err, posts) {
    if (posts) {
      return res.render('posts', { posts: JSON.parse(posts) });
    }

    Post.getLatest(function(err, posts) {
      if (err) {
        throw err;
      }

      client.set('posts', JSON.stringify(posts));    
      res.render('posts', { posts: posts });
    });
  });
});
```

因此，首先我们检查帖子是否存在于 Redis 缓存中。如果是这样，我们从缓存中提交`posts`数组。否则，我们从数据库中检索内容，然后缓存它。此外，经过一段时间后，我们可以清除 Redis 缓存，以便获取新内容。

## 4.使用 gzip 压缩

打开 gzip 压缩会极大地影响 webapp 的性能。当 gzip 兼容的浏览器请求某些资源时，服务器可以在将响应发送给浏览器之前对其进行压缩。如果你不使用 gzip 来压缩你的静态资源，浏览器可能需要更长的时间来获取它。

在 Express 应用程序中，您可以使用内置的`express.static()`中间件来提供静态内容。此外，您可以使用 [`compression`](https://github.com/expressjs/compression) 中间件来压缩和提供静态内容。下面是一个片段，展示了如何做到这一点:

```
var compression = require('compression');

app.use(compression()); //use compression 
app.use(express.static(path.join(__dirname, 'public')));
```

## 5.尽可能使用客户端渲染

随着许多强大的客户端 MVC/MVVM 框架的出现，如 AngularJS、Ember、Meteor 等。，创建单页应用程序变得非常容易。基本上，不用在服务器端呈现，您只需公开向客户端发送 JSON 响应的 API。在客户端，您可以使用一个框架来使用 JSON 并显示在 UI 上。从服务器发送 JSON 可以节省带宽，从而提高速度，因为不需要在每个请求中都发送布局标记。相反，您只需发送普通的 JSON，然后在客户端呈现出来。

看看我的这个教程，它描述了如何用 Express 4 公开 RESTful APIs。我还写了[另一个教程](https://www.sitepoint.com/creating-crud-app-minutes-angulars-resource/)，展示了如何使用 AngularJS 与这些 API 交互。

## 6.不要在会话中存储太多

在典型的 Express web 应用程序中，会话数据默认存储在内存中。当您在会话中存储太多数据时，会显著增加服务器的开销。因此，您可以切换到其他类型的存储来保存会话数据，或者尽量减少会话中存储的数据量。

例如，当用户登录您的应用程序时，您可以只在会话中存储他们的`id`,而不是存储整个对象。随后，对于每个请求，您可以从`id`中检索对象。您可能还想使用 MongoDB 或 Redis 来存储会话数据。

## 7.优化您的查询

假设你有一个在主页上显示最新文章的博客应用程序。您可以编写类似这样的代码来使用 Mongoose 获取数据:

```
Post.find().limit(10).exec(function(err, posts) {
  //send posts to client
});
```

但问题是 Mongoose 中的`find()`函数获取一个对象的所有字段，而`Post`对象中可能有几个在主页上不需要的字段。例如，`comments`就是这样一个字段，它保存了一个特定帖子的评论数组。因为我们没有显示注释，所以我们可以在获取时将其排除。这样肯定会提高速度。我们可以像这样优化上面的查询:

```
Post.find().limit(10).exclude('comments').exec(function(err, posts) {
  //send posts to client
});
```

## 8.使用标准 V8 功能

并非所有浏览器都支持对集合的不同操作，如`map`、`reduce`、`forEach`。为了克服浏览器兼容性问题，我们在前端使用了一些客户端库。但是有了 Node.js，你就确切知道 Google 的 V8 JavaScript 引擎支持哪些操作了。因此，您可以直接使用这些内置函数在服务器端操作集合。

## 9.在节点前使用 nginx

Nginx 是一个小型轻量级的 web 服务器，可以用来减轻 Node.js 服务器的负载。您可以配置 nginx 来提供静态内容，而不是从 Node 提供静态文件。您还可以设置 nginx 来使用 gzip 压缩响应，这样总的响应大小就会很小。因此，如果你正在运行一个生产应用程序，你可能想使用 nginx 来提高速度。

## 10.缩小并连接 JavaScript

最后，通过将多个 JS 文件缩小并连接成一个文件，可以极大地提高 web 应用程序的速度。当浏览器遇到一个`<script>`元素时，页面呈现被阻塞，直到获取并执行脚本(除非设置了`async`属性)。例如，如果您的页面包含五个 JavaScript 文件，浏览器将发出五个单独的 HTTP 请求来获取这些文件。通过将这五个文件缩小并连接成一个文件，可以极大地提高整体性能。这同样适用于 CSS 文件。您可以使用 Grunt/Gulp 等构建工具来缩小和连接您的资源文件。

## 结论

这 10 个技巧肯定能提高你的网络应用速度。但是，我知道还有进一步改进和优化的空间。如果你有任何这样的提高性能的建议，请在评论中告诉我们。

感谢阅读！

## 分享这篇文章