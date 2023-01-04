# 使用 Express Generator 在几分钟内创建新的 Express.js 应用程序

> 原文：<https://www.sitepoint.com/create-new-express-js-apps-with-express-generator/>

**[Express.js](https://expressjs.com/) 是一个 Node.js web 框架，由于其简单性而广受欢迎。它具有易于使用的路由和对视图引擎的简单支持，这使它远远领先于基本节点 HTTP 服务器。**

然而，启动一个新的 Express 应用程序需要一定数量的样板代码:启动一个新的服务器实例、配置一个视图引擎和设置错误处理。

虽然有各种各样的启动项目和样板可用，但 Express 有自己的命令行工具，可以轻松启动新的应用程序，称为 [express-generator](https://expressjs.com/en/starter/generator.html) 。

## 什么是快递？

Express 内置了许多功能，并且您可以从无缝集成的其他软件包中获得更多功能，但是它开箱后为您提供了三项主要功能:

1.  **路由**。这就是为什么`/home` `/blog`和`/about`都给你不同的页面。Express 允许您将不同的路径放在不同的文件中，从而使您可以轻松地模块化这些代码。
2.  **中间件**。如果你不熟悉这个术语，基本上中间件就是“软件粘合剂”。它在路由收到请求之前访问请求，允许路由处理难以处理的事情，比如 cookie 解析、文件上传、错误等等。
3.  **视图**。视图是 HTML 页面呈现自定义内容的方式。你传入你想要渲染的数据，Express 会用你给定的视图引擎渲染它。

## 入门指南

您需要做的第一件事是在您的机器上安装 Node 和 npm。要做到这一点，要么前往官方节点下载页面获取适合您系统的正确二进制文件，要么使用 nvm 之类的版本管理器。我们将在快速技巧“[使用 nvm](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/) 安装 Node.js 的多个版本”中介绍如何使用版本管理器安装 Node。

使用快速生成器启动新项目就像运行几个命令一样简单:

```
npm install express-generator -g 
```

这会将 Express generator 安装为一个全局包，允许您在终端中运行`express`命令:

```
express myapp 
```

这将创建一个名为`myapp`的新 Express 项目，然后将它放在`myapp`目录中:

```
cd myapp 
```

如果您不熟悉终端命令，这个命令会将您带到`myapp`目录中:

```
npm install 
```

如果你不熟悉 npm，它是[默认的 Node.js 包管理器](https://www.sitepoint.com/beginners-guide-node-package-manager/)。运行`npm install`会安装项目的所有依赖项。默认情况下，`express-generator`包括几个通常与 Express 服务器一起使用的包。

### 选择

生成器 CLI 接受六个[参数](https://expressjs.com/en/starter/generator.html)，但最有用的两个参数如下:

*   **-v<ejs>-T3。这允许您选择要安装的视图引擎。默认为`jade`。尽管这仍然有效，但它已被弃用，您应该始终指定一个替代引擎。</ejs>**
*   **-c** 。默认情况下，生成器会为您创建一个非常基本的 CSS 文件，但是选择一个 CSS 引擎会为您的新应用程序配置中间件来编译上述任何选项。

现在我们已经设置好了项目并安装了依赖项，我们可以通过运行以下命令来启动新的服务器:

```
npm start 
```

然后在浏览器中浏览到 [http://localhost:3000](http://localhost:3000) 。

## 应用程序结构

生成的 Express 应用程序从四个文件夹开始。

### `bin`

`bin`文件夹包含启动应用程序的可执行文件。它启动服务器(在端口 3000 上，如果没有提供替代端口)并设置一些基本的错误处理。你真的不需要担心这个文件，因为`npm start`会为你运行它。

### `public`

`public`文件夹是其中一个重要的文件夹:*连接到你的应用程序的人可以访问这个文件夹中的所有东西*。在这个文件夹中，您将放置 JavaScript、CSS、图像和其他人们加载您的网站时需要的资源。

### `routes`

`routes`文件夹是你放路由器文件的地方。生成器创建了两个文件，`index.js`和`users.js`，作为如何分离应用程序的路由配置的例子。

通常，你的网站上的每条主要路线都有一个不同的文件。所以在这个文件夹中你可能会有名为`blog.js`、`home.js`和/或`about.js`的文件。

### `views`

`views`文件夹是存放模板引擎使用的文件的地方。当您调用`render`方法时，生成器将配置 Express 在这里查找匹配的视图。

在这些文件夹之外，有一个文件你应该很熟悉。

## `app.js`

`app.js`文件是特殊的，因为它设置了您的 Express 应用程序并将所有不同的部分粘在一起。让我们来看看它是做什么的。文件是这样开始的:

```
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan'); 
```

文件的前六行是必需的。如果您是 Node 新手，请务必阅读“[了解 module.exports 和 Node.js 中的 exports](https://www.sitepoint.com/understanding-module-exports-exports-node-js/)”。

```
var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users'); 
```

接下来两行代码`require`Express 生成器默认设置的不同路线文件:`routes`和`users`。

```
var app = express(); 
```

之后，我们通过调用`express()`创建一个新的应用程序。app 变量包含应用程序的所有设置和路由。这个对象将你的应用程序粘合在一起。

```
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade'); 
```

一旦创建了应用程序实例，就为呈现视图设置了模板引擎。如果有必要，您可以在这里更改视图文件的路径。

在此之后，您将看到 Express 被配置为使用中间件。生成器安装了几个您可能在 web 应用程序中使用的常见中间件:

```
app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public'))); 
```

*   **记录器**。运行应用程序时，您可能会注意到所有请求的路线都记录在控制台中。如果你想禁用它，你可以注释掉这个中间件。
*   **express.json** 。您可能会注意到有两行代码用于解析传入的 HTTP 请求的主体。第一行处理 JSON 何时通过 POST 请求发送，并将数据放入`request.body`。
*   **express.urlencoded** 。第二行解析 URL 中的查询字符串数据(例如`/profile?id=5`)并将其放入`request.query`。
*   **cookieParser** 。这将获取客户端发送的所有 cookies，并将它们放在`request.cookies`中。它还允许您在将它们发送回客户端之前，通过更改`response.cookies`来修改它们。
*   **快递**。这个中间件服务于您的`public`文件夹中的静态资产。如果您想重命名或移动公用文件夹，可以在此处更改路径。

接下来是路由:

```
app.use('/', indexRouter);
app.use('/users', usersRouter); 
```

这里，我们的应用程序附带了所需的示例路径文件。如果你需要添加额外的路线，你可以在这里做。

这之后的所有代码都用于错误处理。除非您想改变 Express 处理错误的方式，否则通常不需要修改这段代码。默认情况下，当您处于开发模式时，它被设置为显示路线中发生的错误。

## 引导一个新项目

让我们应用到目前为止所学的知识来启动一个基本的 Express.js 应用程序。

假设您已经安装了作为全局模块的`express-generator`,运行下面的命令来创建一个新的框架项目:

```
express -v hbs signup-form 
```

正如我前面提到的，选择默认(Jade)模板库之外的东西是个好主意。这里我使用了 [Handlebars.js](https://handlebarsjs.com/) ，因为我发现小胡子式的语法易于阅读和使用。

一旦生成器运行，切换到新创建的文件夹并安装依赖项:

```
cd signup-form
npm i 
```

此时，您可能会注意到一些关于软件包漏洞的警告。让我们更新 Handlebars.js 的版本来解决这些问题:

```
npm install hbs@4.1.0 
```

既然已经安装并更新了项目依赖项，那么让我们定制一些样板视图模板。

生成器创建一个布局模板，用于呈现视图之间共享的所有标记。打开`views/layout.hbs`，用以下内容替换内容:

```
<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <meta name="description" content="">
  <meta name="author" content="">

  <title>{{title}}</title>

  <!-- Bootstrap core CSS -->
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css"
    integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">

  <!-- Custom styles for this template -->
  <link href="/stylesheets/style.css" rel="stylesheet">
</head>

<body>
  {{{body}}}
</body>

</html> 
```

这里的标记改编自 [Bootstrap 网站](https://getbootstrap.com/)上显示的[示例](https://getbootstrap.com/docs/4.1/examples/sign-in/)。我们还需要添加一些自定义样式，所以打开`public/stylesheets/style.css`并粘贴以下内容:

```
html,
body {
  height: 100%;
}

body {
  display: -ms-flexbox;
  display: flex;
  -ms-flex-align: center;
  align-items: center;
  padding-top: 40px;
  padding-bottom: 40px;
  background-color: #f5f5f5;
}

.full-width {
  width: 100%;
  padding: 15px;
  margin: auto;
}

.form-signin {
  max-width: 330px;
}
.form-signin .checkbox {
  font-weight: 400;
}
.form-signin .form-control {
  position: relative;
  box-sizing: border-box;
  height: auto;
  padding: 10px;
  font-size: 16px;
}
.form-signin .form-control:focus {
  z-index: 2;
}
.form-signin input {
  border-radius: 0;
  margin-bottom: -1px;
}
.form-signin input:first-of-type {
  border-top-left-radius: 0.25rem;
  border-top-right-radius: 0.25rem;
}
.form-signin input:last-of-type {
  border-bottom-left-radius: 0.25rem;
  border-bottom-right-radius: 0.25rem;
  margin-bottom: 10px;
} 
```

既然我们已经定制了布局，让我们为主页添加标记。打开`views/index.hbs`并用以下内容替换内容:

```
<form action="/subscribe" method="POST" class="form-signin full-width text-center">
  <h1 class="h3 mb-3 font-weight-normal">Join the mailing list</h1>
  <label for="name" class="sr-only">First name</label>
  <input type="text" name="name" class="form-control" placeholder="First name" required autofocus>
  <label for="email" class="sr-only">Email</label>
  <input type="email" name="email" class="form-control" placeholder="Your email" required>
  <label for="emailConfirmation" class="sr-only">Email (confirm)</label>
  <input type="email" name="emailConfirmation" class="form-control" placeholder="Your email (confirm)" required>
  <button class="btn btn-lg btn-primary btn-block" type="submit">Subscribe</button>
  <p class="mt-5 mb-3 text-muted">&copy; 2020</p>
</form> 
```

这将在我们的主页上显示一个时事通讯注册表单。

让我们添加一个可以提交表单的路径，在这里我们可以访问表单数据并对其进行处理。打开文件`routes/index.js`,在主页路径下添加以下路径:

```
router.post("/subscribe", function(req, res, next) {
  const { name, email } = req.body;

  // 1\. Validate the user data
  // 2\. Subscribe the user to the mailing list
  // 3\. Send a confirmation email

  res.render("subscribed", {
    title: "You are subscribed",
    name,
    email
  });
}); 
```

在路由处理程序中，我们从请求对象中提取了表单数据。在处理注册(显示为伪代码)之后，我们将数据传递给我们的`subscribed`视图。

*注意:如果您想了解更多关于在 Node 中使用表单的信息，请阅读“[表单、文件上传和 Node.js 和 Express](https://www.sitepoint.com/forms-file-uploads-security-node-express/) 的安全性”。*

现在让我们通过打开一个新文件`views/subscribed.hbs`，并添加以下标记来创建它:

```
<div class="full-width text-center">
  <h1 class="display-3">Thank You, {{name}}!</h1>
  <p class="lead"><strong>Please check your email</strong> to confirm your subscription to our newsletter.</p>
  <hr>
  <p>
      Having trouble? <a href="">Contact us</a>
  </p>
  <p class="lead">
      <a class="btn btn-primary btn-sm" href="/" role="button">Continue to homepage</a>
  </p>
</div> 
```

尝试一下我们的新站点，在项目文件夹中运行`npm run start`启动它，然后访问 [http://localhost:3000](http://localhost:3000) 。

这是在 CodeSandbox 上运行的演示。

[https://codesandbox.io/embed/cranky-feistel-6u6po?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/cranky-feistel-6u6po?fontsize=14&hidenavigation=1&theme=dark)

## 有用的工具

希望您现在对 express-generator 工具如何在开始新的基于 express 的项目时节省您编写重复样板文件的时间有一个清晰的概念。

通过提供合理的默认文件结构，安装和连接通常需要的中间件，生成器只需几个命令就能为新的应用程序创建一个坚实的基础。

## 分享这篇文章