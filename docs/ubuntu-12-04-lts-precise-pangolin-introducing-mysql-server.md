# Ubuntu 12.04 LTS 精确穿山甲:引入 MySQL 服务器

> 原文：<https://www.sitepoint.com/ubuntu-12-04-lts-precise-pangolin-introducing-mysql-server/>

MySQL 是当今市场上最快的(如果不是最著名的)数据库系统之一。形成任何 LAMP (Linux，Apache，MySQL，PHP)或 LEMP (Linux，Nginx，MySQL，PHP)“stack”中的核心包之一，在这篇文章中我将向您展示如何在 Ubuntu 12.04 LTS Precise Pangolin 上安装、配置和管理 MySQL。

所以让我们开始吧…

## 装置

按照传统方式，让我们通过更新我们的系统来开始这个过程:
打开终端或转到您的控制台并键入:

```
sudo apt-get update && sudo apt-get upgrade
```

接受任何可用的更新，然后安装 MySQL 服务器，如下所示:

```
sudo apt-get install mysql-server mysql-client
```

这个过程不会花很长时间，但在安装过程中，会提示您为 MySQL“root 用户”设置密码。因此，请选择一个强密码，并将其保存在安全的地方以备将来参考。

完成后，运行以下命令来保护您的安装:

```
sudo mysql_secure_installation
```

该实用程序允许您限制对“root”帐户的访问，它删除测试数据库，并允许您删除所有匿名帐户。它使用起来非常简单，但为了帮助您，我提供了以下示例:

```
By default, a MySQL installation has an anonymous user, allowing anyone
to log into MySQL without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MySQL comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MySQL
installation should now be secure.

Thanks for using MySQL!
```

完成后，您将退出安全安装过程，并完成 MySQL 的安装。

## 配置

干得好，MySQL 服务器现在已经安装在您的系统上，但是在您开始在任何环境中使用它之前，您应该花几分钟来配置该服务以支持您的网络需求。

同样，这是一个相对简单的过程，典型系统上的大多数文件都可以在以下位置找到:

```
/etc/mysql/
```

您可以随时编辑/etc/mysql/my.cnf 文件来配置基本设置，如日志文件、端口号、绑定等，但出于本文的目的，我们将坚持最常见的做法。

准备好后，桌面用户应该打开终端类型:

```
sudo gedit /etc/mysql/my.cnf
```

而所有基于服务器的用户应该键入:

```
sudo nano /etc/mysql/my.cnf
```

> 服务器用户可以用他们最喜欢的“文本编辑器”代替“纳米”

对“/etc/mysql/my.cnf”进行任何更改后，记得在重新启动 mysql 服务之前保存并关闭该文件，如下所示:

```
sudo service mysql restart
```

### 绑定地址

默认情况下，MySQL 限制对本地主机的所有访问，如下所示:

```
bind-address		= 127.0.0.1
```

这对于封闭或安全的服务器安装，甚至是独立的桌面开发环境都是理想的，但它并不总是适合每个人的需要，对于那些喜欢全局访问或希望从远程系统提供 tcp/ip 连接的人，只需找到并注释下面一行:

```
bind-address		= 127.0.0.1
```

所以看起来是这样的:

```
#bind-address		= 127.0.0.1
```

最后一个例子如下:

```
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
#bind-address		= 127.0.0.1
```

或者，您可以将 MySQL 配置为侦听来自特定网络地址的连接，如下所示:

```
bind-address		= XXX.XXX.XXX.XXX
```

通过替换 XXX。XXX.XXX.XXX，其 ip 地址与您的需求相关(即您的本地网络或基于互联网的 IP 地址)。
一个例子如下:

```
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address		= 192.168.1.100
```

完成后，保存并关闭文件，然后运行以下命令重新启动 MySQL 服务:

```
sudo service mysql restart
```

### 查询缓存(可选)

MySQL 查询缓存用于提高任何查询的性能，正如大多数 web 开发人员和数据库管理员所知,“查询缓存”可以显著提高 web 应用程序的速度。

它允许 MySQL 服务器存储最近的 SELECT 语句。如果稍后请求相同的 SELECT 语句(由同一个或另一个用户/访问者请求)，则服务器从查询缓存中检索结果，而不是再次执行相同的语句并导致额外的服务器负载。

管理“查询缓存”并不是一门精确的科学，可能会涉及大量的试验和错误，因为它通常取决于所使用的硬件和所使用的应用程序的性质，但是通过对您的工作应用程序进行定期测试，“查询缓存”有可能为您的数据库和服务器的整体性能提供实质性的改进。

您可以通过打开以下文件进行任何更改:

```
/etc/mysql/my.cnf
```

