# 将您的站点从 MySQL 迁移到 PostgreSQL —第 1 部分

> 原文：<https://www.sitepoint.com/site-mysql-postgresql-1/>

当我的网站 [Design Harbor](http://www.designharbor.org) 启动时(当时是横坐标科技)，对于一个 PHP 驱动、数据库支持的网站来说，有一个明智的选择:MySQL。然后，在 2001 年夏天，我把这个网站移植到了 [PostgreSQL](http://www.postgresql.org) (也简称为 Postgres)并且没有回头。

这是由两部分组成的系列文章中的第一部分，描述了我转向 Postgres 的动机，并一步一步地解释了如何将现有的 MySQL 数据转换成 Postgres。第二部分将解释如何更改 PHP 后端以确保它能与新的数据库系统一起工作。

##### 转换的动机

我第一次读到 Postgres 是在 [PHPBuilder](http://www.phpbuilder.com) 的一篇文章中。它比较了 Postgres 和 [MySQL](http://www.mysql.com) ，这是我当时专用的。但是，在我读了这篇文章后，Postgres 在我大脑的信息库中消失了——这是一个有趣的事实，但它与我的网页设计无关。

我继续使用 MySQL，并将 MySQL 的失败和崩溃归咎于我的托管服务的无能，这在当时是无法改变的。只要有可能，我就换了主机。这个新主机的服务和理念与我以前的主机非常不同，因为他们比我以前的主机对安全性和稳定性有更高的承诺。新公司试图说服我使用 Postgres，认为它更稳定，但我拒绝了这个想法，并指出我的整个网站已经为 MySQL 编码。他们让步了，专门为我的网站安装了 MySQL。这就是问题开始的时候。

我的第一份工作是将我的旧 MySQL 数据从旧服务器复制到我的新 Web 主机的 MySQL 服务器上。首先，我将现有数据转储到一个 SQL 文件中，并将其传输到新的 Web 服务器。然后，我继续导入 SQL 文件。一看到这几千行文件，MySQL 立刻崩溃了。当公司重启 MySQL 时，我的数据大约有一半在那里，但 MySQL 只是断断续续地工作。最后，他们必须删除导入的信息，这样我才能再试一次。MySQL 又崩溃了。这个循环又持续了几次，直到我最终决定将我的 SQL 文件分成几部分。我不得不尝试了几次，但我最终能够将我的大部分数据导入到新的 MySQL 服务器中。一切都很好，我松了一口气。

在接下来的几个月里，MySQL 几乎每两周崩溃一次，在 2001 年 6 月底的一次严重崩溃中达到顶点。这一次，MySQL 存储的数据无可救药地遭到破坏，无法恢复。我有一个 SQL 备份文件，但是根据我以前将大量数据导入 MySQL 的经验，我不喜欢从该备份中恢复数据的任务。大约在那个时候，托管公司再次鼓励我将这个用 PHP 编写的网站移植到 Postgres。由于对 MySQL 的失望，我最终决定去做。

##### 将数据从 MySQL 传输到 Postgres

将数据从 MySQL 移植到 Postgres 有点困难，因为 Postgres 支持比 MySQL 更标准的 SQL 版本，并且不可能直接将 SQL 转储到 Postgres。然而，SQL 语法非常相似，所以在我的例子中，这并没有花费太长时间。

##### 转换 MySQL 转储

首先，让你的网络主机为你的账户创建一个数据库。Postgres 中的数据库，就像 MySQL 数据库一样，由包含实际数据的表集合组成。然后使用`mysqldump`命令创建 MySQL 数据库的 SQL 文件转储。

```
mysqldump -u username -p databasename > sqldump.txt
```

使用 FTP 将整个 SQL 转储文件下载到您的计算机上。现在您的计算机上已经有了一个 SQL 文件，您可以将它转换成 Postgres 将导入的文件。

首先，从转储文件中剪切出所有的 MySQL `CREATE TABLE`查询，并将它们粘贴到一个单独的文本文件中。下一步是用 Postgres 能理解的语言重新定义表格。

Postgres 的创建表的 SQL 与 MySQL 的相似，但不完全相同。这里有一个例子:

```
CREATE TABLE practicetable  

{  

someID    SERIAL,  

time      TIMESTAMP DEFAULT now(),  

name      VARCHAR(50),  

address   VARCHAR(50),  

city      VARCHAR(50),  

state     VARCHAR(2),  

country   VARCHAR(3) DEFAULT 'USA',  

postlcode VARCHAR(15),  

age       smallint,  

lattitude real,  

longitude real,  

somebool  boolean,  

message   textitem   

};
```

在 Postgres 表定义中，字段名称后面应该跟字段类型。前面的例子中给出了一些常见的字段类型，但是您可以在关于数据类型的 [Postgres 文档中找到一个详细的列表。Postgres 为不同的任务提供了大量的数据类型选择，可以存储各种类型的数据，从互联网地址和货币信息到几何对象定义。下面是最常用的数据类型的快速概述。](http://www.postgresql.org/idocs/index.php?datatype.html)

**SERIAL** 类型字段相当于 MySQL 自动递增的唯一 ID。Postgres 数据库可以包含 SQL 函数和报告以及表和记录；当您在表中定义序列类型的字段时，自动递增序列函数会自动添加到数据库中。因为自动递增系统驻留在实际的表之外，所以我们可以为惟一 id 的值定制逻辑，并进行其他的工作。从 MySQL 移植到 Postgres 时，缺省动作就足够了。

**VARCHAR** 类型正如它所声称的那样，是一个可变长度的文本字段。字段的长度由括号中的值定义。例如，VARCHAR(5)定义了一个最多可以包含 5 个文本字符的字段。

**SMALLINT** 、 **INT** 、 **BIGINT** 用于定义整数项。SMALLINT 字段可以存储范围从-32768 到+32767 的数字(这个实际大小可能会因您的计算机类型而略有不同；前面是最常见的系统整数大小)。INT 字段可以存储从-2147483648 到+2147483647 的较大数字。BIGINT 字段类型适用于任何更大的类型，并且没有大小限制。

**实数**字段类型适用于包含小数的实数。它们最多可以存储 6 位小数。**双精度**字段类似，但是最多可以保留 15 位小数。

一个**布尔型**字段要么为真要么为假，要么为 1 或 0。它与 MySQL 完全相同。

**时间戳**字段就像它们在 MySQL 中的表亲。每次更新记录时，时间戳都会更新为当前日期和时间。Postgres 时间字段也可以包含时区信息。关于 Postgres 时间数据的更复杂使用的细节，请阅读 [PostgreSQL 文档](http://www.postgresql.org/idocs/index.php)的[日期时间页面](http://www.postgresql.org/idocs/index.php?datatype-datetime.html)。

##### 创建表

一旦您创建了一个单独的表定义 SQL 文件并在 Postgres-speak 中重新定义了表，请检查以确保每个 CREATE TABLE 查询都以分号结束——这是 Postgres 的一个要求。然后使用 telnet 之类的工具连接到您的 Web 主机，并使用下面的方法创建表。

首先，用文本编辑器打开您的表定义文件。然后登录你的主机，输入`psql`运行 Postgres 交互终端 psql。默认身份验证方案使用您的 telnet/FTP 用户名作为 Postgres 帐户。这使得 Postgres 可以自动验证您的身份，而无需强制您输入用户名和密码组合。您的 Web 主机可能会进行不同的身份验证，在这种情况下，您可以给 psql 程序附加一些参数，如下所示:`psql -d databasename -U username -W`。d 允许您指定数据库，-U 指定用户名，-W 要求 psql 提示您输入密码。

运行 psql 后，将每个 CREATE TABLE 查询分别粘贴到 psql 中，然后按 enter 键。如果您在 SQL 语法中犯了一个错误，psql 会告诉您哪里出错了。通过单独输入每个表，您可以收到每个表的调试信息，这大大简化了工作。

如果在输入表定义后，您意识到遗漏了一两个字段，您有两种选择。您可以使用 ALTER TABLE 命令，或者使用 DROP TABLE 删除该表，然后在修复后再次输入该表。如果您使用第二种方法，您将遇到 Postgres 警告，它会从丢弃的表中留下某些工件，妨碍创建新表。

要使用 DROP TABLE 命令，请键入`DROP TABLE practicetable;`。这将删除该表，但是当您重新定义该表时，您将收到一个错误。删除表不会消除表中 SERIAL 类型字段的序列。当您试图重新创建表时，这些剩余的序列会妨碍您的工作。要解决这个问题，在删除表之前使用`DROP SEQUENCE sequencename;`删除序列。不幸的是，序列名称与序列名称不同。当您定义一个序列类型字段时，Postgres 会自动生成一个序列，命名如下: *tablename_colname_seq* 。在 practicetable 的情况下，DROP SEQUENCE 语句应该是这样的:`DROP SEQUENCE practicetable_someID_seq;`。然后你可以放下桌子重新开始。

插入完表格后，键入`z`来仔细检查表格列表。当你完成时，输入`q`将退出 psql。终于到了准备将数据导入 Postgres 的时候了。

##### 清理垃圾场

因为 MySQL 主要遵循 SQL 语言标准，所以从 SQL 转储文件导入实际数据并不太难。但是，有一个警告，要求我们在将 SQL 转储交给 Postgres 之前对其进行编辑。

MySQL 和 Postgres 在数据输入方面的主要区别在于引号。在 Postgres 中，字符串值(包含文本的值)必须用单引号括起来。MySQL 两者都允许，但是幸运的是 mysqldump 程序使用单引号，这对 Postgres 是友好的。然而，MySQL 和 Postgres 在如何处理字符串中出现的引号上有所不同。其中 MySQL 用一个 **`""`** 表示引号，Postgres 需要一个 **`"`** 。使用文本编辑器的搜索/替换功能将所有的 **`""`** 替换为 **`"`** 。有趣的是，Postgres 用`''`来代表单引号；MySQL 也使用这种方法，所以您不必更改单引号转义。

##### 导入到 Postgres

在您修复了 SQL 转储文件中的引号之后，将该文件上传到您的 Web 主机的机器日志中，就像您之前创建表一样。导航到 SQL 转储文件所在的目录。启动 psql，但是使用几个不同的命令行选项:`psql -f sqldump.txt`，并用您给文件指定的新名称替换 sqldump.txt。该命令获取整个 SQL 文件，并将其导入正确的 Postgres 表中。和以前一样，您可能需要添加额外的选项，以便 psql 能够正确地验证您的身份。如果您收到任何错误，psql 会告诉您它们发生在哪里。搜索文件的这一部分，尝试找出并修复问题，并将有问题的查询手动输入 psql 命令行工具。在我的情况下，一切顺利进口，我准备完成这项工作。直到过了一会儿，我才注意到另一个问题。

在我开始使用新的 Postgres 驱动的网站后，我遇到了 MySQL 和 Postgres 之间的另一个不兼容问题。由序列类型自动递增字段使用的 Postgres 序列从 1 开始，并且每当插入一个具有序列类型字段的记录时自动递增 1。然而，当我导入 MySQL 转储时，转储中的 SQL 定义了我的主整数键的值。在我的例子中，我有唯一的 id 一直数到 60，但是序列仍然是 1。我执行的每个 INSERT 语句都失败了，因为序列给出的结果不是唯一的 ID。沮丧之余，我运行了 60 条 INSERT 语句来使序列达到正确的位置，但是后来我从一个经常使用 Postgres 的朋友那里学到了一个更快的修复方法。这是他让我做的:

使用 telnet 之类的终端程序连接到您的主机。然后像定义表时一样启动 psql 程序。首先，确定表中存在的最高 ID 值。这可以使用`SELECT fieldname FROM tablename WHERE fieldname=MAX(fieldname);`来完成。然后使用`DROP SEQUENCE table_colname_seq;`从数据库中删除该序列，其中*表* 是表名，*列名*是序列字段的名称。然后使用`CREATE SEQUENCE table_colname_seq START 61;`重新创建序列，用数据库中 ID 值最高的数字加 1 替换 61。

##### 安装 GUI 工具

一旦我成功地将数据放入 Postgres，我需要一种方法让我不熟悉 Unix 的合作伙伴成功地处理数据库中的数据。我对 MySQL 的选择是 phpMyAdmin，这是一个在线查看和编辑数据库的好工具。对我来说幸运的是，phpMyAdmin 已经以 [phpPgAdmin](http://www.greatbridge.org/project/phppgadmin) 的形式移植到 Postgres 上了。

phpPgAdmin 的安装很简单。首先，从 phpPgAdmin 网站下载最新的稳定发行版，并把它放在你的主机账户上，这样你就可以在网上看到它了。然后使用 telnet 之类的工具登录到您的主机上。导航到 phpPgAdmin.tar.gz 文件所在的目录，键入`tar -xzvf phpPgAdmin.tar.gz`来解包程序(你可能需要替换一个不同的文件名；这只是作为一个例子)。接下来，转到解包过程创建的新子目录，阅读自述文件。

最后，在你的 Web 主机上的文本编辑器中打开*config.inc.php*(VI、pico 和 emacs 是流行的文本编辑器；向你的主人咨询一下，看看他们有什么可以提供的。在这个文件中，我找到了 phpPgAdmin 的配置说明。填写文件中的几个问题，然后在浏览器中加载页面。PhpPgAdmin 将提示您输入用户名和登录名，然后您可以开始通过它的界面管理您的数据库。

##### 结论

Postgres 是一个更稳定、更可靠的数据库服务器，可以处理比 MySQL 大得多的负载。它还具有更高级的特性，并且比它的同类产品更接近 SQL 标准。如果您遵循上述建议，可以使用默认的 SQL 转储文件将数据从 MySQL 转换为 Postgres。这可能会成为一个令人困惑的任务，但很好的帮助是可用的。如果你发现自己被难住了，你可以在非常有用的 [Postgres 网站](http://www.postgresql.org)上找到文档资源。

本系列的下一篇教程将探讨 PHP 中 MySQL 和 Postgres 访问之间的差异，并提供解释、提示和技巧来快速使您现有的代码对 Postgres 友好。

做出改变:像我一样，你会很高兴你这样做了。

## 分享这篇文章