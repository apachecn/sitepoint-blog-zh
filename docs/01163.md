# 如何优化 MySQL:索引、慢速查询、配置

> 原文：<https://www.sitepoint.com/optimize-mysql-indexes-slow-queries-configuration/>

MySQL 仍然是世界上最流行的关系数据库，然而，它仍然是最未优化的——许多人保留默认值，懒得进一步研究。在这篇文章中，我们将看看一些 MySQL 优化技巧，我们已经涵盖了，并结合他们的新奇，后来出现了。

## 配置优化

第一个——也是最容易被忽略的！–性能升级每个 MySQL 用户应该做的就是调整配置。5.7(当前版本)比它的前辈有更好的默认设置，但是在这些基础上进行改进还是很容易的。

我们将假设你使用的是基于 Linux 的主机或一个好的[流浪者](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)箱，就像我们的[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)一样，所以你的配置文件将在`/etc/mysql/my.cnf`中。有可能你的安装实际上会加载一个二级配置文件到那个配置文件中，所以仔细看看——如果`my.cnf`文件没有太多内容，那么`/etc/mysql/mysql.conf.d/mysqld.cnf`文件可能有。

### 编辑配置

您需要熟练使用命令行。即使你还没有接触过，现在也是时候了。

如果你正在本地编辑一个流浪者盒子，你可以通过用`cp /etc/mysql/my.cnf /home/vagrant/Code`将文件复制到共享文件夹中，并用一个常规的文本编辑器编辑它，然后在完成后将它复制回原位，从而将文件复制到主文件系统中。否则，通过执行`sudo vim /etc/mysql/my.cnf`来使用像 vim 这样的简单文本编辑器。

*注意:修改上面的路径以匹配配置文件的实际位置——它可能实际上在`/etc/mysql/mysql.conf.d/mysqld.cnf`* 中

### 手动调整

下面的手动调整应该是现成的。根据[这些提示](https://www.percona.com/blog/2016/10/12/mysql-5-7-performance-tuning-immediately-after-installation/)，将其添加到`[mysqld]`部分下的配置文件中:

```
innodb_buffer_pool_size = 1G # (adjust value here, 50%-70% of total RAM)
innodb_log_file_size = 256M
innodb_flush_log_at_trx_commit = 1 # may change to 2 or 0
innodb_flush_method = O_DIRECT 
```

*   `innodb_buffer_pool_size`–缓冲池是内存中缓存数据和索引的存储区域。它用于将频繁访问的数据保存在内存中，当您运行专用或虚拟服务器时，数据库通常会成为瓶颈，因此为应用程序的这一部分提供最多的 RAM 是有意义的。因此，我们给它 50-70%的内存。在 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/innodb-buffer-pool-resize.html)中有一个缓冲池大小指南。
*   日志文件的大小在这里有很好的解释，但简单来说就是在清除日志之前要存储多少数据。请注意，在这种情况下，日志不是错误日志或您可能习惯的东西，而是指示检查点时间，因为对于 MySQL，写操作发生在后台，但仍然会影响前台性能。大的日志文件意味着更好的性能，因为创建的新的和更小的检查点更少，但是在崩溃的情况下恢复时间更长(更多的东西需要重新写入数据库)。
*   `innodb_flush_log_at_trx_commit`在这里被解释为,表示日志文件发生了什么。使用 1 是最安全的设置，因为日志在每次事务后都被刷新到磁盘。使用 0 或 2 时，酸性较低，但性能更好。这种情况下的差异不足以抵消设置为 1 带来的稳定性优势。
*   `innodb_flush_method`–为了完成刷新，将其设置为`O_DIRECT`以避免双缓冲。除非 I/O 系统的性能非常低，否则应该总是这样做。在大多数托管服务器上，比如 DigitalOcean droplets，你会有固态硬盘，所以 I/O 系统将是高性能的。