向下滚动以找到以下行，并进行所需的更改:

```
# * Query Cache Configuration
#
query_cache_limit	= 1M
query_cache_size        = 16M
```

其中(M=MB)“查询缓存限制”是将被缓存的最大查询大小(以字节为单位)，“查询缓存大小”是以字节为单位的缓存大小。一个有效的“经验法则”是，开始时估计您的缓存在 32M-256M 之间(取决于可用的 RAM 总量)，但是如果您的数据不断更新，那么减少缓存可能是更好的解决方法。

> 将“query_cache_size”值设置为 0 将有效地禁用缓存。

这种变化的典型例子如下:

```
# * Query Cache Configuration
#
query_cache_limit	= 2M
query_cache_size        = 32M
```

和往常一样，完成后，在通过运行以下命令重新启动 MySQL 服务之前，保存并关闭文件:

```
sudo service mysql restart
```

## 服务状态

要停止 MySQL 服务器，请使用:

```
sudo mysql stop
```

要启动 MySQL 服务器，请使用:

```
sudo mysql start
```

要重启 MySQL 服务器，请使用:

```
sudo mysql restart
```

要检查 MySQL 服务器的状态，请使用:

```
sudo netstat -tap | grep mysql
```

但是对于那些喜欢更多关于 MySQL 服务器的信息的人，您可以使用:

```
/usr/sbin/mysqld --help --verbose
```

是的，这个最后的命令确实提供了很多信息:-)

## MySQL 工具

有许多软件包可以帮助你使用你自己的 MySQL 服务器。这些包括但不限于:

*   MySQL 工作台

    ```
    sudo apt-get install mysql-workbench
    ```

*   MySQL 导航器

    ```
    sudo apt-get install mysql-navigator
    ```

*   艾玛

    ```
    sudo apt-get install emma
    ```

*   MySQL 管理员

    ```
    sudo apt-get install mysql-admin
    ```

*   PHPMyAdmin

    ```
    sudo aptitude install phpmyadmin
    ```

    (需要带 PHP 的 Apache)

要安装以上任何一个，只需打开 Ubuntu 软件中心，搜索相关工具或使用所示的命令行代码。

> 我建议先看看 [MySQL 工作台【T1:-)](http://www.mysql.com/products/workbench/)

## MySQL 命令

MySQL 使用 SQL 或“结构化查询语言”，虽然这超出了本文的范围，但我将为您提供一个关于如何在命令行上管理 MySQL 服务器的简明教程:

### 一般访问

您可以通过以下方式访问您的 MySQL 服务器:

```
mysql -u root -p
```

使用您的 MySQL“root”密码和验证您自己，您将被授予访问 MySQL 管理控制台的权限，如下所示:

```
Welcome to the MySQL monitor.  Commands end with ; or g.
Your MySQL connection id is 37
Server version: 5.5.22-0ubuntu1 (Ubuntu)

Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or 'h' for help. Type 'c' to clear the current input statement.

mysql>
```

从现在开始，您将在 MySQL 中工作，直到您按照下面的指令退出应用程序。在 mysql >提示符下键入:

```
exit;
```

退出应用程序后，您的原始终端或控制台环境将被恢复。

### 添加数据库用户

要添加名为“personsname”的用户并从本地主机授予访问权限，请执行以下操作:

```
mysql> GRANT ALL ON databasename.* TO personsname@localhost IDENTIFIED BY 'Add-Your-Password-Here';
FLUSH PRIVILEGES;
```

或者，您可以使用网络变体:

```
mysql> GRANT ALL ON databasename.* TO personsname@192.168.1.10 IDENTIFIED BY 'Add-Your-Password-Here';
FLUSH PRIVILEGES;
```

### 查看数据库目录

要列出当前数据库，请在 mysql >提示符下键入以下 sql 命令:

```
mysql> show databases;
```

### 创建数据库目录

要添加名为 XXX 的新数据库，请在 mysql >提示符下键入以下 sql 命令:

```
mysql> create database XXX;
```

> 如果你想了解更多，那么我建议你简单地看一下[SQL by site point](https://www.sitepoint.com/books/sql1/)来更深入地了解 SQL 语法。这是一本很好的读物:-)

## 日志文件

在我离开之前，根据您的配置更改，对于典型安装，所有日志文件都可以在以下位置找到:

```
/var/log/
```

> 记住，如果你运行的是防火墙，你需要为你的 MySQL 服务器打开端口 3306

所以直到下次…
我希望你继续享受使用 Ubuntu 12.04 LTS 精确穿山甲。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Ubuntu Linux](https://learnable.com/courses/ubuntu-linux-65) 。

## 分享这篇文章