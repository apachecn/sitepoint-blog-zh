# 2022 年 9 个最佳 JavaScript 和类型脚本格式

> 原文：<https://www.sitepoint.com/javascript-typescript-orms/>

本文将简要解释什么是对象关系映射(ORM ),什么是 ORM *库*,以及为什么您应该考虑在您的下一个 JavaScript 项目中使用 ORM。我们还将根据您作为项目开发人员和维护人员的需求，帮助您评估最好的 JavaScript 和 [TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) ORM 库。

我们将了解以下每个工具:

*   [Knex.js: SQL 查询生成器](#knexjssqlquerybuilder)
*   测序
*   [书架](#bookshelf)
*   [水线](#waterline)
*   [Objection.js](#objectionjs)
*   [猫鼬](#mongoose)
*   [型态分类](#typegoose)
*   [类型表](#typeorm)
*   [麦克风](#mikroorm)
*   [棱镜](#prisma)

## 对象关系映射

对象关系映射可能看起来很复杂，但它的目的是让程序员的生活更轻松。要从数据库中获取数据，您需要编写一个查询。那是不是意味着要学 SQL？不，对象关系映射使你有可能用你选择的语言编写查询。

对象关系映射是一种将数据库查询结果转换成实体类实例的技术。一个**实体**只是一个数据库表的对象包装器。它包含映射到数据库表列的属性。实体实例有执行 CRUD 操作的方法，并支持包含自定义逻辑的附加功能，如验证和数据加密。

如果您正在构建一个小项目，安装 ORM 库并不是必需的。使用 SQL 语句来驱动您的应用程序应该足够了。ORM 对于从数百个数据库表中获取数据的大中型项目非常有用。在这种情况下，您需要一个框架，允许您以一致且可预测的方式操作和维护应用程序的数据层。

实体类是业务应用程序的构建块，因为它们被设计用来封装实现业务规则的逻辑。定义业务规则是为了确保自动化流程只在业务策略的范围内执行。业务规则的示例包括:

*   顾客折扣
*   贷款审批
*   销售佣金
*   运输和税收计算

## ORM 库

对象关系映射通常是在**库**的帮助下进行的。术语 ORM 通常指的是一个实际的 ORM 库——一个对象关系*映射器*——它为您执行对象关系映射的工作。

通常，业务规则需要执行多个需要批量运行的 SQL 语句。如果一个 SQL 语句失败，它会使数据库处于不一致的状态。大多数 ORM 库支持一个被称为**事务**的特性，它可以防止这种事件的发生。如果一条 SQL 语句无法在一个事务的上下文中运行，那么在该批处理中成功执行的所有其他 SQL 语句都会通过一个称为**回滚**的操作被撤销。

因此，使用 ORM 库来构建数据层有助于确保数据库始终保持一致的状态。ORM 库通常包含许多更重要的特性，例如:

*   查询生成器
*   迁移脚本
*   用于生成样板代码的 CLI 工具
*   用测试数据预先填充表格的播种特性

在本文中，我将提供每个 ORM 库如何工作的片段:

*   初始设置和配置
*   基本 CRUD 操作
*   高级查询操作

我还包含了一些重要信息，比如发布日期、用户数量、文档链接以及支持渠道(如果有的话)。我还将讨论与查询性能、库维护和体系结构哲学相关的重要问题，这些都是您在做决定时应该重点考虑的。

我已经根据发布日期从最早到最新排列了列表。我根据主要支持的语言将列表分为两部分:JavaScript 和 TypeScript。

在我们开始评估之前，让我们先来看看 Knex.js，这是一个流行的 SQL **查询构建器**，它已经集成了这里列出的许多 ORM 库。Knex.js 非常灵活，通常比一些 ORM 库执行得更好，这些 ORM 库有自己内置的查询构建器实现。在选择使用 Knex.js 作为基础的 ORM 库时，这是一个优势。

### Knex.js: SQL 查询生成器

*   **发射**:2012 年 12 月
*   [网站](http://knexjs.org/)
*   [GitHub](https://github.com/knex/knex):158.6k 使用
*   **数据库:** Postgres、MSSQL、MySQL、MariaDB、SQLite3、Oracle 和 Amazon Redshift

Knex.js 是目前最成熟的 JavaScript SQL 查询构建器，可以在 Node.js 和浏览器(通过 webpack 或 Browserify)中运行。它能够生成高性能的 SQL 查询，与手工编写的 SQL 语句不相上下。

那么什么是查询构建器呢？

它只是一个 API，提供一组可以链接在一起形成查询的函数。这里有一个例子:

```
knex({ a: 'table', b: 'table' })
  .select({
    aTitle: 'a.title',
    bTitle: 'b.title'
  })
  .whereRaw('?? = ??', ['a.column_1', 'b.column_2'])

SQL Output:
select `a`.`title` as `aTitle`, `b`.`title` as `bTitle` from `table`
as `a`, `table` as `b` where `a`.`column_1` = `b`.`column_2` 
```

这就引出了一个问题，为什么要使用查询构建器而不是编写原始的 SQL 语句。我给你四个理由:

*   它帮助您从数据库的 SQL 方言中抽象出代码，使切换更容易。
*   它消除或大大减少了 SQL 注入攻击您的应用程序的机会。
*   它允许使用动态条件轻松构建查询。
*   它附带了用于执行数据库开发操作的附加功能和 CLI 工具。

这些功能包括:

*   连接池
*   回调和承诺接口
*   流接口
*   交易支持
*   模式支持
*   移民
*   播种

在您的应用程序中安装它需要您安装 Knex.js 包，以及您正在使用的数据库的驱动程序:

```
$ npm install knex --save

# Then add one of the following (adding a --save) flag:
$ npm install pg
$ npm install sqlite3
$ npm install mysql
$ npm install mysql2
$ npm install oracledb
$ npm install mssql 
```

下面是一个安装代码示例:

```
const knex = require('knex')({
  client: 'mysql',
  connection: {
    host : '127.0.0.1',
    user : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test'
  }
});

knex.schema.createTable('users', function (table) {
  table.increments();
  table.string('name');
  table.timestamps();
})

Outputs:
create table `users` (`id` int unsigned not null auto_increment primary key, `name` varchar(255),
`created_at` datetime, `updated_at` datetime) 
```

下面是一个基本查询的示例:

```
knex('users').where({
  first_name: 'Test',
  last_name:  'User'
}).select('id')

Outputs:
select `id` from `users` where `first_name` = 'Test' and `last_name` = 'User' 
```

也支持原始 SQL 语句。下面是一个复杂查询的示例:

```
const subcolumn = knex.raw('select avg(salary) from employee where dept_no = e.dept_no')
.wrap('(', ') avg_sal_dept');

knex.select('e.lastname', 'e.salary', subcolumn)
.from('employee as e')
.whereRaw('dept_no = e.dept_no')

Outputs:
select `e`.`lastname`, `e`.`salary`, (select avg(salary) from employee where dept_no = e.dept_no)
avg_sal_dept from `employee` as `e` where dept_no = e.dept_no 
```

Knex.js 还支持 TypeScript，这很好，因为它允许您编写如下代码:

```
import { Knex, knex } from 'knex'

interface User {
  id: number;
  age: number;
  name: string;
  active: boolean;
  departmentId: number;
}

const config: Knex.Config = {
  client: 'sqlite3',
  connection: {
    filename: './data.db',
  },
});

const knexInstance = knex(config);

try {
  const users = await knex<User>('users').select('id', 'age');
} catch (err) {
  // error handling
} 
```

在上面的 TypeScript 示例中，Knex.js 几乎就像一个 ORM。但是，没有创建实体对象实例。相反，接口定义用于创建具有类型安全属性的 JavaScript 对象。

请注意，本文中列出的许多 ORM 库都使用 Knex.js。其中包括:

*   书架
*   Objection.js
*   米克罗托姆

ORM 库通常在 Knex.js 的基础上提供额外的特性。让我们在下一节看看它们。

## JavaScript ORM 库

在这个类别中，这里列出的所有库都是用 JavaScript 编写的，可以直接在 Node.js 中运行，TypeScript 支持要么通过内置类型提供，要么通过 [@types/node](https://www.npmjs.com/package/@types/node) 定义包提供。如果您想要一流的对 TypeScript 项目的支持，您应该跳到 **[TypeScript ORM 库](#typescriptormlibraries)** 部分。

在数据访问层，有两种常用的架构模式:

*   [数据映射器](https://en.wikipedia.org/wiki/Data_mapper_pattern)
*   [活动记录](https://en.wikipedia.org/wiki/Active_record_pattern)

使用**数据映射器模式**，实体类是纯的，只包含属性。CRUD 操作和业务规则在被称为**库**的容器中实现。这里有一个例子:

```
const repository = connection.getRepository(User);.

const user = new User();
user.firstName = "Timber";
await repository.save(user);

const allUsers = await repository.find(); 
```

使用**活动记录模式**，CRUD 操作和业务规则的逻辑在实体类中实现。下面是一个类似的实现示例:

```
const user = new User();
user.firstName = "Timber";
await user.save();

const allUsers = await User.find(); 
```

使用这两种模式各有利弊。这些模式由 Martin Fowler 在他 2003 年的书《企业应用架构的模式》中命名。如果你想知道关于这个问题的更详细的信息，你应该去看看这本书。本文中列出的大多数 ORM 库支持一种或两种模式。

让我们现在开始看它们。

### 序列

*   **发射**:2010 年 7 月
*   [网站](https://sequelize.org/)
*   [GitHub](https://sequelize.org/):271k 使用
*   [松弛](https://sequelize-slack.herokuapp.com/)
*   **数据库** : Postgres、MySQL、MariaDB、SQLite、微软 SQL Server

Sequelize 是一个非常成熟和受欢迎的 Node.js ORM 库，它有很好的文档，包含了解释清楚的代码示例。它支持我们在以前的库中已经提到过的许多数据层特性。与 Bookshelf 不同，它有自己的查询生成器，性能与 Knex.js 一样好

安装这个库非常简单，数据库驱动程序也非常简单:

```
$ npm i sequelize # This will install v6

# And one of the following:
$ npm i pg pg-hstore # Postgres
$ npm i mysql2
$ npm i mariadb
$ npm i sqlite3
$ npm i tedious # Microsoft SQL Server 
```

下面是安装代码示例以及 CRUD 和基本查询语句示例:

```
const { Sequelize } = require('sequelize');

// Connect to database
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: /* one of 'mysql' | 'mariadb' | 'postgres' | 'mssql' */
});

// Create Model
const User = sequelize.define('User', {
  // Model attributes are defined here
  firstName: {
    type: DataTypes.STRING,
    allowNull: false
  },
  lastName: {
    type: DataTypes.STRING
    // allowNull defaults to true
  }
}, {
  // Other model options go here
});

// Create instance
const jane = User.build({ firstName: "Jane", lastName: "Doe" });
await jane.save(); // save to database

// Shortcut for creating instance and saving to database at once
const jane = await User.create({ firstName: "Jane", lastName: "Doe" });

// Find all users
const users = await User.findAll();
console.log(users.every(user => user instanceof User)); // true
console.log("All users:", JSON.stringify(users, null, 2)); 
```

以下是如何编写复杂查询的示例:

```
// What if you wanted to obtain something like WHERE char_length("content") = 7?
Post.findAll({
  where: sequelize.where(sequelize.fn('char_length', sequelize.col('content')), 7)
});
// SELECT ... FROM "posts" AS "post" WHERE char_length("content") = 7

// A more complex example
Post.findAll({
  where: {
    [Op.or]: [
      sequelize.where(sequelize.fn('char_length', sequelize.col('content')), 7),
      {
        content: {
          [Op.like]: 'Hello%'
        }
      },
      {
        [Op.and]: [
          { status: 'draft' },
          sequelize.where(sequelize.fn('char_length', sequelize.col('content')), {
            [Op.gt]: 10
          })
        ]
      }
    ]
  }
}); 
```

在最后一个复杂查询示例中，SQL 输出是:

```
SELECT
  ...
FROM "posts" AS "post"
WHERE (
  char_length("content") = 7
  OR
  "post"."content" LIKE 'Hello%'
  OR (
    "post"."status" = 'draft'
    AND
    char_length("content") > 10
  )
) 
```

Sequelize 支持原始 SQL 语句，这使得开发人员可以灵活地编写复杂的高性能 SQL 语句。结果也可以映射到对象实体实例。这里有一个例子:

```
// Callee is the model definition. This allows you to easily map a query to a predefined model
const projects = await sequelize.query('SELECT * FROM projects', {
  model: Projects,
  mapToModel: true // pass true here if you have any mapped fields
});
// Each element of `projects` is now an instance of Project 
```

顺序化的主要缺点是开发速度变慢了，而且[问题](https://github.com/sequelize/sequelize/issues/12956)堆积起来而没有解决。幸运的是，其中一个维护者[宣布](https://github.com/sequelize/sequelize/issues/12956)这个库将从 2021 年开始得到应有的关注。请注意，本文中的所有 ORM 库项目都是开源的，它们确实需要开发人员的帮助才能做得更好。

### 书架

*   **发射**:2013 年 3 月
*   [网站](https://bookshelfjs.org/)
*   [GitHub](https://github.com/bookshelf/bookshelf):22.4k 使用
*   [插件](https://bookshelfjs.org/#official-plugins)
*   **数据库** : PostgreSQL、MySQL、SQLite3

书架是我们现有的最古老和最基本的 ORM JavaScript 库之一。它构建在 Knex.js SQL Query Builder 之上，并从数据映射器模式中汲取了许多想法。它提供了额外的功能，例如:

*   eager 和嵌套-eager 关系加载
*   多态关联
*   支持一对一、一对多和多对多关系。

不幸的是没有内置的验证支持。但是，它可以通过第三方库(如 [`checkit`](https://github.com/tgriesser/checkit) )用代码实现。

在您的项目中安装书架如下:

```
$ npm install knex
$ npm install bookshelf

# Then add one of the following:
$ npm install pg
$ npm install mysql
$ npm install sqlite3 
```

安装程序代码如下所示:

```
// Setting up the database connection
const knex = require('knex')({
  client: 'mysql',
  connection: {
    host     : '127.0.0.1',
    user     : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test',
    charset  : 'utf8'
  }
})
const bookshelf = require('bookshelf')(knex)

// Define User model
const User = bookshelf.model('User', {
  tableName: 'users',
  posts() {
    return this.hasMany(Posts)
  }
})

// Define Post model
const Post = bookshelf.model('Post', {
  tableName: 'posts',
  tags() {
    return this.belongsToMany(Tag)
  }
})

// Define Tag model
const Tag = bookshelf.model('Tag', {
  tableName: 'tags'
})

// Unfortunate example of unreadable code
new User({id: 1}).fetch({withRelated: ['posts.tags']}).then((user) => {
  console.log(user.related('posts').toJSON())
}).catch((error) => {
  console.error(error)
}) 
```

您需要查阅 Knex.js 文档来了解如何执行查询和 CRUD 事务。书架的文档没有涉及到这个。

有趣的是， [Strapi](https://strapi.io/) ，一个无头 CMS，使用[书架](https://www.npmjs.com/package/strapi-connector-bookshelf)作为它的默认数据库连接器。但是，以下问题值得注意:

*   文档不是特别有用
*   在撰写本文时，该库已经五个月没有更新了

### 吃水线

*   **发射**:2013 年 5 月
*   [网站](https://waterlinejs.org)
*   [GitHub](https://github.com/balderdashy/waterline):8.5k 使用
*   [文档](https://sailsjs.com/documentation/concepts/models-and-orm)
*   **数据库**:本地磁盘/内存、MySQL、MongoDB 和 Postgres(官方适配器)
*   [社区数据库适配器](https://sailsjs.com/documentation/concepts/extending-sails/adapters/available-adapters) : Oracle、SAP、Cassandra、IBM、Apache Derby、Redis、Solr 等等

Waterline 是 Node.js 框架 [Sails.js](https://sailsjs.com/) 使用的默认 ORM。使用 Sails.js 开发项目时，构建自己的数据库 API 所需编写的代码量大大减少。这是通过使用约定胜于配置的理念和包含访问数据库和执行 CRUD 功能的样板代码的[蓝图 API](https://sailsjs.com/documentation/concepts/blueprints) 来实现的。此外，Sails.js 提供了一个命令行界面，可以帮助开发人员生成 API 路由，执行迁移和其他数据层功能。通过[类型定义](https://github.com/DefinitelyTyped/DefinitelyTyped)包可以获得类型脚本支持。

在本文中，我们将假设您希望将 Waterline ORM 作为一个独立的应用程序来使用，这是可能的。让我们看看如何安装和设置它。

安装要求您安装水线库，然后安装一个数据库适配器:

```
$ npm install --save waterline

# Install database adapters
$ npm install --save sails-mysql
$ npm install --save-dev sails-disk 
```

以下是安装代码的部分示例:

```
const Waterline = require('waterline');
const sailsDiskAdapter = require('sails-disk');
const waterline = new Waterline();

const userCollection = Waterline.Collection.extend({
  identity: 'user',
  datastore: 'default',
  primaryKey: 'id',

  attributes: {
    id: {
        type: 'number',
        autoMigrations: {autoIncrement: true}
    },
    firstName: {type:'string'},
    lastName: {type:'string'},

    // Add a reference to Pets
    pets: {
      collection: 'pet',
      via: 'owner'
    }
  }
});

waterline.registerModel(userCollection); 
```

以下是一些 CRUD 代码的部分示例:

```
(async ()=>{
    // First we create a user
    var user = await User.create({
      firstName: 'Neil',
      lastName: 'Armstrong'
    });

    // Then we create the pet
    var pet = await Pet.create({
      breed: 'beagle',
      type: 'dog',
      name: 'Astro',
      owner: user.id
    });

    // Then we grab all users and their pets
    var users = await User.find().populate('pets');
  })() 
```

下面是一个基本查询代码的示例:

```
var thirdPageOfRecentPeopleNamedMary = await Model.find({
  where: { name: 'mary' },
  skip: 20,
  limit: 10,
  sort: 'createdAt DESC'
}); 
```

在处理复杂查询时，文档似乎缺少这一部分。如果你打算使用 Sails.js，那么使用 Waterline ORM 是显而易见的。但是作为独立的，ORM 库面临以下问题:

*   文档与 Sails.js 文档混合在一起。
*   在撰写本文时，库包已经有九个月没有更新了。

### Objection.js

*   **发射**:2015 年 4 月
*   [网站](https://vincit.GitHub.io/objection.js/)
*   [GitHub](https://github.com/vincit/objection.js):5.7k 使用
*   [插件](https://vincit.github.io/objection.js/guide/plugins.html#_3rd-party-plugins)
*   **数据库** : SQLite3、Postgres 和 MySQL(包括所有 Knex.js 支持的数据库)

Objection.js 是一个最小的 Node.js ORM 库，旨在不妨碍您的工作，并使其易于访问 SQL 数据库。在这一类别中，Objection.js 是最年轻的，它似乎击败了许多反对使用 ORM 库的论点。

Objection.js 文档非常优秀。它写得很好，因为您可以很容易地找到构建应用程序数据层的清晰说明。语法清晰易懂。它构建在 Knex.js 之上，并具有对 TypeScript 的官方内置支持。它在一个 ORM 中包含了你需要的所有东西。

看看这些数字，这是相当令人惊讶的反对意见。js 并没有受到应有的欢迎。像 Sequelize 和 TypeORM 这样的 ORM 库确实提供了更多的特性，这可能是它们受欢迎的原因。然而，我认为 Objection.js 团队决定采用的一系列特性非常适合开源库。这意味着随着时间的推移会出现更少的错误，小团队能够及时解决它们。通过查看 [issues](https://github.com/Vincit/objection.js/issues) 选项卡，您可以看到这方面的证据，在撰写本文时，该选项卡有大约 50 个未解决的问题。

相比之下，Sequelize 和 TypeORM 的功能更强大，但不幸的是，它们给维护人员带来了大量的积压工作。目前，每个人都有 1，000 多个问题没有解决，而且对项目做出贡献的维护者的数量似乎也没有增加。

如果你对选择这个库有任何疑问，请查看这个 [testimonals](https://github.com/Vincit/objection.js/issues/1069) 链接。

让我们看看安装步骤和一些示例代码。首先，您需要安装 Objection.js、Knex.js 和一个数据库适配器:

```
npm install objection knex

# Install database adapter
npm install pg
npm install sqlite3
npm install mysql
npm install mysql2 
```

设置代码非常简单，几乎不需要任何解释:

```
const { Model } = require('objection');
const Knex = require('knex');

// Initialize knex.
const knex = Knex({
  client: 'sqlite3',
  useNullAsDefault: true,
  connection: {
    filename: 'example.db'
  }
});

// Give the Knex instance to Objection.
Model.knex(knex);

// Person model.
class Person extends Model {
  static get tableName() {
    return 'persons';
  }

  static get relationMappings() {
    return {
      children: {
        relation: Model.HasManyRelation,
        modelClass: Person,
        join: {
          from: 'persons.id',
          to: 'persons.parentId'
        }
      }
    };
  }
}

async function createSchema() {
  if (await knex.schema.hasTable('persons')) {
    return;
  }

  // Create database schema. You should use Knex migration files
  // to do this. We create it here for simplicity.
  await knex.schema.createTable('persons', table => {
    table.increments('id').primary();
    table.integer('parentId').references('persons.id');
    table.string('firstName');
  });
}

async function main() {
  // Create some people.
  const sylvester = await Person.query().insertGraph({
    firstName: 'Sylvester',

    children: [
      {
        firstName: 'Sage'
      },
      {
        firstName: 'Sophia'
      }
    ]
  });

  console.log('created:', sylvester);

  // Fetch all people named Sylvester and sort them by ID.
  // Load `children` relation eagerly.
  const sylvesters = await Person.query()
    .where('firstName', 'Sylvester')
    .withGraphFetched('children')
    .orderBy('id');

  console.log('sylvesters:', sylvesters);
}

createSchema()
  .then(() => main())
  .then(() => knex.destroy())
  .catch(err => {
    console.error(err);
    return knex.destroy();
  }); 
```

下面是一个基本查询的例子:

```
// query 1
const person = await Person.query().findById(1);

//query 2
const middleAgedJennifers = await Person.query()
  .select('age', 'firstName', 'lastName')
  .where('age', '>', 40)
  .where('age', '<', 60)
  .where('firstName', 'Jennifer')
  .orderBy('lastName'); 
```

基本查询的 SQL 输出:

```
-- query 1
select "persons".* from "persons" where "persons"."id" = 1

-- query 2
select "age", "firstName", "lastName"
from "persons"
where "age" > 40
and "age" < 60
and "firstName" = 'Jennifer'
order by "lastName" asc 
```

下面是一个复杂查询的示例:

```
const people = await Person.query()
  .select('persons.*', 'parent.firstName as parentFirstName')
  .innerJoin('persons as parent', 'persons.parentId', 'parent.id')
  .where('persons.age', '<', Person.query().avg('persons.age'))
  .whereExists(
    Animal.query()
      .select(1)
      .whereColumn('persons.id', 'animals.ownerId')
  )
  .orderBy('persons.lastName');

console.log(people[0].parentFirstName); 
```

复杂查询的 SQL 输出:

```
select "persons".*, "parent"."firstName" as "parentFirstName"
from "persons"
inner join "persons"
  as "parent"
  on "persons"."parentId" = "parent"."id"
where "persons"."age" < (
  select avg("persons"."age")
  from "persons"
)
and exists (
  select 1
  from "animals"
  where "persons"."id" = "animals"."ownerId"
)
order by "persons"."lastName" asc 
```

除了 Knex.js 已经提供的特性之外，Objection.js 还具有:

*   官方打字稿支持
*   支持生命周期挂钩
*   使用 [JSON 模式](http://json-schema.org/)语法的内置验证支持
*   [插件](https://vincit.github.io/objection.js/guide/plugins.html#_3rd-party-plugins)

图书馆维护得很好。对于 SQL 数据库，Objection.js 似乎是 JavaScript 应用程序的最佳 ORM 库。不幸的是，它不支持 NoSQL 数据库。但是我们的下一个特色库支持 NoSQL 数据库。

### 猫鼬

*   **发射**:2010 年 4 月
*   [网站](https://mongoosejs.com)
*   [GitHub](https://github.com/Automattic/mongoose/):1.4m 使用
*   [松弛](http://slack.mongoosejs.io/)
*   [插件](https://plugins.mongoosejs.io/)
*   **数据库** : MongoDB

如果您计划使用 MongoDB 作为您的数据库，那么 Mongoose 很可能是您的选择。它是目前 Node.js 世界中最流行的 ORM 库。Mongoose 使用模式语法来定义模型。其功能列表包括:

*   内置型铸件
*   确认
*   查询构建
*   通过中间件挂钩

Mongoose 只支持 MongoDB，所以安装只需要一个包:

```
npm install mongoose 
```

下面是设置代码的示例:

```
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test', {useNewUrlParser: true, useUnifiedTopology: true});

// With Mongoose, everything is derived from a Schema.
const kittySchema = new mongoose.Schema({
   name: {
    type: String,
    required: true
  }
});
const Kitten = mongoose.model('Kitten', kittySchema);

const fluffy = new Kitten({ name: 'fluffy' });
fluffy.save(function (err, fluffy) {
    if (err) return console.error(err);
    console.log(fluffy.name, 'saved!')
  }); 
```

在 Mongoose 中有两种定义查询的方法。下面是两个例子:

```
// With a JSON doc
Person.
  find({
    occupation: /host/,
    'name.last': 'Ghost',
    age: { $gt: 17, $lt: 66 },
    likes: { $in: ['vaporizing', 'talking'] }
  }).
  limit(10).
  sort({ occupation: -1 }).
  select({ name: 1, occupation: 1 }).
  exec(callback);

// Using query builder
Person.
  find({ occupation: /host/ }).
  where('name.last').equals('Ghost').
  where('age').gt(17).lt(66).
  where('likes').in(['vaporizing', 'talking']).
  limit(10).
  sort('-occupation').
  select('name occupation').
  exec(callback); 
```

当然，没有原始 SQL 选项，因为 MongoDB 是一个 NoSQL 数据库。MongoDB 也不支持事务。如果这对您的项目很重要，您将需要坚持使用 SQL 数据库。

与这里列出的所有其他开源 ORM 库相比，Mongoose 的一个关键优势是它的开发是由 [Tidelift](https://tidelift.com/) 平台赞助的。这意味着安全问题可以及早发现和修补。

一个缺点是 Mongoose 不正式支持 TypeScript。非正式地，您可以使用 TypeScript，但是维护您的模型需要一些额外的工作。幸运的是，我们将看到的下一个 ORM 库解决了这个问题。

## 类型脚本 ORM 库

在此类别中，此处列出的所有库都为 TypeScript 项目提供了一流的支持。您可以在 JavaScript 项目中使用它们，但是文档主要是为类型脚本代码编写的。

### 打字鹅

*   **发射**:2017 年 3 月
*   [网站](https://typegoose.GitHub.io/typegoose/)
*   [GitHub](https://github.com/typegoose/typegoose):2k 使用
*   **数据库** : MongoDB

Typegoose 是一个用 TypeScript 轻松编写 Mongoose 模型的“包装器”。这个库解决了必须维护单独的 Mongoose 模型和 TypeScript 接口的问题。使用 Typegoose，您只需要使用 Typegoose 接口定义您的模型模式。

在底层，它使用 Reflect 和 reflect-metadata API 来检索属性的类型，因此可以显著减少冗余。

在项目中安装 Typegoose 需要几个包:

```
npm i -s @typegoose/typegoose # install typegoose itself
npm i -s mongoose # install peer-dependency mongoose
npm i -D @types/mongoose # install all types for mongoose 
```

下面是一个用 JavaScript 编写的 Mongoose 模型的例子:

```
const kittenSchema = new mongoose.Schema({
  name: String
});

const Kitten = mongoose.model('Kitten', kittenSchema);

let document = await Kitten.create({ name: 'Kitty' });
// "document" has no types 
```

下面是使用 Typegoose 库用 TypeScript 编写的相同模型:

```
class KittenClass {
  @prop()
  public name?: string;
}

const Kitten = getModelForClass(KittenClass);

let document = await Kitten.create({ name: 'Kitty' });
// "document" has proper types of KittenClass 
```

以下代码示例显示了设置过程以及如何执行 CRUD 命令:

```
import { prop, getModelForClass } from '@typegoose/typegoose';
import * as mongoose from 'mongoose';

class User {
  @prop()
  public name?: string;

  @prop({ type: () => [String] })
  public jobs?: string[];
}

const UserModel = getModelForClass(User); // UserModel is a regular Mongoose Model with correct types

(async () => {
  await mongoose.connect('mongodb://localhost:27017/', { useNewUrlParser: true, useUnifiedTopology: true, dbName: "test" });

  const { _id: id } = await UserModel.create({ name: 'JohnDoe', jobs: ['Cleaner'] } as User); // an "as" assertion, to have types for all properties
  const user = await UserModel.findById(id).exec();

  console.log(user); // prints { _id: 59218f686409d670a97e53e0, name: 'JohnDoe', __v: 0 }
})(); 
```

因为 Typegoose 只是 ORM 库的一个 TypeScript 包装器，所以您必须查看 Mongoose 文档来了解如何执行 CRUD 任务。

### 字体

*   **投放**:2016 年 2 月 21 日
*   [网站](https://typeorm.io)
*   [GitHub](https://github.com/typeorm/typeorm):71.8k 使用
*   [松弛](https://typeorm.slack.com/join/shared_invite/zt-gej3gc00-hR~L~DqGUJ7qOpGy4SSq3g#/)
*   **数据库** : MySQL、MariaDB、Postgres、CockroachDB、SQLite、微软 SQL Server、Oracle、SAP Hana、sql.js、MongoDB

TypeORM 是目前最流行的为 TypeScript 项目构建的 ORM 库。它可以在许多平台上运行，包括:

*   节点. js
*   浏览器
*   手机版——Cordova、PhoneGap、Ionic、React Native 和 NativeScript
*   电子

该库还支持活动记录和数据映射模式，允许开发人员构建高质量、可伸缩和可维护的数据库驱动的应用程序。它深受 Hibernate、Doctrine、Entity Framework 等其他 ORM 的影响。这意味着拥有 Java 和 Ruby 背景的开发人员将会有宾至如归的感觉。

TypeORM 是一种可以在 Node.js、浏览器、Cordova、PhoneGap、Ionic、React Native、NativeScript、Expo 和 Electron 平台上运行的 ORM，可以与 TypeScript 和 JavaScript 一起使用。它的目标是始终支持最新的 JavaScript 特性，并提供额外的特性来帮助您开发任何类型的使用数据库的应用程序——从具有几个表的小型应用程序到具有多个数据库的大型企业应用程序。

安装 TypeORM 需要安装多个包，包括数据库适配器和附加的 TypeScript 包:

```
npm install typeorm --save

# You need to install reflect-metadata shim:
npm install reflect-metadata --save

# and import it somewhere in the global place of your app (for example in app.ts):
# import "reflect-metadata";

# You may need to install node typings:
npm install @types/node --save-dev

# Install a database driver:
npm install mysql --save (you can install mysql2 instead as well)
npm install pg --save
npm install sqlite3 --save
npm install mssql --save
npm install sql.js --save
# To make the Oracle driver work, you need to follow the installation instructions from their site.
npm install oracledb --save
# for SAP Hana
npm i @sap/hana-client
npm i hdb-pool
# for MongoDB (experimental)
npm install mongodb --save 
```

接下来，您需要在`tsconfig.json`中启用以下设置:

```
"emitDecoratorMetadata": true,
"experimentalDecorators": true, 
```

您可能还需要在编译器选项的`lib`部分启用`es6`，或者从`@types`安装`es6-shim`。

或者，您可以简单地使用 TypeORM CLI 工具来为您搭建项目，而不是手动设置 TypeORM 项目:

```
npm install typeorm -g
typeorm init --name MyProject --database mysql 
```

可以使用数据映射器实现来定义模型:

```
// Define entity model first
import {Entity, PrimaryGeneratedColumn, Column} from "typeorm";

@Entity()
export class User {

    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    firstName: string;

    @Column()
    lastName: string;

    @Column()
    age: number;

}

// Perform CRUD tasks
const repository = connection.getRepository(User);

const user = new User();
user.firstName = "Timber";
user.lastName = "Saw";
user.age = 25;
await repository.save(user);

const allUsers = await repository.find();
const firstUser = await repository.findOne(1); // find by id
const timber = await repository.findOne({ firstName: "Timber", lastName: "Saw" });

await repository.remove(timber); 
```

或者，您可以使用活动记录模式来定义您的模型:

```
import {Entity, PrimaryGeneratedColumn, Column, BaseEntity} from "typeorm";

@Entity()
export class User extends BaseEntity {

    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    firstName: string;

    @Column()
    lastName: string;

    @Column()
    age: number;

}

const user = new User();
user.firstName = "Timber";
user.lastName = "Saw";
user.age = 25;
await user.save();

const allUsers = await User.find();
const firstUser = await User.findOne(1);
const timber = await User.findOne({ firstName: "Timber", lastName: "Saw" });

await timber.remove(); 
```

TypeORM 使用自己的[查询构建器](https://typeorm.io/#/select-query-builder)提供了多种构建查询的方式。这是它的一个例子:

```
const firstUser = await connection
    .getRepository(User)
    .createQueryBuilder("user")
    .where("user.id = :id", { id: 1 })
    .getOne(); 
```

下面是 SQL 输出:

```
SELECT
    user.id as userId,
    user.firstName as userFirstName,
    user.lastName as userLastName
FROM users user
WHERE user.id = 1 
```

下面是一个复杂查询的示例:

```
const posts = await connection.getRepository(Post)
    .createQueryBuilder("post")
    .where(qb => {
        const subQuery = qb.subQuery()
            .select("user.name")
            .from(User, "user")
            .where("user.registered = :registered")
            .getQuery();
        return "post.title IN " + subQuery;
    })
    .setParameter("registered", true)
    .getMany(); 
```

虽然 TypeORM 似乎涵盖了为应用程序构建数据层所需的所有特性，但是有几个棘手的问题需要注意。最值得注意的是关于性能的问题，该问题已在本[未解决问题](https://github.com/typeorm/typeorm/issues/3857)中报告和记录。

由于该库支持大量的特性，未解决问题的积压已经堆积到相当高的水平，给核心维护人员带来了沉重的负担。这个问题已经由维护者[在这里](https://github.com/typeorm/typeorm/issues/3267)解决了，他们在这里讨论了 TypeORM 的未来。

不管怎样，TypeORM 是目前最流行的 TypeScript ORM。这意味着从长远来看，找到熟悉这个库的开发人员来支持你的项目会更容易。希望有更多的贡献者加入核心维护团队，帮助稳定 ORM。

### 米克罗托姆

*   **发射**:2018 年 3 月 11 日
*   [网站](https://mikro-orm.io/)
*   GitHub:206 使用
*   [松弛](https://join.slack.com/t/mikroorm/shared_invite/enQtNTM1ODYzMzM4MDk3LWM4ZDExMjU5ZDhmNjA2MmM3MWMwZmExNjhhNDdiYTMwNWM0MGY5ZTE3ZjkyZTMzOWExNDgyYmMzNDE1NDI5NjA)
*   **数据库** : MongoDB、MySQL、MariaDB、PostgreSQL 和 SQLite

MikroORM 是这个列表中最年轻的 Node.js TypeScript ORM 进入者之一。它支持 SQL 和 NoSQL 数据库，这是很多 ORM 都没有做到的惊人壮举。它深受[主义](https://www.doctrine-project.org/)和 [Nextras ORM](https://nextras.org/orm/) 的启发。熟悉这些功能的人应该会觉得使用 MikroORM 如鱼得水。

该库通过[身份映射模式](https://martinfowler.com/eaaCatalog/identityMap.html)针对事务和性能进行了优化。它还支持数据映射器模式。[文档](https://mikro-orm.io/docs/installation)非常出色，可以轻松导航到特定主题。使用较新的 ORM 库的一个主要优点是，它们被设计用来克服旧的和较大的库所面临的许多架构问题。

当您浏览我提供的代码示例时，您会注意到语法变得更容易理解了。这是构建长期可维护的大型项目的关键。现在让我们来看一下安装过程:

```
npm i -s @mikro-orm/core @mikro-orm/mongodb     # for mongo
npm i -s @mikro-orm/core @mikro-orm/mysql       # for mysql
npm i -s @mikro-orm/core @mikro-orm/mariadb     # for mariadb
npm i -s @mikro-orm/core @mikro-orm/postgresql  # for postgresql
npm i -s @mikro-orm/core @mikro-orm/sqlite      # for sqlite 
```

接下来，您需要在`tsconfig.json`中启用对[装饰者](https://www.typescriptlang.org/docs/handbook/decorators.html)和 [`esModuleInterop`](https://www.typescriptlang.org/tsconfig#esModuleInterop) 的支持:

```
"experimentalDecorators": true,
"emitDecoratorMetadata": true,
"esModuleInterop": true, 
```

然后调用`MikroORM.init`作为应用引导的一部分:

```
const orm = await MikroORM.init({
  entities: [Author, Book, BookTag],
  dbName: 'my-db-name',
  type: 'mongo', // one of `mongo` | `mysql` | `mariadb` | `postgresql` | `sqlite`
  clientUrl: '...', // defaults to 'mongodb://localhost:27017' for mongodb driver
});
console.log(orm.em); // access EntityManager via `em` property 
```

MikroORM 提供了一个命令行工具`@mikro-orm/cli`，您可以使用`npx`或通过本地安装来访问它，如下所示:

```
# manually
$ node node_modules/.bin/mikro-orm
# via npx
$ npx mikro-orm
# or via yarn
$ yarn mikro-orm 
```

命令行工具有助于开发过程，并可以帮助您执行以下任务:

*   模式管理
*   将 SQL 文件导入数据库
*   生成实体
*   数据库迁移

MikroORM 提供了三种定义实体类的方法。这里有一个使用[反射元数据](https://www.npmjs.com/package/reflect-metadata)语法的例子:

```
@Entity()
export class Book extends BaseEntity {

  @Property()
  title!: string;

  @ManyToOne(() => Author)
  author!: Author;

  @ManyToOne(() => Publisher, { wrappedReference: true, nullable: true })
  publisher?: IdentifiedReference<Publisher>;

  @ManyToMany({ entity: 'BookTag', fixedOrder: true })
  tags = new Collection<BookTag>(this);

} 
```

一旦定义了实体，就可以使用[实体管理器](https://mikro-orm.io/docs/entity-manager)来保存和查询数据:

```
// use constructors in your entities for required parameters
const author = new Author('Jon Snow', 'snow@wall.st');
author.born = new Date();

const publisher = new Publisher('7K publisher');

const book1 = new Book('My Life on The Wall, part 1', author);
book1.publisher = publisher;
const book2 = new Book('My Life on The Wall, part 2', author);
book2.publisher = publisher;
const book3 = new Book('My Life on The Wall, part 3', author);
book3.publisher = publisher;

// just persist books, author and publisher will be automatically cascade persisted
await orm.em.persistAndFlush([book1, book2, book3]);

// or one by one
orm.em.persist(book1);
orm.em.persist(book2);
orm.em.persist(book3);
await orm.em.flush(); // flush everything to database at once

// Update existing book
const book = await orm.em.findOne(Book, 1);
book.title = 'How to persist things...';

// no need to persist `book` as its already managed by the EM
await orm.em.flush();

// Retrieve all books
const books = await orm.em.find(Book, {});
for (const book of books) {
  console.log(book.title);
} 
```

实体的查询可以通过一个名为`FilterQuery`的 conditions 对象来完成。以下是不同的例子:

```
// search by entity properties
const users = await orm.em.find(User, { firstName: 'John' });

// for searching by reference you can use primary key directly
const id = 1;
const users = await orm.em.find(User, { organization: id });

// or pass unpopulated reference (including `Reference` wrapper)
const ref = await orm.em.getReference(Organization, id);
const users = await orm.em.find(User, { organization: ref });

// fully populated entities as also supported
const ent = await orm.em.findOne(Organization, id);
const users = await orm.em.find(User, { organization: ent });

// complex queries with operators
const users = await orm.em.find(User, { $and: [{ id: { $nin: [3, 4] } }, { id: { $gt: 2 } }] });

// you can also search for array of primary keys directly
const users = await orm.em.find(User, [1, 2, 3, 4, 5]);

// and in findOne all of this works, plus you can search by single primary key
const user1 = await orm.em.findOne(User, 1); 
```

该库还支持:

*   提取部分实体
*   获取分页结果
*   使用自定义 SQL 片段

要执行更复杂的查询，您可以使用[查询构建器](https://mikro-orm.io/docs/query-builder)。下面是一些示例代码:

```
const qb = orm.em.createQueryBuilder(Author);
qb.update({ name: 'test 123', type: PublisherType.GLOBAL }).where({ id: 123, type: PublisherType.LOCAL });

console.log(qb.getQuery());
// update `publisher2` set `name` = ?, `type` = ? where `id` = ? and `type` = ?

console.log(qb.getParams());
// ['test 123', PublisherType.GLOBAL, 123, PublisherType.LOCAL]

// run the query
const res1 = await qb.execute(); 
```

在幕后，MikroORM 的查询构建器使用 Knex.js，您可以通过`qb.getKnexQuery()`函数访问它。这意味着您想要构建和运行的所有复杂和原始的 SQL 查询都可以执行。因此，在您的技术堆栈中选择 MikroORM，您将获得灵活性和性能优势。查询构建器上的[文档](https://mikro-orm.io/docs/query-builder)有很多查询构建的例子——包括不同类型的连接——这里就不一一列举了。您将高兴地了解到，查询构建器提供了一个在开发期间显示其 SQL 输出的功能，而无需启用调试选项。这里有一个例子:

```
const qb = orm.em.createQueryBuilder(BookTag, 't');
qb.select(['b.*', 't.*'])
  .leftJoin('t.books', 'b')
  .where('b.title = ? or b.title = ?', ['test 123', 'lol 321'])
  .andWhere('1 = 1')
  .orWhere('1 = 2')
  .limit(2, 1);

console.log(qb.getQuery());
// select `b`.*, `t`.*, `e1`.`book_tag_id`, `e1`.`book_uuid_pk` from `book_tag` as `t`
// left join `book_to_book_tag` as `e1` ON `t`.`id` = `e1`.`book_tag_id`
// left join `book` as `b` ON `e1`.`book_uuid_pk` = `b`.`uuid_pk`
// where (((b.title = ? or b.title = ?) and (1 = 1)) or (1 = 2))
// limit ? offset ? 
```

令人关切的一个问题是，该图书馆还很年轻，用户数量很少。然而，该图书馆的创始人已经启用了 GitHub 赞助商功能，允许他们筹集资金，以便他们可以全职从事该项目。我相信这是一种比兼职做不同项目更好的开源开发方法。通过让全职开发人员从事开源项目，他们可以专注于维护库的质量，并确保将积压保持在最低限度。我真的希望他们能很快找到一个大赞助商。

### 普里斯马

*   **发布**:2019 年 4 月
*   [网站](https://www.prisma.io/)
*   [GitHub](https://github.com/prisma/prisma):5.7k 使用
*   **数据库** : PostgreSQL，MySQL，SQLite，SQL Server

Prisma 是本文中最新的打字稿 ORM。它将自己描述为“下一代 ORM ”,使应用程序开发人员能够轻松地使用数据库。它提供了以下工具:

*   **[Prisma Client](https://www.prisma.io/docs/concepts/components/prisma-client/)** :提供对数据库的类型安全访问的客户端库
*   **[Prisma Migrate](https://www.prisma.io/docs/concepts/components/prisma-migrate/)**([preview](https://www.prisma.io/docs/about/releases#preview)):一个迁移工具，当您对模式文件进行更改时，它会自动生成
*   Prisma Studio :一个浏览和管理数据库数据的现代图形用户界面

Prisma 与我们看到的所有其他 ORM 都非常不同。它不使用对象模型(实体类)，而是使用一个模式文件来映射所有的表和列。迁移工具使用该文件生成 SQL 迁移文件，客户端库使用该文件生成类型定义。所有生成的类型定义都存储在一个`.prisma/client/index.d.ts`文件夹中。下面是为`User`类型生成的表示的示例:

```
export declare type User = {
  id: string
  email: string
  name: string | null
} 
```

您可能已经注意到，模型中的`posts`引用没有出现在 TypeScript 定义中。推荐的解决方案是创建一个`User`类型的变体，如下所示:

```
import { Prisma } from '@prisma/client'
// Define a type that includes the relation to `Post`
type UserWithPosts = Prisma.UserGetPayload<{
  include: { posts: true }
}> 
```

当您编写查询时，将检查您的代码以确保您没有引用不存在的属性，并且您为每个属性分配了正确的数据类型。当您执行查询时，所有结果都将以普通 JavaScript 对象的形式返回。

传统的 ORM 通过将表映射到编程语言中的模型类，提供了一种面向对象的方式来处理关系数据库。这种方法导致许多由[对象关系阻抗不匹配](https://en.wikipedia.org/wiki/object%E2%80%93relational_impedance_mismatch)引起的问题。

建立一个 Prisma 项目是一个过程，你可以在这里找到完整的说明。目前，我们只是在评估。以下是基本安装步骤:

```
npm install prisma typescript ts-node @types/node --save-dev 
```

您需要如下更新`tsconfig.json`:

```
{
  "compilerOptions": {
    "sourceMap": true,
    "outDir": "dist",
    "strict": true,
    "lib": ["esnext"],
    "esModuleInterop": true
  }
} 
```

首先在位于`prisma/schema.prisma`的模式文件中创建应用程序的数据模型:

```
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_UR
}

model Post {
  id        Int      @default(autoincrement()) @id
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  title     String   @db.VarChar(255)
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
}

model Profile {
  id     Int     @default(autoincrement()) @id
  bio    String?
  user   User    @relation(fields: [userId], references: [id])
  userId Int     @unique
}

model User {
  id      Int      @default(autoincrement()) @id
  email   String   @unique
  name    String?
  posts   Post[]
  profile Profile?
} 
```

接下来，您需要使用`prisma migrate` CLI 工具将您的数据模型映射到数据库模式:

```
npx prisma migrate dev --name init --preview-feature 
```

我们将跳过安装过程，看看我们在`index.ts`中的设置代码:

```
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

async function main() {
  const allUsers = await prisma.user.findMany()
  console.log(allUsers)
}

main()
  .catch(e => {
    throw e
  })
  .finally(async () => {
    await prisma.$disconnect()
  }) 
```

下面是一个演示如何保存数据和查询记录的示例:

```
async function main() {
  await prisma.user.create({
    data: {
      name: 'Alice',
      email: 'alice@prisma.io',
      posts: {
        create: { title: 'Hello World' },
      },
      profile: {
        create: { bio: 'I like turtles' },
      },
    },
  })

  const allUsers = await prisma.user.findMany({
    include: {
      posts: true,
      profile: true,
    },
  })
  console.dir(allUsers, { depth: null })
} 
```

当您运行上述代码时，结果将作为 JavaScript 对象返回，如下所示:

```
[
  {
    email: 'alice@prisma.io',
    id: 1,
    name: 'Alice',
    posts: [
      {
        content: null,
        createdAt: 2020-03-21T16:45:01.246Z,
        id: 1,
        published: false,
        title: 'Hello World',
        authorId: 1,
      }
    ],
    profile: {
      bio: 'I like turtles',
      id: 1,
      userId: 1,
    }
  }
] 
```

Prisma 的文档看起来很漂亮，似乎有很多内容。不幸的是，我发现很难找到你需要的信息。要么是由于过于复杂的导航系统，要么是具体内容缺失。信息分布在多个部分，包括:

*   概念
*   指导
*   参考
*   支持/帮助文章

Prisma 是一个较新的库，它遵循不同的数据层构建理念。它似乎也比 MikroORM 发展得更快，尤其是因为它是在一年后推出的。

## 结论

最后，我想简单讨论一下反对在项目中使用 ORM 库的理由。主要论点包括:

*   庞大、低效的查询
*   使用图书馆的挫败感
*   迁移问题:保持实体类和数据库模式同步
*   使用原始 SQL 选项时失去类型安全性

你可以在这里和这里阅读所有反对使用 ORM 库的理由。

看了所有当前的 JavaScript 和 TypeScript ORM 库，您应该知道每一个都有不同的实现。大多数反对 ORM 库的观点已经被新的解决了，比如 Object.js 和 Prisma。如果您决定不使用 ORM 库，您将不得不决定组成您的数据层堆栈的各个工具和库。

在我看来，为你的项目选择一个 ORM 是最好的解决方案，因为这个原因:**文档**。

作为开发人员，我们很不擅长记录自己的代码。如果我们要实现一个定制的解决方案，或者实现一个不太知名的库，未来的维护者将很难让你的应用程序跟上它的业务需求。

然而，如果您使用一个文档化良好的 ORM 库，那么在您离开项目很久之后，他们在您的应用程序上工作会变得容易得多。这是因为 ORM 灌输了良好的代码实践，比如架构和模式，比如数据映射器。虽然这可能会引入一个学习曲线，但从长远来看更好。

我希望我已经提供了有用的信息，可以帮助您评估项目的 ORM 库。如果您需要建议，请选择最适合企业级项目的 TypeScript ORM 库。

## 分享这篇文章