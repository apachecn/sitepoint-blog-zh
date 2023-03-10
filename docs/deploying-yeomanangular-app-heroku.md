# 将自耕农/Angular 应用程序部署到 Heroku

> 原文：<https://www.sitepoint.com/deploying-yeomanangular-app-heroku/>

你已经用 Yeoman 启动了你的 AngularJS 应用程序，现在你准备好向全世界展示它了吗？

你可以，只要把它部署到 Heroku！

## 先决条件

为了充分利用本教程，我们建议您具备以下技能和资源:

*   一个终端和命令行的基本知识
*   [NodeJS](http://nodejs.org/) 和 NPM 安装完毕
*   一个 [Heroku](https://id.heroku.com/signup) 账号和 [Heroku 工具箱](https://toolbelt.heroku.com/)
*   现有的约曼生成的静态应用程序

## 文件

你可以在这里找到这个教程项目[的回购。](https://github.com/Haizyfox/sp-yeoman-heroku)

## 让我们向全世界展示您的应用程序！

### 节点包

由于我们的 Yeoman 站点只是一组静态页面，我们不能把它贴在 Heroku 上，然后期望它能工作。Heroku 需要一个服务器来为我们提供网页。

我们将使用 Node 为我们的静态站点提供一些文件和更改。首先，让我们安装必要的依赖项来部署到 Heroku。

```
npm install gzippo express --save
```

Gzippo 让我们提供 gzipped 资产，Express 是一个简单的 Node 应用程序框架，这将使我们的站点服务变得更加容易

### 服务器文件

我们需要创建一个服务器文件:web.js

**/web.js**

```
var gzippo = require('gzippo');
  var express = require('express');
  var app = express();

  app.use(express.logger('dev'));
  app.use(gzippo.staticGzip("" + __dirname + "/dist"));
  app.listen(process.env.PORT || 5000);
```

这就是我们为我们的网站提供服务所需要的一切。

### 分布式目录

请注意，我们的服务器提供/dist 目录。如果你看不到/dist 目录，那是因为我们还没有构建我们的应用程序。

```
grunt build
```

这将编译所有内容，并为您提供一个干净的 dist/ directory。咕噜是不是很棒？

**重要**
值得注意的是，默认情况下 git 会忽略 dist/目录(他们假设你只想对开发项目进行版本控制，而不是对编译后的 app)。由于 Heroku 使用 git 进行部署，我们需要删除 dist/ from。gitignore 以确保它被提交。

### Heroku

现在让我们添加一个 Procfile，也是在 root 中。Procfile 告诉 Heroku 如何启动我们的应用程序。在本例中，我们告诉它使用 NodeJS 来运行我们的 web.js 服务器文件。

/procfile

```
web: node web.js
```

对！关键时刻到了！让我们部署到 Heroku！首先，让我们将项目目录转换成 git repo

```
git init
```

假设您有一个 Heroku 帐户，并且安装了 Heroku Toolbelt，只需运行:

```
heroku create <your_app_name>
```

现在提交您的代码并通过推送到 Heroku 来部署它！

```
git add .
  git commit -m "Initial Commit"
  git push heroku master
```

您可能需要将您的 web 工作人员扩大到至少一个:

```
heroku ps:scale web=1
```

要在 Heroku 上打开您的应用并惊叹您的成就，请运行:

```
heroku open
```

如果你想让我写更多关于 Angular 的工作，请在评论中告诉我。

## 分享这篇文章