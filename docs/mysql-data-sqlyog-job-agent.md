# 使用 SQLyog 作业代理同步 MySQL 数据

> 原文：<https://www.sitepoint.com/mysql-data-sqlyog-job-agent/>

作为 MySQL 开发人员，我们经常需要保持两个数据库完全同步。

例如，假设一个客户的网络商店数据存储在他的 ISP 的 MySQL 服务器上，他需要每天对这些数据进行本地备份。这可以通过以下方式之一实现:

*   使用 PHP 之类的脚本语言删除客户端本地数据库中的所有数据，并从主服务器再次插入所有数据。这种方法可能在一段时间内有效，但是随着数据量的增加，整个过程会变得非常缓慢。
*   使用 MySQL 的复制特性在两台 MySQL 服务器之间复制数据。这个选项也不合适，因为 ISP 没有在他们的 MySQL 服务器上给我们足够的权限来允许我们这样做。此外，设置复制不是一件容易的事情

因此，问题来了，在两个 MySQL 数据库之间同步数据的最好和最有效的方法是什么？

一个解决方案是由 SQLyog 工作代理(SJA)提供的。在 Windows 上，它可以通过屡获殊荣的 MySQL GUI SQLyog 获得。Linux 版的 SJA 是免费的，可以从[webyog.com](http://www.webyog.com)下载

在本文中，我们将讨论如何使用 SQLyog Job Agent for Windows 设置和安排数据同步。

##### 概观

SJA 是一个高性能的多线程应用程序，旨在自动化和调度两个 MySQL 主机之间的数据同步。

SJA 还可以用作命令行工具，接受以 XML 编码的作业定义文件作为一个参数。您可以手动创建作业定义文件，也可以使用 SQLyog 附带的向导之一。如果使用 SQLyog 创建作业文件，您不需要了解任何关于 XML 或作业定义模式的知识。

SJA 不需要在已经运行 MySQL 服务器的主机上进行任何安装。您可以使用任何主机来运行 SJA。例如，您可以使用 SJA 来保持您的生产数据库(可能由 ISP 托管)与位于您的 PC 或 LAN 上的测试数据库完全同步。

SJA 使用一种有效的算法来生成校验和，以识别这些变化。因此，只有自上次同步以来插入、更新或删除的行才会在主机之间传输。

##### 入门指南

您可以使用 SQLyog 提供的向导启动并运行 SJA，或者从命令行运行 SJA。Linux 用户只能使用命令提示符选项。

##### 使用 SQLyog 向导配置同步作业

首先，我们需要安装 SQLyog。webyog.com 有 30 天的试用版。安装后，启动工具菜单中的数据库同步向导。您将看到的第一个屏幕是:

![1283_SQLog1.gif](img/1629bc2bfad3dfbc2642698d6fb22543.png)

此页面允许您启动新的同步会话，或编辑现有的同步会话。

选择“开始新的同步会话”，然后单击“下一步”开始新的同步会话。

![1283_SQLog2.gif](img/6fdc3effc294c25d13a83ee8e38b7a1d.png)

在这个屏幕上，您需要提供关于源数据库和目标数据库的详细信息。源数据库是包含要复制的数据的数据库。目标数据库是包含与源数据库同步的数据的数据库。在源数据库中不做任何修改。在同步过程中，目标数据库中多余的行将被删除。

向导的下一页允许您选择要同步的表。

![1283_SQLog3.gif](img/5e5a1be76754c8565e1524e11df6f530.png)

选择[全部]…将同步数据库中存在的所有表格。如果您不想同步所有表格，请单独选择每个表格，而不要选择[全部]…选项。您甚至可以使用“上移…”和“下移…”选项来更改表格的同步顺序。

此外，您可以将 SJA 配置为只检测特定列的更改。这使得它成为同步数据的理想工具，即使带宽有限。由于 SQLyog 使用校验和来检测更改，因此取消选择 BLOB 数据可能会显著加快该过程，因为这些列不会用于生成校验和。要选择特定的列，请检查相应的表并选择列。

![1283_SQLog4.gif](img/3f52535798071f19a6e02fee6cf4825f.png)

单击“SQL WHERE”按钮将打开另一个窗口，您可以在其中指定有效的 SQL Where 子句。

![1283_SQLog5.gif](img/935d5d67367c25a20caa558199f0ffa8.png)

当您不想同步存档数据时，这非常有用。

“选择表”页面中的两个选项是:

*   **出错时中止同步**-选中此选项会在遇到问题时中止同步操作。如果不选中它，SQLyog 将继续同步过程。否则，它将在第一次出现错误时停止。
*   **设置`FOREIGN_KEY_CHECKS=0 for Target`**-如果选中此选项，SQLyog 会在目标服务器上同步之前发出`SET FOREIGN_KEY_CHECK=0`命令。它允许您在不验证外键完整性的情况下同步数据。

选择下一页上的立即同步…将启动同步过程。SQLyog 启动 SJA 作为子进程来同步数据库。向导的最后一步显示了同步过程的完整细节。

![1283_SQLog6.gif](img/77c486feadc6a63ad9119511444384c3.png)

![1283_SQLog7.gif](img/27afbf57757e496abcad151bebf389a8.png)

##### 从命令提示符运行 SJA

您还可以将 SJA 作为一个命令行工具来执行，该工具接受包含会话详细信息的 XML 文件。在 Linux 中，使用 SJA 的唯一方法是从命令提示符下运行它。您可以从命令行执行 SJA，并将 XML 文件作为参数。SJA 的句法是:

```
sja  <jobfile> [â€“l<logile>]
```

```
sja supports the following options:
	*   `-l<filename>`-`sja`将记录同步数据库时遇到的所有错误的文件。如果没有指定日志文件，`sja`将在可执行文件的当前目录下创建一个默认的日志文件 sja.log，并将所有错误信息记录在该文件中。

##### 计划同步过程

在 Windows 中，SQLyog 使用 Windows 任务计划程序来计划同步过程。如果您想安排它，只需单击“后退”按钮，然后选择“保存并安排它”...选项。

![1283_SQLog8.gif](img/13111ab0588eb9b4cd5f43141641efd4.png)

在安排会话之前，您需要将会话详细信息保存在 XML 文件中。单击“完成”按钮启动 Windows 任务计划程序对话框。



##### SJA 作业文件示例

以下是包含同步作业详细信息的两个 SJA 文件示例。

```
<job version="1.2">    

<abortonerror abort="no" />   

<fkcheck check="no" />   

<syncjob>   

<source>   

<host>123.123.0.1</host>   

<user>root</user>   

<pwd />   

<port>3306</port>   

<database>data</database>   

</source>   

<target>   

<host>localhost</host>   

<user>root</user>   

<pwd>complex</pwd>   

<port>3306</port>   

<database>localcopy</database>   

</target>   

<tables all="yes" />   

</syncjob>   

</job>
```

(示例 1:所有表和所有列)

```
<job version="1.2">   

<syncjob>   

<abortonerror abort="yes" />   

<fkcheck check="yes" />   

<source>   

<host>localhost</host>   

<user>root</user>   

<pwd></pwd>   

<port>3306</port>   

<database>mysql</database>   

</source>   

<target>   

<host>localhost</host>   

<user>root</user>   

<pwd></pwd>   

<port>3306</port>   

<database>new_mysql</database>   

</target>   

<tables all="no">   

<table>   

<name>`columns_priv`</name>   

<columns all="yes" />   

</table>   

<table>   

<name>`db`</name>   

<columns all="no">   

<column>`Host`</column>   

<column>`Db`</column>   

<column>`User`</column>   

</columns>   

</table>   

<table>   

<name>`host`</name>   

<columns all="yes" />   

<sqlwhere>host like '%%'</sqlwhere></table>   

</tables>   

</syncjob>   

</job>
```

(示例 2:带有 WHERE 子句的特定表和列)

##### 要记住的要点

SQLyog 使用 MySQL 的`concat_ws()`函数来生成校验和。众所周知，这个函数在不同版本的 MySQL 上给出不同的结果。如果源和目标都运行相同版本的 MySQL，SJA 工作得最好。

SQLyog 检查两个表的结构和主键的相似性。如果它们不相同，SQLyog 将在同步过程中跳过该表。

有关同步的所有信息(包括错误和其他信息)都存储在日志文件 sja.log 中，该文件位于 SQLyog 的安装目录中。

##### 结论

在两台服务器之间同步数据通常是一项全职工作，而跟踪分布在客户站点上的重复数据库(所有这些数据库都在不同时间发生变化)有可能成为一场管理噩梦。幸运的是，SJA 允许您同步数据库，即使在偶尔连接的环境中，也不需要经历复杂的设置和安装任务。

## 分享这篇文章

```