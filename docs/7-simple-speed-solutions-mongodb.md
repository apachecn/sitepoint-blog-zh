# MongoDB 的 7 个简单速度解决方案

> 原文：<https://www.sitepoint.com/7-simple-speed-solutions-mongodb/>

![MongoDB Logo](img/0db0c28990a22a60c3f1b9f4e9ec03b1.png)

MongoDB 是一个快速的 NoSQL 数据库。不幸的是，它并不能解决所有的性能问题，一个复杂的查询就能让您的代码停止工作。我最近遭受了这种命运，当您的应用程序突然变得不稳定时，可能很难知道去哪里看。我希望这些建议能帮助你避免我所经历的痛苦！

## 1.检查您的 MongoDB 日志

默认情况下，MongoDB 记录所有耗时超过 100 毫秒的查询。它的位置是在你的配置的`systemLog.path`设置中定义的，在基于 Debian 的发行版如 Ubuntu 中通常是`/var/log/mongodb/mongod.log`。

日志文件可能很大，因此您可能希望在分析之前清除它。从 **mongo** 命令行控制台，输入:

```
use admin;
db.runCommand({ logRotate : 1 });
```

将启动一个新的日志文件，旧数据将保存在一个名为备份日期和时间的文件中。您可以删除备份或将其移动到其他地方以供进一步分析。

当用户访问您的系统时，查看日志也很有用。例如:

```
tail -f /var/log/mongodb/mongod.log
```

