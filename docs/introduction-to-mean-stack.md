# 平均堆栈简介

> 原文：<https://www.sitepoint.com/introduction-to-mean-stack/>

从首字母缩略词 LAMP (Linux Apache MySql PHP)诞生到现在已经是很久以前的事了。今天，另一个非常常见的堆栈是均值堆栈。可能不是所有人都知道缩写“MEAN”代表什么，所以让我们从基本的开始。MEAN 代表 [MongoDB](https://www.mongodb.org/) ， [Express](http://expressjs.com/) ， [AngularJS](https://angularjs.org/) 和 [Node.js](https://nodejs.org/) 。它提供了一个完整的 JavaScript 解决方案来构建网站和 web 应用程序。从数据库到后端代码和前端代码，一切都是用 JavaScript 写的。如果您不熟悉 MEAN stack 使用的技术，您可以在下一节找到关于这些技术的简短介绍。

[你可以在 GitHub](https://github.com/sitepoint-editors/sp-mean) 上找到这篇文章的完整源代码。请随意叉它，玩它。

## 意思是

**M** 代表 **MongoDB** ，世界领先的 NoSQL 数据库。这是一种文档类型的数据库，它将其数据存储到一个类似 JSON 格式的二进制文件中，称为 BSON(二进制 JSON)。它很容易使用，对于 JavaScript 开发人员来说，应该像使用 JSON 一样简单。

**E** 代表 **Express** ，这是一个为 Node.js 构建的轻量级极简框架。它是为 web 应用程序和 API 创建的，并为开发人员提供了无数的 HTTP 工具。

**A** 代表 **AngularJS** ，谷歌构建的模型-视图-无论什么 JS 框架。AngularJS 使 API 消费变得尽可能简单，因此将其用作 MEAN 的客户端对每个开发人员都非常有帮助。此外，AngularJS 针对移动开发进行了优化，因此通过在浏览器上使用相同的代码库，您已经为正在开发的 web 应用程序构建了一个移动应用程序。

**N** 代表 **Node.js** ，快递的基础。它运行在 Chrome 的 V8 引擎上，能够进行非阻塞、事件驱动的 I/o。node . js 应用程序将处理单个服务上的多个请求，而不会相互阻塞(因此是非阻塞的)。

## 先决条件

开始之前，确保你已经安装了 [MongoDB](http://mongodb.org/) 和 [Node.js](https://nodejs.org/) 。Node.js 附带了一个名为`npm`的包管理器，我们将使用它进行包(依赖)管理。如果你对 PHP 有经验，`npm`相当于[作曲家](https://getcomposer.org/)，而[包装家](https://packagist.org/)相当于[npmjs.org](https://www.npmjs.com/)。在那里，您会发现您正在使用的每个 PHP 包几乎都有一个等效的包，这使得向 Node.js web 应用程序的过渡更加简单。

## 第一台 Express 服务器

首先，在您的终端上运行以下命令:

```
npm init
```

回答完问题后，它将创建一个包含必要信息的`package.json`文件。这是我的:

```
{
  "name": "sp-mean",
  "version": "0.1.0",
  "description": "An introduction to MEANstack for SitePoint",
  "main": "server.js",
  "scripts": {
    "start": "node ./server"
  },
  "author": "Aldo Ziflaj",
  "license": "None"
}
```

`"main": "server.js"`意味着将在服务器上执行的主文件是`server.js`。

第二步，我们通过执行以下命令添加`express`作为依赖项:

```
npm install express --save
```

这将添加一个存储依赖关系的`node_modules`目录。请记住，您应该从版本控制中忽略这个文件夹。

此时，下一步是编写充当服务器的 Express 应用程序:

```
var express = require('express'),
    app = express();

app.get('/', function(req, res) {
    res.send("Hello from Express");
});

app.listen(3000, function() {
    console.log("Server ready. Listening on port 3000");
});
```

通过执行`npm start`，它将在 [http://localhost:3000](http://localhost:3000) 上启动服务器，并在您浏览到索引时发送`Hello from Express`。

## 使用 MongoDB

为了从 Express 应用程序中使用 MongoDB，我们将使用来自[npmjs.com](https://www.npmjs.com/)的一个包。你可以使用的软件包有一长串，包括 [mongodb](https://www.npmjs.com/package/mongodb) 、[MongoDB](https://www.npmjs.com/package/monk)或[mongose](https://www.npmjs.com/package/mongoose)。对于这个例子，我将使用 monk。要开始安装，请运行以下命令:

```
npm install monk --save
```

然后，通过执行以下命令启动 Mongo 控制台

```
mongo
```

现在，通过执行以下命令将一些数据插入 Mongo:

```
use starwars;

db.character.insert({
    name: "Luke", 
    surname: "Skywalker", 
    side: "Light", 
    weapon: "Lightsaber"
});

db.character.insert({
    name: "Yoda",
    side: "Light",
    weapon: "Lightsaber"
});

db.character.insert({
    sith_name: "Vader",
    side: "Dark",
    weapon: "Lightsaber"
});

db.character.insert({
    sith_name: "Sidious",
    side: "Dark",
    weapon: "Force lightning"
});
```

这应该够我们收藏了。如你所见，我们不必为所有记录设置相同的键:卢克和尤达大师没有`sith_name`。这在 MongoDB 中是合法的，因为它是无模式的；只要你插入合法的 JavaScript 对象，你就可以开始了。

现在我们可以使用`monk`从数据库中获取数据并显示给用户。

首先，要求`server.js`顶部的依赖关系:

```
var monk = require('monk');
```

现在获取对我们之前创建的集合的引用:

```
var swChars = monk('localhost:27017/starwars').get('character');
```

这不妨写成:

```
var db = monk('localhost:27017/starwars');
var swChars = db.get('character');
```

第一行代码为您提供了对数据库(`starwars`)的引用，以防您需要使用多个集合。接下来，我们`get()`引用我们正在使用的集合，即`character`集合。

现在，让我们给用户一个当他浏览到`/character`时我们存储的字符的完整列表:

```
app.get('/character', function (req, res) {
  swChars.find({}, function (err, docs) {
    if (err == null) {
      res.json(docs);
    } else {
      console.log(err);
    }
  });
});
```

通过使用`find()`函数，我们查询由`swChars`引用的集合。第一个参数是查询规则，但是因为我们显示了所有的字符，所以我在那里放了一个空的 JavaScript 对象。第二个参数是从数据库集合中提取记录后执行的回调函数。

我们可以通过查询`side`来展示一些记录，比如说绝地武士:

```
app.get('/jedi', function (req, res) {
  swChars.find({side: "Light"}, function (err, docs) {
    if (err == null) {
      res.json(docs);
    } else {
      console.log(err);
    }
  });
});
```

Monk 涵盖了大量的函数来帮助您使用 Mongo 数据库。查看它的 GitHub 库，了解如何使用 monk 的更多信息。

## 前端有棱角

构建后端一直很简单。没有太多的样板代码，简单的 API 路径和真正简单的数据持久化。现在，为了向用户显示这些信息，我们将使用 AngularJS。

使用 Bower 获得 AngularJS:

```
# create a bower.json file to store dependencies
bower init
bower install angular#1.4.3 --save
```

对于客户端，创建这些文件/文件夹:

```
assets/js/ngapp.js
assets/js/controllers
assets/js/services

```

第一个是我们正在构建的基本 AngularJS 应用程序。第二个是控制器所在的目录，第三个是服务(比如工厂)所在的目录。要创建基本角度应用程序，将其置于`ngapp.js`:

```
var app = angular.module('starwars', []);
```

不要忘记把这个包含在`index.html`文件中。

现在，为了从后端 API 获取数据，我们将创建一个工厂。在`services`文件夹中创建一个名为`StarWarsFactory.js`的文件:

```
app.factory('StarWarsFactory', function ($http) {
  return {
    characters: function () {
      return $http.get('/character');
    },

    jedi: function () {
      return $http.get('/jedi');
    }
  }
});
```

现在要使用它，添加一个简单的控制器，名为`MainCtrl.js`:

```
app.controller('MainCtrl',function(StarWarsFactory) {
  var self = this;
  StarWarsFactory.characters().success(function(data) {
    self.charList = data;
  });
});
```

将所有这些 JS 文件包含在`index.html`文件中，并放置这个`div`来显示从服务器获取的数据:

```
<div ng-controller="MainCtrl as m">
  <ul>
    <li ng-repeat="item in m.charList">
      <span ng-if="item.side === 'Light'">
        {{item.name}} {{item.surname}} uses {{item.weapon}}
      </span>

      <span ng-if="item.side === 'Dark'">
        Darth {{item.sith_name}} uses {{item.weapon}}
      </span>
      </li>
  </ul>
</div>
```

最后，要从 Express 应用程序中提供这个服务，您必须删除只显示“Hello from Express”的`/`路线，并用这段代码代替:

```
app.use('/', express.static(__dirname + '/'));

app.get('/', function (req, res) {
  res.sendFile(path.join(__dirname + "/index.html"));
});
```

当你浏览到`localhost:3000`时，你会看到角色列表和他们选择的武器。

## 结论

在这篇文章中，我们学习了如何使用一个叫做 MEAN 的堆栈来构建一个简单的应用程序。我们使用 Mongo 将数据存储为 JavaScript 对象，在后端以 Express 构建 API，在前端以 AngularJS 作为 API 的客户端。

[你可以在 GitHub](https://github.com/sitepoint-editors/sp-mean) 上找到这篇文章的完整源代码。请随意叉它，玩它。

最后，你应该知道 MEAN 有很多生成器。在这篇文章中，我想到了不使用生成器，让完全的初学者更容易进入 MEAN。如果你对 MEAN 更有经验，可以查看任何生成器，如 [mean.io](http://mean.io/#!/) 、 [Yeoman 生成器](https://github.com/meanjs/generator-meanjs)甚至是 [Express 生成器](https://www.npmjs.com/package/express-generator)，并在你的项目中使用它们。

## 分享这篇文章