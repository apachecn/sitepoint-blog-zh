# MySQL 路线图—计划了什么？

> 原文：<https://www.sitepoint.com/mysql-roadmap/>

长期以来，MySQL 一直被认为是 Web 开发人员的数据库平台。事实上，多年来，它被认为是“真正的”关系数据库系统的继子女，如 IBM 的 DB2、Oracle 和微软的 SQL Server。然而，最近几年，MySQL 无论是从开发角度，还是在国际开发人员和公司 IT 人员的眼中，都有了突飞猛进的发展。

部分开发于 20 世纪 80 年代，SQL 部分构建于 1995 年，其核心开发人员 Michael Widenius(与 David Axmark 和 Allan Larsson 一起)也创建了 MySQL AB。当前的稳定版本是 4.01 版。

当然，MySQL 也是一个开源解决方案。根据 MySQL AB 的 Arjen Lentz 的说法，免费和开源模式是最好的——它包括公众对代码的审查、第三方安全审计、整个开发过程中的反馈以及在许多不同环境中的实战测试。

MySQL 在国际市场的渗透是巨大的，总下载量超过 2000 万次，活跃安装量为 400 万次，每日下载量接近 35，000 次。在这个用户群中包括诸如 Yahoo！、谷歌、思科、NASA、邓白氏、汉莎、Hyperion 和 Sabre Holdings。

许可也是独一无二的，在 GPL 开源许可下有一个可免费下载的版本，对于那些需要支持和担保的人有一个商业许可。

##### 我们现在在哪里？版本 4.0x(生产)

一段时间以来，MySQL 一直是 Java、Perl 和 PHP Web 开发环境中的理想之选。在个人网站层面和企业层面，强大的 Web 应用程序已经在 MySQL 数据库引擎上成功运行。通过最近的公开基准测试，MySQL 的性能与包括 IBM、微软和甲骨文在内的行业巨头进行了比较，MySQL 的能力得到了进一步加强。

令许多没有采用开源数据库的人感到惊讶的是，在性能方面，MySQL 与 Oracle 并驾齐驱，并且在 eWeek 的测试实验室进行的无情测试中超过了其他人。

在当前的稳定版本中有一些限制，其中最重要的两个是子查询和存储过程。随着 Web 应用程序变得越来越复杂，这些问题已经成为平台被广泛接受的障碍。一些公司，如 NASA，已经使用外部脚本如 Perl 解决了这些限制。

然而，4.0x 生产版本中的一些新增功能帮助 MySQL 成为数据库引擎市场中的重要玩家。这些变化中最重要的是包含了用于事务数据库工作的 InnoDB，正如 eWeek 指出的，能够在同一个数据库中使用 InnoDB 和 ISAM 来满足非事务需求——这是 MySQL 独有的优势。当与缓存查询的能力相结合时，后一种能力是 MySQL 令人印象深刻的性能背后的驱动力。这个缓存特性允许将大型查询的结果存储在内存中，使以后的查询几乎可以立即返回结果。

额外的功能，如数据库复制，完善了特性集，使 MySQL 成为 Web 应用程序开发的首选。