默认值是合理的，但是您可以配置[日志级别详细程度](https://docs.mongodb.org/manual/reference/method/db.setLogLevel/)或者修改[分析参数](https://docs.mongodb.org/manual/reference/method/db.setProfilingLevel/)并将查询时间更改为 100 毫秒之外的值。最初，您可以将它设置为 1 秒，以捕捉最糟糕的违规查询，然后在每成功修复一次后，将它减半。

注意包含“COMMAND”的行，其执行时间以毫秒为单位。例如:

```
2016-02-12T11:05:08.161+0000 I COMMAND  
    [conn563] command project.$cmd 
    command: count { 
        count: "test", 
        query: { published: { $ne: false }, 
        country: "uk" } 
    } 
    planSummary: IXSCAN { country: 1 } 
    keyUpdates:0 
    writeConflicts:0 
    numYields:31 
    reslen:44 
    locks: { 
        Global: { 
            acquireCount: { r: 64 } 
        }, 
        MMAPV1Journal: { 
            acquireCount: { r: 32 } 
        }, 
        Database: { 
            acquireCount: { r: 32 } 
        }, 
        Collection: { 
            acquireCount: { R: 32 } 
        } 
    } 403ms
```

这将帮助您确定潜在的瓶颈在哪里。

## 2.分析你的疑问

像许多数据库一样，MongoDB 提供了一个 [`explain`工具](https://docs.mongodb.org/manual/tutorial/analyze-query-plan/)，它揭示了数据库操作是如何工作的。您可以将`explain('executionStats')`添加到查询中。例如:

```
db.user.find(
  { country: 'AU', city: 'Melbourne' }
).explain('executionStats');
```

或者将其追加到集合中:

```
db.user.explain('executionStats').find(
  { country: 'AU', city: 'Melbourne' }
);
```

这将返回一个很大的 JSON 结果，但是有两个主要值需要检查:

*   `executionStats.nReturned` —返回的文件数量，以及
*   `executionStats.totalDocsExamined` —为查找结果而扫描的文档数量。

如果检查的文档数量大大超过返回的数量，查询可能效率不高。在最坏的情况下，MongoDB 可能必须扫描集合中的每个文档。因此，查询将受益于索引的使用。

有关更多信息和示例，请参考 [MongoDB 手册](https://docs.mongodb.org/manual/)中的[分析查询性能](https://docs.mongodb.org/manual/tutorial/analyze-query-plan/)和 [db.collection.explain()](https://docs.mongodb.org/manual/reference/method/db.collection.explain/) 。

## 3.添加适当的索引

NoSQL 数据库需要索引，就像它们的关系表兄弟一样。索引由一组一个或多个字段组成，以加快查询速度。例如，您可以索引一个`user`集合中的`country`字段。当查询搜索“AU”时，MongoDB 可以在索引中找到它，并引用所有匹配的文档，而不必扫描整个`user`集合。

使用`createIndex`创建索引。按升序对`user`集合中的`country`字段进行索引的最基本命令:

```
db.user.createIndex({ country: 1 });
```

大多数索引可能是单个字段，但是您也可以在两个或多个字段上创建复合索引。例如:

```
db.user.createIndex({ country: 1, city: 1 });
```

有很多索引选项，更多信息请参考 MongoDB 手册[索引介绍](https://docs.mongodb.org/manual/core/indexes-introduction/)。

## 4.分类时要小心

您几乎肯定希望对结果进行排序，例如，按照国家代码升序返回所有用户:

```
db.user.find().sort({ country: 1 });
```

当您定义了索引时，排序会有效地工作。上面定义的单一或复合指数都是合适的。

如果没有定义索引，MongoDB 必须自己对结果进行排序，这在分析大量返回的文档时会有问题。数据库对排序操作施加了 32MB 的内存限制，以我的经验来看，1000 个相对较小的文档就足以将它推到极限。MongoDB 不一定会返回错误—只是一组空记录。

排序限制可能会以意想不到的方式出现。假设你有一个关于`country`代码的索引，就像之前一样:

```
db.user.createIndex({ country: 1 });
```

一个查询现在对`country`和`city`都按升序排序:

```
db.user.find().sort({ country: 1, city: 1 });
```

虽然可以使用`country`索引，但是 MongoDB 仍然必须按照辅助`city`字段本身进行排序。这很慢，可能会超过 32MB 的排序内存限制。因此，您应该创建一个复合索引:

```
db.user.createIndex({ country: 1, city: 1 });
```

排序操作现在已被完全索引，并将快速运行。您还可以按照相反的顺序`country`和`city`进行排序，因为 MongoDB 可以从索引的末尾开始，并向后工作。例如:

```
db.user.find().sort({ country: -1, city: -1 });
```

但是，如果您尝试按国家降序而城市升序排序，就会出现问题:

```
db.user.find().sort({ country: -1, city: 1 });
```

我们的索引无法使用，因此您必须禁止无索引的二级排序标准，或者创建另一个合适的索引:

```
db.user.createIndex({ country: -1, city: 1 });
```

同样，这也可以用于颠倒顺序的查询:

```
db.user.find().sort({ country: 1, city: -1 });
```

## 5.创建两个或更多连接对象

在构建应用程序时，您可以使用一个持久数据库连接对象来提高效率，该对象可在所有查询和更新中重用。

MongoDB 按照从每个客户端连接接收命令的顺序运行所有命令。虽然您的应用程序可能会对数据库进行异步调用，但是每个命令都是同步排队的，并且必须在处理下一个命令之前完成。如果您有一个复杂的查询需要十秒钟来运行，那么没有其他人可以在同一时间在同一连接上与您的应用程序进行交互。

通过定义多个数据库连接对象可以提高性能。例如:

1.  一个处理大多数快速查询
2.  一个处理较慢的文档插入和更新
3.  一个用于处理复杂的报告生成。

每个对象都被视为一个独立的数据库客户机，不会延迟其他对象的处理。应用程序应该保持响应。

## 6.设置最大执行时间

MongoDB 命令需要运行多久就运行多久。执行缓慢的查询可能会阻碍其他查询，您的 web 应用程序最终可能会超时。这可能会在 Node.js 程序中引发各种奇怪的不稳定问题，这些程序会愉快地继续等待异步回调。

您可以使用 [maxTimeMS()](https://docs.mongodb.org/v3.2/reference/method/cursor.maxTimeMS/) 指定以毫秒为单位的时间限制:例如，允许 100 毫秒(十分之一秒)来查询`user`集合中的文档，其中`city`字段以字母“A”开头:

```
db.user.find({ city: /^A.+/i }).maxTimeMS(100);
```

您应该为任何可能花费大量时间的命令设置一个合理的`maxTimeMS`值。不幸的是，MongoDB 不允许定义全局超时值，必须为单个查询设置该值(尽管有些库可能会自动应用默认值)。

## 7.重建您的索引

如果你对你的结构是高效的感到满意，但是查询仍然运行缓慢，你可以尝试在每个集合上重建索引。例如，从 **mongo** 命令行重建`user`集合索引:

```
db.user.reIndex();
```

如果所有其他的都失败了，你可以考虑一个[数据库修复](https://docs.mongodb.org/manual/reference/command/repairDatabase/)来发现和修复任何问题。这应被视为所有其他选择都已用尽时的最后手段。在继续之前，我建议使用 [mongodump](https://docs.mongodb.org/manual/reference/program/mongodump/) 或其他合适的方法进行完整备份。

愿您所有的 MongoDB 查询保持快速高效！如果你有更多的表演技巧，请告诉我。

## 分享这篇文章