# SQL 与 NoSQL:区别

> 原文：<https://www.sitepoint.com/sql-vs-nosql-differences/>

四十多年来，SQL(结构化查询语言)数据库一直是主要的数据存储机制。随着 MySQL、PostgreSQL 和 SQLite 等网络应用和开源选项的兴起，使用在 20 世纪 90 年代末出现了爆炸式增长。

NoSQL 数据库自 20 世纪 60 年代就已经存在，但最近随着 MongoDB、CouchDB、Redis 和 Apache Cassandra 等流行选项的出现而受到关注。

您会发现许多教程解释了如何使用特定风格的 SQL 或 NoSQL，但是很少有人讨论为什么您应该选择一个而不是另一个。我希望填补这个空白。在本文中，我们将讨论基本的区别。在后面的后续文章中，我们将研究典型场景并确定最佳选择。

大多数例子适用于流行的 MySQL 和 T2 的 MongoDB 和 NoSQL 的数据库系统。其他 SQL/NoSQL 数据库类似，但在功能和语法上会有细微的差别。

## SQL 与 NoSQL 的圣战

在我们进一步讨论之前，让我们先澄清一些误解…

神话:NoSQL 取代 SQL
这就像说船被汽车取代了，因为它们是更新的技术。SQL 和 NoSQL 做同样的事情:存储数据。他们采取不同的方法，这可能有助于或阻碍你的项目。尽管感觉更新，并抓住最近的头条新闻，NoSQL 不是 SQL 的替代品— *它是一个替代*。

神话:NoSQL 比 SQL 好/差
一些项目更适合使用 SQL 数据库。有些更适合 NoSQL。有些人可以互换使用。这篇文章不可能是一个 SitePoint 的攻击，因为你不能在任何地方都应用相同的一揽子假设。

**误区:SQL vs NoSQL 是明显的区别**
这不一定是真的。一些 SQL 数据库采用了 NoSQL 特性，反之亦然。这些选择可能会变得越来越模糊，而 NewSQL 混合数据库可能会在未来提供一些有趣的选择。

**神话:语言/框架决定数据库**
我们已经习惯了技术栈，例如—

*   LAMP: Linux，Apache，MySQL (SQL)，PHP
*   MEAN: MongoDB (NoSQL)，Express，Angular，Node.js
*   。NET、IIS 和 SQL Server
*   Java，Apache 和 Oracle。

这些堆栈的发展有实际的、历史的和商业的原因——但不要认为它们是规则。你可以在你的 [PHP](http://php.net/manual/en/class.mongodb.php) 或者[中使用一个 MongoDB NoSQL 数据库。NET](http://docs.mongodb.org/ecosystem/drivers/csharp/) 项目。你可以在 Node.js 中连接到 [MySQL](https://www.npmjs.com/package/mysql) 或者 [SQL Server](https://www.npmjs.com/package/mssql) ，你可能找不到那么多教程和资源，但是你的需求应该决定数据库类型——*而不是语言*。

也就是说，不要故意刁难自己！选择一种不寻常的技术组合或者 SQL 和 NoSQL 的混合是可能的，但是你会发现寻找支持和雇佣有经验的开发人员更加困难。)

记住这一点，让我们看看主要的区别…

## SQL 表与 NoSQL 文档

SQL 数据库提供了相关数据表的存储。例如，如果您运行一个在线书店，图书信息可以添加到一个名为`book`的表中:

| 国际标准图书编号 | 标题 | 作者 | 格式 | 价格 |
| --- | --- | --- | --- | --- |
| 9780992461225 | JavaScript:新手到忍者 | 达伦·琼斯 | 电子书 | Twenty-nine |
| 9780994182654 | 跳跃启动 Git | 肖米克·戴特里 | 电子书 | Twenty-nine |

每一行都是不同的图书记录。设计死板；不能使用同一个表存储不同的信息，也不能在需要数字的地方插入字符串。

NoSQL 数据库存储类似 JSON 的字段-值对文档，例如

```
{
  ISBN: 9780992461225,
  title: "JavaScript: Novice to Ninja",
  author: "Darren Jones",
  format: "ebook",
  price: 29.00
}
```

