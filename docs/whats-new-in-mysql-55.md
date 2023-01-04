# MySQL 5.5 的新特性

> 原文：<https://www.sitepoint.com/whats-new-in-mysql-55/>

自 2009 年 4 月甲骨文收购太阳微系统公司以来，MySQL 的未来一直不确定。幸运的是，所有怀疑该公司意图的人可以稍微放松一下: [MySQL 5.5 已经发布](http://dev.mysql.com/downloads/mysql/5.5.html)。

关于 MySQL 5.5 有一些令人印象深刻的说法，你会发现报告详细描述了高达 4000%的速度提升。拿那些有很大保留的来说，但是你通常可以预期 5.5 版本会更快。

让我们更深入地了解一下这些新功能的最佳之处…

## InnoDB 成为默认的存储引擎

是时候了。对于大多数应用程序来说，InnoDB 是一个更好的选择有很多、[很多](https://www.sitepoint.com/mysql-transactions-php-emulation/)、[很多](https://www.sitepoint.com/mysql-foreign-keys-quicker-database-development)原因，然而 MyISAM 到目前为止仍然是默认的。从 MySQL 5.5 开始，开发人员将拥有现成的可靠性和稳定性，支持 ACID 事务、外键支持和崩溃恢复。

此外，最新的 InnoDB 存储引擎提供了更好的性能、可扩展性和灾难性故障恢复能力。

## 更好的可用性

多服务器复制系统的性能得到了提高，具体表现在:

*   **半同步复制**
    为了提高故障转移的可靠性，主设备可以等待，直到至少有一个从设备记录了事务。
*   **复制心跳**
    主节点定期向所有从节点发送消息。因此，从设备知道主设备何时出现故障，这有助于估计主设备和从设备更新之间的延迟。

## 新的加载 XML 命令

[LOAD XML](http://dev.mysql.com/doc/refman/5.5/en/load-xml.html) 将数据从 XML 文件读入表格。支持三种不同的格式:

```
 <row column1="value1" column2="value2" .../> 
```

或者

```
 <row>
	<column1>value1</column1>
	<column2>value2</column2>
</row> 
```

或者

```
 <row>
	<field name="column1">value1</field>
	<field name="column2">value2</field>
</row> 
```

## 新信号/再信号命令

这两个命令都允许您在存储过程、函数、事件、触发器中实现异常处理。

[信号](http://dev.mysql.com/doc/refman/5.5/en/signal.html)实际上与 PHP 和其他语言中的‘throw’命令相同。它允许您将错误号、SQLSTATE 值和消息传递回调用代码。

让你传播一个信号异常，也许是在做了进一步的工作之后，比如数据清理。RESIGNAL 可以传递错误的原始版本或修改版本。

## 新的 TO_SECONDS()函数

处理日期和时间并不总是像您预期的那样容易。文件系统、PHP 解释器和 MySQL 本身有不同的存储时间的方法，所以许多开发人员求助于 UNIX 时间戳或其他整数格式。

[TO_SECONDS()](http://dev.mysql.com/doc/refman/5.5/en/date-and-time-functions.html#function_to-seconds) 可以帮助解决一些问题；给定任何日期或日期时间值，它返回自 0 年以来的秒数(如果传递任何其他值，则为 NULL)。

## 可插拔认证

到目前为止，连接到 MySQL 服务器的客户机必须传递用户名和密码，并根据 mysql.user 表中的记录进行身份验证。现在可以创建一个插件，使用你自己的系统和凭证来处理认证。

## 提高了 Windows 的性能

许多企业在 Windows 服务器上安装 MySQL。这通常是有意义的；他们已经有具备 Windows 专业知识的 IT 管理员，但是需要一个经济高效的数据库解决方案，或者想要安装一个需要 MySQL 的 web 应用程序。

MySQL 开发团队已经整理了系统，提高了在 Win32 和 Win64 平台上的性能。

你评测过 MySQL 5.5 吗？您体验过性能提升吗？你会升级吗？5.5 版本让您对 Oracle 对数据库的承诺放心了吗？

## 分享这篇文章