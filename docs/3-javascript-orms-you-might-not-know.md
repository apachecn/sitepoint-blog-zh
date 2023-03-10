# 你可能不知道的 3 种 JavaScript 形式

> 原文：<https://www.sitepoint.com/3-javascript-orms-you-might-not-know/>

当我写第一行 JavaScript 代码时，我从未想过它会超越浏览器脚本和制作交互式网站。我错了，因为 JavaScript 实际上可以在任何地方使用:

*   您可以在浏览器中使用它，而不必受限于带有 DOM API 的 ECMAscript。像 [CoffeeScript](http://coffeescript.org/) 和 [Dart](https://www.dartlang.org/) 这样的语言，可以移植到普通的旧 JavaScript，可以帮助你开发更快更好的代码。
*   你可以在服务器上使用它。Node.js 和[它的许多框架](http://nodeframework.com/)在数量上不断增加，并帮助前端开发人员成为全栈开发人员。
*   你可以把它用在手机应用上。使用 [Cordova](https://cordova.apache.org/) 和像 [Ionic](http://ionicframework.com/) 或 [NativeScript](https://www.nativescript.org/) 这样的框架，你可以更快地构建移动应用，而不需要了解 Java 或 Swift/Objective-C 等其他语言。由于这些框架，你甚至可以在移动平台之间共享源代码。
*   你可以建造物联网。SitePoint 已经发表了 Patrick Catanzariti 的一些文章，这些文章对此非常有帮助。

随着您正在开发的应用程序越来越复杂，您需要一些东西来帮助您处理存储在数据库中的所有数据，无论是在 DB 服务器(如 PostgreSQL)中，在移动设备的嵌入式 SQLite 中，还是在浏览器中。对 ORM 的需求来了。Java 社区有 [Hibernate](http://hibernate.org/orm/) ，PHP 开发者可以使用[主义 ORM](http://www.doctrine-project.org/projects/orm.html) ，JavaScript 社区有自己的 ORM。

在本文中，我将向您介绍一些 JavaScript ORMs，它们可以帮助您在下一个应用程序中处理复杂的数据。

## Bookshelf.js

[Bookshelf.js](http://bookshelfjs.org/) 是 Node.js 的 ORM，设计用于 PostgreSQL、MySQL、MariaDB 和 SQLite3。它建立在 [Knex](http://knexjs.org/) SQL 查询构建器之上，并遵循 [Backbone.js](http://backbonejs.org/) 中的一些模式，如模型和集合以及类似的命名约定。如果你曾经使用过 Backbone，你可能会很快适应 Bookshelf。

要安装 Bookshelf，您需要安装 Knex 和一个数据库驱动程序:

```
# get knex
$ npm install knex --save

# get bookshelf
$ npm install bookshelf --save

# get one of these DB drivers
$ npm install pg
$ npm install mysql
$ npm install mariasql
$ npm install sqlite3
```

安装完成后(确保传递了`--save`标志，以便将它添加到`package.json`文件中)，您可以在 Node.js 应用程序中使用它，如下所示:

```
var knexInstance = require('knex')({
  client: 'mysql', // or what DB you're using
  connection: {
    host     : '127.0.0.1',
    user     : 'scott',
    password : 'tiger', // Scott's cat name
    database : 'db_name',
    charset  : 'utf8'
  }
});
// Initialize Bookshelf by passing the Knex instance
var bookshelf = require('bookshelf')(knexInstance); 

var User = bookshelf.Model.extend({
  tableName: 'users'
});
```

如您所见，书架对象是通过将 Knex 实例作为参数传递来创建的。然后，您可以使用`Model`的`extend()`方法在您的应用程序中创建模型，就像上面例子中表`users`的`User`模型一样。请记住，`bookshelf`是您在整个应用程序中应该使用的唯一书架实例。所以最好让它在应用程序中的任何地方都可以访问，比如把它封装在一个单例中，或者放在一个不同的文件中，需要的时候再调用。

书架允许您建立一对一、一对多和多对多的关系。在我们的例子中，它大概是这样的:

```
var User = bookshelf.Model.extend({
  tableName: 'users',
  posts: function() {
    return this.hasMany(Post);
  }
});

var Post = bookshelf.Model.extend({
  tableName: 'posts',
  user: function() {
    return this.belongsTo(User);
  }
});
```

如果你想看的话，[你可以在 GitHub](https://github.com/tgriesser/bookshelf/) 上找到书架。

## 序列

Sequelize 是 Node.js 和 io.js 的另一个 ORM(它们是[最终合并在一起的](http://www.infoq.com/news/2015/05/nodejs-iojs))。它支持 PostgreSQL、MySQL、MariaDB、SQLite 和 MSSQL，并具有可靠的事务支持、关系、读取复制和
等特性。您可以通过运行以下命令来安装它:

```
# Install Sequelize
$ npm install --save sequelize

# Install the DB driver
$ npm install --save pg pg-hstore

# For both mysql and mariadb dialects
$ npm install --save mysql 
$ npm install --save sqlite3

# MSSQL
$ npm install --save tedious
```

现在，您已经准备好使用它，如下例所示:

```
var Sequelize = require('sequelize');
var sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'mysql'|'mariadb'|'sqlite'|'postgres'|'mssql', // use one of these

  pool: {
    max: 5,
    min: 0,
    idle: 10000
  },

  // SQLite only
  storage: 'path/to/database.sqlite'
});

// Or you can simply use a connection uri
var sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname');
```

就像使用 Bookshelf.js 一样，您只需要一个到数据库的连接。之后，您可以创建一个模型，如下所示:

```
var User = sequelize.define('user', {
  firstName: {
    type: Sequelize.STRING,
    field: 'first_name' // `first_name` column matches User.firstName
  },
  lastName: {
    type: Sequelize.STRING
  }
}, {
  freezeTableName: true // Model tableName (`user`) will be the same as the model name
});
```

上面的`Sequelize.STRING`匹配 SQL 中的一个`VARCHAR`。其他数据类型有`Sequelize.INTEGER`代表`INTEGER`，`Sequelize.BLOB`代表`BLOB`(或者 Postgres 中的`bytea`)。你可以在这里阅读完整的列表。

Sequelize 允许你写表之间的关系[。例如，如果您有一个名为`Project`的模型和另一个名为`Developer`的模型，并且想要为一个项目指派多个开发人员，您可以这样做:](http://docs.sequelizejs.com/en/latest/docs/associations/)

```
Project.hasMany(Developer, {as: 'devs'})
```

这将确保在每个模型中添加必要的字段(在这种情况下，将`project_id`添加到开发人员模型)。或者，如果你觉得你不能从 Sequelize API 中获利，[你可以运行原始 SQL 查询](http://docs.sequelizejs.com/en/latest/docs/raw-queries/)。

[Sequelize 也可以在 GitHub 上找到](https://github.com/sequelize/sequelize)

## 洛夫菲尔德

[Lovefield](https://google.github.io/lovefield/) 不是真正的 ORM。它实际上是一个 web 应用的关系数据库，建立在 IndexedDB 之上，由 Google 开发，完全用 JavaScript 编写。它不支持原始 SQL 查询，但它附带了一个试图模仿 SQL 语法的 API。

您可以使用 [Bower](http://bower.io/) 安装它:

```
$ bower install lovefield --save
```

或者 [npm](https://www.npmjs.com/) :

```
$ npm install lovefield --save
```

将它添加到 HTML 文件中后，您就可以开始将它用作前端关系数据库了。创建数据库和表很简单:

```
// create the database for a `To Do list`
var todoDB = lf.schema.create('todo_db', 1);

var item = todoDB.createTable('items')
      .addColumn('id', lf.Type.INTEGER)
      .addColumn('task', lf.Type.STRING)
      .addColumn('deadline', lf.Type.DATE_TIME)
      .addColumn('done', lf.Type.BOOLEAN)
      .addPrimaryKey(['id']);
```

这段代码片段展示了如何创建一个名为`todo_db`的数据库和一个名为`items`的表，其中包含给定的列(`id`作为主键、`task`、`deadline`和`done`)。此时，要获得所有未完成任务的列表，代码应该是:

```
todoDB.select()
  .from(item)
  .where(item.done.eq(false))
  .exec(); // exec() returns a Promise
```

上面的代码非常类似于 SQL，同样的查询应该是:

```
SELECT * 
    FROM todo_db.items 
    WHERE done = FALSE;
```

你也可以点餐，就像这样:

```
todoDB.select()
  .from(item)
  .where(item.done.eq(false))
  .orderBy(item.deadline, lf.Order.DESC) // order by deadline
  .exec();
```

除了这些简单的查询，Lovefield 还能够处理更复杂的查询，比如连接。如果我在两个名为`project`和`developer`的表中有引用(分别引用表`projects`和`developers`),并且想要查看一个给定开发人员的所有项目，我会写:

```
db.select()
  .from(developer, project)
  .where(lf.op.and(
    developer.projectId.eq(project.id),
    developer.id.eq('12345')
  ))
  .exec();

// these are both equivalent
db.select()
  .from(developer)
  .innerJoin(project, developer.projectId.eq(project.id))
  .where(developer.id.eq('12345'))
  .exec();
```

所以你可以看到 Lovefield 充当了 IndexedDB 之上的 SQL 层。它还支持 ACID 事务(原子性、一致性、隔离性、持久性)、限制和跳过(分页时很有用)、参数化查询等等。

就像其他 ORM 一样，你可以从 GitHub 下载 Lovefield。

## 结论

SQL 知识是每个开发人员都应该具备的技能，但是编写 SQL 查询很无聊，尤其是在有这么多 ORM 的情况下。当它们使你的工作更简单时，为什么不首先使用它们呢？在本文中，我已经介绍了目前为止发布的一些最重要的 ORM。感谢为它们中的每一个提供的例子，现在你应该能够有意识地决定使用哪一个，哪一个适合你的需要。

你呢？你用的是哪个 JavaScript ORM？在下面评论你的选择。

## 分享这篇文章