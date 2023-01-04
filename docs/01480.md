# 在云中部署 PHP 应用程序的终极指南

> 原文：<https://www.sitepoint.com/ultimate-guide-deploying-php-apps-cloud/>

### 目录 

*   [云服务器简介](#introductiontocloudservers)
*   [Heroku](#heroku)
*   [做一个小小的改变](#makeasmallchange)
*   [数据库，缓存& Cron 作业](#databasecachingcronjobs)
*   [谷歌云](#googlecloud)
*   [数据库，缓存& Cron 作业](#databasecachingcronjobs-1)
*   [IBM BlueMix](#ibmbluemix)
*   [数据库，缓存& Cron 作业](#databasecachingcronjobs-2)
*   [微软 Azure](#microsoftazure)
*   [数据库，缓存& Cron 作业](#databasecachingcronjobs-3)
*   [亚马逊网络服务](#amazonwebservices)
*   [数据库、缓存和 Cron 作业](#databasecachingandcronjobs)
*   拉勒维尔锻造厂
*   发送
*   [部署者](#deployer)
*   [结论](#conclusion)
*   [评论](#comments)

*这篇文章最初发表在 [Auth0 博客](https://auth0.com/blog/the-ultimate-guide-to-deploying-php-applications?utm_source=sitepoint)上，并在他们的允许下全文转载于此。*

**TL；DR:** 在开发者中有一个流行的口头禅是这样的*编写、测试和部署*。在本教程中，我将向您展示如何将您的 PHP 应用程序部署到不同的云服务器平台，如[谷歌云](https://cloud.google.com)、[微软 Azure](https://azure.microsoft.com) 、 [Heroku](https://heroku.com) 、 [IBM Bluemix](https://www.ibm.com/cloud-computing/bluemix) 等。

* * *

## 云服务器简介

云服务器基本上是运行在云计算环境中的虚拟服务器。在云中托管和部署应用程序有很多好处。它们是:

*   经济高效。
*   您可以根据自己的需要自由修改服务器软件。
*   云服务器的伸缩性非常好。
*   稳定和安全。

事实上，为了降低成本和复杂性，许多公司已经将他们的基础设施转移到了云中。这是中小型企业的绝佳选择。如果你像我一样写了很多教程，做了 POCs(概念证明)，对你来说也是一个很棒的选择！

一个通用的 PHP 应用程序涉及到常见的 *LAMP (Linux、Apache、Mysql 和 PHP)* 栈。

*   **Linux:** 运行其他软件包的操作系统。
*   **Apache:** 运行 PHP 代码的 web 服务器。一个流行的替代软件是 *Nginx* 。
*   **MySQL:** 数据库。一个流行的替代软件是 PostgreSQL。
*   **PHP:** 构建应用的服务器端语言。

*有关 LAMP / MAMP / WAMP 的速成课程，请参见此[优质资源](https://www.sitepoint.com/premium/courses/local-development-environments-for-designers-and-developers-2856)。*

现在让我们来看看如何将 PHP 应用程序部署到几个云服务器平台上。

## Heroku

Heroku 是一个云平台，帮助您以现代方式部署和托管您的应用程序。它为你做了所有繁重的工作。让我们快速看一下如何在 heroku 上部署和维护一个 PHP 应用程序。

![PHP-Heroku Architecture](img/78e24679cd08b26bb7cd894448256a06.png)
*PHP Heroku 架构*

如果您没有帐户，请在[heroku.com](https://www.heroku.com/)上创建一个。然后继续安装 [heroku cli](https://devcenter.heroku.com/articles/heroku-cli) 。安装好之后，克隆这个简单的 [starwars PHP 应用程序](https://github.com/sitepoint-editors/starwars-phpapp)。

> Heroku 在 dyno 中运行您的 PHP 应用程序，这是一个智能容器，它提供了一个现代化的堆栈，您可以选择 web 服务器(Apache 或 Nginx)和运行时(PHP 或 HHVM)。

确保您遵循以下步骤:

*   从`.env.example`创建一个`.env`文件。
*   您需要拥有一个拥有 [Auth0](https://auth0.com/signup) 的账户。
*   转到您的 [Auth0 仪表板](https://manage.auth0.com/#/)并点击“创建新客户端”按钮。
*   命名您的新应用程序，并选择“常规网络应用程序”。
*   在新 Auth0 客户端应用的设置中，将`http://localhost:8000`添加到允许的回调 URL 中。
*   复制出你的*客户 id* 、*客户秘密*、*域名*和*回拨网址*。确保在`.env`文件中将它们分配给正确的变量。

我们有一个`composer.json`文件，其中包含应用程序需要的包的列表。继续在您的本地机器上运行`composer install`来安装这些包。不熟悉作曲？[这个视频](https://www.sitepoint.com/premium/screencasts/php-dependencies-made-easy-with-composer?aref=bskvorc)可能会有帮助。

继续运行应用程序。应用程序应该像这样运行:

![Landing page](img/4d62c54d22d6d031598c1bb293c471cf.png)
*登陆页面*

![Logged In User](img/b7cfee75c5fa9128ccdd1c3bf120b3f1.png)
*登录用户*

厉害！我们的应用程序在本地运行。该部署了！我们要做的第一件事是向我们的应用程序的根目录添加一个`Procfile`。

创建一个没有任何文件扩展名的名为`Procfile`的新文件，并添加如下内容:

```
web: vendor/bin/heroku-php-apache2 
```

> Procfile 是应用程序根目录中的一个文本文件，它定义了进程类型，并显式声明了在 heroku 上启动应用程序应该执行什么命令。

如果您对使用 *Nginx* 作为 web 服务器感兴趣，那么您的 *Procfile* 的内容应该是:

```
web: vendor/bin/heroku-php-nginx 
```

现在您已经添加了 Procfile，继续将项目上传到 [GitHub](https://github.com) 或 [Bitbucket](https://bitbucket.org/) 。我的是[starwazap](https://github.com/unicodeveloper/starwazapp)。

前往[dashboard.heroku.com/apps](https://dashboard.heroku.com/apps)创建一个新的应用程序，如下所示:

![Create a new app](img/f0dd02a1e0bcc90e654868c9e1e1c789.png)
*从仪表盘创建新应用*

给它起一个像这样的名字:

![Give the app a name](img/817ee1653fca7e5e2314d066f8a2a696.png)
*给 app 起个名字*

选择部署方法。在我们的例子中，我们将像这样使用 GitHub:

![Connect to GitHub](img/a4d39ec120096af6e3b230f49c976ebe.png)
*连接 GitHub*

> 之所以选择 GitHub，是为了让开发和维护过程非常顺畅。开发人员可以使用 git 工作流开发新特性。

现在，在圈出的区域中键入回购的名称，并单击**搜索**。Heroku 会在你的 GitHub 账户下搜索你的回购，并显示如下

![Search for repo](img/6895ea69eeef7b2580aab76d5c193c8d.png)
*寻找回购*

像这样点击`connect`

![Click on connect](img/f7d81298bbec81101b67ef2a7cca7864.png)
*点击连接按钮*

Heroku 会像这样连接回购协议

![Connected repo](img/f58a94ab936018dbe9a1da2a9d3c40b2.png)
*连通工程*

向下滚动一点。这是有趣的部分。Heroku 允许你通过一个按钮来启用自动部署。它还为您提供了一个选项，让您在部署到生产环境之前等待持续集成过程通过。在真实的应用程序中，你会有一个测试套件来测试你的代码库。开发人员的代码运行在测试套件上。如果通过，代码将被推向生产。

![Option to enable deploy](img/1a9b7da0a143c03350c81860f58b2ed3.png)

单击以启用自动部署。我们没有任何 CI 服务，因此不需要启用该选项。现在，让我们部署主分支。

> **注意:**可以有其他分支，并指定想要哪个分支进行生产。在我们的例子中，主分支是生产分支。

点击**部署分支**。Heroku 将扫描您的`composer.lock`文件，安装必要的软件包，并部署！

![Deploy](img/434f22a9e6b413b45eece6bc1cc40ec1.png)
*终于部署完毕*

点击**查看**按钮查看您的应用。

![Ooops](img/65e3c4d5f57c3fdb849aa860d89ce4d2.png)
*错误 500*

不对不对！我们遇到了一个 500 错误。啊哈，我们还没有设置任何环境变量。在本地，我们有一个`.env`文件。在 Heroku 上，没有`.env`文件，但是有一个设置环境变量的方法。转到仪表板中的**设置**，将它们添加为配置变量，如下所示:

![Add config variables](img/54bcc399c1796f585e1dba18c236f59a.png)

哦，还有一件事！在我的例子中，新的回调 url 是`http://starwazapp.herokuapp.com`。确保您将新的回拨 url 添加到您的 [Auth0 仪表板](https://manage.auth0.com)中的*允许的回拨 URL*。

你的应用程序应该是生活和工作了！

![App working](img/8713d24dacd764242f75b6acf5273f7b.png)
*直播 App*

### 做一点小小的改变

让我们对我们的应用程序做一个小小的改动，看看它如何毫不费力地部署到生产中。

打开`index.php`，将`<p>`标签的内容从`Heard you don't want to migrate to PHP 7? Dare us!`更改为`Star Wars - The Awakening!`。提交并推送到您的主分支。现在，转到 Heroku 仪表板的**活动**选项卡，注意构建。重新加载你的应用程序，你会看到不同之处。

![Build Succeeded](img/26dcf1b48670a4a67d9d9895e07063ee.png)
*打造成功*

![New version of app](img/fe207bad5b0bda87b52f9ada2c233783.png)
*新版本*

### 数据库、缓存和 Cron 作业

让我们快速讨论一下如何处理数据库、缓存和 cron 作业。在 Heroku 上，你可以用 PHP 使用 [ClearDB](http://w2.cleardb.net/) 和 [Postgres](https://elements.heroku.com/addons/heroku-postgresql) 。将 ClearDB 添加到您的应用程序中，如下所示:

```
heroku addons:create cleardb:ignite 
```

这个命令提供一个新的 ClearDB 数据库，并返回应用程序将用来访问它的 URL。你需要做的就是把它作为一个环境变量添加到你的应用程序中，并在你的应用程序的配置中解析它，如下所示:

> ClearDB 是一个强大的、容错的云中数据库即服务，适用于 MySQL 驱动的应用程序。

```
<?php
$url = parse_url(getenv("CLEARDB_DATABASE_URL"));

$server = $url["host"];
$username = $url["user"];
$password = $url["pass"];
$db = substr($url["path"], 1);

$conn = new mysqli($server, $username, $password, $db);
?> 
```

*使用 mysqli*

你可以调整它以适应 PDO 风格。将 Postgres 添加到您的应用程序中，如下所示:

```
heroku addons:create heroku-postgresql:hobby-dev 
```

前往[插件](https://elements.heroku.com/addons)，你会看到你的 PHP 应用可以使用的其他类型的数据库。

Heroku 为缓存提供了一系列插件，从`memcache`，到`fastly`，到`ironcache`，等等。你可以在 Heroku 查看如何在 PHP 上使用 [memcache。](https://devcenter.heroku.com/articles/memcachedcloud#using-memcached-from-php)

最后，您可以使用 [Heroku Scheduler](https://devcenter.heroku.com/articles/scheduler) 在您的应用程序上以预定的时间间隔运行作业。

## 谷歌云

[谷歌云平台](https://cloud.google.com)是一个巨大而可信的云平台，世界各地的许多公司都采用它来部署和托管他们的应用程序。你的应用将运行在支持谷歌所有产品的同一基础设施上。您还需要什么样的信心来保证您的应用程序能够很好地扩展，从而轻松地服务于成千上万的用户呢？

谷歌云为托管 PHP 应用程序提供了不同的选择。该平台提供[应用引擎(完全管理)](https://cloud.google.com/appengine/)、[计算引擎(可扩展虚拟机)](https://cloud.google.com/compute/)和[容器引擎(Kubernetes 集群)](https://cloud.google.com/container-engine/)。

在我们的例子中，我们将使用 App Engine。它将基础设施抽象化。让我们开始将我们著名的**星球大战**应用程序部署到谷歌应用程序引擎。

使用 Google App Engine 时，可以选择**标准的**或者**灵活的**环境。后者，顾名思义，允许你安装任何可以在 Debian Linux 上运行的 PHP 扩展，有一个可配置的 Nginx web 服务器，可写的文件系统，最新的 PHP 版本，并允许你使用`composer.json`运行部署脚本。

我们将使用灵活的环境。继续[创建一个新项目](https://console.cloud.google.com/projectselector/appengine/create?lang=flex_php&st=true)。点击**创建**，给项目起个名字，选择你想为你的应用提供服务的地区，并启用计费。

> **注意:**未经您的允许，不会向您收费。

现在，下载[谷歌 SDK](https://cloud.google.com/sdk/docs/) 并安装谷歌云工具。

![Installing Google SDK](img/aec05bb21e40749fd749f169ac793328.png)
*安装谷歌 SDK*

![Running gcloud](img/9d652df91541b8aeebebe96c26978599.png)
*运行 gcloud*

继续在我们项目的根目录下创建一个`app.yaml`文件，如下所示:

*app.yaml*

```
runtime: php
env: flex 
```

所以，我们的`.env`文件已经推送到 Google Cloud 了。另一种方法是将环境变量添加到`app.yaml`文件中，如下所示:

```
...
env_variables:
  # The values here will override those in ".env". This is useful for
  # production-specific configuration. However, feel free to set these
  # values in ".env" instead if you prefer.
  APP_LOG: errorlog 
```

现在，通过运行`gcloud app deploy`从控制台部署应用程序。

获取 URL，在我的例子中是`https://starwars-166515.appspot.com/`，并在您的 [Auth0 仪表板](https://manage.auth0.com)中添加**允许的来源(CORS)** 和**允许的回调 URL**。同样在您的`.env`文件中添加到`AUTH0_CALLBACK_URL`的 URL。

再次运行`gcloud app deploy`以供应新版本的应用程序。立即查看您的应用。应该是这样生活的:

![Live App](img/fd1ee43ae947fb0b00a1804ceb9b939d.png)
*直播 App*

### 数据库、缓存和 Cron 作业

Google Cloud 提供了一个云 SQL 实例平台。点击查看如何[为您的应用配置、连接和创建 MySQL 实例。](https://cloud.google.com/sql/docs/mysql/create-instance)

你也可以在[谷歌应用引擎](https://cloud.google.com/sql/docs/mysql/phpmyadmin-on-app-engine)上使用 [phpMyAdmin](https://www.phpmyadmin.net/) 。

Google App Engine 包括标准 [Memcache](http://php.net/manual/en/book.memcache.php) 和[Memcached](http://php.net/manual/en/book.memcached.php)API 的实现。查看如何在 Google Cloud 上的应用中使用 [Memcache。](https://cloud.google.com/appengine/docs/standard/php/memcache/using)

App Engine Cron 服务允许您配置定期计划的任务，这些任务在定义的时间或定期间隔运行。查看如何在 Google Cloud 上通过 PHP 调度 cron 作业和使用任务队列。

将 [Laravel](https://cloud.google.com/community/tutorials/run-laravel-on-appengine-flexible) 、 [Symfony](https://cloud.google.com/community/tutorials/run-symfony-on-appengine-flexible) 和 [WordPress](https://cloud.google.com/php/tutorials/wordpress-app-engine-flexible) 应用部署到谷歌云平台相对容易。

## IBM BlueMix

IBM Bluemix 允许您在强大、高性能的全球云基础设施上轻松配置、部署和扩展。让我们开始将我们著名的**星球大战**应用程序部署到 IBM Bluemix 上。

[像这样在 Bluemix](https://console.ng.bluemix.net/registration/) 上注册:

![BlueMix Signup](img/a803cb2c62b61d329131de28b06ee0d0.png)
*注册 Bluemix*

> **注意:**Bluemix 平台提供 30 天的免费试用期，因此您有机会在提交您的信用卡信息之前尝试部署您自己的应用程序。

去创造一个组织和空间。我把我的空间命名为`prod`。

![Dashboard](img/d03520d794639712442f99533029b329.png)

现在，继续安装 [Cloud Foundry CLI](https://github.com/cloudfoundry/cli#downloads) 。完成后，从终端登录，如下所示:

```
cf api https://api.ng.bluemix.net/
cf login 
```

![Authenticating via the terminal](img/94e87fba0b7439eea9aa5749a3128600.png)
*登录 Bluemix*

下一步是在 app 的根目录下创建一个`manifest.yml`文件。`manifest.yml`文件包含了你的应用程序的基本信息，比如名称，为每个实例分配多少内存，以及路径。我们的清单文件应该如下所示:

```
---
applications:
  - name: starwarsapp
    memory: 512M
    instances: 1
    host: starwarsapp 
```

您也可以在清单文件中显式指定 buildpack。令人欣慰的是，当你推送一个应用时，Cloud Foundry 会自动检测需要哪个 [buildpack](https://github.com/cloudfoundry/php-buildpack) 。

> 构建包为您的应用程序提供框架和运行时支持。构建包通常会检查用户提供的产品，以确定要下载哪些依赖项，以及如何配置应用程序来与绑定的服务进行通信。

最后，通过运行如下命令部署您的应用程序:

```
cf push <yourapp> 
```

必须是唯一的名称。

![Starting Deploy](img/76c87ce9156314fa8913b5dc7707b79e.png)
*开始部署*

![Ending Deploy](img/dc0660f965a7ddbc2a0cf77a7e277a35.png)
*结局部署*

现在尝试运行该应用程序。在我的例子中，url 是`starwarsapp.mybluemix.net`。哎呀，一个 500 错误。我们还没有加载环境变量。我们如何用 Bluemix 做到这一点？

您可以使用 Cloud Foundry CLI 或 Bluemix 用户界面来设置环境变量。让我们使用 Bluemix 用户界面。因此，请遵循以下步骤:

*   打开 Bluemix [仪表盘](https://console.ng.bluemix.net/dashboard/apps/)。

    ![Dashboard - App running](img/96ccf32f7084f41ad4112e3afa4f897c.png)

*   点击应用程序。您将被重定向到另一个页面，其中包含有关该应用程序的更多详细信息。

*   从左侧面板中选择**运行时间**。

    ![Click on Runtime](img/869c1cab190a4aa6062adb7d0092d9d1.png)

*   现在，点击**环境变量**

    ![Select Environment Variables](img/d8139fe3cf6edbf988f5671ef0688138.png)

*   向下滚动并点击**添加**按钮，添加环境变量，如下所示

    ![Add environment variables](img/bcc19e190a179ef07b61ad4a546ea121.png)

*   点击`Save`按钮。一旦你这样做，你的应用程序将自动重启。

现在获取 URL，在我的例子中是`https://starwarsapp.mybluemix.net/`，并将其添加到您的 [Auth0 仪表板](https://manage.auth0.com)中的**允许的来源(CORS)** 和**允许的回调 URL**。

现在检查你的应用程序——它应该是活的！

### 数据库、缓存和 Cron 作业

Cloud Foundry 提供了创建服务的能力。IBM Bluemix 提供了 Cloudant NoSQL 数据库(CouchDB 的 Bluemix 名称)。您可以使用`cf`工具创建数据库服务，如下所示:

```
cf create-service cloudantNoSQLDB Lite starwarsapp 
```

IBM Bluemix 也提供 ClearDB MySQL 服务。所以，你可以使用`cf`工具来创建一个，如下所示:

```
cf create-service cleardb spark starwarsapp 
```

他们提供 [MongoDB](https://new-console.stage1.ng.bluemix.net/catalog/services/compose-for-mongodb/) 、 [PostgreSQL](https://new-console.stage1.ng.bluemix.net/catalog/services/compose-for-postgresql/) 、 [RethinkDB](https://new-console.stage1.ng.bluemix.net/catalog/services/compose-for-rethinkdb/) 。

您可以随时使用 Cloud Foundry 工具来检查许多东西，如日志、环境变量等。像这样:

*   `cf logs --recent yourapp`–显示您的应用程序的日志。
*   `cf env yourapp`–显示与您的应用程序相关的环境变量。
*   `cf marketplace`–显示 Bluemix 提供的所有服务。

IBM Bluemix 还提供了 [Redis Cloud](https://console.ng.bluemix.net/catalog/services/redis-cloud) ，这是一个完全托管的云服务，用于以高度可用和可伸缩的方式在 Bluemix 中托管和运行 Redis 数据集。

IBM Bluemix 提供了 [**工作负载调度器**](https://console.ng.bluemix.net/catalog/services/workload-scheduler) 服务。此服务允许您将您的应用程序与计划工作流的功能相集成。远远超越 cron，利用 Bluemix 内外的作业调度。根据您的需要，在您的应用程序中轻松创建工作流，以便在特定时间根据事件(例如，当文件被删除或更新时)定期运行。您可以使用*工作负载调度器用户界面*或者使用 API。

在此开始[计划作业。](https://console.ng.bluemix.net/docs/services/WorkloadScheduler/index.html)

此外，以下是如何[在 IBM Bluemix](https://www.ibm.com/blogs/bluemix/2014/06/getting-started-laravel-bluemix/) 上部署您的 Laravel 应用程序。

## 微软 Azure

微软 Azure 是另一个巨大的云平台，让你可以轻松扩展你的应用。让我们开始在 Azure 上部署我们的星球大战应用程序。

借助 Microsoft Azure，您可以通过以下方式进行部署:

*   FTP。
*   与云文件夹同步。
*   本地饭桶。
*   基于云的源代码控制服务，如 GitHub 或 Bitbucket。

在我们的例子中，我们将使用 Git 设置部署。

1.  首先，用微软 Azure 创建一个[账户。](https://portal.azure.com)

    ![Dashboard](img/f3685a2ed47cbc4a342b34efef29714b.png)
    *仪表盘*

2.  点击左侧面板上的**新**。

3.  点击市场旁边的**查看所有**。

4.  点击 **Web + SQL** ，然后继续创建。

    ![Web + SQL](img/9a7c2bbee8f210871e08a1336b2f2d62.png)

5.  系统会提示您选择您喜欢的套餐类型。我选择了*免费试用*。这样，你将获得 200 美元的 Azure 点数。

6.  给你的应用命名，然后创建一个 SQL 数据库。嗯，我们的应用程序不需要它，但出于某种原因，Azure 强迫你创建它。

    ![Create a new app](img/8c31390fa3bde015a1c17ab598748a50.png)
    *创建新 app*

7.  现在我们的应用程序已经创建完毕，点击左侧面板的**应用程序服务**即可查看您的应用程序。

    ![App services](img/8282e4d8df8b01e0e02431cca1f9d6f5.png)
    *新 app*

8.  点击应用程序，选择*部署选项*，然后点击`GitHub`。

9.  授权访问您的回购，选择项目和分支。在我的例子中，我有一个`azure`分支。这是我将代码部署到 Azure 平台的分支。

    ![Configuration in your app](img/ee13959750204023dc466ec7dfc4fc56.png)

10.  查看部署通知。

    ![Deployment Notifications](img/31fa83d9d3c729970dc00115a8c0897e.png)

    现在，浏览到`http://[yoursitename].azurewebsites.net`。我的情况是`http://starwarzapp.azurewebsites.net`。

    哎呀！，有一个 HTTP 500 错误。发生什么事了？好的，我们需要再次设置环境变量。

11.  在**应用服务**中进入你的应用，点击**应用设置**，然后在右边添加环境变量。

现在获取应用程序的 URL，在我的例子中是`http://starwarzapp.azurewebsites.net/`，并在你的 [Auth0 仪表板](https://manage.auth0.com)中添加**允许的来源(CORS)** 和**允许的回调 URL**。

1.  默认情况下，azure 部署不会对我们的`composer.json`或`composer.lock`文件做任何事情。因此，没有软件包被安装。现在回到**应用服务**，点击你的应用，然后进入**开发工具**，选择**扩展**。选择`Composer`扩展并同意法律条件。

    ![Add extension](img/7a62ef94e0d07d9eb32e8c7c4045491a.png)

2.  现在，对你的应用程序做一点修改，然后再次推送到 GitHub。您应该看到它像这样展开:

    ![Make a change](img/614ec3b816f67ef8e310651e4cc88700.png)

    ![Deployment details](img/ceb695c5d6710cbcfc40f5868dfaec67.png)

3.  现在，请再次查看您的应用。应该是[直播](http://starwarzapp.azurewebsites.net)和工作！

    ![Landing Page](img/080cec6e3a4389ff527f32105f5b6a51.png)

### 数据库、缓存和 Cron 作业

微软 Azure 提供 *Azure Redis 缓存*。它基于流行的开源 Redis 缓存。很容易创建和使用，如下所示:

1.  点击**新建>数据+存储> Redis 缓存**。
2.  输入缓存的名称，选择区域并创建它。

查看[文档](https://azure.microsoft.com/en-us/services/cache)了解如何使用它。

对于调度和运行任务，Azure 提供了一个[调度器](https://azure.microsoft.com/en-us/services/scheduler/)。它允许您:

*   调用 Azure 内部或外部的服务。
*   按任何计划运行作业。
*   将 Azure 存储队列用于长期运行或离线作业。
*   调用 Azure 服务总线队列。

查看[如何使用调度器](https://docs.microsoft.com/en-us/azure/scheduler/scheduler-get-started-portal)创建和管理作业。

在部署我们的应用程序时，我们已经谈了一些关于建立数据库的内容，但是让我们快速地看一下如何建立一个 MySQL 数据库。

1.  登录 Azure 门户网站。

2.  点击仪表板左侧面板中的**新建**。在市场中选择**数据+存储**，然后选择 MySQL 数据库。

3.  继续配置您的新 MySQL 数据库。输入名称，选择您的订阅、位置并填写必填字段。创造！

4.  连接到数据库。

    ![The connection info](img/5d665230627205a9820defcb2ccb312b.png)

Laravel 开发者可以很容易地在 Azure 上为他们的应用程序配置一个 MySQL 数据库。

## 亚马逊网络服务

与其他任何云平台相比，更多的公司使用 AWS(亚马逊网络服务)来存储各种数据，从图像和 mp3 文件到视频。事实上，许多组织，如优步、Spotify 或 Salesforce，完全使用亚马逊网络服务——用于托管、部署和基础设施。AWS 有大量的开发者产品。

我们将用于部署著名的星球大战应用程序的服务是**亚马逊弹性豆茎**。让我们开始吧。

*   如果你还没有 AWS 账户，请注册一个 [AWS 账户。](https://aws.amazon.com/account/)
*   头转向[弹性豆茎控制台](https://us-west-2.console.aws.amazon.com/elasticbeanstalk)。
*   创建新应用程序。
    ![Create new app](img/42a092771f604b686ca87ae8a15b6572.png)
*   点击`create web server`。
    ![Create web server](img/5417dc7d20e93e0155ebcf7fbf5f0ae3.png)
*   创建 web 服务器环境。
    ![Environment type](img/76428e5fe25315369f8bfd47e3710432.png)
*   上传您的代码。Elastic Beanstalk 要求您上传代码库的 zip 文件。你可以手动拉上拉链，但我更喜欢在我的终端上这样做:`zip ../starwarsapp.zip -r * .[^.]*`
*   现在，像这样上传到 AWS:
    ![Upload code to AWS](img/56a009c308cab3e65f83e8bfc0721df3.png)
*   检查应用程序 URL 的可用性。我的长这样:
    ![URL](img/3df4d707d83100b482643529fb281944.png)
*   下一页允许我们配置一个数据库实例。我们的应用程序不需要，所以我们可以跳过这个。
    ![Skip setting up DB Instance](img/b6073077a586e41f644457fa550532ad.png)
*   这一步允许我们修改我们的配置细节。默认的对我们的应用来说是可以的。
    ![Configuration details](img/8c64a836328b3aacc742eb65c45b3480.png)
*   现在，像这样添加你的环境变量:
    ![Environment Variables](img/cf517bb5d6eac312d5a87e2e8c00bfbd.png)

现在获取 URL，在我的例子中是`http://starwarzapp.us-west-2.elasticbeanstalk.com`，并添加到**允许的来源(CORS)** 和**允许的回调 URL**到你的 [Auth0 仪表板](https://manage.auth0.com)。确保将它作为环境变量添加到 Elastic Beanstalk 中。

*   像这样添加权限:
    ![Permission](img/79b092146a443818d76b5d615abe87ba.png)
*   启动前请查看信息。
    ![Review](img/0b33d2d909fd8d2d1876687b5e507559.png)
*   发射。

![Deployed](img/10e7a334140fdd3ca75b15627d30efbb.png)

![Live app](img/47714b41164b90e8d2c6ffcedbc932d7.png)
*直播 app*

了解如何部署:

*   弹性豆茎的一个应用程序。
*   一款 CakePHP 应用到弹性豆茎。
*   弹性豆茎的 Symfony2 应用程序。
*   [一个 WordPress 网站来的弹性豆茎](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/php-hawordpress-tutorial.html)。
*   [一个 Drupal 网站到弹性豆茎](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/php-hadrupal-tutorial.html)。

### 数据库、缓存和 Cron 作业

您可以使用 Amazon 关系数据库服务(Amazon RDS) DB 实例来存储应用程序收集和修改的数据。该数据库可以附加到您的环境中并由 Elastic Beanstalk 管理，也可以在外部创建和管理。查看如何[轻松地将 DB 实例添加到您的应用程序](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_PHP.rds.html)。

对于缓存，亚马逊网络服务提供了[elastic cache](https://aws.amazon.com/elasticache)。它是一种 web 服务，可以轻松部署、操作和扩展云中的内存数据存储或缓存。Amazon ElastiCache 支持两个开源内存引擎:

*   [再说](https://aws.amazon.com/elasticache/redis)。
*   Memcached。

Amazon ElastiCache 会自动检测和替换故障节点，减少与自我管理的基础设施相关的开销，并提供一个弹性系统来降低数据库过载的风险，这种风险会降低网站速度并增加应用程序加载时间。通过与 [Amazon CloudWatch](https://aws.amazon.com/cloudwatch) 的集成，Amazon ElastiCache 增强了与 Redis 或 Memcached 节点相关的关键性能指标的可见性。

像 [AirBnb](https://www.airbnb.com) 、 [Healthguru](http://www.healthguru.com) 、 [PlaceIQ](http://www.placeiq.com) 和 [Tokyo Data Network](http://www.tdn.co.jp) 这样的公司使用 ElastiCache 在多个层缓存 HTML 片段、昂贵的数据库查询结果、短暂的会话数据和搜索结果。

> 点击查看[如何为 PHP 安装 ElastiCache 集群客户端。](http://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/Appendix.PHPAutoDiscoverySetup.html)

这是一篇关于用 ElastiCache 和 Elastic Beanstalk 构建 PHP 访问者计数器的优秀文章

您可以在 Elastic Beanstalk 上设置 cron 作业。了解如何在亚马逊网络服务(AWS)弹性豆茎上运行 cron 作业。

## 拉勒韦尔锻造厂

由[泰勒·奥特威尔](https://twitter.com/taylorotwell)创建的 Laravel Forge 是一个帮助你在几分钟内部署和启动你的应用的平台。它为你做了繁重的工作。Forge 负责在 Linode、Digital Ocean 和 AWS 等流行的云托管提供商上提供您的服务器。它最初是为 Laravel 应用程序构建的，但现在它支持几乎所有的 PHP 应用程序。

![Laravel Forge](img/7a86d9b041deb59f2027c4fa6fd81f5f.png)
*拉勒韦尔锻造*

除了配置服务器之外，Laravel Forge 还允许您轻松完成以下任务:

*   它与 LetsEncrypt 集成，为您的应用程序生成免费的 SSL 证书。
*   轻松管理作业和队列。
*   通过与同事共享服务器的管理仪表板，与您的团队进行协作。

Matt Stauffer 有一篇关于部署你的第一个 Laravel 应用程序 Forge 的惊人文章。

詹姆斯·法尔赫斯特也有一个关于使用 Laravel Forge 设置 AWS 服务器的很好的指南。

最流行的教育 PHP 平台，[laracasts.com](https://laracasts.com)有一个关于服务器管理的[系列与 Forge](https://laracasts.com/series/server-management-with-forge) 。

当然，我们会在我们的 [Laravel 课程](https://www.sitepoint.com/premium/courses/laravel-5-2930?aref=bskvorc)中提到它，也会在类似[这些](https://www.sitepoint.com/setting-up-php-7-servers-with-laravel-forge-and-digitalocean/)的深入教程中涉及它。

## 发送(e)

Envoyer 是一个允许零停机 PHP 部署的平台——它是 SaaS 版的[特使](https://www.sitepoint.com/how-can-i-use-laravel-envoy-or-deployer-with-semaphoreci/)。它允许你集成各种服务，如 Gitlab，Slack，Bitbucket 等。

使用 Envoyer，您可以执行:

*   无缝部署回滚。
*   部署到多台服务器。
*   监控 Cron 作业。
*   执行应用程序运行状况检查。

## 部署者

[Deployer](https://deployer.org) 是 PHP 的部署工具。它允许您执行以下操作:

*   并行运行任务。
*   原子部署。
*   回滚。
*   以配方的形式创建部署脚本。

它与 *Laravel* 、 *Symfony* 、 *CakePHP* 、 *Yiiframework* 、 *Zend* 、 *FuelPHP* 、 *Drupal* 、 *WordPress* 和 *Magento* 协同工作。查看这篇关于使用 Deployer 部署 PHP 应用程序的优秀文章[。](https://www.sitepoint.com/deploying-php-applications-with-deployer)

## 结论

我们不可能涵盖部署 PHP 应用程序的所有不同选项。PHP 是一种经过多年发展的企业语言，因此需要更有效的方式将 PHP 应用从个人的本地机器部署到生产中。希望本指南涵盖了您将 PHP 应用程序部署到所有主要云提供商的所有基本需求。然而，我推荐另一个资源，在[学习部署 PHP 应用程序](http://www.deployingphpapplications.com)中有广泛的知识。

你如何处理你的部署？请在下面的评论区告诉我吧！

## 分享这篇文章