Percona 的另一个工具可以帮助我们自动找到剩余的问题。请注意，如果我们在没有上述手动调整的情况下运行它，4 个修复中只有 1 个会被手动识别，因为其他 3 个取决于用户偏好和应用程序的环境。

![Superhero speeding](img/5fff190991de019354a674d7ba62ce48.png)

### 变量检查器

要在 Ubuntu 上安装变量检查器:

```
wget https://repo.percona.com/apt/percona-release_0.1-4.$(lsb_release -sc)_all.deb
sudo dpkg -i percona-release_0.1-4.$(lsb_release -sc)_all.deb
sudo apt-get update
sudo apt-get install percona-toolkit 
```

对于其他系统，遵循[说明](https://www.percona.com/downloads/percona-toolkit/LATEST/)。

然后，使用以下命令运行工具包:

```
pt-variable-advisor h=localhost,u=homestead,p=secret 
```

您应该会看到类似于下面这样的输出:

```
# WARN delay_key_write: MyISAM index blocks are never flushed until necessary.

# NOTE max_binlog_size: The max_binlog_size is smaller than the default of 1GB.

# NOTE sort_buffer_size-1: The sort_buffer_size variable should generally be left at its default unless an expert determines it is necessary to change it.

# NOTE innodb_data_file_path: Auto-extending InnoDB files can consume a lot of disk space that is very difficult to reclaim later.

# WARN log_bin: Binary logging is disabled, so point-in-time recovery and replication are not possible. 
```

这些都不重要，不需要修复。我们唯一可以添加的是用于复制和快照目的的二进制日志记录。

*注意:在新版本中，binlog 大小将默认为 1G，PT 不会注意到。*

```
max_binlog_size = 1G
log_bin = /var/log/mysql/mysql-bin.log
server-id=master-01
binlog-format = 'ROW' 
```

*   `max_binlog_size`设置决定了二进制日志的大小。这些日志记录您的事务和查询，并创建检查点。如果一个事务大于最大值，那么一个日志在保存到磁盘时可能会大于最大值，否则，MySQL 会保持这个限制。
*   `log_bin`选项完全启用二进制日志记录。没有它，就没有快照或复制。请注意，这可能会占用大量磁盘空间。在激活二进制日志记录时，服务器 ID 是一个必要选项，因此日志知道它们来自哪个服务器(用于复制),并且格式就是日志的写入方式。

正如你所看到的，新的 MySQL 有一些默认设置，这使得它几乎可以投入生产了。当然，每个应用程序都是不同的，并有额外的定制调整适用。

### MySQL 调谐器

[调优器](https://raw.githubusercontent.com/major/MySQLTuner-perl/master/mysqltuner.pl)将以更长的时间间隔监控数据库(大约每周在一个实时应用上运行一次),并根据日志中看到的内容提出修改建议。

只需下载即可安装:

```
wget https://raw.githubusercontent.com/major/MySQLTuner-perl/master/mysqltuner.pl
chmod +x mysqltuner.pl 
```

用`./mysqltuner.pl`运行它将要求您输入数据库的管理员用户名和密码，并从快速扫描中输出信息。例如，这是我的 InnoDB 部分:

```
[--] InnoDB is enabled.
[--] InnoDB Thread Concurrency: 0
[OK] InnoDB File per table is activated
[OK] InnoDB buffer pool / data size: 1.0G/11.2M
[!!] Ratio InnoDB log file size / InnoDB Buffer pool size (50 %): 256.0M * 2/1.0G should be equal 25%
[!!] InnoDB buffer pool <= 1G and Innodb_buffer_pool_instances(!=1).
[--] Number of InnoDB Buffer Pool Chunk : 8 for 8 Buffer Pool Instance(s)
[OK] Innodb_buffer_pool_size aligned with Innodb_buffer_pool_chunk_size & Innodb_buffer_pool_instances
[OK] InnoDB Read buffer efficiency: 96.65% (19146 hits/ 19809 total)
[!!] InnoDB Write Log efficiency: 83.88% (640 hits/ 763 total)
[OK] InnoDB log waits: 0.00% (0 waits / 123 writes) 
```

同样，重要的是要注意，这个工具应该每周运行一次，因为服务器一直在运行。一旦一个配置值被更改并且服务器重新启动，它应该在一周后运行。最好设置一个 cronjob 来为您完成这项工作，并定期向您发送结果。

* * *

确保每次配置更改后都重新启动 mysql 服务器:

```
sudo service mysql restart 
```

## 指数

接下来，让我们关注索引——这是许多数据库管理员的主要痛点！尤其是那些直接进入 ORM 的人，他们从未真正接触过原始 SQL。

注意:关键字和索引这两个术语可以互换使用。

您可以将 MySQL 索引与书中的索引进行比较，这可以让您轻松找到包含您正在寻找的主题的正确页面。如果没有任何索引，你就必须在整本书中寻找包含该主题的页面。

可以想象，按索引搜索比浏览每一页要快得多。因此，向数据库添加索引通常会加快选择查询的速度。然而，还必须创建和存储索引。所以更新和插入查询会比较慢，并且会消耗更多的磁盘空间。一般来说，如果您已经正确地索引了表，那么您不会注意到更新和插入的区别，因此建议在正确的位置添加索引。

只包含几行的表不会真正从索引中受益。你可以想象，搜索 5 页并不比先去索引，得到页码，然后打开特定的页面慢多少。

那么，我们如何发现要添加哪些索引，以及存在哪些类型的索引呢？

### 唯一/主索引

主索引是数据的主要索引，这是默认的寻址方式。对于用户帐户，这可能是一个用户 ID，或者用户名，甚至是一个主电子邮件。主索引是唯一的。唯一索引是在一组数据中不能重复的索引。

例如，如果一个用户选择了一个特定的用户名，其他任何人都不能获取它。向`username`列添加“唯一”索引解决了这个问题。如果其他人试图插入一个已经存在用户名的行，MySQL 会抱怨。

```
...
ALTER TABLE `users` 
ADD UNIQUE INDEX `username` (`username`);
... 
```

主键/索引通常是在创建表时定义的，唯一索引是在事后通过修改表来定义的。

主键和唯一键都可以同时在一列或多列上创建。例如，如果您想确保每个国家只能定义一个用户名，您可以在这两列上创建一个唯一的索引，如下所示:

```
...
ALTER TABLE `users`
ADD UNIQUE INDEX `usercountry` (`username`, `country`),
... 
```

唯一的索引放在您经常要访问的列上。因此，如果用户帐户被频繁请求，并且数据库中有许多用户帐户，这是一个很好的用例。

### 常规索引

常规索引便于查找。当您需要快速查找特定列或列组合的数据时，它们非常有用，但这些数据不需要唯一。

```
...
ALTER TABLE `users`
ADD INDEX `usercountry` (`username`, `country`),
... 
```

以上将使搜索每个国家的用户名变得更快。

索引还有助于提高排序和分组速度。

### 全文索引

全文索引用于全文搜索。只有 InnoDB 和 MyISAM 存储引擎支持全文索引，并且只支持 CHAR、VARCHAR 和 TEXT 列。

这些索引对于您可能需要进行的所有文本搜索非常有用。在正文中查找单词是全文的专长。在帖子、评论、描述、评论等中使用这些。如果您经常允许在应用程序中搜索它们。

### 降序索引

不是特殊类型，是一种改造。从 8+版本开始，MySQL 支持[降序索引](https://dev.mysql.com/doc/refman/8.0/en/descending-indexes.html)，也就是说可以降序存储索引。当您有大量的表，经常需要先添加最后的数据，或者以这种方式排列条目的优先级时，这就很方便了。降序排序总是可能的，但是会有一点性能损失。这进一步加快了速度。

```
CREATE TABLE t (
  c1 INT, c2 INT,
  INDEX idx1 (c1 ASC, c2 ASC),
  INDEX idx2 (c1 ASC, c2 DESC),
  INDEX idx3 (c1 DESC, c2 ASC),
  INDEX idx4 (c1 DESC, c2 DESC)
); 
```

当处理数据库中的日志、从最后到第一个加载的帖子和评论等时，考虑将 DESC 应用于索引。

### 助手工具:解释

当考虑优化查询时，解释工具将是无价的。在一个简单的查询前加上`EXPLAIN`将会以非常深入的方式处理它，分析使用中的索引，并向您显示命中和未命中的比率。您将会注意到它需要处理多少行才能得到您想要的结果。

```
EXPLAIN SELECT City.Name FROM City
JOIN Country ON (City.CountryCode = Country.Code)
WHERE City.CountryCode = 'IND' AND Country.Continent = 'Asia' 
```

您可以使用`EXTENDED`进一步扩展:

```
EXPLAIN SELECT City.Name FROM City
JOIN Country ON (City.CountryCode = Country.Code)
WHERE City.CountryCode = 'IND' AND Country.Continent = 'Asia' 
```

阅读[这篇精彩、详细的文章](https://www.sitepoint.com/using-explain-to-write-better-mysql-queries/)，了解如何使用它并应用这些发现。

### 助手工具:Percona for Duplicate Indexes

之前安装的 Percona Toolkit 也有一个用于检测重复索引的工具，当使用第三方 CMSes 或只是检查您是否意外添加了比需要更多的索引时，这可以派上用场。例如，默认的 WordPress 安装在`wp_posts`表中有重复的索引:

```
pt-duplicate-key-checker h=localhost,u=homestead,p=secret

# ########################################################################
# homestead.wp_posts
# ########################################################################

# Key type_status_date ends with a prefix of the clustered index
# Key definitions:
#   KEY `type_status_date` (`post_type`,`post_status`,`post_date`,`ID`),
#   PRIMARY KEY (`ID`),
# Column types:
#      `post_type` varchar(20) collate utf8mb4_unicode_520_ci not null default 'post'
#      `post_status` varchar(20) collate utf8mb4_unicode_520_ci not null default 'publish'
#      `post_date` datetime not null default '0000-00-00 00:00:00'
#      `id` bigint(20) unsigned not null auto_increment
# To shorten this duplicate clustered index, execute:
ALTER TABLE `homestead`.`wp_posts` DROP INDEX `type_status_date`, ADD INDEX `type_status_date` (`post_type`,`post_status`,`post_date`); 
```

正如您在最后一行所看到的，它还为您提供了如何消除重复索引的建议。

### 助手工具:Percona 用于未使用的索引

Percona 还可以检测未使用的索引。如果您正在记录缓慢的查询(参见下面的瓶颈部分)，您可以运行该工具，它将检查这些记录的查询是否使用了查询所涉及的表中的索引。

```
pt-index-usage /var/log/mysql/mysql-slow.log 
```

有关该工具的详细用法，请参见此处的[和](https://www.percona.com/doc/percona-toolkit/LATEST/pt-index-usage.html)。

## 瓶颈

本节将解释如何检测和监控数据库中的瓶颈。

```
slow_query_log  = /var/log/mysql/mysql-slow.log
long_query_time = 1
log-queries-not-using-indexes = 1 
```

应将上述内容添加到配置中。它将监视超过 1 秒的查询，以及那些不使用索引的查询。

一旦这个日志有了一些数据，您就可以使用前面提到的`pt-index-usage`工具或者产生如下结果的`pt-query-digest`工具来分析它的索引使用情况:

```
pt-query-digest /var/log/mysql/mysql-slow.log

# 360ms user time, 20ms system time, 24.66M rss, 92.02M vsz
# Current date: Thu Feb 13 22:39:29 2014
# Hostname: *
# Files: mysql-slow.log
# Overall: 8 total, 6 unique, 1.14 QPS, 0.00x concurrency ________________
# Time range: 2014-02-13 22:23:52 to 22:23:59
# Attribute          total     min     max     avg     95%  stddev  median
# ============     ======= ======= ======= ======= ======= ======= =======
# Exec time            3ms   267us   406us   343us   403us    39us   348us
# Lock time          827us    88us   125us   103us   119us    12us    98us
# Rows sent             36       1      15    4.50   14.52    4.18    3.89
# Rows examine          87       4      30   10.88   28.75    7.37    7.70
# Query size         2.15k     153     296  245.11  284.79   48.90  258.32
# ==== ================== ============= ===== ====== ===== ===============
# Profile
# Rank Query ID           Response time Calls R/Call V/M   Item
# ==== ================== ============= ===== ====== ===== ===============
#    1 0x728E539F7617C14D  0.0011 41.0%     3 0.0004  0.00 SELECT blog_article
#    2 0x1290EEE0B201F3FF  0.0003 12.8%     1 0.0003  0.00 SELECT portfolio_item
#    3 0x31DE4535BDBFA465  0.0003 12.6%     1 0.0003  0.00 SELECT portfolio_item
#    4 0xF14E15D0F47A5742  0.0003 12.1%     1 0.0003  0.00 SELECT portfolio_category
#    5 0x8F848005A09C9588  0.0003 11.8%     1 0.0003  0.00 SELECT blog_category
#    6 0x55F49C753CA2ED64  0.0003  9.7%     1 0.0003  0.00 SELECT blog_article
# ==== ================== ============= ===== ====== ===== ===============
# Query 1: 0 QPS, 0x concurrency, ID 0x728E539F7617C14D at byte 736 ______
# Scores: V/M = 0.00
# Time range: all events occurred at 2014-02-13 22:23:52
# Attribute    pct   total     min     max     avg     95%  stddev  median
# ============ === ======= ======= ======= ======= ======= ======= =======
# Count         37       3
# Exec time     40     1ms   352us   406us   375us   403us    22us   366us
# Lock time     42   351us   103us   125us   117us   119us     9us   119us
# Rows sent     25       9       1       4       3    3.89    1.37    3.89
# Rows examine  24      21       5       8       7    7.70    1.29    7.70
# Query size    47   1.02k     261     262  261.25  258.32       0  258.32
# String:
# Hosts        localhost
# Users        *
# Query_time distribution
#   1us
#  10us
# 100us  ################################################################
#   1ms
#  10ms
# 100ms
#    1s
#  10s+
# Tables
#    SHOW TABLE STATUS LIKE 'blog_article'\G
#    SHOW CREATE TABLE `blog_article`\G
# EXPLAIN /*!50100 PARTITIONS*/
SELECT b0_.id AS id0, b0_.slug AS slug1, b0_.title AS title2, b0_.excerpt AS excerpt3, b0_.external_link AS external_link4, b0_.description AS description5, b0_.created AS created6, b0_.updated AS updated7 FROM blog_article b0_ ORDER BY b0_.created DESC LIMIT 10 
```

如果您喜欢手动分析这些日志，您也可以这样做——但是首先您需要将日志导出为一种更“可分析”的格式。这可以通过以下方式实现:

```
mysqldumpslow /var/log/mysql/mysql-slow.log 
```

附加参数可以进一步过滤数据，并确保只导出重要的内容。例如:按平均执行时间排序的前 10 个查询。

```
mysqldumpslow -t 10 -s at /var/log/mysql/localhost-slow.log 
```

其他参数参见[文档](https://dev.mysql.com/doc/refman/5.7/en/mysqldumpslow.html)。

## 结论

在这篇全面的 MySQL 优化文章中，我们研究了让 MySQL 飞起来的各种技术。

我们处理了配置优化，我们通过了一些索引，我们消除了一些瓶颈。然而，这主要是理论上的——对于将这些技术应用于真实应用程序的真实使用案例，敬请关注我们即将推出的性能月项目！

我们错过了什么技巧和窍门吗？让我们知道！

## 分享这篇文章