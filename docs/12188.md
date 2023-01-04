# MySQL:MyISAM 表的利与弊

> 原文：<https://www.sitepoint.com/mysql-myisam-table-pros-con/>

与大多数数据库系统不同，MySQL 提供了许多表格类型。选择正确的类型对您的 web 应用程序至关重要，但是很少有开发人员意识到这一点，直到为时已晚。在本文中，我们研究 MyISAM 表。

MyISAM 是默认的 MySQL 表类型。如果您从未有意指定类型，您的表将使用 MyISAM 引擎。这可能是一种优势，原因有几个。

**1。简单性**
MyISAM 表很简单。如果您不熟悉 MySQL 或数据库，我建议您从 MyISAM 表开始。

在下面的例子中，我们将从 mysql >命令提示符下创建一个新的数据库和表。(你确实安装了 MySQL 吧？如果没有，看看[如何安装 MySQL](https://www.sitepoint.com/how-to-install-mysql/) 。)

```
 CREATE DATABASE mysqltest;
USE mysqltest;

CREATE TABLE mysqltest.articles (
id int UNSIGNED NOT NULL AUTO_INCREMENT,
title TEXT NOT NULL,
body MEDIUMTEXT,
PRIMARY KEY (id))
ENGINE = MyISAM; 
```

这将创建一个新的数据库 *mysqltest* ，以及一个名为 *articles* 的新表，其中包含列 id(一个自动生成的数字)、标题和正文。确定最佳的列数据类型非常复杂。

**2。速度**
MySQL 快。非常快。在正常情况下，基准测试表明 MyISAM 表胜过所有其他通用数据库，并且使用的系统资源更少。

**3。全文搜索**
考虑我们上面的表格。如果我们需要在标题和正文中搜索关键字，该怎么办？一个简单的解决方案是添加全文索引:

```
 ALTER TABLE mysqltest.articles ADD FULLTEXT alltext (title, body); 
```

现在，我们可以使用以下内容查找所有带有“数据库”和/或“文章”字样的文章:

```
 SELECT * FROM mysqltest.articles
WHERE MATCH(title, body) AGAINST ('database article'); 
```

我们甚至可以按最相关的文章排序，首先创建一个简单的搜索引擎:

```
 SELECT *, MATCH(title, body) AGAINST ('database article') AS rel
FROM mysqltest.articles
ORDER BY rel DESC; 
```

## MyISAM 表的缺点

有几种情况可能不适合使用 MyISAM。

**1。数据完整性差**
MyISAM 表不支持事务或外键约束。

考虑一个银行应用程序，其中您正在转账。这通常涉及两条 SQL UPDATE 语句；一个账户的借方，另一个账户的贷方。如果 MySQL 或服务器在错误的时间点出现故障，你可能会两个账户都有钱，或者都没钱。

**2。糟糕的崩溃恢复**
MySQL 是可靠的，但是 MyISAM 表在崩溃后可能会损坏。该问题通常可以使用修复表来修复，但这是一个额外的管理开销。

**3。表锁定**
MyISAM 使用表级锁定。当插入或更新一行时，对该表的所有其他更改都会被挂起，直到该请求完成。

要证明这是否会给 web 应用程序带来问题并不容易，但是一般来说，如果执行的插入/更新操作多于选择操作，那么 MyISAM 可能不是最好的表类型。

## 应该用 MyISAM 吗？

如果您是 MySQL 的新手，您的 web 应用程序很简单，必须很快，或者使用全文搜索，MyISAM 是一个理想的选择。但是，当数据完整性是首要任务时，不应该使用它。可能的应用包括内容管理系统、在线书签工具、RSS 阅读器或简单的搜索引擎。

注意，您可以在同一个数据库中混合使用不同的表类型。实际上，如果坚持使用单一的表类型，可能会减少开发人员的困惑。

另请参见:

*   [MySQL:InnoDB 表的利与弊](https://www.sitepoint.com/mysql-innodb-table-pros-cons/)
*   [如何安装 Apache](https://www.sitepoint.com/how-to-install-apache-on-windows/)
*   [如何安装 PHP](https://www.sitepoint.com/how-to-install-php-on-windows/)
*   [如何安装 MySQL](https://www.sitepoint.com/how-to-install-mysql/)
*   [如何使用 MySQL 外键加快数据库开发](https://www.sitepoint.com/mysql-foreign-keys-quicker-database-development/)

## 分享这篇文章