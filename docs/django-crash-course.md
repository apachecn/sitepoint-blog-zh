# 姜戈的速成班

> 原文：<https://www.sitepoint.com/django-crash-course/>

您是否在寻找一个能够大幅缩短构建数据驱动的 web 应用程序所需时间的 web 框架？如果是这样，基于 Python 的 Django 可能值得研究。Django 拥有支持大量代码重用的独特架构和自动管理系统，是独立 web 开发人员的最佳选择，也是大型 web 项目的强大工具。在本文中，我将向您展示如何建立自己的基于 Django 的网站。

## 介绍姜戈

Django 是一个成熟的高级 web 应用程序框架，它使 web 开发人员能够在创纪录的时间内启动并运行健壮的应用程序。它由 Python 驱动，您可能不熟悉，但很容易掌握。如果你需要一个快速的 Python 速成班，我推荐在 10 分钟内学习 Python 的*，尽管关于 Python 语言的 [*快速、轻松的教程*](http://heather.cs.ucdavis.edu/~matloff/Python/PythonIntro.pdf) 也提供了很多深度。*

 *Django 对于 web 开发人员的关键特性是由多个独立的、基于功能的应用程序驱动的单个 web 站点的概念。这些应用程序可能提供博客、用户登录系统，甚至 TinyURL 风格的重定向服务。您的网站由一组应用程序组成，在构建网站时，您只需选择现有的应用程序(或者构建自己的应用程序)来提供您需要的功能。

另一个杀手锏是自动管理系统。当您在 Django 中构建模型时，您会得到一个免费的管理系统来添加、删除和更新它们，自动管理底层数据库。管理系统将为最终用户提供一个丰富的界面来更新数据库中的数据。自动管理系统是一个完整的端到端的数据接口解决方案，开箱即可投入生产使用；它也非常可定制。*  *## 开始之前

在阅读本教程之前，您应该对数据库后端有基本的 web 开发经验，最好有基本的 Python 背景。有使用一些主要框架构建 PHP 应用程序的经验就足够了。Django 利用了 web 应用程序的复杂方法，并且确实需要对 web 开发概念有扎实的掌握。

本教程需要安装 Django。浏览安装文档(如果你还没有设置好的话),点击安装正式版本。这篇文章是用 1.2 alpha 编写的；1.1 版本现已推出，本教程适用于任何 1.x 版本。

安装时，确保`django-admin.py`工具在您的系统路径上。这应该是自动发生的，但是要检查，打开您系统的终端或命令行控制台并运行`django-admin.py`；如果输出显示为`Type 'django-admin.py help' for usage`(或者类似的东西)，那么就可以开始了。

为了探索 Django 框架，我们将创建自己的博客应用程序(尽管有很多可用的)，并用它来驱动一个简单的博客站点。我们还将使用内置的管理、评论和认证应用程序。确保您熟悉命令行，因为我们会经常使用它。

你可以在这里下载一份[完成的应用程序，但是我们将会覆盖所有需要的代码，所以你最好简单地跟着做并创建文件。](http://sitepoint-examples.s3.amazonaws.com/django/spblog.zip)

## 设置管理站点

首先选择一个文件夹来存储 Django 项目——我使用的是`~/Development/Django/`——然后在命令行中切换到这个文件夹。我们需要为我们的网站启动一个新的 Django 项目，所以当你准备好的时候运行`django-admin.py`**start project**`spblog`。然后切换到新创建的`spblog`文件夹，看看`django-admin.py`工具创建的文件(Windows 用户:用 **dir** 代替 **ls** )。这是它在终端中的外观:

```
$ `cd ~/Development/Django`$ `django-admin.py startproject spblog`$ `cd spblog`$ `ls`__init__.py  manage.py  settings.py  urls.py
```

`django-admin.py`脚本创建了四个文件，但是我们只需要担心两个:`settings.py`和`urls.py`。

你的第一个 Django 应用程序已经准备好了！让我们启动一个测试服务器，并试一试:

```
$ `python manage.py runserver`Validating models...0 errors foundDjango version 1.2 pre-alpha SVN-11600, using settings 'spblog.settings'Development server is running at http://127.0.0.1:8000/Quit the server with CONTROL-C.
```

在这个特定的 Django 项目中，`manage.py`脚本提供了对许多有用工具的访问。其中一个工具是内置的 web 服务器(仅用于开发目的！)，它现在在`http://127.0.0.1:8000/`为您的 web 应用提供服务。将该 URL 加载到您的 web 浏览器中，您将看到以下页面。

**图一。成功了！**

![It worked!](img/0d743aec82b2eff54804f11d5d20070e.png)

正如欢迎页面所示，我们仍然需要一个应用程序来提供内容。让我们从内置的管理站点开始。

要使用管理站点，我们首先需要配置数据库。我们将使用基于文件的 SQLite 数据库，并安装管理应用程序。首先打开`settings.py`并更改以下设置:

```
DATABASE_ENGINE = 'sqlite3'DATABASE_NAME = 'dev.db'
```

我们还需要将`django.contrib.admin`添加到`INSTALLED_APPS`设置的末尾:它应该是这样的:

```
INSTALLED_APPS = (  'django.contrib.auth',  'django.contrib.contenttypes',  'django.contrib.sessions',  'django.contrib.sites',  'django.contrib.admin',)
```

现在回到你的命令行。我们将使用`manage.py` syncdb 命令创建数据库，这也将提示我们创建一个管理员用户帐户。您的命令历史应该是这样的:

```
$ `python manage.py syncdb`Creating table auth_permissionCreating table auth_groupCreating table auth_userCreating table auth_messageCreating table django_content_typeCreating table django_sessionCreating table django_siteCreating table django_admin_logYou just installed Django's auth system, which means you don't have any superusers defined.Would you like to create one now? (yes/no): `yes`Username (Leave blank to use '…'): E-mail address: …Password: Password (again): Superuser created successfully.Installing index for auth.Permission modelInstalling index for auth.Message modelInstalling index for admin.LogEntry model
```

在此过程中，使用您选择的用户名和密码创建一个帐户，您将使用该帐户登录管理站点。

在使用管理站点之前，我们还需要完成一项任务:配置 URL。Django 有一种独特的 URL 路由方法，其中 URL 不会自动映射到特定的请求处理代码(或者控制器方法/动作，如果您使用了另一个 MVC 框架的话)。相反，我们使用正则表达式将 URL 映射到视图。关于 Django 中 URL 的更多细节，请参见文档中的 [URL Dispatcher 部分。](http://docs.djangoproject.com/en/dev/topics/http/urls/)

打开`urls.py`，取消与管理系统相关的这些行的注释(Python 单行注释以`#`开头):

```
from django.contrib import adminadmin.autodiscover()⋮(r'^admin/', include(admin.site.urls)), 
```

现在，所有以`admin/`开头的 URL 都将被委托给管理系统。此外，我们调用`autodiscover`方法:这将使管理系统检查数据库，以发现它将管理什么数据库表。我们把这个命令放到`urls.py`中，因为它会在服务器每次启动时运行一次。

保存`urls.py`并返回命令行；开发服务器应该已经自动重启了(如果没有，用**Ctrl****C**杀死它，再运行一次)。

现在，如果我们在`http://localhost:8000/`刷新浏览器，我们会看到一个不同的页面:

**图二。找不到页面**

![Page not found](img/4ad7d6e950f714387dce21400e6867a4.png)

这是一个特殊的 404 页面，仅用于开发目的——一旦你在设置中将 *`DEBUG`* 从`True`切换到`False`，它将不再出现。然而，现在您已经配置了一些 URL，Django 不再显示欢迎页面，而是提供一个有效 URL 的列表(假设您试图访问的 URL 是无效的)。由于我们的`admin/`网址似乎没问题，让我们前往`http://localhost:8000/admin/`检查我们的管理网站。

使用运行 syncdb 命令时配置的用户名和密码登录到管理站点；您应该会看到管理主页:

**图 3。Django 行政部门**

![Django administration](img/69f1cca7f0ba9365a680872962698390.png)

您的管理网站已经准备就绪！花些时间去探索，创建一些用户，并试验可用的工具。* 

## *分享这篇文章*