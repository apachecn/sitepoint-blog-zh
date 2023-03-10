# MongoDB 简介

> 原文：<https://www.sitepoint.com/an-introduction-to-mongodb/>

MongoDB 是一个跨平台、开源的 NoSQL 数据库，许多现代基于节点的 web 应用程序使用它来保存数据。

在这篇初学者友好的教程中，我将演示如何安装 Mongo，然后开始使用它来存储和查询数据。我还将介绍如何在节点程序中与来自[的 Mongo 数据库进行交互，并强调 Mongo 与传统关系数据库(如 MySQL)之间的一些差异。](https://www.sitepoint.com/premium/books/node-js-web-development-fourth-edition/)

## 什么是 MongoDB？

MongoDB 是一个面向文档的数据库。这意味着它不使用表和行来存储数据，而是使用类似 JSON 的*文档*的*集合*。这些文档支持嵌入字段，因此相关数据可以存储在其中。

MongoDB 也是一个无模式数据库，所以在插入数据之前，我们不需要指定列的数量或类型。

下面是一个 MongoDB 文档的示例:

```
{
  _id: ObjectId(3da252d3902a),
  type: "Tutorial",
  title: "An Introduction to MongoDB",
  author: "Manjunath M",
  tags: [ "mongodb", "compass", "crud" ],
  categories: [
    {
      name: "javascript",
      description: "Tutorialss on client-side and server-side JavaScript programming"
    },
    {
      name: "databases",
      description: "Tutorialss on different kinds of databases and their management"
    },
  ],
  content: "MongoDB is a cross-platform, open-source, NoSQL database..."
} 
```

如您所见，该文档有许多*字段* ( `type`，`title`等。)，其中存储了*值*(《教程》、《MongoDB 入门》等。).这些值可以包含字符串、数字、数组、子文档数组(例如，`categories`字段)、地理坐标等等。

`_id`字段名称被保留用作主键。它的值在集合中必须是唯一的，不可变的，并且可以是数组以外的任何类型。

*提示:对于那些想知道“类 JSON”是什么意思的人，Mongo 内部使用了一种叫做 [BSON](http://bsonspec.org/) 的东西(Bin ary JSON 的缩写)。实际上，在使用 MongoDB 时，您并不需要非常了解 BSON。*

正如您可能猜到的，NoSQL 数据库中的一个**文档**对应于 SQL 数据库中的一个**行**。一组文档合在一起被称为**集合**，它大致等同于关系数据库中的**表**。

下表总结了不同的术语:

| SQL Server | MongoDB |
| --- | --- |
| 数据库ˌ资料库 | 数据库ˌ资料库 |
| 桌子 | 收藏品 |
| 排 | 文件 |
| 圆柱 | 田 |
| 索引 | 索引 |

如果你正在开始一个新项目，并且不确定是选择 Mongo 还是 MySQL 之类的关系数据库，现在可能是阅读我们的教程 [SQL vs NoSQL:如何选择](https://www.sitepoint.com/sql-vs-nosql-choose/)的好时机。

说了这么多，让我们继续安装 MongoDB。

## 安装 MongoDB

注意:如果你只是想按照本教程学习，而不想在你的电脑上安装任何软件，你可以使用一些在线服务。例如，Mongo playground 是一个简单的沙盒，用于在线测试和共享 MongoDB 查询。

MongoDB 有各种版本。我们感兴趣的是 MongoDB 社区版。

该项目的主页上有关于安装 Mongo 的优秀文档，我不会在这里重复。相反，我将为您提供每个主要操作系统的说明链接:

*   [在 Windows 上安装 MongoDB 社区版](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)
*   [在 macOS 上安装 MongoDB 社区版](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)
*   [在 Ubuntu 上安装 MongoDB 社区版](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

如果你使用的不是基于 Ubuntu 的 Linux 版本，你可以查看[这个页面](https://docs.mongodb.com/manual/installation/#mongodb-community-edition-installation-tutorials)来获得其他发行版的安装说明。MongoDB 通常也可以通过官方的 Linux 软件渠道获得，但有时这会带来一个过时的版本。

### 安装后配置

一旦为您的系统安装了 MongoDB，您可能会遇到以下错误:

```
dbpath (/data/db) does not exist.
 Create this directory or give existing directory in --dbpath.
 See http://dochub.mongodb.org/core/startingandstoppingmongo 
```

这意味着 Mongo 无法找到(或访问)它用来存储数据库的目录。这很容易补救:

```
sudo mkdir -p /data/db
sudo chown -R `id -un` /data/db 
```

第一个命令创建了`data/db`目录。第二个设置权限，以便 Mongo 可以写入该目录。

### 安装指南针图形用户界面

在本教程中，我们将使用命令行，但是 MongoDB 还提供了一个名为 Compass 的工具，可以使用 GUI 连接和管理您的数据库。

如果您使用的是 Windows，Compass 可以作为 Mongo 主安装的一部分进行安装(只需从向导中选择合适的选项)。否则，您可以[在此](https://docs.mongodb.com/compass/master/install/)下载适用于您各自操作系统的 Compass。

看起来是这样的:

![Mongo DB Compass GUI](img/de670904d3e694793e0896efb0e84b18.png)

### 蒙哥贝壳

我们可以通过打开 Mongo shell 来测试我们的安装。你可以通过打开一个终端窗口并输入`mongo`来实现。

*注意:这里假设`<mongodb installation dir>/bin`在你的路径上。如果由于某种原因不是这样，请切换到`<mongodb installation dir>/bin`目录并重新运行该命令。*

如果您得到一个`Error: couldn't connect to server`错误，您将需要用命令`mongod`启动 Mongo 服务器(在第二个终端窗口中)。

一旦你进入 Mongo shell，输入`db.version()`来查看你正在运行的 MongoDB 版本。在写的时候，这个应该输出`4.2.2`。

请注意，您可以随时通过运行`quit()`退出 Mongo shell，并通过按`Ctrl` + `C`退出 Mongo 守护进程。

现在让我们熟悉一些 MongoDB 基础知识。

## 基本数据库操作

如果您还没有进入 Mongo shell(通过在终端中键入`mongo`):

```
[mj@localhost ~]$ mongo
MongoDB shell version v4.2.2
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("08a624a0-b330-4233-b56b-1d5b15a48fea") }
MongoDB server version: 4.2.2 
```

让我们从创建一个数据库开始。为了创建数据库，MongoDB 有一个`use DATABASE_NAME`命令:

```
> use exampledb
switched to db exampledb 
```

要显示所有现有的数据库，请尝试`show dbs`:

```
> show dbs

admin          0.000GB
config         0.000GB
local          0.000GB 
```

`exampledb`不在列表中，因为我们需要将至少一个文档插入数据库。要插入文档，可以使用`db.COLLECTION_NAME.insertOne({"key":"value"})`。这里有一个例子:

```
> db.users.insertOne({name: "Bob"})
{
   "acknowledged" : true,
   "insertedId" : ObjectId("5a52c53b223039ee9c2daaec")
} 
```

MongoDB 自动创建一个新的`users`集合，并插入一个带有键值对`'name':'Bob'`的文档。返回的 ObjectId 是插入的文档的 Id。MongoDB 在创建时为每个文档创建一个惟一的 ObjectId，它成为`_id`字段的默认值。

现在我们应该能够看到我们的数据库:

```
>show dbs
admin          0.000GB
config         0.000GB
exampledb      0.000GB
local          0.000GB 
```

类似地，您可以确认集合是使用`show collections`命令创建的:

```
> show collections
users 
```

我们创建了一个数据库，添加了一个名为`users`的集合，并向其中插入了一个文档。现在让我们试着放下它。要删除一个现有的数据库，使用`dropDatabase()`命令，如下所示:

```
>db.dropDatabase()
{ "dropped" : "exampledb", "ok" : 1 } 
```

`show dbs`确认数据库确实被删除:

```
> show dbs
admin          0.000GB
config         0.000GB
local          0.000GB 
```

关于更多的数据库操作，请参考 MongoDB 参考页面上的[数据库命令](https://docs.mongodb.com/manual/reference/command/)。

## 用户管理

到目前为止，您可能已经注意到 MongoDB 没有启用任何类型的访问控制。

虽然不必提供用户名和密码对开发来说很好，但这是在生产中使用 Mongo 时应该改变的。

以下是创建具有完全读/写权限的数据库用户的步骤:

*   确保您已经在没有任何访问控制的情况下启动了 Mongo 服务器(通常通过键入`mongod`)。
*   通过键入`mongo`打开一个 shell。
*   在 shell 中，将一个角色为`readWrite`的用户添加到`exampledb`数据库中。这将提示您输入密码。显然，将“manjunath”替换为您想要的用户名:
    `js
    use exampledb
    db.createUser(
    {
    user: "manjunath",
    pwd: passwordPrompt(),
    roles: [ { role: "readWrite" ]
    }
    )` 
*   退出 Mongo shell。
*   关闭 Mongo 服务器，然后使用`mongod --auth`重启它。连接到此实例的客户端现在必须进行自我验证。
*   像这样重新打开一个壳:`mongo --authenticationDatabase "exampledb" -u "manjunath" -p`。现在会提示您输入密码。

更多信息，请参考[项目关于启用访问控制](https://docs.mongodb.com/manual/tutorial/enable-authentication/)的文档。

## MongoDB CRUD 操作

你可能已经知道，CRUD 的首字母缩写代表 **c** reate、 **r** ead、 **u** pdate 和 **d** elete。这是构建应用程序时不可避免的四种基本数据库操作。例如，任何现代应用程序都有能力创建一个新用户，读取用户数据，更新用户信息，如果需要，还可以删除用户帐户。让我们使用 MongoDB 在数据库级别完成这项工作。

### 创建操作

创建与[将文档](https://docs.mongodb.com/manual/tutorial/insert-documents/)插入收藏集是一样的。在上一节中，我们使用`db.collection.insertOne()`语法插入了一个文档。还有一种叫做`db.collection.insertMany()`的方法可以让你一次插入多个文档。下面是语法:

```
> db.collection.insertMany([ <document 1> , <document 2>, ... ]) 
```

让我们创建一个`users`集合，并用一些实际用户填充它:

```
> use exampledb
> db.users.insertMany([
   { name: "Tom",age:15, email: "tom@example.com" },
   { name: "Bob", age:35, email:"bob@example.com" },
   { name: "Kate", age: 27, email: "kate@example.com" },
   { name: "Katherine", age:65, email:"katherine@example.com"}
])

{
   "acknowledged" : true,
   "insertedIds" : [
      ObjectId("5e25bb58ba0cf16476aa56ff"),
    ObjectId("5e25bb58ba0cf16476aa5700"),
    ObjectId("5e25bb58ba0cf16476aa5701"),
    ObjectId("5e25bb58ba0cf16476aa5702")
   ]
} 
```

`insertMany`方法接受一个对象数组，作为回报，我们得到一个`ObjectId`的数组。

### 读取操作

读取操作用于从集合中检索一个文档或多个文档。读取操作的语法如下:

```
> db.collection.find(query, projection) 
```

要检索所有用户文档，您可以执行以下操作:

```
> db.users.find().pretty()
{
  "_id" : ObjectId("5e25bb58ba0cf16476aa56ff"),
  "name" : "Tom",
  "age" : 15,
  "email" : "tom@example.com"
}
{
  "_id" : ObjectId("5e25bb58ba0cf16476aa5700"),
  "name" : "Bob",
  "age" : 35,
  "email" : "bob@example.com"
}
{
  "_id" : ObjectId("5e25bb58ba0cf16476aa5701"),
  "name" : "Kate",
  "age" : 27,
  "email" : "kate@example.com"
}
{
  "_id" : ObjectId("5e25bb58ba0cf16476aa5702"),
  "name" : "Katherine",
  "age" : 65,
  "email" : "katherine@example.com"
} 
```

这对应于 SQL 数据库的`SELECT * FROM USERS`查询。

`pretty`方法是一个[光标方法](https://docs.mongodb.com/manual/reference/method/js-cursor/)，还有许多其他的方法。您可以链接这些方法来修改查询和查询返回的文档。

也许您需要过滤查询以返回集合的子集——例如查找所有 30 岁以下的用户。您可以像这样修改查询:

```
> db.users.find({ age: { $lt: 30 } })
{ "_id" : ObjectId("5e25bb58ba0cf16476aa56ff"), "name" : "Tom", "age" : 15, "email" : "tom@example.com" }
{ "_id" : ObjectId("5e25bb58ba0cf16476aa5701"), "name" : "Kate", "age" : 27, "email" : "kate@example.com" } 
```

在这个例子中，`$lt`是一个查询过滤器操作符，它选择字段值小于 30 的文档。有许多比较和逻辑查询过滤器可用。您可以在[查询选择器文档](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors)中看到完整的列表。

*注意:在 Mongo 中，您可以使用正则表达式复制 SQL 的`like`查询。例如，`SELECT * FROM users WHERE name LIKE 'Kat%'`翻译成`db.users.find({ name: /Kat.*/ })`。*

### 更新操作

一个[更新操作](https://docs.mongodb.com/manual/tutorial/update-documents/)修改集合中的文档。与创建操作类似，MongoDB 提供了各种更新文档的方法。例如:

1.  `db.collection.updateOne(<filter>, <update>, <options>)`
2.  `db.collection.updateMany(<filter>, <update>, <options>)`。

如果您需要向集合中的所有现有文档添加一个额外的字段，比如说，`registration`，您可以这样做:

```
> db.users.updateMany({}, {$set: { registration: "incomplete"}})
{ "acknowledged" : true, "matchedCount" : 4, "modifiedCount" : 4 } 
```

第一个参数是一个空对象，因为我们想要更新集合中的所有文档。`$set`是一个更新操作符，它用指定的值设置一个字段的值。您可以使用`db.users.find()`来验证额外的字段是否已添加。

为了更新符合特定标准的文档的值，`updateMany()`接受一个过滤器对象作为它的第一个参数。例如，您可能想要为所有 18 岁以上的用户覆盖`registration`到`complete`的值。你可以这样做:

```
> db.users.updateMany(
  {age:{ $gt: 18} },
  {$set: { registration: "complete"}
})

{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 } 
```

要更新单个用户的注册详细信息，您可以执行以下操作:

```
> db.users.updateOne(
 {email: "tom@example.com" },
 {$set: { registration: "complete"}
})

{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 } 
```

### 删除操作

一个[删除操作](https://docs.mongodb.com/manual/tutorial/remove-documents/)从集合中删除一个文档。要删除一个文档，可以使用`db.collection.deleteOne(<filter>, <options>)`方法，要删除多个文档，可以使用`db.collection.deleteMany(<filter>, <options>)`方法。

要根据特定标准删除文档，您可以使用我们在读取和更新操作中使用的过滤器操作符:

```
> db.users.updateOne(
 {email: "tom@example.com" },
 {$set: { status: "dormant"}
})

{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.users.deleteMany( { status: { $in: [ "dormant", "inactive" ] } } )

{ "acknowledged" : true, "deletedCount" : 1 } 
```

这将删除所有状态为“休眠”或“非活动”的文档。

## 模式验证

在本教程的前面，当我说 Mongo 是一个无模式数据库时，我有点过于简化了。

它*是*无模式的，因为我们不需要在插入数据之前指定列的数量或类型。然而，也可以定义一个 JSON 模式，并使用它来实施数据的验证规则。

让我们创建一个`validatedUsers`集合，在这里我们可以使用`validator`构造来指定一个`name`是强制的，并且一个`email`字段匹配特定的模式:

```
> db.createCollection("validatedUsers", {
  validator: {
    $jsonSchema: {
      required: [ "name", "email" ],
      properties: {
        name: {
          bsonType: "string",
          description: "must be a string and is required"
        },
        email: {
          bsonType: "string",
          pattern: "^.+\@.+$",
          description: "must be a valid email and is required"
        }
      }
    }
  }
})

{ "ok" : 1 } 
```

现在，如果我们试图插入不正确的数据，我们会收到一个验证错误:

```
> db.validatedUsers.insertOne({ name: "Jim", email: "not-an-email" })

2020-01-22T09:56:56.918+0100 E  QUERY    &lsqb;js] uncaught exception: WriteError({
  "index" : 0,
  "code" : 121,
  "errmsg" : "Document failed validation",
  "op" : {
    "_id" : ObjectId("5e280e5847eb18010666530c"),
    "name" : "Jim",
    "email" : "not-an-email"
  }
}) :
WriteError({
  "index" : 0,
  "code" : 121,
  "errmsg" : "Document failed validation",
  "op" : {
    "_id" : ObjectId("5e280e5847eb18010666530c"),
    "name" : "Jim",
    "email" : "not-an-email"
  }
})
WriteError@src/mongo/shell/bulk_api.js:458:48
mergeBatchResults@src/mongo/shell/bulk_api.js:855:49
executeBatch@src/mongo/shell/bulk_api.js:919:13
Bulk/this.execute@src/mongo/shell/bulk_api.js:1163:21
DBCollection.prototype.insertOne@src/mongo/shell/crud_api.js:264:9
@(shell):1:1 
```

您可以在项目文档中阅读[更多关于模式验证的内容。](https://docs.mongodb.com/manual/core/schema-validation/)

## MongoDB 驱动程序概述

对于与 MongoDB 服务器通信的应用程序，您必须使用一个名为**驱动程序**的客户端库。驱动程序位于数据库服务器之上，允许您使用驱动程序 API 与数据库进行交互。MongoDB 有针对所有流行语言和环境的官方和第三方驱动程序。

Node.js 最流行的驱动包括[原生 MongoDB 驱动](https://docs.mongodb.com/ecosystem/drivers/node/)和[mongose](https://mongoosejs.com/)。我将在这里简要讨论这两个问题。

### MongoDB Node.js 驱动程序

这是 Node.js 的官方 [MongoDB 驱动。驱动程序可以使用回调、承诺或`async … await`与数据库交互。](https://mongodb.github.io/node-mongodb-native/)

您可以像这样安装它:

```
npm install mongod 
```

下面的例子演示了如何将驱动程序连接到服务器，并列出`users`集合中的所有文档。

*注意:如果您使用用户名和密码连接到 Mongo 服务器，您需要在代码中指定这些细节。*

#### 名称和密码

如果您使用用户名和密码连接到 Mongo 服务器，您需要在代码中指定这些细节。

```
const MongoClient = require('mongodb').MongoClient;
const url = 'mongodb://localhost:27017/exampledb';

// With authentication:
// const url = 'mongodb://<userName>:<passWord>@localhost:27017/exampledb';
// Further reading: https://docs.mongodb.com/manual/reference/connection-string/

(async () => {
  let client;

  try {
    client = await MongoClient.connect(url, {
      useNewUrlParser: true,
      useUnifiedTopology: true
    });

    const db = client.db('exampledb');
    const collection = db.collection('users');
    const users = await collection.find().toArray();
    console.log(users);
  } catch (err) {
    console.log(err.stack);
  }

  if (client) {
    client.close();
  }
})(); 
```

`MongoClient.connect`返回一个承诺。任何错误都被`catch`块捕获，任何数据库操作都进入`try`块。如果你浏览一下 [Mongo 驱动文档](https://mongodb.github.io/node-mongodb-native/3.5/)，你会发现这个 API 和我们在 shell 中使用的非常相似。

### 猫鼬司机

MongoDB 的另一个流行的 Node.js 驱动是[mongose](http://mongoosejs.com/docs/index.html)。Mongoose 是建立在官方 MongoDB 驱动之上的。当 Mongoose 发布的时候，它有很多本地 MongoDB 驱动没有的特性。一个突出的特性是能够定义映射到数据库集合的模式结构。然而，MongoDB 的最新版本以 JSON 模式和模式验证的形式采用了其中的一些特性。

除了模式，Mongoose 的其他奇特特性还包括[模型](https://mongoosejs.com/docs/models.html)、[验证器](https://mongoosejs.com/docs/validation.html)和[中间件](https://mongoosejs.com/docs/middleware.html)、[填充方法](https://mongoosejs.com/docs/populate.html)、[插件](https://mongoosejs.com/docs/plugins.html)等等。你可以在猫鼬文档中读到更多关于这些的内容。

您可以像这样安装 Mongoose:

```
npm install mongoose 
```

这是上一个例子中的猫鼬:

```
const mongoose = require('mongoose');

async function run() {
  await mongoose.connect('mongodb://localhost:27017/exampledb', {
    useNewUrlParser: true,
    useUnifiedTopology: true
  });

  const userSchema = new mongoose.Schema({ name: String, age: String, email: String });
  const User = mongoose.model('User', userSchema);

  const users = await User.find();
  console.log(users);
  mongoose.connection.close();
}

run().catch(error => console.log(error.stack)); 
```

在 Mongoose 中，一切都以 a [模式](https://mongoosejs.com/docs/guide.html)开始。每个模式映射到一个 MongoDB 集合，并定义该集合中文档的形状。

## 结论

MongoDB 是一个流行的 NoSQL 数据库解决方案，适合现代开发需求。在本教程中，我们已经介绍了 MongoDB 的基础知识、Mongo shell 和一些流行的驱动程序。我们还探索了 Mongo shell 中常见的数据库操作和 CRUD 操作。现在是时候让您开始尝试我们在这里介绍的内容以及更多内容了。如果想了解更多，我建议用 MongoDB 和 Node 创建一个 REST API，让自己熟悉常见的数据库操作和方法。

## 分享这篇文章