注意[MySQL 手册在线](http://www.mysql.com/doc/en/index.html)有设置和利用本文讨论的特性所需的所有文档。此外，如果您计划试用 4.1 版或更高版本，请确保使用开发环境，因为某些更改在安装和授权更新完成后无法降级。

##### 为什么选择 InnoDB？

InnoDB 提供了一个事务存储环境，包括提交、回滚和崩溃恢复。这在多个用户同时使用的 Web 应用程序中非常有用，例如订单管理或 CRM 解决方案。InnoDB 表的创建很简单，正如下面的 create table 语句所展示的那样。

```
CREATE TABLE prospects (a INT, b CHAR (20), INDEX (a)) ENGINE=InnoDB;
```

InnoDB 还根据 MySQL 的文档建立自己的字典，因此在使用 InnoDB 的数据库中可以有许多同名的表(例如，客户、顾客、订单等等)。

最后，InnoDB 允许在同一个 sql 中混合查询 InnoDB 和非 InnoDB 表，当然，当与查询缓存结合使用时，性能会得到优化。

##### 查询缓存

查询缓存在 Web 环境中最有用。数据库驱动的网站主要是从数据库内容生成的动态页面。缓存可以显著提高这些环境的性能。

如果没有打开查询缓存，只需使用`mysqld --query_cache_size=10m`进行测试，它将查询缓存设置为 10MB(使用 k 表示 KB，g 表示 GB)。您可以使用 set 命令将其配置为在 MySQL 启动时启动:

```
mysql> SET query_cache_size = 10240;
```

##### 复制数据库

使用新的 MySQL Administrator，现在可以通过图形用户界面和 MySQL 命令行来管理复制。复制可以用作备份工具、负载平衡工具，或者在迁移到新硬件(可能是 IP 发生变化的新托管环境)时用作迁移工具。

考虑到随着新版本的发布，复制特权会有许多选项和变化，对这个主题的讨论超出了本文的范围。有关更多信息，请参见 MySQL 网站上的[复制文档。](http://www.mysql.com/doc/en/Replication_HOWTO.html)

##### 下一步—版本 4.1 (alpha)

这个版本中主要的标识性添加是子查询。子查询只是嵌套在现有 select 语句中的 select 语句。不幸的是，在 4.1 之前的版本中，子查询要么需要避免，要么需要开发一些复杂的连接和/或联合。简单地说，在检查 sql 时，子查询更容易编写和读取。

下面显示了一个简单但功能强大的子查询示例，它不能通过连接来完成:

```
SELECT partner_name, number_clients_referred 

  FROM partners 

  WHERE number_clients_referred = (SELECT MAX(number_clients_referred) FROM employees);
```

如果 course 在“partners”表中找到所有等于“employees”表中最大值的值，则为 if。

##### “金蛋”——5.0 版

5.0 版本目前正在开发中，但在预览版中，已知关系数据库系统中的一个重要特性是存储过程。存储过程(sp)允许将一组 sql 语句存储在一个命令中并被调用。这些命令只在服务器端运行，并且非常有用，因为一旦被调用，它们不需要客户端交互。此外，sps 可以提高数据的安全性和完整性。

Sps 可用于简单的活动，如对正在处理并加载到数据库中的外部数据的多个原始导入进行数据验证。它们也有助于执行复杂的报告生成，返回到无法用单个 sql 语句处理的客户端结果。

##### 数据有效性

例如，假设您正在开发一个黄页 Web 应用程序，并从许多来源接收业务数据，然后将这些数据聚合并加载到一个数据库中。在这种情况下，您可以使用 sp 来验证所有网站和电子邮件地址是否符合您的正确格式，以及所有电话号码是否以正确的格式显示，并使用破折号来分隔国家代码、区号、前缀等。

##### 报表生成

假设您正在部署一个报告解决方案，该解决方案为希望查看客户账户和交易的投资顾问编制报告，以及显示佣金和投资回报率的计算数据。存储过程将使您能够将这些不同的 sql 语句包装到一个对象中，从而生成一个完整的在线语句供查看和分发。

##### MySQL 集群

刚刚在佛罗里达州的 MySQL 用户大会上宣布，MySQL 已经发布了 MySQL Cluster 的预览版。该系统声称 99.999%的正常运行时间可用性，并试图进入目前由 Oracle、IBM 和微软的 sql servers 等公司主导的市场。

正如我在声明的[天在 Open Sourcery 中提到的，Web 开发者的一个关键因素是双重许可(GPL 开源和商业支持许可)。这使得 Web 开发人员可以下载并构建 MySQL Cluster 的测试环境，而不需要任何许可成本(这是其他供应商不可能做到的！)作为当前和潜在客户的概念验证。](https://www.sitepoint.com/blog/)

虽然该系统被吹捧为大规模安装的系统之一，如当前的 MySQL 客户 NASA、Sabre Holdings 和其他公司，但为任何需要商业或外联网关键正常运行时间的公司带来高级集群的能力是巨大的。

虽然目前的下载只提供预览模式，但 MySQL 一直在幕后为他们现有的许多企业客户端构建定制集群。通过预览产品，开源社区将能够回顾、评论和推荐这一新产品发展中的特性。

现在可以从 MySQL 获得两篇论文。一个作为介绍性白皮书；另一篇文章提供了新的 MySQL 集群架构的技术概述。

*   介绍性白皮书—[http://www . practical applications . net/kb/MySQL-cluster-white paper . pdf](http://www.practicalapplications.net/kb/mysql-cluster-whitepaper.pdf)
*   架构概述—[http://www . practical applications . net/kb/MySQL _ cluster _ architecture . pdf](http://www.practicalapplications.net/kb/mysql_cluster_architecture.pdf)

##### 包扎

MySQL 无疑已经成为 Web 开发的典型数据库。此外，它是企业环境中一颗冉冉升起的新星，在这种环境中，数据量通常以 TB 计。MySQL 已经被证明可以处理每张表 5000 万条记录，并且只会在 release 5 中提高它的可伸缩性。

如果你还没有尝试过 MySQL，那么从 Kevin Yank 的教程系列开始是一个很好的地方，它讲述了如何在动态网站的开发中使用 MySQL 和 PHP。

## 分享这篇文章