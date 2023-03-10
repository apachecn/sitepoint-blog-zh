# MySQL 事务陷阱&好的部分

> 原文：<https://www.sitepoint.com/mysql-transaction-gotchas-good-parts/>

在我之前的文章[“MySQL 事务&为什么你不能在 PHP 中模拟它们”](https://www.sitepoint.com/mysql-transactions-php-emulation)中，我们讨论了为什么事务是有用的，以及一些简单的 SQL 命令如何使你的应用程序更加健壮。但是在 web 开发人员的生活中，很少有事情是那么容易的…

## 无法回滚的语句

不幸的是，回滚并不是对每个数据库操作的全局撤销。如果对模式进行基本更改，任何现有的事务都将被提交，并且更改将在它自己的单个事务中运行。需要注意的声明有:

*   创建数据库
*   改变数据库
*   删除数据库
*   创建表格
*   更改表格
*   翻桌
*   重命名表格
*   截断表格
*   创建索引
*   下降指数
*   创建事件
*   丢弃事件
*   创建功能
*   丢弃功能
*   创建过程
*   丢弃过程

本质上，您不能撤消主要的数据库更改，例如:

```
 START TRANSACTION; 

DROP TABLE MyImportantData;
-- existing (empty) transaction is COMMIT-ed
-- table is dropped permanently

ROLLBACK;
-- no chance, mate - your data's gone 
```

**note:** TEMPORARY tables

可以创建、更改和删除临时表，而不会导致隐式提交。然而，也不可能回滚这些操作。

## 保存点

我们已经听够了例外，所以让我们看看另一个好的部分。保存点是事务中有效命名的位置。您可以回滚到任何保存点，而不会影响早期的 SQL 更新…这有点像 Photoshop 中的历史调色板。

一个示例是演示保存点的最简单方法:

```
 START TRANSACTION;

-- add record to tableA
INSERT INTO tableA VALUES (1,2,3);

-- create a savepoint
SAVEPOINT tableAupdated;

-- add record to tableB
INSERT INTO tableB VALUES (4,5,6);

-- Whoops! Let's undo the tableB updates...
ROLLBACK TO tableAupdated;

-- Only tableA is updated
COMMIT; 
```

您可以在事务中设置并回滚到任意数量的保存点标识符。

或者，您可以使用以下方法删除保存点:

```
 RELEASE SAVEPOINT savepointName; 
```

一旦事务发生提交或回滚，所有保存点都将被删除。

事务和保存点易于使用，可以保护您宝贵的 InnoDB 表数据。还有继续使用 MyISAM 的理由吗？

**note:**Want more?

如果你想阅读更多 Craig 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章