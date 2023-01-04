# MySQL 与 PostgreSQL 的比较

> 原文：<https://www.sitepoint.com/mysql-compared-with-postgresql/>

[PSNC](http://monstera.man.poznan.pl/) 发表了一份[MySQL 4.1 和 PostgreSQL 8.0](http://monstera.man.poznan.pl/wiki/index.php/Mysql_vs_postgres) 的对比。

如果你有兴趣知道使用 MySQL 而不是 PostgreSQL 会错过什么特性，那么[对照表](http://monstera.man.poznan.pl/wiki/index.php/Mysql_vs_postgres#Comparison)提供了一个方便的总结。MySQL 5.0(正在开发中)越来越接近 PostgreSQL 令人印象深刻的特性集，视图和存储过程都在列表中。然而，文章称，在性能方面，MySQL 仍然胜过 PostgreSQL。给出了一些基准的细节。

自从我开始使用 MySQL 以来，它已经取得了很大的进步，增加了对子查询(在 4.1 中)、布尔全文搜索和事务(InnoDB)的支持。

来自文章:

> MySQL 比 PostgreSQL 简单，但速度快得多。MySQL 不支持许多高级特性，而这些特性在大型关系型复杂数据库中可能很重要。但是对于 JRA1 测量架构，MySQL 应该足够了(我们可能不会使用最先进的特性)。

目前，MySQL 是 LAMP (Linux、Apache、MySQL 和 PHP)架构的主要部分，预装在许多主机帐户上，并与许多操作系统捆绑在一起。这使得它对新手和中级 web 程序员非常有吸引力，因为开始使用它相对容易和便宜。凭借其不断扩展的功能集和[双许可](http://www.mysql.com/company/legal/licensing/)，MySQL 正在寻求扩大其在其他细分市场的使用。

这篇文章还包含更详细的比较信息的链接，例如 mysql.com 的[功能比较页面。](http://dev.mysql.com/tech-resources/features.html)

通过[开源 vs](http://opensourceversus.com/modules.php?op=modload&name=News&file=article&sid=139)，一探究竟。

## 分享本文