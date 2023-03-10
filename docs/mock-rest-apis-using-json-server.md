# 快速提示:使用 json-server 模仿 REST APIs

> 原文：<https://www.sitepoint.com/mock-rest-apis-using-json-server/>

有时，你需要在没有后端的情况下构建应用程序前端的原型。创建一个基本的模拟 API 来进行开发是非常耗时的。json-server 库为您解决了这个问题，它提供了一种快速简单的方法来创建用于开发和测试的复杂 RESTful APIs。

这个快速技巧将教你如何使用 [json-server](https://github.com/typicode/json-server) 创建模拟 REST APIs，让你在短短 30 秒内就可以获得一个全功能的 API。

## 先决条件

你应该对 RESTful 原则和如何使用 API 有基本的了解。

你需要以下工具:

*   [nodejs](https://nodejs.org/en/)–JSON-server 构建在 nodejs 之上。
*   测试你的模拟服务器的路线。

> **Windows 用户:**从 [curl 下载页面](https://curl.haxx.se/download.html)可以获得 32 位和 64 位版本的 curl 二进制文件，您可以按照本文中的示例进行操作。

本教程假设您将使用类似 bash 的终端。

## 装置

要安装 json-server，请打开您的终端并输入:

```
$ npm install -g json-server 
```

这将在您的系统上全局安装 json-server，这样您就可以从您喜欢的任何目录启动服务器。

## 资源

在 RESTful API 中，资源是一个具有类型、相关数据、与其他资源的关系以及一组操作方法的对象。例如，如果你正在为电影创建一个 API，那么电影就是一个资源。您可以使用 API 在这个资源上应用 CRUD 操作。

让我们用一个`/movies`资源创建一个 API。

### 创建资源

创建一个名为`db.json`的文件，并向其中添加以下内容:

```
{
  "movies": [
    {"id": 1, "name": "The Godfather", "director":"Francis Ford Coppola", "rating": 9.1},
    {"id": 2, "name": "Casablanca", "director": "Michael Curtiz", "rating": 8.8}
  ]
} 
```

保存文件后，使用以下命令启动服务器:

```
$ json-server --watch db.json 
```

就是这样！现在你有了一个电影 API 你可以从这个服务器上获取电影，添加新电影，删除电影，以及其他一些东西。

为了测试我们的模拟 API，我们可以使用 curl 发出一个 HTTP 请求:

```
$ curl -X GET "http://localhost:3000/movies" 
```

这将返回您在此服务器上的所有电影的列表。在上面的例子中，你会得到两部电影。现在要获得 id 为 1 的电影，只需在 URI 的末尾指定 id:http://localhost:3000/movies/1。

要向服务器添加电影，您可以向 API 发送一个 POST 请求，其中包含电影的详细信息。例如:

```
$ curl -X POST -H "Content-Type: application/json" -d '{
  "id": 3,
  "name": "Inception",
  "director": "Christopher Nolan",
  "rating": 9.0
}' "http://localhost:3000/movies" 
```

这将使用新的电影数据进行响应。为了检查记录是否已成功添加，让我们尝试获取 id 为 3:

```
$ curl -X GET "http://localhost:3000/movies/3" 
```

类似地，您可以使用其他 HTTP 动词(如 PUT 和 DELETE)来访问和修改该服务器上的数据。按照惯例，POST 用于创建新实体，而 PUT 用于更新现有实体。

> **注意:** PUT、POST 和 PATCH 请求需要有一个`Content-Type: application/json`头集。

## 特征

json-server 为模拟 API 提供了许多有用的特性，您需要在后端手工构建这些特性。让我们来探索其中的一些特性:

### 过滤

通过将过滤器作为查询字符串附加到 URI，可以将过滤器应用于您的请求。例如，如果您想获得一部名为“卡萨布兰卡”的电影的详细信息，您可以向您的资源 URI 发送一个 get 请求，并附加一个问号(？)后跟要作为筛选依据的属性名及其值:

```
$ curl -X GET "http://localhost:3000/movies?name=Casablanca" 
```

您还可以通过在不同的过滤器之间添加一个&符号来组合多个过滤器。例如，如果我们也想在上面的例子中按 id 过滤，我们可以使用:

```
$ curl -X GET "http://localhost:3000/movies?name=Casablanca&id=2" 
```

### 经营者

API 还为您提供了逻辑操作符，使过滤变得容易。您可以使用`_gte`和`_lte`作为大于和小于运算符。您还可以使用`_ne`从响应中排除一个值。

例如，如果您想要分级大于或等于 9 的所有电影:

```
$ curl -X GET "http://localhost:3000/movies?rating_gte=9" 
```

请注意，您可以使用&符号组合多个运算符。因此，要获得评分在 5 到 7 之间的所有电影，您需要发出以下请求:

```
$ curl -X GET "http://localhost:3000/movies?rating_gte=5&rating_lte=7" 
```

### 页码

在现实世界中，您将处理大量数据。通过 json-server 内置的分页支持，将这些数据加载到一点大小的块中是很容易的，分页支持固定为每页 10 个项目。

例如，如果您想访问 movies API 的第 3 页，发送一个 GET 请求:

```
$ curl -X GET "http://localhost:3000/movies?_page=3" 
```

这将响应项目 21-30。

### 整理

您可以使用`_sort`和`_order`属性从您的 API 请求排序的数据。例如，如果您希望电影列表按名称(字母顺序)降序排列，那么您将发送以下请求:

```
$ curl -X GET "http://localhost:3000/movies?_sort=name&order=DESC" 
```

json-server 还提供了许多其他特性。您可以在 [json-server 文档](https://github.com/typicode/json-server)中详细探索这些和上述特性。

## 为您的 API 生成模拟数据

在 API 中几乎没有数据的情况下测试前端并不好玩。您可以使用类似于 [faker.js](https://github.com/marak/Faker.js/) 的模块为您的模拟 API 创建一些样本数据。

使用以下命令安装软件包:

```
$ npm install faker 
```

现在创建一个名为 fake-data-generator.js 的文件，并在其中输入以下内容:

```
var faker = require('faker');

var db = { movies: [] };

for (var i=1; i<=1000; i++) {
  db.movies.push({
    id: i,
    name: faker.random.words(),
    director: faker.name.firstName() + ' ' + faker.name.lastName(),
    rating: Math.floor(Math.random()*100+1)/10
  });
}

console.log(JSON.stringify(db)); 
```

这里，我们创建了 1000 个不同的假电影条目，faker 用于生成电影标题和导演姓名。评级是通过生成 1 到 100 之间的随机数，然后除以 10 来创建的。

要使用这个脚本创建一个`db.json`文件，请在您的终端中运行以下命令:

```
$ node fake-data-generator.js > db.json 
```

现在你有一个 1000 部电影的数据库。你现在有大量的假数据可以用来开发和/或测试你的应用。

## 结论

现在，您应该能够快速创建自己的模拟 API，并向其中添加测试数据。json-server 库允许您快速原型化前端代码，而不需要投入任何时间(几乎)来创建前端后端。

这个工具会成为你工作流程的一部分吗，或者你有其他已经成功使用的方法吗？在下面的评论中分享你的想法和建议吧！

## 分享这篇文章