# MySQL 表类型的自由性能

> 原文：<https://www.sitepoint.com/free-performance-with-mysql-table-types/>

上周，SitePoint 发布了它的第二个[Twitter away](https://twitter.com/sitepointdotcom)，向任何在 Twitter 上关注我们的人免费赠送一份关于 [*成功自由职业者原则*](https://www.sitepoint.com/books/freelancer1/) 的 PDF。可以预见的是，我们为读者建立的声称 PDF 的网站在免费赠送的前几个小时就被流量淹没了，幕后的 MySQL 服务器很快就停止了。

首先，简单介绍一下背景:在 2008 年我们第一次推出 Twitter away 的时候，Twitter 总部已经放宽了我们一天内可以发送的 Twitter 直接消息(DMs)的最大数量限制。为了分发免费的 pdf 文件，我们简单地编写了一个 PHP 脚本，每隔几分钟就会浏览我们的 Twitter 关注者，识别出新的，然后给他们发送一个带有下载链接的 DM。

这一次，当我们坐下来计划 Twitaway 2009 时，很明显我们的工作会更加困难。Twitter 总部比以前更不合作，阻挠我们放宽每日 DM 限额的请求——他们甚至不告诉我们每日限额是多少。有一段时间，我们不确定如何能够私下向我们的 Twitter 粉丝提供下载链接。

解决方案最终来自 Mal，他是 SitePoint 总部团队的一名新开发人员。我们将使用 Twitter 新的 [OAuth 支持](http://apiwiki.twitter.com/OAuth-FAQ)来请求临时访问您的 Twitter 帐户，使用它来验证您确实在关注我们，然后只需在您的浏览器中显示 PDF 下载链接。不需要 DM！

当然，编写一个支持这种基于 OAuth 的工作流的 PHP 应用程序要比编写一个带有“在 Twitter 上关注我们”链接的静态网页复杂得多。不仅写起来更难，而且处理大量的流量也会更难。

于是我们就这样了:Twitaway 发布当天刚过午夜，应用程序停止了响应。我们的监控系统清楚地表明，当我们的 PHP 服务器安然度过这场风暴时，MySQL 数据库服务器的 CPU 使用率已经飙升至 100%，它用手指堵住耳朵，开始前后摇晃，并高呼“na-na-na-na-na-na-na-I-not-listening！”

事实证明，对于我们的开发团队来说，这是选择合适的 MySQL 表类型的重要性的重要一课。请继续阅读，了解我们是如何解决这个问题的，以及我们为什么应该预测到这个问题。

## 选择正确的表格类型

当我们登录到被淹没的 MySQL 服务器时，我们可以看到(使用 [SHOW PROCESSLIST](http://dev.mysql.com/doc/refman/5.4/en/show-processlist.html) 命令)它收到的绝大多数查询都是针对我们设置的包含 PHP 会话数据的数据库表的。

默认情况下，PHP 将用户的会话变量存储在 web 服务器硬盘上的文件中。然而，在我们的例子中，我们有多个 web 服务器需要共享相同的会话数据，所以我们配置 PHP 将其会话存储在 MySQL 数据库服务器上。

遗憾的是，在数据库中创建会话表时，我们未能指定表类型:

```
CREATE TABLE sessions (
  sesskey VARCHAR(32) NOT NULL DEFAULT '' PRIMARY KEY,
  expiry INT(11),
  expireref VARCHAR(64),
  sessdata LONGTEXT
);
```

MySQL 支持许多不同的存储引擎。当你创建一个表时，默认情况下 MySQL 会使用 [**MyISAM 存储引擎**](http://dev.mysql.com/doc/refman/5.4/en/myisam-storage-engine.html) 来创建一个 MyISAM 表。MyISAM 是一种相对简单的表类型，它放弃了一些高级特性，以提供更好的性能。在 MySQL 的早期，MyISAM 表类型有助于使它成为性能最好的数据库之一。

这些特征似乎使 MyISAM 成为会话数据表之类的东西的自然选择。不幸的是，MyISAM 有一个致命弱点:**表级锁定**。为了将更改写入 MyISAM 表，MySQL 必须锁定整个表，防止任何其他查询读取该表，直到更改完成。

当成千上万的 SitePoint 读者涌入我们的 Twitaway 站点索取他们的书时，多个 PHP 进程竞相创建、更新和读取这个表中的会话数据。由于 MyISAM 的表级锁定，他们不得不一次一个地这样做，在不断备份的数据库服务器上等待轮到他们。

从表面上看， [**InnoDB 存储引擎**](http://dev.mysql.com/doc/refman/5.4/en/innodb.html) 似乎不是提高数据库性能的好选择。它支持一系列 MyISAM 不支持的特性，包括[事务](http://dev.mysql.com/doc/refman/5.4/en/storage-engine-compare-transactions.html)和[强制引用完整性](http://dev.mysql.com/doc/refman/5.4/en/innodb-foreign-key-constraints.html)，所有的文献都会告诉你将数据写入 InnoDB 表比使用 MyISAM 表要慢。

但是 MyISAM 有一个致命弱点，InnoDB 有一个秘密武器:**行级锁定**。将数据写入 InnoDB 表时，只有受影响的行被锁定，而不是整个表。访问同一个表的其他部分的查询可以同时发生。在像会话数据这样的情况下，正在处理的每个浏览器请求都可能与不同的会话相关，行级锁定正是医生所要求的！

考虑到这一点，您可能会猜到我们是如何解决数据库服务器的问题的。我们只是将会话表转换为 InnoDB 表:

```
ALTER TABLE sessions ENGINE = InnoDB;
```

很快，数据库服务器上的 CPU 负载降到了更合理的水平，Twitaway 站点恢复了在线，人们欢欣鼓舞。我们可以通过在最初创建表时指定表的类型来省去很多麻烦:

```
CREATE TABLE sessions (
  sesskey VARCHAR(32) NOT NULL DEFAULT '' PRIMARY KEY,
  expiry INT(11),
  expireref VARCHAR(64),
  sessdata LONGTEXT
) ENGINE = InnoDB;
```

切换到 InnoDB 让我们度过了第一个晚上，但是到了第二天早上，我们想知道是否有更好的表格类型供我们使用。经过一点研究之后， [**内存存储引擎**](http://dev.mysql.com/doc/refman/5.4/en/memory-storage-engine.html) 看起来像是一个可能的候选。

如果 MyISAM 为了性能而牺牲特性，那么内存表类型将这种情况推向了极端。内存表从不写入磁盘，因此如果重新启动数据库服务器，存储在任何内存表中的数据都会丢失。然而，内存表不需要磁盘访问，这一事实使得它们非常快！因为会话数据本来就是易变的，所以我们并不介意这个特定的表是否会在服务器重启后失效。

根据文档，内存表具有与 MyISAM 相同的表级锁定弱点。然而，当我们将我们的会话表切换到内存存储引擎时，我们的数据库服务器的 CPU 使用率下降到几乎为零！

内存表有一些苛刻的限制，这意味着它们不是解决性能问题的灵丹妙药。抛开消失的数据不谈，内存表不能包含像 TEXT 或 BLOB 这样的大型列类型。事实上，您可以在内存表行中存储的最大数据是 255 个字符的 CHAR 或 VARCHAR。在我们的例子中，我们实际上必须缩短几个会话变量名，以确保它们合适！

此时，我们的会话表在中等负载下运行良好。随着本文和我们计划的其他一些推广的流量到达服务器，我们将密切关注，以决定内存存储引擎是否能够完成任务，或者我们是否应该切换回 InnoDB 以利用行级锁定。无论是哪种情况，我们都学到了重要的一课:**当性能很重要时，请花时间考虑您选择的 MySQL 存储引擎。**在我们的例子中，它将我们从一个繁忙的数据库服务器带到一个显示 CPU 使用率为零的服务器。

## 分享这篇文章