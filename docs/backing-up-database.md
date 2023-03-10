# 备份您的数据库

> 原文：<https://www.sitepoint.com/backing-up-database/>

互联网目前正经历着从几乎没有交互性的静态网站向由数据库生成的动态网站的转变，论坛、免费电子邮件和其他基于网络的应用程序等工具正被用来吸引访问者。因此，为了避免主机、站点和数据库崩溃时的灾难，经常备份数据库是至关重要的。无论如何，定期备份你的网站是个好主意。

在这篇短文中，我们将了解如何备份一些常见的数据库:MySQL、PostGreSQL、Microsoft SQL Server 和 Microsoft Access。

##### 谁需要做备份

在开始之前，您需要评估您的情况，以决定多久备份一次数据库。例如，如果你运营一个个人“日记/博客”类型的网站，大约每周更新一次，并有一个不时插入评论的留言簿，那么你可能只需要每一到两周更新一次，这取决于你有多少“重要”信息。另一方面，如果你运行一个关于各种主题的高流量论坛，或者你运行一个 SitePoint 大小的站点(超过 300 篇文章，一个每天有数百个帖子的 vBulletin 论坛，大量的模板等等)，那么你需要根据你提供的信息的重要性，每隔几天对你的站点和数据库进行备份。

一般来说，信息越重要或者流量越大，就越应该考虑将数据库备份到不同的位置。这意味着不仅要将其备份到服务器上的另一个目录，还要备份到本地机器、磁带备份、不同网络上的另一个服务器，甚至可能是免费空间提供商(如车道或 iDrive)。小心总是有好处的。

##### 备份应该多久进行一次

例如，SitePoint 会在每天的特定时间将其站点备份到澳大利亚的本地计算机以及服务器本身(但位置略有不同)。硬件，如 RAID-5 驱动器、磁带备份和镜像设备，也协作帮助备份。

因此，一旦你考虑了你应该多久备份一次你的数据库，请点击下面的链接(取决于你的数据库类型)并按照步骤操作！

注意:每当我在任何指南中提到“备份区域”时，都是指存储备份的地方——无论是您自己的硬盘驱动器、服务器上的磁带备份还是网络上的其他服务器。

##### 关系型数据库

MySQL 是在 Unix 和 Linux 机器上使用的流行数据库；Windows 版本最近也变得开源了(MySQL 也走上了 GPL 的道路)。当与 PHP 结合时，这两者形成了一个强大的平台，让您可以编写交互式的基于 web 的应用程序。

在 Unix/Linux 上备份 MySQL 数据库有三种主要方法(Windows 用户应该会发现命令相当相似):

