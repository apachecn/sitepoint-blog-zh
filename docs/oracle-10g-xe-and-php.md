# Oracle 10g XE 和 PHP

> 原文：<https://www.sitepoint.com/oracle-10g-xe-and-php/>

如果你错过了，昨天甲骨文公司宣布了他们数据库的免费(如啤酒)版本-[甲骨文 10g 快捷版(XE)](http://www.oracle.com/corporate/press/2005_oct/103105_databasexe_finalsite.html)-基本上是一个“精简”版本-一些行业分析[在这里](http://news.com.com/Oracle+to+offer+free+database/2100-1012_3-5920796.html)。撇开此举的意义不谈，更有趣的是有一出戏。今天，我设法在不到 1.5 小时的时间内完成了从 PHP 到 Oracle 的“Hello World”(遇到了一个特定的小故障，需要重新安装，否则时间会更短)。以下是如何…

可在[这里](http://www.oracle.com/technology/products/database/xe/index.html)下载。请注意，在您开始实际下载之前，您已经获得了许多链接，需要同意的在线许可表(勾选所有复选框)，最后需要填写注册信息。下载本身大约是 150Mb，您的系统将需要至少 250Mb 的内存来实际运行数据库。

我把它安装在 Windows XP 下。还有一个 Linux 版本，我将在本周晚些时候在 Ubuntu 下试用(最近有一些硬盘问题，这意味着重新安装所有东西)。OSX 还没有(标准的 Oracle DB 支持 OSX，所以这只是时间问题)。

下载完成后，运行安装程序。它会要求您输入“系统”用户(想想“根”用户)的密码，您应该确保记住这个密码。剩下的安装只需点击“下一步”即可。完成后，它会启动一个浏览器，这样您就可以登录到您的本地实例[html db](http://www.oracle.com/technology/products/database/htmldb/index.html)——大致相当于 phpMyAdmin。一般来说，如果您曾经尝试过安装完整的 Oracle 数据库，相比之下，这种安装是轻而易举的(甚至不是基于 Java 的安装程序——InstallShield 我相信，但可能是错误的)。

要登录，您需要用户名“system”和您在安装过程中输入的密码。然后，您需要遵循“入门”指南中提供的说明(安装程序会在您的“开始”菜单上添加一个指向该指南的链接)，特别是如何启用“HR”用户。

做完这些，下一步是 PHP。首先你需要创建几个环境变量(控制面板>系统>高级>环境变量)；

*   `ORACLE_SID = XE`(…把它想象成你的数据库的名字)
*   `ORACLE_HOME = C:oraclexeapporacleproduct10.2.0server`(…假设默认安装路径)

如果你将 Apache 作为 Windows 服务运行，现在最简单的方法就是重启 Windows。

现在编辑你的`php.ini`并取消注释`extension=php_oci8.dll`(我假设这里你有`extension_dir`指向正确的地方)——注意我运行的是 PHP 5.0.5，下面的代码反映了这一点。重启 Apache 并检查您的`phpinfo()`。如果您没有得到关于无法找到 php_oci8.dll 的错误消息，那么一切可能都是好的。如果您*确实*得到了那个错误消息，要么是 dll *真的*找不到(检查它是否存在于您的`extension_dir`中)，要么是上述两个环境变量设置不正确(或者 Apache 正在作为服务运行，而您还没有重新启动 Windows)。

假设一切顺利，您可以看到由`phpinfo()`列出的 OCI8 扩展，这里有一个类似“Hello World”的东西…

```
<?php
$conn = oci_connect('hr','hr'); // This assumes you followed the Getting Start guide...

$sql = "SELECT * FROM employees";

$stmt = oci_parse($conn, $sql);

oci_execute($stmt);

echo '<pre>';

while ( $row = oci_fetch_assoc($stmt) ) {
  print_r($row);
}

echo '</pre>';
?>

```

Oracle 为 PHP 开发人员提供了更多的信息。

通过阅读和我所做的一些尝试，除了资源限制(每个系统一个数据库，最大 1GB 或内存使用量，最大 4GB 数据库大小)，它似乎拥有普通 Oracle 数据库的所有功能(如果有人有相关信息，我会有兴趣了解更多)。

请注意，我遇到的可能对非美国用户有帮助的小故障是，我第一次尝试安装时将 Windows 设置为瑞士德语作为首选语言，这导致我在尝试使用 SQL*Plus 登录时出现[ORA-27101](http://ora-27101.ora-code.com/)(html db 无法启动)。重新安装后，切换到美国英语(控制面板>区域设置>使所有标签上的所有内容都是“美国英语”)解决了这个问题。

无论如何，总的来说是一次愉快的安装体验。Oracle 已经明确地“解决”了如何轻松开始的问题。这是否会导致那些可能选择开源数据库的人的大量使用还很难说。由于 Oracle XE 的使用受到资源限制，我怀疑它是否真的是共享主机的一个选项，但对于需要随产品分发数据库的软件供应商来说，它可能很有用。对于开发人员来说，这无疑是开始了解 Oracle 的好地方。

## 分享这篇文章