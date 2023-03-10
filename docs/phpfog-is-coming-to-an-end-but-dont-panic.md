# PHPFog 即将结束——但是不要惊慌！

> 原文：<https://www.sitepoint.com/phpfog-is-coming-to-an-end-but-dont-panic/>

你听说 PHPFog 要结束了吗？不，嗯——是的。没错，在最近的邮件列表中，该公司表示 PHPFog 将在 2013 年 1 月底消失。但是如果你在他们的平台上，不要慌！新的联合平台可能比你现在习惯的更好。

如今，云风靡一时，因此我们中的许多人都有部署在一个或多个云提供商处的应用程序，无论它们是我们的宠物项目、[专业项目](http://phpclouddevelopmentcasts.com/ "PHP Cloud Development Casts")还是我们雇主的项目。由于 AppFog 是较大的供应商之一，拥有约 980 万美元的风险投资，因此这一公告不应被忽视，因为您可能会受到直接或间接的影响。

为了确保您不会被这种转变所困扰，我写了这篇文章来做两件事:

1.  如果你想和他们呆在一起，我会帮助你确保你完全跟上变化的速度，以及你需要做什么来进行迁移
2.  如果你想离开，我会帮助你成功地完成这个过程

## 什么是 app fog–它有什么不同？

如果您不熟悉 PHPFog 或 AppFog，这里有一个快速介绍。

PHPFog 是一个高质量的云 PaaS(平台即服务),简而言之，它允许开发人员专注于让我们兴奋的核心东西——开发出色、快速、高可伸缩、高性能的应用程序。

像其他云平台的[财富一样，例如](http://blog.newrelic.com/2011/05/26/the-list-which-cloud-platform-and-hosting-providers-are-giving-their-customers-great-deals-on-new-relic/ "The List: Which cloud platform and hosting providers are giving their customers great deals on New Relic? | New Relic Blog") [cloudControl](http://www.cloudcontrol.com "cloudControl - Cloud App Platform - supercharging development") 、 [Joyent](http://joyent.com "Joyent - Home") 、 [Rackspace](http://www.rackspace.com/cloud/ "Open Public, Private, Hybrid Clouds by Rackspace") 和 [EngineYard](http://www.engineyard.com/ "Ruby On Rails and PHP Cloud Hosting PaaS | Managed Rails Environment | Engine Yard Platform as a Service") ，PHPFog 减轻了我们设置和管理驱动我们的应用程序的后端服务器以及所有相关服务的需要，例如数据库、缓存服务器、反向代理服务器、防火墙等等。我们能够设计我们的应用程序，使用它所依赖的所有组件，如 MongoDB、MySQL、Memcache、NewRelic 和 Blitz，测试它，分析和改进它的性能，然后部署它，因为我们知道这些组件是可用的，并且得到专业的支持。

然而，11 月 13 日，该公司宣布 PHPFog 即将寿终正寝，将被 AppFog 取代。所以如果你想和他们在一起，你需要知道你在做什么。

AppFog 与 PHPFog 非常相似(甚至被该公司称为 PHPFog 2.0)，但在某些方面它更多，而在某些方面它更少。从实际的角度来看，你不太可能看到有效的区别；您仍然可以在那里创建和部署优秀的应用程序。但是 AppFog 最棒的一点是它提供的技术范围扩大了。

### 技术

尽管 PHPFog 只支持 PHP，但 AppFog 是一个多技术平台，支持 PHP、Java、Scala、Python、Node、Ruby 和 Erlang。我不知道你怎么想，但对我来说，这很令人兴奋。不仅仅是从技术的角度，而是从随之而来的更大的整体开发人员社区的角度。

### 插件

不仅仅是新技术让 AppFog 变得更好，还有更多插件。有了它，您可以获得一系列选项，包括 PostgreSQL、Redis、RabbitMQ 和 Memcachier。除此之外，该公司表示，这一名单将继续增长。

### 新部署流程

PHPFog 上的部署只使用 Git，而新平台提供了我们今天使用的三个关键版本控制工具: [Git](http://git-scm.com/ "Git") 、 [Subversion (SVN)](http://subversion.tigris.org/ "subversion.tigris.org") 和 [Mercurial](http://mercurial.selenic.com/ "Mercurial SCM") 。为了简单起见，他们提供了一个定制工具`af`，允许您管理部署过程，而不管您的项目是由哪个版本控制系统控制的。要全面了解 af，[查看在线文档](https://docs.appfog.com/getting-started/af-cli "AF Command Line Tool - AppFog Documentation")。

### 截止日期是什么时候

截止日期将根据您的计划是免费还是付费而有所不同。如果你参加的是免费计划，那么你可以在今年 12 月 21 日之前参加。否则，通过付费计划，你可以等到明年 1 月 25 日**。根据应用程序的复杂程度，最好尽早开始。**

 **### 其他注意事项

除了这四个方面，如果你和他们在一起，还有一些其他的事情你应该知道。为了简单起见，它们是:

*   数据库的大小限制在 100Mb 以内
*   没有持久的文件系统(尽管据说它在管道中)
*   您需要使用`af`来查看错误日志
*   没有专用的数据库支持，只有共享

但关键的一点是，对我们来说重要的不仅仅是技术，对吗？正如公告邮件所说，该公司正专注于一个平台，而不是两个。如果做得好，这对我们消费者和开发者来说都是一件好事。他们不再将精力分散在需要多种资源(无论是人力、财力还是其他资源)的多个项目上，而是专注于一个项目，从而能够为我们的项目提供最好的工具和平台。

## 我想转变

好吧，你决定留下来。您需要考虑的两件事是数据库连接和迁移。在[迁移指南](https://docs.appfog.com/migration#when-discontinued "Migrating from PHP Fog - AppFog Documentation")中，涵盖了两点。为了节省时间，我将把它们包括在内。

### 数据库连接

他们说在 AppFog 下将会有一个新的环境变量，`VCAP_SERVICES`，它将存储连接信息。他们提供的样本如下:

```
<?php
$services_json = json_decode(getenv("VCAP_SERVICES"),true);
$mysql_config = $services_json["mysql-5.1"][0]["credentials"];
$username = $mysql_config["username"];
$password = $mysql_config["password"];
$hostname = $mysql_config["hostname"];
$port = $mysql_config["port"];
$db = $mysql_config["name"];
$link = mysql_connect("$hostname:$port", $username, $password);
$db_selected = mysql_select_db($db, $link);
```

### 数据库迁移

然后，他们列出了迁移数据库的四个步骤。这些是:

1.  从应用程序控制台使用 phpMyAdmin 从 PHP Fog 导出您现有的数据库。
2.  下载并安装 af gem 和 caldecott:

    ```
    $ gem install af
    $ gem install caldecott
    ```

3.  创建要连接的 MySQL 服务:

    ```
    $ af login 
    $ af create-service
    ```

4.  连接到您的数据库服务:

    ```
    $ af import-service <service> <url>
    ```

* `service`是您要导入的服务的名称。
* `url`是您的数据库所在的 URL。

## 我想换供应商

如果你在听完所有这些后不热衷于和他们呆在一起，他们的官方建议[是开一个支持票](http://support.appfog.com/ "AppFog")，希望它能让你找回更换供应商所需的一切。显然他们希望你和他们在一起，但是如果这种改变不适合你，不要觉得有义务继续下去。始终做您认为对您、您的应用程序和您的组织最有利的事情。

## 有进一步的消息吗？

肯定是有的。除了在线文档之外，该公司还表示，他们将在未来几天和几周内发布一系列博客帖子和进一步的文档，具体说明如何迁移您的应用。

我很高兴他们在官方邮件中提到了“平台之间的一些边缘差异”。这将表明，他们正在认真对待这一过程，并真诚地让这一过程尽可能顺利和无痛。所以，如果你担心你会需要一些特别的帮助，那么这些信息很可能会回答你的问题。

## 结论

无论您是 PHPFog 的客户并希望过渡到 AppFog，还是您觉得 AppFog 不是您应用程序的未来，您现在都有了继续前进所需的关键信息和相关链接，让您安心。

我希望这篇文章已经为你提供了做出明智决定所需的所有信息，并且没有留下任何未回答的问题。但是，如果有，那么就阅读公司发布的官方迁移指南，或者[与他们的支持团队联系](http://support.appfog.com/ "AppFog")。

那你是哪一个？你会留下来还是该离开了？请在下面的评论中分享你的想法。

## 分享这篇文章**