2.  Use the mysqldump command.
    If you have Telnet/SSH access to your MySQL server, log in and issue the following command for each database you want to back up:

    ```
    shell> mysqldump -u user -ppassword --opt -full database_name > backupfile.sql
    ```

    然后将结果文件移动到您首选的备份区域。如果你需要更多关于 mysqldump 命令的信息，那么只需查看这个 URL:[www.mysql.com/documentation/mysql](http://www.mysql.com/documentation/mysql/commented/manual.php?section=mysqldump)

3.  Copy all the relevant table files.
    If the server isn’t updating anything (or you’ve deliberately killed mysqld for this purpose) then you can copy all the files with the following extensions in your MySQL data directory:

    *.frm
    *。myd
    *。myi

    确保将文件复制和下载到首选备份区域后，重新启动 MySQL 守护程序。

    提示:完成备份后，使用–log-update 开关重启 MySQL。这将允许您跟踪自上次“转储”以来在 MySQL 表中所做的所有修改。

    要恢复转储，您应该恢复到现有数据库，或者使用

    ```
    shell> mysqladmin create database_name
    ```

    然后发出以下命令:

    ```
    shell> mysql -u user -ppassword database_name < backup-file.sql
    ```

4.  If you don’t have access to Telnet/SSH and you’re unable to do backups using the methods described above, you should ask your host if it is possible for them to do a backup for you and put the backups in a separate directory so that you can easily FTP your backups to your selected backup areas. Otherwise, if you have access to phpMyAdmin, you can use the following procedure:
    *   访问 phpMyAdmin，并选择您希望“转储”(备份)的数据库。
    *   向下滚动，您会看到一个带项目符号的点:“查看数据库的转储(模式)”以及一些单选按钮和复选框。选择“结构和数据”，然后单击“添加删除表”和“发送”，并单击“开始”。这将把“转储”保存到您的硬盘上。

    要使用 phpMyAdmin 恢复转储，只需在选择了正确的数据库后，通过执行以下操作将文件插入到正确的位置:

    *   选择要插入数据的数据库，或创建一个新数据库。
    *   插入您已经有的适当的 SQL 查询，或者只粘贴。sql 文件放入“文本文件的位置”下的文本框中，然后开始！

    提示:使用 crontab 作业定期安排备份。

##### 一种数据库系统

尽管不广为人知，PostgreSQL 是一个强大的“对象关系”数据库系统，支持许多 MySQL 没有的强大功能。同样，PHP 与 PostgreSQL 结合得非常好。

备份 PostgreSQL 数据库也很容易。在 Telnet/SSH 中，发出以下 pg_dump 命令:

```
shell> pg_dump dbname > dbname.pgdump
```

您可以使用以下命令从转储中恢复数据库:

```
shell> cat dbname.pgdump | psql dbname
```

当然，要确保用适当的名称替换上面命令中的' dbname'。

如果您有一个大型 PostgreSQL 数据库，您将需要使用 gzip 或 split 来将结果文件减小到一个更易于管理的文件大小。gzip:发出以下命令:

```
shell> pg_dump dbname | gzip > filename.dump.gz
```

并重新加载到新的数据库中…

```
shell> createdb dbname

shell> gunzip -c filename.dump.gz | psql dbname

or

shell> cat filename.dump.gz | gunzip | psql dbname
```

split:发出以下命令:

```
shell> pg_dump dbname | split -b 1m - filename.dump.
```

并重新加载到新的数据库中…

```
shell> createdb dbname

shell> cat filename.dump.* | pgsql dbname
```

提示:使用 crontab 作业定期安排备份。

##### 接近

Access 被许多公司广泛使用，尽管它最初的目的不是作为 Web 数据库。Access 有能力在网上同时处理 30 个用户，并与 ASP 很好地交互。

也就是说，备份您的 Access 数据库非常简单。只需将您站点上的 Access 数据库 FTP 到您的备份区域，并在需要时再次上传。

##### SQL Server

微软为中型企业提供的数据库解决方案(尽管它既适合小型企业也适合大型企业)，SQL server 是一个为高级用户设计的强大应用程序。您可以使用点击式界面通过 Enterprise Manager 管理您的数据库，或者使用事务和 SQL 子选择。ASP 和 SQL Server 提供了一个有效的组合，特别是对于基于 web 的应用程序和需要电子商务功能的站点。

要备份数据库，您应该以管理员或具有 SQL Server 权限的用户身份登录，并通过提供的界面执行备份。如果您无法访问您的服务器，那么您应该询问您的系统管理员。有几种方法可以在 SQL Server 上进行备份；不幸的是，这篇文章没有篇幅讨论所有这些问题，所以我让你直接从马的嘴里读出来；从微软的 TechNet 查看这篇文章

##### 结论

我们刚刚研究了如何用一些常见的数据库系统备份您的数据库，并提供了一些关于各种相关问题的实用技巧。无论你运行什么类型的数据库驱动的网站——无论是你的个人日记、Slashdot 风格的新闻门户还是 24/7 的电子商务应用程序——你都应该实现某种备份机制。如果灾难以某种形式降临到你的网站上，你会感激安全网。

## 分享这篇文章