类似的文档可以存储在一个**集合**中，它类似于一个 SQL 表。但是，您可以在任何文档中存储您喜欢的任何数据；NoSQL 数据库不会抱怨。例如:

```
 {
  ISBN: 9780992461225,
  title: "JavaScript: Novice to Ninja",
  author: "Darren Jones",
  year: 2014,
  format: "ebook",
  price: 29.00,
  description: "Learn JavaScript from scratch!",
  rating: "5/5",
  review: [
    { name: "A Reader", text: "The best JavaScript book I've ever read." },
    { name: "JS Expert", text: "Recommended to novice and expert developers alike." }
  ]
} 
```

SQL 表创建了一个严格的数据模板，所以很难出错。NoSQL 更加灵活和宽容，但是能够在任何地方存储任何数据会导致一致性问题。

## SQL 模式 vs NoSQL 模式

在 SQL 数据库中，只有在定义了被称为*模式*的表和字段类型之后，才能添加数据。该模式可选地包含其他信息，例如—

*   **主键** —适用于单个记录的唯一标识符，如 ISBN
*   **索引** —对常用查询字段进行索引，以帮助快速搜索
*   **关系** —数据字段之间的逻辑链接
*   诸如**的功能触发**和**存储过程**。

在开发任何业务逻辑来操作数据之前，必须设计和实现您的数据模式。以后可以进行更新，但是大的改变可能会很复杂。

在 NoSQL 数据库中，可以随时随地添加数据。不需要预先指定文档设计甚至集合。例如，在 MongoDB 中，下面的语句将在一个新的`book`集合中创建一个新文档，如果之前没有创建过的话:

```
db.book.insert(
  ISBN: 9780994182654,
  title: "Jump Start Git",
  author: "Shaumik Daityari",
  format: "ebook",
  price: 29.00
);
```

*(MongoDB 会自动为集合中的每个文档添加一个惟一的`_id`值。您可能仍然想要定义索引，但是如果需要的话，可以稍后再做。)*

NoSQL 数据库可能更适合初始数据要求难以确定的项目。也就是说，不要把困难误认为懒惰:在项目开始时忽略设计一个好的数据存储会导致以后的问题。

## SQL 规范化与 NoSQL 反规范化

假设我们想要将出版商信息添加到书店数据库中。一个出版商可以提供多个书目，因此在 SQL 数据库中，我们创建了一个新的`publisher`表:

| 身份证明（identification） | 名字 | 国家 | 电子邮件 |
| --- | --- | --- | --- |
| SP001 | 站点点 | 澳大利亚 | feedback@sitepoint.com |

然后，我们可以将一个`publisher_id`字段添加到我们的`book`表中，该表通过`publisher.id`引用记录:

| 国际标准图书编号 | 标题 | 作者 | 格式 | 价格 | 发布者 id |
| --- | --- | --- | --- | --- | --- |
| 9780992461225 | JavaScript:新手到忍者 | 达伦·琼斯 | 电子书 | Twenty-nine | SP001 |
| 9780994182654 | 跳跃启动 Git | 肖米克·戴特里 | 电子书 | Twenty-nine | SP001 |

这最小化了数据冗余；我们不会重复每本书的出版商信息——只是引用它。这种技术被称为规范化，具有实际的好处。我们可以在不改变`book`数据的情况下更新单个发布者。

我们可以在 NoSQL 使用标准化技术。`book`收藏中的文档—

```
{
  ISBN: 9780992461225,
  title: "JavaScript: Novice to Ninja",
  author: "Darren Jones",
  format: "ebook",
  price: 29.00,
  publisher_id: "SP001"
}
```

—引用`publisher`集合中的文档:

```
{
  id: "SP001"
  name: "SitePoint",
  country: "Australia",
  email: "feedback@sitepoint.com"
}
```

然而，这并不总是可行的，原因将在下面变得明显。我们可以选择不规范我们的文档，并为每本书重复出版商信息:

