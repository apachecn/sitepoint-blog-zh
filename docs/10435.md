# PHP 开发人员犯的 10 大 MySQL 错误

> 原文：<https://www.sitepoint.com/mysql-mistakes-php-developers/>

*通过我们的截屏了解更多关于 MySQL 的信息[命令行上的 MySQL](https://www.sitepoint.com/premium/screencasts/mysql-on-the-command-line)。*

数据库是大多数 web 应用程序的基本组件。如果你正在使用 PHP，你可能正在使用 MySQL——LAMP 栈的一个组成部分。

PHP 相对容易，大多数新开发人员可以在几个小时内编写功能代码。然而，构建一个稳固、可靠的数据库需要时间和专业知识。以下是我犯过的十个最严重的 MySQL 错误(有些适用于任何语言/数据库)…

## 1.使用 MyISAM 而不是 InnoDB

MySQL 有很多数据库引擎，但是你最有可能遇到的是 [MyISAM](https://www.sitepoint.com/mysql-myisam-table-pros-con/) 和 [InnoDB](https://www.sitepoint.com/mysql-innodb-table-pros-cons/) 。
T5 默认使用 MyISAM。然而，除非您正在创建一个非常简单或实验性的数据库，否则这几乎肯定是错误的选择！MyISAM 不支持外键约束或事务，而外键约束或事务对于数据完整性至关重要。此外，每当插入或更新记录时，整个表都会被锁定；随着使用量的增加，这会对性能产生不利影响。

解决方案很简单:使用 InnoDB。

## 2.使用 PHP 的 mysql 函数

PHP 从第一天起就提供了 MySQL 库函数(或者说几乎没有区别)。很多应用依赖于 mysql_connect、mysql_query、mysql_fetch_assoc 等。但是 [PHP 手册声明](http://uk2.php.net/manual/en/mysqli.overview.php):

> 如果您使用的是 MySQL 版本 4.1.3 或更高版本，强烈建议您使用 mysqli 扩展。

MySQL 或 MySQL 改进扩展有几个优点:

*   (可选的)面向对象的接口
*   准备好的语句(有助于防止 SQL 注入攻击并提高性能)
*   多语句和事务支持

或者，如果你想支持多个数据库，你应该考虑 PDO。

## 3.不净化用户输入

这大概应该是#1: **永远不要相信用户输入**。使用服务器端 PHP 验证每个字符串——不要依赖 JavaScript。最简单的 SQL 注入攻击依赖于代码，例如:

```
 $username = $_POST["name"];
$password = $_POST["password"];
$sql = "SELECT userid FROM usertable WHERE username='$username' AND password='$password';";
// run query... 
```

这可以通过在用户名字段中输入“`admin'; --`”来破解。SQL 字符串将等同于:

```
 SELECT userid FROM usertable WHERE username='admin'; 
```

狡猾的黑客可以以“管理员”的身份登录；他们不需要知道密码，因为它在 SQL 之外被注释掉了。

## 4.不使用 UTF-8

我们这些在美国、英国和澳大利亚的人很少考虑英语以外的语言。我们愉快地完成了我们的杰作，却发现它不能用于别处。

UTF-8 解决了许多国际化问题。尽管在 PHP 6.0 版之前不会得到正确的支持，但你可以将 MySQL 字符集设置为 UTF-8。

## 5.偏爱 PHP 胜过 SQL

当你刚接触 MySQL 时，用你所知道的语言解决问题是很诱人的。这会导致不必要的和更慢的代码。例如，不是使用 MySQL 的原生 [AVG()函数](http://dev.mysql.com/doc/refman/5.1/en/group-by-functions.html#function_avg)，而是使用 PHP 循环通过对记录集中的所有值求和来计算平均值。

还要注意 PHP 循环中的 SQL 查询。通常，运行一个查询然后遍历结果会更有效。

一般来说，在分析数据时要利用数据库的优势。一点 SQL 知识大有帮助。

## 6.没有优化您的查询

99%的 PHP 性能问题都是由数据库引起的，一个糟糕的 SQL 查询就可能对您的 web 应用程序造成严重破坏。MySQL 的 [EXPLAIN 语句](http://dev.mysql.com/doc/refman/5.1/en/using-explain.html)、[查询分析器](http://dev.mysql.com/tech-resources/articles/using-new-query-profiler.html)，以及[多](http://www.jetprofiler.com/) [其他](http://myprofi.sourceforge.net/) [工具](http://dev.mysql.com/doc/refman/5.1/en/slow-query-log.html)都可以帮你找到那个流氓 SELECT。

## 7.使用错误的数据类型

MySQL 提供了一系列数字、字符串和时间数据类型。如果要存储日期，请使用日期或日期时间字段。使用整数或字符串会使 SQL 查询更加复杂，如果不是不可能的话。

发明自己的数据格式往往很有诱惑力；例如，在 string 中存储序列化的 PHP 对象。数据库管理可能更容易，但 MySQL 将成为一个愚蠢的数据存储，它可能会导致以后的问题。

## 8.在选择查询中使用*

永远不要使用*返回表中的所有列——这很懒。您应该只提取您需要的数据。即使您需要每个字段，您的表也不可避免地会发生变化。

## 9.索引不足或索引过多

根据一般经验，索引应该应用于选择查询的 WHERE 子句中命名的任何列。

例如，假设我们有一个带有数字 ID(主键)和电子邮件地址的用户表。在登录过程中，MySQL 必须通过搜索电子邮件来找到正确的 ID。有了索引，MySQL 可以使用快速搜索算法几乎立即找到电子邮件。如果没有索引，MySQL 必须按顺序检查每条记录，直到找到地址。

向每一列添加索引是很诱人的，但是，在每次插入或更新表的过程中都会重新生成索引。这会影响性能；仅在必要时添加索引。

## 10.忘记备份

这种情况可能很少见，但数据库会失败。硬盘可以停止。服务器可能会爆炸。网络主机可能会破产。丢失 MySQL 数据是灾难性的，因此请确保您已经准备好自动备份或复制。

## 11.额外错误:没有考虑其他数据库！

MySQL 可能是 PHP 开发人员使用最广泛的数据库，但它不是唯一的选择。 [PostgreSQL](http://www.postgresql.org/) 和[火鸟](http://www.firebirdsql.org/)是其最接近的竞争对手；两者都是开源的，不受公司控制。微软提供 [SQL Server Express](http://www.microsoft.com/express/Database/) 和甲骨文提供[10g Express](http://www.oracle.com/technetwork/database/express-edition/overview/index.html)；两者都是大型企业版的免费版本。甚至对于小型或嵌入式应用来说，SQLite 也可能是一个可行的选择。

我错过了你最糟糕的 MySQL 错误吗？

*通过我们的截屏了解更多关于 MySQL 的信息[命令行上的 MySQL](https://www.sitepoint.com/premium/screencasts/mysql-on-the-command-line)。*

## 分享这篇文章