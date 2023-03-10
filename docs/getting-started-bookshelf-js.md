# 书架入门. js

> 原文：<https://www.sitepoint.com/getting-started-bookshelf-js/>

在过去的三年里，我们看到了 JavaScript 的流行。多年来，人们已经多次尝试将这种流行语言带到服务器上。这些尝试中最流行的是 [Node.js](http://nodejs.org/) ，它作为一种编写服务器应用程序的快捷方式呈现给了社区。Node 的卖点是速度，包括性能和开发时间。随着这种受欢迎程度的提高，社区不断发展，项目受益于更多的贡献者，产生了像 [Express.js](http://expressjs.com/) 这样的高质量模块。

因此，人们开始使用 Node 构建完整的后端。后端系统应该做的最重要的事情之一是有效地与数据库通信。这就是**对象关系映射**或 ORM 软件的用武之地。通常，开发人员需要精通他们使用的编程语言和 SQL，以便与数据库进行通信。ORM 通过允许开发人员在他们选择的编程语言中使用对象与数据库进行交互，使生活变得更加轻松。本文介绍 ORM，并特别关注 Bookshelf.js ORM。

## 什么是 ORM？

维基百科将对象关系映射定义为:

> 一种编程技术，用于在面向对象编程语言中的不兼容类型
> 系统之间**转换数据**。这在
> 效果中创建了一个**“虚拟对象数据库”**，可以从到
> 编程语言中的**使用**

现在在我们的例子中，编程语言是 JavaScript，不兼容的系统是 MySQL 等关系数据库系统。这意味着 ORM 库应该允许我们像与常规 JavaScript 对象交互一样与数据库进行通信。Node.js 有很多 ORM 库，比较流行的有 [Persistence.js](https://github.com/coresmart/persistencejs) 、 [Sequelize.js](http://sequelizejs.com/) 和 [Bookshelf.js](http://bookshelfjs.org) 。本文将介绍 Bookshelf.js。

## Bookshelf.js 示例

数据库交互通常围绕着四个 CRUD 操作——创建、读取、更新和删除。Bookshelf.js 提供了一种直观的方式来实现这一点，例如，创建操作如下所示:

```
new Post({name: 'New Article'}).save().then(function(model) {
  // ...
});
```

假设`Post`是具有相应数据库表的模型，并且`name`是对应于数据库表中的列的属性。

类似地，读操作如下所示:

```
// select * from `user` where `email` = 'user@mail.com'
new User({email: 'user@mail.com'})
  .fetch()
  .then(function(model) {
    console.log(model.get('gender'));
  });
```

注意代码中的`then`调用。Bookshelf.js 支持基于[承诺的](https://www.sitepoint.com/overview-javascript-promises/)接口，在这种情况下，这意味着只有当查询成功时，才会调用传入`then`的匿名函数。`model`是生成的 JavaScript 对象，您可以用它来访问与`User`相关的属性。在我们的例子中,`model.get('gender')`返回用户的性别。

## 用 Bookshelf.js 和 Express.js 构建 API

对于一个更完整的例子，假设我们被委托使用以下资源为一个博客构建一个 JSON API:

```
GET  /api/article
GET  /api/article/:article_id
POST /api/article 
```

并且，客户端已经有了一个 MySQL 数据库，如下表所示:

```
create table article (
  id int not null primary key,
  title varchar(100) null,
  body text null,
  author varchar(100) null
);
```

首先，我们需要用`package.json`设置我们的 Express.js 环境:

```
{
  "name": "article_api",
  "description": "expose articles via JSON",
  "version": "0.0.1",
  "private": true,
  "dependencies": {
    "bluebird": "^2.1.3",
    "body-parser": "^1.3.1",
    "express": "4.4.3",
    "mysql": "*",
    "knex": "*",
    "bookshelf": "*"
  }
}
```

我们需要`knex`查询构建器，因为`bookshelf`依赖于它，我们需要`bluebird`的承诺。
我们的`app.js`结构现在看起来是这样的:

```
// When the app starts
var express = require('express');
var app = express();
var bodyParser = require('body-parser');
var Promise = require('bluebird');

var dbConfig = {
  client: 'mysql',
  connection: {
    host: 'localhost',
    user: 'root',
    password: 'your_password',
    database: 'blog',
    charset: 'utf8'
  }
};

var knex = require('knex')(dbConfig);
var bookshelf = require('bookshelf')(knex);

app.set('bookshelf', bookshelf);

var allowCrossDomain = function(req, res, next) {
  res.header('Access-Control-Allow-Origin', '*');
  next();
};

app.use(allowCrossDomain);
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded());

// parse application/json
app.use(bodyParser.json());

// parse application/vnd.api+json as json
app.use(bodyParser.json({type: 'application/vnd.api+json'}));

// elsewhere, to use the bookshelf client:
var bookshelf = app.get('bookshelf');

// {our model definition code goes here}

app.listen(3000, function() {
  console.log('Express started at port 3000');
});
```

我们的 MySQL 数据库叫做`blog`。我们需要定义文章模型，并将其绑定到`article`表。我们将把`// {our model definition code goes here}`替换为:

```
var Article = bookshelf.Model.extend({
  tableName: 'article'
});
```

信不信由你，这就是在 Bookshelf.js 中定义一个模型的全部内容。我们现在可以使用这个模型在我们的 API 中查询数据库。首先，`GET /api/article`方法应该返回数据库中的所有文章:

```
app.get('/api/article', function(req, res) {
  new Article().fetchAll()
    .then(function(articles) {
      res.send(articles.toJSON());
    }).catch(function(error) {
      console.log(error);
      res.send('An error occured');
    });
});
```

Bookshelf.js 中的`fetchAll`取数据库表中的所有条目，`catch`只在出错时执行(文档中有更多模型方法)。

## 结论

Node.js 已经发展成为一种技术，可以通过 Express.js 这样的模块用于构建 web 应用程序和 API。bookshelf . js 通过包装底层数据库表结构并公开常规 JavaScript 对象进行查询，使 Node.js 应用程序与关系数据库的对话变得更加容易。这篇文章提供了一个高层次的介绍。演示项目的完整实现可在 [GitHub](https://github.com/jsprodotcom/blog-api) 上获得。

## 分享这篇文章