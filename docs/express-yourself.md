# 表达自己的思想

> 原文：<https://www.sitepoint.com/express-yourself/>

Express 是一个 Node 的 web 框架，灵感来自于 Ruby 的 Sinatra 框架 T2 T3。它拥有大量的功能，高性能，并带有一个方便的可执行文件来快速创建应用程序。在本文中，我们将了解如何使用 express，当您创建一个新的 express 网站时会得到什么，最后添加一个路线和视图来显示 Twitter 的搜索结果。

## 安装 Express

Express 可以通过 [npm(节点包管理器)](http://npmjs.org/)轻松安装:

```
 $> npm install express -g 
```

末端的`-g`开关使 express 全球可用。这意味着我们可以从任何地方运行 express 可执行文件来快速创建新的应用程序。express 可执行文件通常被称为 express:

```
 $> type express
express is hashed (/usr/local/bin/express) 
```

如果您单独键入命令，express 将在当前目录中创建新的应用程序。如果当前目录不为空，系统会提示您并询问您是否要继续:

```
 $> express
destination is not empty, continue? 
```

如果要继续，请输入“y ”,或者输入“n”中止。你并不局限于在当前目录下创建新的应用程序，你只需要将 express 路径传递到你想要创建新应用程序的地方。您也可以向它传递其他选项:

*   `-s`启用会话
*   `-t`或`--template <engine>`来指定您的模板引擎(express 默认使用 jade)
*   `-c`或`--css`添加手写笔 CSS 支持(默认为普通 CSS)
*   `-v`或`--version`输出您已安装的 express 的当前版本
*   `-h`或`--help`输出该帮助并退出

支持的模板引擎列表在 [express guide](http://expressjs.com/guide.html#template-engines) 和 [express wiki](https://github.com/visionmedia/express/wiki) 上。在本文中，我们将使用 [EJS](https://github.com/visionmedia/ejs) 。让我们创建一个名为`myapp`的新站点:

```
 $> express -t ejs myapp

create : myapp
create : myapp/package.json
create : myapp/app.js
create : myapp/public
create : myapp/routes
create : myapp/routes/index.js
create : myapp/views
create : myapp/views/layout.ejs
create : myapp/views/index.ejs
create : myapp/public/javascripts
create : myapp/public/images
create : myapp/public/stylesheets
create : myapp/public/stylesheets/style.css

dont forget to install dependencies:
$> cd myapp && npm install 
```

express 在 myapp 文件夹中为我们创建了一个新站点。我们得到了:一个`package.json`文件，一个用于应用程序代码的`app.js`文件，一个用于路由的路由目录，一个用于视图模板的视图目录，以及一个用于 CSS、图像和客户端 JavaScript 的公共目录。Express 还提醒我们安装依赖项，并给出了这样做的命令。在我们忘记之前，我们最好现在就输入:

```
 $> cd myapp && npm install
npm http GET https://registry.npmjs.org/express/2.5.5
npm http GET https://registry.npmjs.org/ejs
npm http 304 https://registry.npmjs.org/ejs
npm http 304 https://registry.npmjs.org/express/2.5.5
npm http GET https://registry.npmjs.org/mime
npm http GET https://registry.npmjs.org/qs
npm http GET https://registry.npmjs.org/mkdirp/0.0.7
npm http GET https://registry.npmjs.org/connect
npm http 304 https://registry.npmjs.org/mime
npm http 304 https://registry.npmjs.org/qs
npm http 304 https://registry.npmjs.org/mkdirp/0.0.7
npm http 304 https://registry.npmjs.org/connect
npm http GET https://registry.npmjs.org/formidable
npm http 304 https://registry.npmjs.org/formidable
ejs@0.6.1 ./node_modules/ejs 
express@2.5.5 ./node_modules/express 
├── mkdirp@0.0.7
├── qs@0.4.2
├── mime@1.2.5
└── connect@1.8.5 
```

您应该会看到类似的内容，尽管具体的输出可能会有所不同，这取决于哪些版本的依赖项可以通过 npm 获得，以及您已经安装了哪些依赖项。如果你现在输入`node app.js`并浏览到 [http://localhost:3000](http://localhost:3000/) ，你应该会看到*欢迎快递*页面。让我们看看 express 生成的代码，以及欢迎页面是如何使用它的。

## app.js

`app.js`是您配置站点和定义路线的地方。`app`变量是我们的 express 服务器，我们调用它的`configure`函数来告诉 express 使用哪个视图引擎，在哪里可以找到静态资源，以及如何处理错误。查看默认的 app.js 代码，您会看到`app.configure`被调用了三次:

*   第一次在没有名称参数的情况下调用*——这将把设置应用到每个环境*
*   第二次调用时将`development`作为第一个参数——这些`errorHandler`设置将只应用于开发环境
*   第三次使用第一个参数`production`调用它——生产环境中的`errorHandler`将关闭异常转储和堆栈跟踪

现在看看`app.js`中的最后两行:

```
 app.listen(3000);
console.log("Express server listening on port %d in %s mode", app.address().port, app.settings.env); 
```

这告诉 express 在哪个端口上监听连接，然后向控制台记录一条消息，确认它正在监听的端口以及当前环境。

```
 $ node app.js 
Express server listening on port 3000 in development mode 
```

默认情况下`app.settings.env`被设置为`development`。要将其更改为另一个环境，例如生产环境，请在对`console.log`的调用上方插入以下代码行:

```
 app.settings.env = 'production'; 
```

重启应用程序`ctrl+c`，然后再次输入`node app.js`，模式应该已经变为生产:

```
 $ node app.js 
Express server listening on port 3000 in production mode 
```

## 按指定路线发送

Express 公开了 6 个 HTTP 动词作为定义路由的 API，并允许您通过命名参数、通配符、正则表达式以及这三者的组合来捕获参数。捕获的值可通过`req.params`访问:

```
 // using a named parameter
app.get('/foo/:id', function(req, res) {
	var fooId = req.params.id;
	...
});

// using regular expressions
app.get(/^/foo/(d+)/, function(req, res) {
	var fooId = req.params[1];
	...
});

// using a combination of the two
app.get('/foo/:id([0-9]+)', function(req, res) {
	var fooId = req.params.id;
	...
}) 
```

以上三条路线展示了从 URL 获取`fooId`的不同方式。

在`app.js`中，您将看到为欢迎页面定义的路线。它处理对“/”的 GET 请求:当接收到对该路径的请求时，回调函数`routes.index`被触发。

。

`routes.index`功能在`routes/index.js`中定义。`routes/index.js`是一个模块，它导出函数用作路由处理器。如果你打开它，你会看到组成`routes.index`函数的代码:

```
 exports.index = function(req, res){
  res.render('index', { title: 'Express' })
}; 
```

这告诉 express 呈现索引视图。作为第二个参数传递给`render`的对象是视图可用的数据。

## 视图

当我们创建`myapp`时，我们告诉 express 使用 EJS 模板引擎。如果您查看 views 文件夹，您会看到 express 为我们创建了几个文件:一个 layout.ejs 文件和一个 index.ejs 文件。layout.ejs 是页面布局 HTML 的主体，index.ejs 包含特定于页面的 HTML。如果您打开 layout.ejs，您会看到类似这样的内容:

```
 <!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel="stylesheet" href="/stylesheets/style.css" />
  </head>
  <body>
    <%- body %>
  </body>
</html> 
```

除了常规的 HTML，我们传递给视图的对象的`title`在< title >标签中得到输出，来自特定于页面的模板(比如 index.ejs)的内容通过`<%- body %>`行得到包含。让我们打开`index.ejs`看看里面有什么:

```
 <h1><%= title %></h1>
<p>Welcome to <%= title %></p> 
```

我们再次从传递给视图的对象中输出`title`，首先在<h1&t；标记，然后在< p >标记。如果您在`routes/index.js`中更改分配给`title`的值并重启应用程序，当您刷新浏览器时，您应该会看到该值发生变化。

## 添加新路线

让我们添加一个新的路线和视图，显示最近提到 Sitepoint 的 10 条推文。我们需要添加一个到`app.js`的路由，一个路由的回调函数，以及一个显示 tweets 的视图:

1.  打开`app.js`并为 tweets 页面添加一条路线:

    ```
     app.get('/twitter', routes.twitter); 
    ```

2.  接下来，打开`routes/index.js`并在顶部添加这一行来导入 http 模块:

    ```
     var http = require('http'); 
    ```

3.  停留在`routes/index.js`并添加以下功能:

    ```
     /*
     * GET twitter search results
     */
    exports.twitter = function(req, res) {
      var options = {
        host: 'search.twitter.com',
        port: 80,
        path: '/search.json?q=sitepoint&rpp=10'
      };

      http.get(options, function(response) {
        var tweets = '';
        response.on('data', function(data) {
          tweets += data;
        }).on('end', function() {
          var tmp = JSON.parse(tweets),
            topTen = tmp.results;
          res.render('twitter', { title: 'Latest from Twitter', tweets: topTen });
        });
      }).on('error', function(e) {
        res.writeHead(500);
        res.write('Error: ' + e);
      }); 
    } 
    ```

    这里我们使用 [`http.get`](http://nodejs.org/docs/latest/api/http.html#http.get) 向 [Twitter 搜索 API](https://dev.twitter.com/docs/api/1/get/search) 发出 HTTP GET 请求。我们向`http.get`传递`options`对象，告诉它从哪里获取数据。然后我们定义几个回调函数:`on('data', ...)`在每次接收到一些数据时将`data`附加到`tweets`；当接收到所有数据时，调用`on('end', ...)`,并将 tweets 发送到 twitter 视图。

4.  最后，在 views 文件夹中创建一个`twitter.ejs`文件。打开文件并添加以下内容:

    ```
     <h1><%= title %></h1>
    <% if (tweets.length) { %>
      <ul>
        <% tweets.forEach(function(t) { %>
          <li>
            <h2>
              <a href="http://twitter.com/<%= t.from_user %>" rel="external">
                <%= t.from_user_name %>
                <span><%= t.from_user %></span>
              </a>
            </h2>
            <blockquote>
              <p><%= t.text %></p>
            </blockquote>
            <a href="http://twitter.com/#!/<%= t.from_user %>/status/<%= t.id %>" rel="external">Open</a>
          </li>
        <% }); %>
      </ul>
    <% } // endif %> 
    ```

    显示标题后，视图检查我们是否有一些 tweets 要显示。如果我们这样做了，`forEach`遍历每条推文，使用回调函数显示推文的文本，以及推文来自谁。

重启应用程序，然后浏览到[http://localhost:3000/Twitter](http://localhost:3000/twitter)，如果一切顺利，你会看到最近提到 Sitepoint 的十条推文列表。

这就是使用快速 web 框架在 Node 中创建网站的快速介绍。我们研究了当您创建一个新的 express 站点时，express 为您提供了什么，快速查看了它如何使用路由和视图，并添加了一个新的路由和视图来显示来自 Twitter 的搜索结果。express 还有很多功能，还有很多我们没有涉及到的。如果你有兴趣了解更多，那就去[http://expressjs.com/guide.html](http://expressjs.com/guide.html)，看看[快速维基](https://github.com/visionmedia/express/wiki)。

## 分享这篇文章