# 构建一个 Node.js 支持的聊天室 Web 应用程序:Express 和 Azure

> 原文：<https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-expess-azure/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

这个 [Node.js](http://nodejs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) 教程系列将帮助您构建一个完全部署在云中的 Node.js 支持的实时聊天室 web 应用程序。在这个系列中，你将学习如何在你的 Windows 机器上设置 [Node.js](http://nodejs.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) (或者如果你在 Mac 上只学习概念)，如何用 [Express](http://expressjs.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 开发 web 前端，如何将 node express 应用部署到 [Azure](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=12779-DEV-sitepoint-link) ，如何使用 [Socket。IO](http://socket.io/?WT.mc_id=12833-DEV-sitepoint-othercontent) 添加实时层，以及如何一起部署。这是一篇初级到中级水平的文章——你应该了解 HTML5 和 JavaScript。

> [第一部分——node . js 简介](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-getting-started/)
> 
> **第二部分——欢迎使用 Node.js 和 Azure 表达**
> 
> 第 3 部分——用 Node.js、Mongo 和 Socket 构建后端。IO
> 
> [第 4 部分——用 Bootstrap 构建聊天室用户界面](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-chatroom-ui-bootstrap/)
> 
> [第 5 部分——用网络套接字连接聊天室](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-connecting-websockets)
> 
> [第六部分——压轴和调试远程 Node.js 应用](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-debugging-remotely/)

## 第 2 部分:欢迎使用 Node.js 和 Azure 进行表达

欢迎来到 Node.js 实用教程系列的第 2 部分:构建基于 Node.js 的聊天室 web 应用程序。

在这一期中，我将向您展示如何启动一个新的基于 [Express](http://expressjs.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 的节点项目，并将其部署到 Azure。

## 什么是快递？

Express 是一个最小的、开源的、灵活的 Node.js web 应用程序框架，旨在使开发网站、web 应用程序和 API 更加容易。

## 为什么用快递？

Express 通过路由支持帮助您响应 HTTP 请求，以便您可以编写对特定 URL 的响应。Express 支持多个模板引擎来简化 HTTP 响应的生成。

您需要确保 Node.js 已正确安装并准备就绪。参见本教程系列第 1 部分:[node . js 简介](http://blogs.msdn.com/b/cdndevs/archive/2014/09/04/node-js-tutorial-series-a-chatroom-for-all-part-1-introduction-to-node.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)。

## 我们开始吧

用 Visual Studio 启动一个新的 Node.js 项目相当简单。

1.  启动 Visual Studio。在文件菜单上，点击**新建**，然后点击**项目**。

![Getting Started](img/f5c7eb1d2e24b6c5b2b0eb7c49f7b73b.png)

3.  你会想去左边的**已安装的** > **模板** > **JavaScript** 菜单项，选择右边的**基本 Windows Azure Express 应用**。为您的项目选择一个位置和名称，然后单击**确定**。

![New Project Page](img/3e721c80a94eba610c5a77f047b08413.png)

5.  一条消息将通知您需要使用 npm 软件包管理器安装在`package.json`中定义的依赖项。请看这里对 npm [的解释。](http://dailyjs.com/2012/05/03/windows-and-node-1/?WT.mc_id=12833-DEV-sitepoint-othercontent)

![npm Download Window](img/78f6101b2ee0e638b47c4ebec363e914.png)

7.  将生成一个项目，其中包含一个名为`app.js`的文件。我们将从那里开始。

### app.js 解释

```
//
/**
 * Module dependencies.
 */

var express = require('express');
var routes = require('./routes');
var user = require('./routes/user');
var http = require('http');
var path = require('path');

var app = express();

// all environments
app.set('port', process.env.PORT || 3000);
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.json());
app.use(express.urlencoded());
app.use(express.methodOverride());
app.use(app.router);
app.use(require('stylus').middleware(path.join(__dirname, 'public')));
app.use(express.static(path.join(__dirname, 'public')));

// development only
if ('development' == app.get('env')) {
  app.use(express.errorHandler());
}

app.get('/', routes.index);
app.get('/users', user.list);

http.createServer(app).listen(app.get('port'), function(){
  console.log('Express server listening on port ' + app.get('port'));
});
```

#### 第 6 到 10 行

第 6 到 10 行加载各种模块，包括 express、http 和 path。有趣的是，我们还在 routes 文件夹中加载了一个名为 routes 的模块(稍后会解释)和一个名为 user 的模块。

#### 第 12 行

在这一行，我们调用了函数`express()`，它将创建我们的应用程序。当我们决定创建一个 HTTP 服务器时，将使用这个应用程序，它将是包含我们的 web 应用程序的所有属性以及在请求中接收的 URL 和处理其响应的函数之间的映射的对象。

#### 第 15 到 17 行

在这几行中，我们设置了各种配置参数，比如服务器将在哪个端口上运行(第 15 行)以及模板 HTML 文件将在哪个目录中找到(第 16 行)。在第 17 行，我们指定了我们想要使用的模板引擎，在本例中是 [Jade](http://jade-lang.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 。Jade 是一个流行的模板引擎，它使得编写 HTML 变得非常容易，并且没有额外的尖括号(< >)的语法要求。通过用下面的代码替换第 17 行，您可以将模板引擎更改为简单地返回 HTML，而不做任何进一步的操作:

```
app.set('view engine', 'html');
```

#### 第 18 到 23 行

在这些行中，我们设置了各种配置参数。您可以通过查看 [API 文档](http://expressjs.com/4x/api.html#express?WT.mc_id=12833-DEV-sitepoint-othercontent)找到每个参数的含义。本教程不需要解释这些配置参数。

#### 第 24 和 25 行

这几行很有趣，因为我们在这里指定中间件来处理 [Stylus](http://learnboost.github.io/stylus/?WT.mc_id=12833-DEV-sitepoint-othercontent) CSS 表和 HTML。中间件是在接收请求和返回响应之间自动插入到函数调用中的一个层。在这种情况下，我们要求 Express 为所有请求运行手写笔中间件和静态中间件，其中 URL 指定了我们项目的公共文件夹内的路径。我们在这个服务器上使用 CSS 和 JavaScript，而不是对那个 URL 执行请求功能。

#### 第 27 到 30 行

在这些行中，如果环境被设置为开发而不是生产，我们指定 Express 来处理错误。你不用担心这些台词。

#### 第 32、33 行

在这几行中，我们最终将 HTTP 请求中的 URL 路径映射到处理响应的特定函数。我们将很快回到这个话题。

#### 第 35 到 38 行

在这几行中，我们创建了一个 HTTP 服务器，并指定了端口，以及一个回调函数，表示服务器已经启动。

### 按指定路线发送

路由和如何正确地做路由是一个有争议的话题，没有正确的答案。有许多模块实现了 Express 和 Node.js 的路由，每个模块都有不同的风格和结构。我们将坚持使用 Express 打包的路由引擎。在`app.js`中，我们已经指定了路由引擎，并从路由目录中导入了路由模块。我们在第 32-33 行添加了路线。换句话说，我们将浏览器中的 URL 映射到服务器上响应请求的函数。那些将处理请求的函数在 routes 目录中。我们来看看`index.js`。

```
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

虽然只有三行，但是这三行做了大量的工作。第 1 行向 exports 变量添加了一个名为 index 的函数。每次加载一个模块时，Node.js 都会创建一个 exports 变量，以允许您将函数和变量传递给其他模块，在本例中就是`app.js`模块。

函数索引采用两个参数，`req`和`res`。如果您还记得第 1 部分中的[，参数`req`保存了收到的请求，参数`res`保存了一个变量，您可以在其中写入您的响应。在这种情况下，我们在接受两个参数的响应变量中执行 render 函数。第一个是指定要使用的视图的参数(视图是视图目录中的一个文件),文件的扩展名不是必需的，所以`index`将生成`index.jade`。第二个参数是一个包含可以插入到 jade 模板中的数据的对象。](http://blogs.msdn.com/b/cdndevs/archive/2014/09/04/node-js-tutorial-series-a-chatroom-for-all-part-1-introduction-to-node.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)

### 索引模板

模板是一种完全不同的语言，在本教程中不会解释。整个教程系列都需要 HTML 知识，在这种情况下，您会注意到 jade 模板语言几乎总是直接映射到 HTML。

```
extends layout

block content
  h1= title
  p Welcome to #{title}
```

除了关键字`block`和`extends`之外，其他关键字的含义与 HTML 中的完全相同。这个模板将被转换成下面的 HTML。

```
<!DOCTYPE html>
<html>
<head>
    <title>Express</title>
    <link rel="stylesheet" href="/stylesheets/style.css">
</head>
<body>
    <h1>Express</h1>
    <p>Welcome to Express</p>
</body>
</html>
```

您会注意到生成的`h1`标签包含我们之前在渲染函数中传递的 title 值。您还会注意到它被直接插入到了`p`标签中，与文本对齐。毫无疑问，您还会注意到生成的整个 HTML 包含了 Jade 中没有映射的内容。这就是`extends`关键词的用武之地。在这种情况下，我们选择扩展`layout.jade`文件。

```
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content
```

你会注意到,`block content`在两个文件中都出现了，jade 用它来指定这个 HTML 块放在这里(在`layout.jade`文件中),这就是它的样子(在`index.jade`文件中)。

在`layout.jade`文件中，您会注意到一个到`style.css`文件的链接，这个链接在我们最初的项目中似乎并不存在。这个文件是使用我们在`app.js`中设置的手写笔中间件从`style.styl`代码生成的。

你有它！我们如何从`app.js`到定义响应的路线，最后到声明该响应看起来像什么的视图。如果您选择通过点击**调试**按钮在本地运行 web 应用程序(您可以通过点击右侧的下拉按钮选择不同的浏览器)。

![An Express App Running Locally](img/50859c075b2028218c92e841e7f70527.png)

按下后，将启动 Node.js 服务器并打开 Internet Explorer 到根 URL。

![Running the Node Server](img/13831382afdb94efd13951fa5f6e42d3.png)

![The Front-end of the Express App in IE](img/7f40f62cbc503ba1b21ec582394995d9.png)

## 发布到 Azure(针对使用 Visual Studio 的用户)

现在我们已经有了一个基于 Express 的 Node.js 应用程序，让我们点击几下就把它部署到云中。你可以部署到任何支持 Node.js 的云，其中包括 [Nodejitsu](https://www.nodejitsu.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 、 [Heroku](https://www.heroku.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 和 [Engine Yard](https://www.engineyard.com/?WT.mc_id=12833-DEV-sitepoint-othercontent) 。我将使用微软 Azure，因为我可以在那里免费运行 Node.js 网站。

你可以在这里注册免费试用微软 Azure。你将获得 220 美元用于所有 Azure 服务。对于我们正在使用的服务， [Azure 网站](http://azure.microsoft.com/en-us/services/websites/?WT.mc_id=12833-DEV-sitepoint-othercontent)，你实际上可以运行 10 个网站，而不用花一毛钱。

1.  一旦你设置好 Azure 帐户，我们将返回 IDE，右键单击 Express 项目，并从菜单中选择 **Publish** 项。

![Publishing to Azure Step 1](img/76a9e35e815e3a3cb8720a6d98c06eb0.png)

3.  **发布**菜单项将打开一个带有几个选项的向导，您将需要选择目标 Microsoft Azure 网站。

4.  这一步会要求您登录。请在此处使用与您在 Azure 注册时相同的 Microsoft 帐户。

5.  你需要点击**新建**来创建一个新的 Azure 网站。如果您已经创建了一个，只需从下拉列表中选择即可。

![Publishing to Azure Step 2](img/b2a3e2c8d8768624955f7c68a3798854.png)

7.  完成向导中的字段(如下所示)。确保选择一个唯一的站点名称，然后单击**创建**。

![Publishing to Azure Step 3](img/aaff93ba4c9891aeacef63d2a237dec8.png)

9.  你将会看到一个预填充的向导，在按钮的底部有一个**发布**，点击发布。

![Publishing to Azure Step 4](img/107a5dee315fd2271e71a8990b8f7ebb.png)

## 您现在已发布到 Azure Cloud！

在 Azure 门户网站中浏览 Azure 网站。你可以在这里看一段[的视频。](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Deep-Dive/01?WT.mc_id=12833-DEV-sitepoint-othercontent)

*注意:如果您在发布时遇到错误，请确保将您的项目放在更靠近驱动器根目录的位置，以避免临时复制错误。*

![The Express App Status Page in Azure](img/5750eb984d7c8db98c6ce1972b85998b.png)

![The Express App Front-end](img/a6f7f01994edf2f9de96ac45ec4d170a.png)

### 请继续关注第 3 部分！

第 3 部分——用 Node.js、Mongo 和 Socket 构建后端。IO [这里](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-node-mongodb-socket/)。你可以通过关注[我的推特账号](https://twitter.com/ramisayar?WT.mc_id=12833-DEV-sitepoint-othercontent)来了解这篇文章和其他文章的最新动态。

## Azure 上节点的更多学习

为了更深入地学习 Node，我的课程可以在微软虚拟学院的这里[找到。](http://www.microsoftvirtualacademy.com/training-courses/building-apps-with-node-js-jump-start?WT.mc_id=12833-DEV-sitepoint-othercontent)

或者类似节点题材的一些较短格式的视频:

*   六部分系列:用 node 构建应用程序。JS
*   [漫步穿越节点(Coding4Fun)](http://channel9.msdn.com/coding4fun/blog/A-Stroll-Through-Node?WT.mc_id=12833-DEV-sitepoint-othercontent)

本文是微软网站开发技术系列的一部分。我们很高兴与您分享斯巴达项目 [*及其*](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)*[新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=12779-DEV-sitepoint-link)。在 [*modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即*](http://modern.ie/?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint) *。**

## 分享这篇文章