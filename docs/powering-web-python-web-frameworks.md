# 使用 Python Web 框架为 Web 提供动力

> 原文：<https://www.sitepoint.com/powering-web-python-web-frameworks/>

## 介绍

Web 框架主要是服务器端技术，旨在完成一系列专门的任务。与此相关，Python Web 框架是模块和包的集合，使开发人员能够构建 Web 应用程序，而无需考虑某些细节。这些由所选框架处理的细节消除了诸如性能瓶颈之类的复杂性。

因此，开发人员可以自由地专注于 Web 开发职责，尤其是应用程序逻辑。最后，已经开发了许多 Web 框架来促进 Pythonic Web 开发，包括 Zope、CherryPy 和 Django。

## 用 Django 开发 Web 应用程序

Django 是一个 Python Web 框架，旨在支持快速应用开发(RAD)。通过严格遵守不重复(DRY)原则，Django 促进了高水平、高性能 Web 应用程序的开发，而无需花费时间重新发明轮子。因此，用 Django 编写完整的动态、数据驱动的网站和应用程序是一项非常简单的任务，只需要两个必要的步骤:

*   确保安装了 Python。
*   下载并安装 Django。

例如，考虑构建一个数据驱动的网站。

在终端中，导航到新网站的所需位置，并通过在提示符下键入`python`来启动 Python 交互式 shell。然后，输入以下代码:

```
django-admin.py startproject my_new_web_site[code]

Here, the built-in command, <code>django-admin.py</code>, is used to create an on-the-fly project named “my_new_web_site”; the name may be any desired name.

Used to manage Django applications, the <code>django-admin.py</code> executable shell supplies a project with a list of Django subcommands. In this example, the <code>startproject</code> subcommand is used to create a Django project tree with the following structure: 

[code]my_new_web_site/
	manage.py
  	my_new_web_site/
		__init__.py
		settings.py
		urls.py
		wsgi.py
```

`my_new_web_site`表示“我的新网站”项目的父目录。为了避免混淆，重命名这个目录是个好主意，因为 Django 默认情况下对 Python 包使用相同的名称。

y 是一个多用途实用程序，用于与项目沟通。

`my_new_web_site/my_new_web_site`表示项目的 Python 包的实际位置。

`settings.py`是设置和存储项目各种配置的地方。

`urls.py`是一个重要的文件，提供各种功能，例如从 Python 代码中解耦 URL，以及 URL 函数和 Python 回调之间的映射(Django“views”)。

假定存在 WSGI 兼容的 Web 服务器，则使用`wsgi.py`。

## 运行应用程序

此时，项目已经开发完成，可以通过发出以下命令在 Web 浏览器中查看:

```
$ python manage.py runserver
```

该命令将返回到终端，输出类似如下:

```
Validating models...

0 errors found
November 09, 2013 - 17:57:17
Django version 1.6, using settings 'my_new_web_site.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

只需将 Web 浏览器定向到地址:http://127.0.0.1:8000/，将显示以下消息:

> 成功了！祝贺你的第一个 Django 页面。

这就完成了创建由 Python 和 Django 框架支持的 Web 项目和应用程序的最基本步骤。然而，该项目并不十分实用。为了使该项目成为一个动态的、数据驱动的应用程序，应该添加一个数据库，这也将消除编写内容的许多乏味和低效。

## 添加数据库功能

按照设计，Django 将应用程序的内容保存在 SQLite 数据库中，不需要任何进一步的操作。而且，虽然 SQLite 对于数据库新手或者在应用程序增长不是问题的情况下已经足够了，但是使用 MySQL 这样的数据库可能是最好的。

例如，假设已经安装了 mysql，通过在 MySQL 提示符下运行以下命令创建一个 MySQL 数据库:

```
mysql> CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
mysql> CREATE DATABASE my_new_web_site;
mysql> GRANT ALL ON my_new_web_site.* TO 'username'@'localhost';
```

成功创建新数据库后，通过添加以下内容修改`settings.py`中的配置设置:

```
DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.mysql',
		'NAME': 'my_new_web_site',
		'USER': 'username',
		'PASSWORD': 'password',
		# localhost: leave “HOST” empty for domain sockets or use “127.0.0.1” for TCP.
		'HOST': '',
		# Leave the port number empty if using the default port, 8000.
		'PORT': '',
	}
}
```

这创建了一个新的 MySQL 数据库，可以在整个应用程序中使用，但是它既没有初始化数据库，也没有创建应用程序“超级用户”(管理员)。要创建“超级用户”，运行`syncdb`命令并按照屏幕提示进行操作:

```
$ python manage.py syncdb

Creating tables ...
Creating table auth_permission
Creating table auth_group_permissions
Creating table auth_group
Creating table auth_user_groups
Creating table auth_user_user_permissions
Creating table auth_user
Creating table django_content_type
Creating table django_session
Creating table django_site

…

Username (leave blank to use 'myusername'):
Email address:
Password:
Password (again):
Superuser created successfully.
Installing custom SQL ...
Installing indexes ...
Installed 0 object(s) from 0 fixture(s)
```

这个应用现在有了一个完整的基于 MySQL 的后端，可以通过`manage.py`访问:

```
$ python manage.py shell
>>> from django.contrib.auth.models import User
>>> User.objects.all()
[<User: myusername>]
```

此时，项目已经完全开发完成，可以使用了:运行一个 Web 应用程序。

## 结论

Python Web 框架旨在将传入的 HTTP 请求与特定的 Python 代码相匹配，或者提供响应和请求对象。此外，这些框架还用于开发大型 Web 应用程序，如内容管理系统(CMSs)、概念原型等等。有许多 Python Web 框架适合面向对象的 Web 应用程序开发。

而且，正如本介绍性教程所示，一些框架是全栈应用程序，可用于创建功能完整、可扩展、数据驱动的应用程序。

## 分享这篇文章