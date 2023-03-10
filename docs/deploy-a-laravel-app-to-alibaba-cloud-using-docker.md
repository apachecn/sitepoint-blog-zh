# 使用 Docker 将 Laravel 应用部署到阿里云

> 原文：<https://www.sitepoint.com/deploy-a-laravel-app-to-alibaba-cloud-using-docker/>

在最近的比赛中，我们收到了许多优秀的参赛作品，以寻找充分利用阿里云服务的最佳技巧。对于我们的评委来说，从这么多有帮助和有趣的参赛作品中选出获胜者是一项有趣但具有挑战性的任务。但是，经过激烈的讨论和激烈的辩论，他们决定比赛的一等奖授予 Magyar András，他提交了使用 Docker 将 Laravel 应用程序部署到阿里云容器服务的技巧。它是一个全面的指南，演示了几种相关技术的使用——Docker、Laravel、Apache、Redis、Git，并连接了阿里云容器服务、[容器注册中心](https://int.alibabacloud.com/m/1000018792/)、阿里巴巴的[虚拟私有云](https://int.alibabacloud.com/m/1000018793/)和[资源访问管理](https://int.alibabacloud.com/m/1000018794/)。

在本教程中，我们将使用 Docker 和阿里云[容器服务](https://int.alibabacloud.com/m/1000018791/)部署一个 Laravel 应用。

## 先决条件

在开始阅读本指南之前，您需要具备以下条件:

*   [Docker](https://www.docker.com/get-started) 安装在你的本地机器上(如果你不能安装最新版本你可以使用 [Docker 工具箱](https://docs.docker.com/toolbox/)
*   [安装在您电脑上的作曲家](https://getcomposer.org/)

## 为部署准备应用程序

首先，你需要一个可以 Dockerize 的 Laravel 应用。您可以从 GitHub 复制[我的示例](https://github.com/magyarandras/laravel-docker)并将其推送到您自己的 git 存储库，或者您可以使用 Composer 创建一个新的 Laravel 应用程序，命令如下:`composer create-project --prefer-dist laravel/laravel appname`

我们需要向 Laravel 应用程序的根目录添加一些文件。

您必须为生产环境创建一个环境配置文件，姑且称之为. env.prod。env 文件，但是不要忘记修改这些值(例如，将 APP_ENV 设置为 production)。

我们还需要一个 web 服务器配置文件(我们将使用 Apache)，为我们的虚拟主机创建一个 vhost.conf 文件。

```
<VirtualHost *:80>
  DocumentRoot /app/public

  <Directory "/app/public">
    AllowOverride all
    Require all granted
  </Directory>

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost> 
```

为了构建我们的容器，我们需要一个 docker 文件，我们将使用多阶段构建:

```
#Install the dependencies using composer

FROM composer:1.7 as build

WORKDIR /app

COPY . /app

RUN composer install

COPY .env.prod .env

#Application

FROM php:7.2-apache

RUN docker-php-ext-install mysqli pdo pdo_mysql

EXPOSE 80

COPY --from=build /app /app

COPY vhost.conf /etc/apache2/sites-available/000-default.conf

RUN chown -R www-data:www-data /app \
&& a2enmod rewrite 
```

我们还需要从容器中排除一些文件和文件夹，所以您应该创建一个. dockerignore 文件(如果您愿意，可以扩展这个列表):

```
.git/
vendor/
node_modules/
yarn-error.log 
```

## 在阿里云容器注册表中创建存储库

在阿里云控制台上，进入*产品>弹性计算>容器注册表*。

![1_menu](img/314d07dfa50f60fd0d2c6ccdd49048b0.png)

首先，您需要设置注册表登录密码。

![3_registry](img/d6ef559f722f7b8c55ad182993579c24.png)

我们必须创建一个名称空间，然后我们可以为应用程序创建一个存储库。

![6_registry](img/ae9fb28565cc058d92bc9126f52a10a9.png)

确保将存储库类型设置为私有，否则无需密码即可访问存储库。您也可以选择存储库的区域。

![7_registry](img/ac8881db95515655e72234ccc51e00e4.png)

容器注册表支持 GitHub、GitLab 和 Bitbucket 作为代码源，这真的很有用。如果您使用其中的一个，您可以选择它，但是为了简单起见，我们将在本教程中使用本地存储库选项。

您需要在本地计算机上构建容器，并将其推送到注册表(如果您选择 Git 托管服务，容器注册表将自动构建容器，因此您可以跳过这些步骤。)

在您的 Laravel 应用程序的根目录下运行以下命令来构建容器(您可以用自己的标签替换 test/laravel:1.0 标签)。

`docker build -t test/laravel:1.0 .`

如果您在容器注册表中单击您的存储库右侧的 manage，您可以找到您的存储库的地址以及有关如何登录注册表并将映像推送到存储库的指南。

![repo_info](img/5b188ecc71e2c8d3fa5a7de5fe7d90ad.png)

因此，您必须运行以下命令，但是使用您自己的区域、名称空间和存储库:

```
docker login --username=user@example.com registry-intl.eu-central-1.aliyuncs.com
docker tag test/laravel:1.0 registry-intl.eu-central-1.aliyuncs.com/ma_test/laravel1:1.0
docker push registry-intl.eu-central-1.aliyuncs.com/ma_test/laravel1:1.0 
```

当您成功推送图像时，您会在标签选项卡下看到它。

![tags](img/1c4d83834f66344b1bdcab8356d727f8.png)

## 创造一个 VPC

在阿里云控制台上，进入*产品>联网>虚拟私有云*并激活 VPC。

从顶部菜单中选择您所在的地区，并创建一个 VPC 和一个虚拟交换机。

![vpc1](img/6d2e85f481b575a9fb89c13cae8d5162.png)

![vpc2](img/761b868432eccc62376702c831274436.png)

## 创建集群

首先你需要启用 RAM ( *产品>监控管理>资源访问管理*，然后你就可以去*产品>弹性计算>容器服务*。

容器服务支持 Swarm 和 Kubernetes。现在我们将使用 Swarm，所以您应该从左侧菜单中选择 Swarm。

![swarm](img/396821428be55fc505177dc1f2660e8d.png)

单击 Create Cluster 按钮并配置您的集群(不要忘记选择与您为 VPC 选择的区域相同的区域)。

![cs2](img/e228d8a051535e5c70e3d22150747fa7.png)

![cs3](img/53e24f479848c69778723eb3777bce6c.png)

我为演示选择了 2x (2 个节点)1 核 1GB [ECS](https://int.alibabacloud.com/m/1000018795/) 实例，但是如果您愿意，也可以选择不同的配置。

在登录部分，您需要创建 SSH 密钥或设置密码。我强烈推荐 SSH 密钥，但是为了简单起见，现在可以使用密码。

完成配置后，您可以单击“创建”按钮(确认对话框将显示价格信息)。

当集群创建完成并且您可以在集群列表中看到您的集群时，单击 Manage。

您需要登录到您的私有存储库来访问图像，因此单击登录到 Hub 按钮。如果您不知道存储库的域名是什么，您应该转到容器注册表控制面板，然后单击存储库右侧的管理。复制 VPC 地址(例如:`registry-intl-vpc.eu-central-1.aliyuncs.com/ma_test/laravel1` ) —这是您的存储库域名。您的用户名和密码是您的注册表的用户名和密码。

![password2](img/f9c14f98f61c7ddbcb423bb143826c35.png)

现在，集群节点可以从私有存储库中获取您的映像。

## 部署应用程序

在容器服务控制面板上，单击左侧菜单中的应用程序，然后单击创建应用程序。

![1](img/25cfffe40576e1d56ecdcd03493247d3.png)

设置名称和版本，检查拉 Docker 图像(这将确保您最终使用最新版本)，然后点击创建图像按钮。

![2](img/50a538661dec23c16a9944f2055389e6.png)

选择您的图像(在弹出窗口中，您可以在用户选项卡下找到您的图像)和版本。在“网络”部分的“Web 路由”下，单击蓝色加号。对于容器端口，键入 80。对于域类型，您希望它被称为什么。您也可以设置实例的数量(比例)。

![3](img/0087a04073ca4940112182dfaa7008c3.png)

点击创建，然后点击查看应用程序列表。在 Applications 下，您应该会看到您的新容器。单击容器，在服务选项卡中再次单击容器的名称。现在您将看到一些关于您的容器的信息。点击访问端点 URL，您将看到您的 Laravel 应用程序的主页。

![endpoint](img/baa15fa3d38d7590ad75dd8c45476544.png)

## 将应用程序连接到 MySQL 和 Redis

我们将需要一个 [SQL 数据库](https://int.alibabacloud.com/m/1000018797/)和一个会话数据库(我们将使用 Redis)，因此我们必须设置这些服务并配置连接。

转到*产品>ApsaraDB>ApsaraDB 进行 RedisT3。从顶部菜单中选择 VPC 所在的地区。创建一个符合您需求的实例(您可以免费使用一个 4G 主从实例一个月)。创建实例后，您可以在实例列表中看到它，单击 manage。从左侧菜单中选择“白名单设置”,并将默认值修改为 0.0.0.0/0，以允许来自 VPC 中所有计算机的连接，或者您可以添加 ECS 实例的专用 IP/IP 范围。*

从左侧菜单中选择实例信息并复制连接地址。在本地机器上，打开. env.prod 文件，并为 Redis 设置主机和密码。

与此类似:

```
REDIS_HOST=r-4xoba5b097200b94.redis.germany.rds.aliyuncs.com
REDIS_PASSWORD=my-password
REDIS_PORT=6379

You should set the cache and session drivers to redis:
CACHE_DRIVER=redis
SESSION_DRIVER=redis 
```

Laravel 需要一个名为 predis 的包来与 redis 交互。

在您的本地计算机上运行以下命令，这将安装 predis 并将其添加到依赖项列表中:

`composer require predis/predis`

我们配置了 Redis 连接，但是在部署新容器之前，我们还将配置 MySQL 连接。

转到*产品> ApsraraDB > ApsaraDB 进行 RDS* 。从顶部菜单中选择 VPC 所在的地区。创建一个符合您需求的实例(您可以使用一个免费实例一个月，使用 1 个核心 CPU、1 GB 内存、20GB 存储)。

创建实例后，您可以在实例列表中看到它，单击管理。从左侧菜单中选择 Security，并像我们在 Redis 中所做的那样修改白名单。您必须创建一个数据库帐户和一个数据库，授予用户读写权限。

![rds4](img/65c9ccb6ec038a208ee076b744190d44.png)

从左侧菜单中选择连接选项，并复制内部网地址(主机)。

![rds3](img/aa9b25700320d607465870315c48b03f.png)

在本地计算机上，应该编辑. env.prod 文件中的数据库连接设置。与此类似:

```
DB_CONNECTION=mysql
DB_HOST=rm-4xo3wjj1wh2nwi7yn.mysql.germany.rds.aliyuncs.com
DB_PORT=3306
DB_DATABASE=laravel1
DB_USERNAME=laravel1
DB_PASSWORD=my-password 
```

## 部署应用程序的新版本

构建容器并将其推送到注册表中:

您应该运行类似以下的命令(替换区域等):

```
docker build -t test/laravel:1.1 .
docker tag test/laravel:1.1 registry-intl.eu-central-1.aliyuncs.com/ma_test/laravel1:1.1
docker push registry-intl.eu-central-1.aliyuncs.com/ma_test/laravel1:1.1 
```

转到*容器服务>应用*，你应该在列表中看到你的应用，点击更新。更改版本字段的值，并在 yaml 文件(模板)中更改图像。

比如用`registry-intl-vpc.eu-central-1.aliyuncs.com/ma_test/laravel1:1.1`代替`registry-intl-vpc.eu-central-1.aliyuncs.com/ma_test/laravel1:1.0`。单击 OK，容器将被新容器替换。如果你愿意，你也可以使用[蓝绿释放政策](https://www.alibabacloud.com/help/doc-detail/44044.htm)。

![update](img/2129e02331fb5a2149b903ffb8beddfc.png)

现在您的应用程序可以连接到 Redis 和 MySQL。如果要运行数据库迁移，可以单击应用程序，在“服务”选项卡中再次单击应用程序的名称，选择一个容器，然后单击“Web 终端”。您应该运行以下命令:

```
cd /app
php artisan migrate 
```

注意:如果您在尝试运行迁移时收到一个*指定键太长*的错误，您可以在这里找到解决方案[。](https://laravel-news.com/laravel-5-4-key-too-long-error)

## 将您的域名添加到群集

转到容器服务控制面板中的应用程序，然后单击应用程序右侧的更新。在 yaml 文件中，将您的域添加到`aliyun.routing.port_80`。可以用分号分隔值，例如:`aliyun.routing.port_80: laravel1;yourdomain.com`

![domain](img/95a8a3ef3c6a5cd6d38fd27cabc054fe.png)

转到容器服务控制面板中的群集列表。单击群集右侧的管理。单击负载平衡器设置，现在您可以看到负载平衡器的 id。进入*产品>联网> [服务器负载平衡器](https://int.alibabacloud.com/m/1000018798/)* ，复制您的负载平衡器的 IP 地址并添加到您的 DNS 记录中。

如果您愿意，也可以向负载平衡器添加 TLS 证书。

## 使用对象存储服务

你大概是想存储文件，所以需要阿里云对象存储服务。您可以创建一个存储桶，并使用以下官方库将您的应用程序连接到服务:

1.  [阿里云/阿里云-oss-php-sdk-laravel](https://github.com/aliyun/aliyun-oss-php-sdk-laravel)
2.  [阿里云/阿里云-oss-php-sdk-flysystem](https://github.com/aliyun/aliyun-oss-php-sdk-flysystem)

## 结论

您已经成功地将一个 Laravel 应用程序容器化并部署到容器服务中，并配置了几个阿里云服务来协同工作，为您的应用程序提供一个可扩展的、可靠的基础架构。我希望这篇教程对你有用！

## 分享这篇文章