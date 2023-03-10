# 学习 Ruby on Rails:终极初学者教程

> 原文：<https://www.sitepoint.com/learn-ruby-on-rails-7/>

## 数据库配置

为 Rails 应用程序配置数据库非常简单——所有关键信息都包含在一个文件中。我们现在来看看，然后创建一些数据库供我们的应用程序使用。

## 数据库配置文件

环境的分离反映在 Rails 数据库配置文件 database.yml 中。我们在第 1 章《Ruby on Rails》中看到了该文件的示例，事实上，当我们使用`rails`命令时，我们在第 2 章《入门》中创建了自己的配置文件。去看看吧！它位于我们的 Shovell 应用程序的`config`子目录中。

去掉注释后，文件应该是这样的(根据您的 MySQL 配置，您可能需要使用`127.0.0.1`作为您的主机值，而不是`localhost`。):

```
Example 4.1\. 01-database.yml

development:
adapter: mysql
database: shovell_development
username: root
password:
host: localhost
test:
adapter: mysql
database: shovell_test
username: root
password:
host: localhost
production:
adapter: mysql
database: shovell_production
username: root
password:
host: localhost
```

该文件列出了连接到每个环境(开发、测试和生产)的数据库服务器所需的最少信息。有了我们在第 2 章《入门》中安装的 MySQL 的默认设置，我们可以放心地使用`root`用户和一个空密码继续我们的开发——我们所有的开发都应该在本地机器上进行，所以我们不必担心有人访问我们的超级机密的 Shovell 应用程序。

参数数据库设置将在每个环境中使用的数据库的名称。正如配置文件所示，Rails 能够并行支持多个数据库。请注意，我们这里实际上讨论的是不同的数据库，而不仅仅是不同的表——每个数据库可以并行托管任意数量的不同的表。图 4.1 显示了这种架构的图形表示。

![1562_fig4.1](img/f769b77960dc80d6f05f31741b11352b.png)
*图 4.1。Rails 应用程序的数据库架构*

然而，我们当前的配置缺少一个重要的方面:配置文件中引用的数据库还不存在！让我们现在创建它们。

我们可以使用 MySQL 可用的众多图形前端之一来创建这些数据库，或者我们可以直接进入命令行。因为命令相当简单，所以现在让我们从命令行创建数据库；在本章的后面，我们将研究图形数据库客户端。

## 创建数据库

要启动 MySQL 命令行界面，请在命令提示符下键入`mysql -u root`。(在 Mac 上，这个命令叫做`mysql5`而不是`mysql`——Mac 用户喜欢与众不同。)

```
$ mysql -u root
mysql>
```

创建新数据库的命令非常简单:`create database newdatabasename`。

我们将使用它来创建三个数据库——每个环境一个——如图 4.2 所示。

```
Example 4.2\. 02-create-databases.sql

CREATE DATABASE shovell_development;
CREATE DATABASE shovell_test;
CREATE DATABASE shovell_production;
```

## 数据库安全和`root`用户

如果您有数据库方面的经验，您可能会对我们使用 root 用户开发我们的应用程序感到有点不舒服，甚至没有设置密码。这一建议背后的理由如下:

1.MySQL 的默认配置是只能从同一台机器上连接到数据库服务器。这意味着没有人——不管他们是坐在你旁边，还是在世界的另一边工作——能够破坏你的 Rails 开发环境。

2.MySQL 命令行和权限系统是复杂而强大的工具，而数据库安全性这个主题绝对超出了本书的范围。

当然，这不是我推荐给你的生产环境的配置，但是我们将在第 12 章，部署和生产使用中讨论。如果您对保护开发环境中的数据库感兴趣， [MySQL 手册包含一些安装后说明](http://dev.mysql.com/doc/refman/5.0/en/post-installation.html),应该会对您有所帮助。

![1562_fig4.2](img/31f8e9f98b5d9de890d1f0517c2623a1.png)
*图 4.2。为每个环境创建一个数据库*

现在我们的数据库已经存在，我们可以用它们来为我们的应用程序存储数据！

## 开发是默认的数据库

默认情况下，所有 Rails 应用程序都使用开发环境，除非另有说明。因此，您从命令行执行的任何 Rails 命令暂时只会影响开发数据库中的数据。在第 12 章，部署和生产使用中，我们将学习如何切换到生产环境。

**Go to page:** [1](https://sitepoint.com/learn-ruby-on-rails-1/) | [2](https://sitepoint.com/learn-ruby-on-rails-2/) | [3](https://sitepoint.com/learn-ruby-on-rails-3/) | [4](https://sitepoint.com/learn-ruby-on-rails-4/) | [5](https://sitepoint.com/learn-ruby-on-rails-5/) | [6](https://sitepoint.com/learn-ruby-on-rails-6/) | [7](https://sitepoint.com/learn-ruby-on-rails-7/) | [8](https://sitepoint.com/learn-ruby-on-rails-8/) | [9](https://sitepoint.com/learn-ruby-on-rails-9/) | [10](https://sitepoint.com/learn-ruby-on-rails-10/)

## 分享这篇文章