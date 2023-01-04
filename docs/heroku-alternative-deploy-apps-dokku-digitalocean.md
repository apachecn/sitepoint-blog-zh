# Heroku 替代方案:在数字海洋上使用 Dokku 部署应用程序

> 原文：<https://www.sitepoint.com/heroku-alternative-deploy-apps-dokku-digitalocean/>

![Dokku on DigitalOcean](img/219505b3448e9c22e5df86e2f8ca48a4.png)

当 Heroku 宣布他们(相当合理的)免费应用程序的新限制时，我意识到我必须为我目前在 Heroku 上运行的所有小的、低流量的项目找到另一个托管来源。很久以前，Heroku 对只需要一个 dyno 的应用程序完全免费，但在像我这样的混蛋滥用多年后，他们最终允许免费应用程序每天 24 小时运行 18 小时(这对低流量原型来说是可以的)，并在 6 月 1 日批准共享免费时间。

既然我在 Heroku 上运行的应用程序数量如此之多，我认为是时候尝试一下 [Dokku](http://dokku.viewdocs.io/dokku/) 了。Dokku 是一个类似 Heroku 的工具，允许您通过简单地使用 Git 来部署复杂的应用程序。它直接支持 Heroku buildpacks，因此您可以毫无困难地转换现有的应用程序，并且有许多用于数据存储和其他组件的插件。幸运的是，[数字海洋提供了预装的 Dokku 映像](https://www.digitalocean.com/features/one-click-apps/dokku/)，这将省去你自己安装 Dokku 的麻烦；您只需启动服务器，就可以立即开始工作！本文将带您在 DigitalOcean 上建立一个具有自己根域的 Dokku 服务器，并为其部署一个简单的静态站点。

## Dokku 和 Heroku 的区别

*   Dokku 要求运行自己的服务器至少要有一定的舒适度；您可能需要修改 nginx 配置，手动配置一些插件，或者求助于系统工具进行调试。
*   Dokku 利用了 Docker，这是一个很好的平台，但是会增加服务器安装的复杂性。
*   Dokku 需要对 VPS 的 root 访问权限来安装插件、运行命令等。

简而言之，与 Heroku 相比，您需要在 Dokku 上做更多的命令行设置——没有什么是您不能学到的，但是您可能需要做一些轻松的阅读。

## 在数字海洋上创建 Dokku 服务器

![DigitalOcean logo](img/3fe2c9ecf9df5ce2e4c1810f39a40927.png)

首先，登录 DigitalOcean 并[点击此链接](https://cloud.digitalocean.com/droplets/new?image=dokku)使用预装的 Dokku 应用程序在 DigitalOcean 上创建一个新服务器。Dokku 需要至少 1GB 的内存，但 10 美元/月来存放你所有的东西是一个相当小的价格。

对于您的主机名，请输入您想要用来托管您的应用程序的基本域。默认的 Dokku 应用会出现在`<appname>.<hostname>`(例如`myapp.example.com`)。确保你拥有这个域名，如果你需要的话注册它！

## 设置域

您需要将一个域与您的 Dokku 服务器相关联。只需为你的域名设置一个指向你的服务器 IP 的记录。

本文的其余部分将假设您已经为此注册了`example.com`。

我们将进行设置，以便您的应用将在`<appname>.example.com`出现。为此，您需要为您的域添加一些 DNS 记录:

*   创建一个空白的 A 记录指向你的服务器的 IP 地址
*   创建一个通配符(`*`)指向同一个 IP 的一条记录。

解析 DNS 后，您可以继续本教程的剩余部分。

如果您想要在另一个域上托管应用程序，您可以:

*   创建应用程序时将其命名为`otherdomain.com`，这将告诉 Dokku 使用该域，或者
*   在`www.otherdomain.com`建立一个指向`myapp.example.com`的 CNAME DNS 记录。

## 创建您的第一个应用程序

SSH 到您的服务器，并运行以下命令:

```
$ dokku apps:create example.com 
```

这将在您的服务器上创建一个名为“example.com”的应用程序。这是将出现在你的域的根目录下的应用；由于其中有一个`.`，Dokku 将其解释为一个域名，并相应地配置自己。对于未来的应用程序，你可以简单地选择`dokku apps:create appname`，而`appname.example.com`将成为它们的主机。

您可能想在`example.com`上托管一个静态站点。这将让你很容易保持更新，特别是如果你使用静态站点构建器，如[中间人](https://middlemanapp.com/)或[杰基尔](https://jekyllrb.com/)。

这个功能是通过方便的 [buildpack-nginx](https://github.com/dokku/buildpack-nginx) 内置到 Dokku 中的。要设置它，在项目的根目录下，创建一个名为`.static` ( `$ touch .static`)的空文件。这就是 buildpack 检测静态站点的方式。当你把这个推给 Dokku 时，它应该会检测到你正在创建一个静态站点，并相应地继续进行。

您的项目可以像一个`index.html`文件一样简单，包含您想要的任何内容。用`git init`创建一个新的 git repo，并在其中添加`index.html`、`.static`和`.env`。然后，将 Dokku 添加为遥控器:

```
git remote add dokku dokku@example.com:example.com 
```

现在，您应该能够通过简单的 git 推送进行部署:

```
git push dokku master 
```

您应该能够看到 Dokku 为您的应用程序配置了一个容器(检测静态构建包)，当您导航到`example.com`时，您应该看到您创建的`index.html`托管在那里。

## 后续步骤

如果您有任何现有的 Heroku 应用程序，您应该能够简单地用 Git 添加 Dokku remote 并运行`git push dokku master`。

您可能需要建立某种数据库。幸运的是，Dokku 项目目前支持各种数据存储插件，包括 Postgres、Mongo 和 Mysql。它们都非常容易安装和使用。

如果你有任何问题，Dokku 文档非常简单和全面。

## 分享这篇文章