```
{
  ISBN: 9780992461225,
  title: "JavaScript: Novice to Ninja",
  author: "Darren Jones",
  format: "ebook",
  price: 29.00,
  publisher: {
    name: "SitePoint",
    country: "Australia",
    email: "feedback@sitepoint.com"
  }
}
```

这导致了更快的查询，但是更新多条记录中的发布者信息将会慢得多。

## SQL 关系连接与 NoSQL

SQL 查询提供了一个强大的连接子句。我们可以使用一条 SQL 语句获得多个表中的相关数据。例如:

```
SELECT book.title, book.author, publisher.name
FROM book
LEFT JOIN book.publisher_id ON publisher.id;
```

这将返回所有书名、作者和相关的出版商名称(假设已经设置了一个)。

NoSQL 没有等同于 JOIN 的功能，这可能会让那些有 SQL 经验的人感到震惊。如果我们使用如上所述的规范化集合，我们将需要获取所有的`book`文档，检索所有相关的`publisher`文档，并在程序逻辑中手动链接这两个文档。这是反规格化经常是必要的一个原因。

## SQL 与 NoSQL 数据完整性

大多数 SQL 数据库允许您使用外键约束来实施数据完整性规则(除非您仍在使用 MySQL 中较旧的、已失效的 MyISAM 存储引擎)。我们的书店可以—

*   确保所有图书都有一个有效的`publisher_id`代码，该代码与`publisher`表中的一个条目相匹配，并且
*   如果一个或多个图书被分配给出版商，不允许他们被删除。

模式强制数据库遵循这些规则。开发者或用户不可能添加、编辑或删除记录，这可能导致无效数据或孤立记录。

相同的数据完整性选项在 NoSQL 数据库中不可用；您可以存储您想要的内容，而不考虑任何其他文档。理想情况下，单个文档将是关于一个项目的所有信息的唯一来源。

## SQL 与 NoSQL 事务

在 SQL 数据库中，可以在一个事务中执行两个或多个更新，这是一个保证成功或失败的全有或全无包装器。例如，假设我们的书店包含`order`和`stock`表。当订购一本书时，我们向`order`表中添加一条记录，并减少`stock`表中的库存数量。如果我们分别执行这两个更新，一个可能成功，另一个可能失败——从而使我们的数据不同步。在一个事务中放置相同的更新可以确保要么都成功，要么都失败。

