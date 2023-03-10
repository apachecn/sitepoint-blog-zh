# phpMyAdmin 的 3 个轻量级替代方案

> 原文：<https://www.sitepoint.com/phpmyadmin-alternatives/>

当面对 MySQL 管理任务时，许多开发人员依赖于 [phpMyAdmin](http://www.phpmyadmin.net/) 。顾名思义，它是用 PHP 编写的，所以它可以安装在您的 web 服务器上，并通过浏览器访问。如果你觉得这个界面有点过时，你可以考虑[MonoQL](http://sourceforge.net/projects/monoql/)——一个更巧妙的 Ajax 化的替代品。大多数时候，开发人员只想检查一些数据，修改一些记录，或者备份数据库。phpMyAdmin 和 MonoQL 压缩发行版的大小从 2MB 到 7MB 不等，对于大多数日常管理来说，它们已经大材小用了。你在用大锤砸花生。以下是您应该考虑的三种轻量级 MySQL 管理方案。它们都是基于 PHP 的、开源的，非常适合快速数据库任务。

## SQL 好友

[![SQL Buddy](img/3e04b45c7a4b99f4a633d394c5664ddc.png) ](http://www.sqlbuddy.com/) SQL Buddy 提供全面的特性集，支持 SQLite 以及 MySQL 数据库。该工具允许您创建、修改或删除表、索引、外键关系和记录。备份很容易，您可以运行特定的 SQL 查询。SQL Buddy 有一个快速且吸引人的 Ajax 界面，支持多种语言和主题。下载文件只有 320kB (1.1MB 解压缩文件)，不需要安装，只需将文件复制到您的服务器，然后使用数据库用户 ID 和密码登录。总的来说，SQL Buddy 在很多层面上击败了 phpMyAdmin。这不仅仅是一种选择；对你来说，这甚至可能是一个更好的选择。从[sqlbuddy.com](http://www.sqlbuddy.com/)或[code.google.com/p/sql-buddy/](http://code.google.com/p/sql-buddy/)下载 SQL Buddy。

## 爱慕者

[![Adminer](img/0e585915485cb6f136de49b68e8b2f13.png) ](http://www.adminer.org/) Adminer 是一个 186kB 的文件(如果需要多语言版本，也可以是 281kB)。没有复杂的安装—只需上传文件并登录即可。令人惊讶的是，Adminer 支持 MySQL、PostgreSQL、SQLite、SQL Server 和 Oracle，并在 PHP4 或 PHP5 下运行。甚至还有一个 [WordPress 插件版本](http://wordpress.org/extend/plugins/adminer/)。Adminer 可以修改表、索引、外键、视图、存储过程、函数和触发器。很容易浏览或更新数据，并运行自己的 SQL 查询。该界面是功能性的，而不是有吸引力的，但它很快，易于使用。Adminer 易于安装，这使它成为快速和肮脏的数据库管理的理想选择。它已经成为我选择的工具。从[adminer.org](http://www.adminer.org/)或[sourceforge.net/projects/adminer/](http://sourceforge.net/projects/adminer/)下载管理员。

## PHP 迷你管理

[![PHP Mini Admin](img/2edac7e4cdd6583b0cf2bb747d02046d.png)](http://phpminiadmin.sourceforge.net/) 如果 SQL Buddy 和 Adminer 对于你的需求来说过于臃肿，或许你应该考虑 PHP Mini Admin？下载是 10kB。是的，10kB—提取到一个 26kB 的文件中。像 Adminer 一样，您上传文件并登录。可以理解的是，功能更加有限，但是浏览表和检查、导出或导入数据很容易。如果您乐于编写自己的 SQL 命令，它可以做任何事情。PHP 迷你管理是快速和功能，所以你还需要什么？从 phpminiadmin.sourceforge.net 的[下载 PHP 迷你管理。你有首选的基于网络的 MySQL 管理工具吗？欢迎评论…](http://phpminiadmin.sourceforge.net/)

**note:**Want more?

如果你想阅读更多 Craig 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章