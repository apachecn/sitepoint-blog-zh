# 安装新的 MySQL 管理

> 原文：<https://www.sitepoint.com/installing-new-mysql-admin/>

新的[URL = https://www . site point . com/blog-post-view . PHP？id = 151673]MySQL Administrator[/URL]希望其用于管理多个数据库服务器、复制和灾难恢复工具的工具更加强大且非常企业友好。安装这个非常简单，但是，请记住，正如 MySQL 提醒大家的，这是一个 alpha 版本，因此不适用于生产环境。在您的机器上安装之前，请备份您的数据。

为 Linux 安装二进制下载非常简单。从 MySQL 获取文件，网址为–>
[http://www.mysql.com/downloads/administrator.html](http://www.mysql.com/downloads/administrator.html)，发出以下命令(MySQL 推荐):

mysql-administrator-1.0.1a-alpha-linux.tar.gz | tar XV-C/opt

MySQL Administrator 的二进制文件将安装在/opt/mysql-administrator/bin，
中，您可以从这里执行“mysql-administrator”来启动应用程序。

如果您从 Microsoft 平台管理 MySQL 数据库服务器，GUI 也可用于 Windows，并且可以在上面的同一链接中找到。

最后，要从源代码构建，请遵循 MySQL 管理员附带的 MySQL FAQ 中的说明。

==========================
来自 MySQL 管理员 FAQ.txt
===================

问:MySQL Administrator 支持哪些版本的 Linux？

答:MySQL Administrator 已经在:
–Debian unstable
–Fedora core 1
–SuSE 9 上测试过

包括 Gtk+在内的大多数库都是静态链接的，除了下面的
标准库:
glibc 2 . 3 . 2
libx 11 6.2
libstdc++ 5 . 0 . 5

—

问:构建 MySQL Administrator 需要什么？

答:除了 g++开发套件，您还需要以下
库和相应的开发包:

–GTK-2 . x
–gtkmm-2 . x
–libglade-2 . x

—

问:如何自己搭建 MySQL Administrator？

答:以下 BitKeeper 模块可以从
[http://mysql.bkbits.net](http://mysql.bkbits.net)下载，使用 BitKeeper 版本管理系统:

– mysqlxlibrary
– mysqladministratorlibrary
– mysqlcommonguiparts
– mysqladministrator

对于每个目录，执行以下命令:

sh autogen.sh
。/配置
制作
制作安装

如果一切顺利，您应该已经在/usr/local/bin 中构建并安装了 mysql-administrator 二进制文件
。

## 分享这篇文章