在 NoSQL 数据库中，对单个文档的修改是原子性的。换句话说，如果您正在更新一个文档中的三个值，要么全部成功更新，要么保持不变。然而，对于多个文档的更新，没有等价的事务。有类似于[事务的选项](http://docs.mongodb.org/manual/core/write-operations-atomicity/)，但是，在编写本文时，这些选项必须在您的代码中手动处理。

## SQL vs NoSQL CRUD 语法

创建、读取、更新和删除数据是所有数据库系统的基础。本质上—

*   SQL 是一种轻量级声明性语言。它看起来很强大，已经成为一个国际标准，尽管大多数系统实现的语法略有不同。
*   NoSQL 数据库使用类似 JavaScripty 的查询和类似 JSON 的参数！基本操作很简单，但是对于更复杂的查询，嵌套的 JSON 会变得越来越复杂。

快速对比:

| 结构化查询语言 | NoSQL |
| --- | --- |
| 插入新的图书记录 |
| 

```
INSERT INTO book (
  `ISBN`, `title`, `author`
)
VALUES (
  '9780992461256', 
  'Full Stack JavaScript', 
  'Colin Ihrig & Adam Bretz'
);
```

 | 

```
db.book.insert({
  ISBN: "9780992461256",
  title: "Full Stack JavaScript",
  author: "Colin Ihrig & Adam Bretz"
});
```

 |
| 更新图书记录 |
| 

```
UPDATE book
SET price = 19.99
WHERE ISBN = '9780992461256'
```

 | 

```
db.book.update(
  { ISBN: '9780992461256' },
  { $set: { price: 19.99 } }
);
```

 |
| 返回所有超过 10 美元的图书 |
| 

```
SELECT title FROM book
WHERE price > 10;
```

 | 

```
db.book.find(
  { price: { &gt;: 10 } },
  { _id: 0, title: 1 }
);
```

第二个 JSON 对象被称为**投影**:它设置返回哪些字段(默认情况下返回`_id`，因此需要取消设置)。 |
| 计算 SitePoint 图书的数量 |
| 

```
SELECT COUNT(1) FROM book
WHERE publisher_id = 'SP001';
```

 | 

```
db.book.count({
  "publisher.name": "SitePoint"
});
```

这假定使用了非规范化的文档。 |
| 返回书籍格式类型的数量 |
| 

```
SELECT format, COUNT(1) AS `total`
FROM book
GROUP BY format;
```

 | 

```
db.book.aggregate([
  { $group:
    { 
      _id: "$format", 
      total: { $sum: 1 } 
    }
  }
]);
```

这就是所谓的**聚合**:从原始文档集计算出一个新的文档集。 |
| 删除所有 SitePoint 书籍 |
| 

```
DELETE FROM book
WHERE publisher_id = 'SP001';
```

或者，如果适当地指定了外键，也可以删除`publisher`记录并将其级联到关联的`book`记录。 | 

```
db.book.remove({
  "publisher.name": "SitePoint"
});
```

 |

## SQL 与 NoSQL 性能对比

也许是最有争议的比较，NoSQL 经常被认为比 SQL 快。这并不奇怪；NoSQL 更简单的非规范化存储允许您在一次请求中检索特定项目的所有信息。不需要相关的连接或复杂的 SQL 查询。

也就是说，您的项目设计和数据需求将产生最大的影响。设计良好的 SQL 数据库几乎肯定会比设计糟糕的 NoSQL 数据库性能更好，反之亦然。

## SQL 与 NoSQL 扩展

随着数据的增长，您可能会发现有必要在多台服务器之间分配负载。对于基于 SQL 的系统来说，这可能很棘手。你如何分配相关数据？集群可能是最简单的选择；多台服务器访问同一个中央存储，但即使这样也存在挑战。

NoSQL 更简单的数据模型可以使这一过程更容易，许多模型从一开始就具有伸缩功能。那是一种概括，遇到这种情况就要寻求专家的建议。

## SQL 与 NoSQL 的实用性

最后，让我们考虑安全和系统问题。最受欢迎的 NoSQL 数据库已经存在了几年；它们比更成熟的 SQL 产品更容易出现问题。[很多](https://www.trustwave.com/Resources/SpiderLabs-Blog/Mongodb---Security-Weaknesses-in-a-typical-NoSQL-database/) [问题](http://developer.olery.com/blog/goodbye-mongodb-hello-postgresql/) [有](http://www.information-age.com/technology/security/123459001/major-security-alert-40000-mongodb-databases-left-unsecured-internet) [被](http://www.theregister.co.uk/2015/07/21/drongo_mongodbs_spew_600_terabytes_of_unauthenticated_data/) [举报过](https://aphyr.com/posts/322-call-me-maybe-mongodb-stale-reads)，但大多数归结为一个问题:*知识*。

开发人员和系统管理员对较新的数据库系统缺乏经验，所以会犯错误。选择 NoSQL 是因为它感觉更新鲜，或者因为您不可避免地想要避免模式设计，这将导致以后的问题。

## SQL 与 NoSQL 概要

SQL 和 NoSQL 数据库以不同的方式做同样的事情。有可能选择一个选项，然后切换到另一个选项，但是一个小小的计划可以节省时间和金钱。

**SQL 最理想的项目:**

*   可以预先确定的逻辑相关离散数据要求
*   数据完整性至关重要
*   基于标准的成熟技术，具有良好的开发人员体验和支持。

**NoSQL 最理想的项目:**

*   不相关、不确定或不断变化的数据需求
*   更简单或更宽松的项目目标，能够立即开始编码
*   速度和可扩展性至关重要。

对于我们的书店来说，SQL 数据库似乎是最实用的选择——尤其是当我们引入需要强大事务支持的电子商务设施时。在下一篇文章中，我们将进一步讨论项目场景，并确定 SQL 或 NoSQL 数据库是否是最佳解决方案。

## 分享这篇文章