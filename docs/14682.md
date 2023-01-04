# 调整您的 Linux 服务器

> 原文：<https://www.sitepoint.com/tune-your-linux-server/>

对很多人来说，调整 Linux 服务器可能意味着很多事情。调整的一部分是关于安全性的，确保 ftp、邮件、ssh 和 web 服务器的配置设置正确，以便能够访问您的服务器并与之交互，同时为那些可能浏览潜在后门的人勒紧腰带。

然而，调优的一个重要部分是确保只有必要的服务在服务器上运行，以最大化可用的内存、处理器和磁盘空间。

在接下来的专栏中，我将讨论 Webmin([http://www.webmin.com](http://www.webmin.com))，它是用于 Linux 服务器管理(以及 Solaris 和许多 Unix 风格)的主要开源图形用户界面。它也是一个调优 Linux 服务器的优秀工具，因为它提供了对启动和关闭配置脚本以及所有使用的关键 web 环境服务器的根访问。

例如，如果您为您的虚拟主机客户管理一个专用服务器，通过 Webmin 查看启动项目(那些被安排在系统每次启动时启动的项目)可能会发现几个不必要的服务正在运行。您可以禁用 CUPS(用于*nix 打印)、NFS 和 Samba，以及任何您不使用的应用服务器。

在我的一台服务器上，我们发现 Interbase、PostgreSQL 和 MySQL 都在启动时启动，然而，我们只需要 MySQL 用于这台机器。通过禁用这些和其他服务(ldap、named 和 snmpd)，我们看到可用 RAM 增加了 15%。当来自数据库处理的负载是站点的关键部分时，这可以极大地提高性能。

如前所述，安全性在调优中也很重要，Webmin 提供了对 INETD 或 XINETD 的轻松访问，以收紧正在运行的 internet 服务和协议(如 echo、finger、rlogin、rsync、telnet 等)。ftp 和 pop 的协议也在这里，所以一起禁用守护进程不起作用。

## 分享这篇文章