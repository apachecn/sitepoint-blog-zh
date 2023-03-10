# Docker 和 Dockerfiles 变得简单！

> 原文：<https://www.sitepoint.com/docker-and-dockerfiles-made-easy/>

使用虚拟机来配置和管理他们的工作环境已经成为开发人员的第二天性。大多数使用虚拟机的专业人员使用[vagger](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)来处理他们的开发环境。在本文中，我们将从流浪者转移到 Docker，并使用一个小的 Laravel 应用程序来测试一切是否按预期工作。

![Docker Logo](img/c25e77efc0af37354c36b61bf5501a75.png)

## 装置

[安装页面](https://docs.docker.com/installation/)包含几乎所有流行平台的说明。Docker 原生运行在 Linux OS 上，但是我们可以通过安装 Docker 工具箱实用程序在 Windows 和 Mac OS 上使用它。如果你遇到一些问题，你可以访问[这篇文章](https://developer.ibm.com/bluemix/2015/04/16/installing-docker-windows-fixes-common-problems/)，它强调了最常见的问题。您可以通过在终端中运行以下命令来验证它的安装和运行。

```
docker -v
```

```
# output
Docker version 1.8.1, build d12ea79
```

## 码头机器

Docker 机器是保存您的映像和容器的 VM(稍后会详细介绍)。让我们创建第一个虚拟机。

```
docker-machine create --driver virtualbox docker-vm
```

您可以根据自己的喜好更改`driver`选项。在这里查看可用驱动程序的[列表。](https://docs.docker.com/machine/drivers/)

您可以通过运行`docker-machine env docker-vm`命令打印机器的配置。这就是你如何在机器之间切换。

```
# Use the docker-vm
eval "$(docker-machine env docker-vm)"

# Switch to the dev machine
eval "$(docker-machine env dev)"
```

你可以在[文档](https://docs.docker.com/machine/)中读到更多关于`docker-machine`命令的内容，关于我们为什么在这里使用`eval`的解释可以在[这一页](https://docs.docker.com/machine/reference/env/)中找到。

## Docker 图像

Docker 映像是包含一些预安装和配置软件的操作系统盒。您可以在 [Docker Hub](https://hub.docker.com/) 上浏览可用图像列表。事实上，您可以基于另一个映像创建自己的映像，并将其推送到 Docker hub，以便其他用户可以使用它。

`docker images`命令列出了您机器上可用的图像，您可以使用`docker pull <image name>`命令从 hub 下载一个新的图像。对于我们的演示应用程序，我们提取了`mysql`和`nimmis/apache-php5`图像。

![Docker images](img/57556b9cd89f9b71041d6c2341796258.png)

## 码头集装箱

Docker 容器是我们从图像中创建的独立实例。他们也可以为创造一个新的个性化的形象，其他人可以使用的良好开端。您可以使用`docker ps`命令查看可用容器的列表。这只列出了正在运行的容器，但是您可以通过添加`-a`标志(`docker ps -a`来列出所有可用的容器。

![Docker containers](img/b92005925d5eabbaaa2b4c49613d7873.png)

现在，让我们创建一个 Ubuntu 容器。首先，我们需要从 hub 中提取图像，然后从中创建一个新的容器实例。

```
# Get image from the hub
docker pull nimmis/apache-php5

# Create the container
docker run -tid nimmis/apache-php5

# List running containers
docker ps
```

第一个命令可能需要一段时间才能从集线器下载完映像。我们在第二个命令中指定的第一个标志(`t`)意味着我们想要分配一个 TTY 来与容器交互，第二个标志(`i`)意味着我们想要一个交互式的标准输入/输出，最后一个标志(`d`)意味着我们想要在后台运行它。

由于这个容器将托管我们的 web 服务器文档，您可能会想，我们如何从浏览器访问我们的服务器呢？

`run`命令上的`-P`选项将自动从容器向主机公开任何需要的端口，而`-p`选项允许您指定从容器向主机公开的端口。

```
# Automatically exposes the container ports to an available host port
docker run -tid -P nimmis/apache-php5

# We can also specify ports manually <host port>:<container port>
docker run -tid -p 80:80 nimmis/apache-php5
```

现在，您可以使用`docker-machine ip docker-vm`地址和指定的端口访问容器。如果您不知道端口，您可以运行`docker ps`命令并查看`ports`列。

![Apache server](img/afbb969dd7fc6677870b049e1a346a3a.png)

### 集装箱容积

卷是在主机和容器之间共享存储的一种简单方法。它们在容器创建期间被初始化并保持同步。在我们的例子中，我们希望将`/var/www`挂载到本地目录`~/Desktop/www/laravel_demo`。

```
# Option syntax
docker run -v <local dir>:<container dir>
```

因此，我们的容器创建命令将如下所示。

```
docker run -tid -p 80:80 -v ~/Desktop/www/laravel_demo:/var/www nimmis/apache-php5
```

![Laravel Demo screenshot](img/6c97c95febb0dbc39d736ef7768ae791.png)

**注意**:默认的 Apache `DocumentRoot`指令指向`/var/www/html`，所以你要在`/etc/apache2/sites-enabled/000-default.conf`配置文件里面把它改成`/var/www/public`，重启 Apache。您可以使用`exec`命令登录到容器中。

```
docker exec -it <container> bash
```

```
# Restart Apache
/etc/init.d/apache2 restart
```

### 命名容器

尽管您可以为大多数命令使用容器 id，但是根据容器的用途来命名容器或者遵循一个命名约定来避免在每次您想要做某件事情时寻找 ID(使用`docker ps`)总是一个好主意。

```
# Option syntax
docker run --name <container name>
```

因此，我们的完整命令将如下所示。

```
docker run -tid -p 80:80 -v ~/Desktop/www/laravel_demo:/var/www --name wazo_server nimmis/apache-php5
```

现在，如果您想要启动、停止或删除容器，您可以使用它的名称而不是 ID。请务必查看 Docker 文档以了解更多关于容器的详细信息。

```
docker start wazo_server
```

## 数据库容器

此时，我们已经设置好了 Apache 服务器，我们将使用一个单独的容器来托管我们的数据库。如果您曾经不得不同时运行两个或更多的虚拟机，您知道您的机器可能会变得有点慢，并且当您添加更多虚拟机时，情况会变得更糟。添加一个单独的轻量级容器来托管我们的数据库，并添加另一个容器来管理后台作业，这将有助于保持轻量级、独立和可管理性。

我们用和上面一样的方法创建 MySQL 容器。我们将`/var/lib/mysql`文件夹挂载到主机上的本地文件夹，以保持两台机器之间的数据同步。在数据丢失的情况下，我们可以将同一个本地文件夹挂载到另一个容器。

```
docker run -p 3306:3306 --name mysqlserver -e MYSQL_ROOT_PASSWORD=root -d mysql
```

`-e`选项允许您在创建容器时设置一个环境变量。在这种情况下，`MYSQL_ROOT_PASSWORD`将告诉 MySQL 安装过程使用我们在命令中指定的密码。

现在，如果您想从外部连接到数据库，您可以使用从`docker-machine ip docker-vm`命令返回的地址和在`docker run`命令上暴露的端口。我的数据库配置将如下所示。

```
// .env DB_HOST=192.168.59.103 DB_DATABASE=demo
DB_USERNAME=root
DB_PASSWORD=root
```

您可以通过运行 Laravel 数据库迁移命令来测试一切是否正常，并确保您的 mysql 容器正在运行。

```
./artisan migrate
```

![Artisan migrate](img/b4665a739c4f02984e2aa35e3ebc7f34.png)

## 使用链接

链接是一种通过环境变量在容器之间共享连接细节的安全方式。链接到容器的第一种方法是向特定端口公开它，然后在应用程序中使用凭据。

另一种方法是使用 [Docker 链接](https://docs.docker.com/userguide/dockerlinks/)。所以，让我们做和上面一样的事情，但是这次使用链接。

```
# Option syntax
docker run --link <container name or ID>:<alias>
```

```
# Create MySQL container first.
docker run -p 3306:3306 --name mysqlserver -e MYSQL_ROOT_PASSWORD=root -d mysql

# Create web server container
docker run -tid -p 80:80 -v ~/Desktop/www/laravel_demo:/var/www --name wazo_server --link mysqlserver:mysqldb nimmis/apache-php5
```

这里的新选项是`--link <container>:<env alias>`。环境别名将作为连接环境变量的前缀，在我们的例子中是`mysqldb`。你可以登录 web 服务器的容器，运行`printenv | grep MYSQLDB`来打印所有的 MySQL 链接变量。

```
# Log into the web server
docker exec --it wazo_server bash

# Print environment variables
printenv | grep MYSQLDB
```

![Link env](img/f71bd226017abd7c83cd69c041a39c6a.png)

Laravel 使用的 [Dotenv](https://github.com/vlucas/phpdotenv) 包允许嵌套环境变量。这意味着我们可以在`.env`文件中添加环境变量。

```
// .env DB_HOST="{$MYSQLDB_PORT_3306_TCP_ADDR}" DB_DATABASE=demo
DB_USERNAME=root
DB_PASSWORD="{$MYSQLDB_ENV_MYSQL_ROOT_PASSWORD}"
```

从命令行运行`./artisan migrate`命令会产生以下结果。

![Artisan migrate](img/3f7c733e28e6fc673cf7f8b43ac6b83d.png)

## 码头文件

既然我们已经学习了如何使用 Docker 图像、容器以及如何将它们链接在一起，我们将学习如何使用 Dockerfiles 来创建个性化的图像，以及如何使用 Docker compose 来管理我们的容器。

Dockerfiles 帮助你构建和共享类似于流浪者的系统镜像。我们将重点介绍用于配置 Docker 映像的主要命令。在本例中，我们将构建一个 Laravel 5 映像。如果你想继续下去，你可以在 Github 上查看最终代码。

### 从

`FROM`指令让您选择从基础图像开始。我们可以选择从像`Ubuntu:14.04`这样的基本映像开始，或者从已经安装和配置了 Apache/NGINX 和 PHP 的映像开始。在我们的例子中，我们将使用 Ubuntu 映像。

```
FROM ubuntu:14.04
```

### 维护

虽然这是可选部分，但还是建议指定镜像维护者，以供参考。

```
MAINTAINER RAFIE Younes <younes.rafie@gmail.com>
```

### 包封/包围（动词 envelop 的简写）

`ENV`指令允许您定义一个可以通过设置过程访问的环境变量。在我们的例子中，我们将使用它来指定我们想要安装的 Laravel 版本，您也可以更改它并为 Laravel 4 创建一个不同的版本。

```
ENV LARAVEL_VERSION ~5.1.0
```

### 奔跑

`RUN`指令允许我们在 shell(而不是 bash)上运行命令。你可以像`RUN apt-get update`一样运行命令，但是如果你想使用 bash，你应该像下面的`RUN ["/bin/bash", "-c", "apt-get update"]`一样使用它。

```
RUN apt-get update && \
    apt-get -y install apache2 php5 libapache2-mod-php5 php5-mcrypt php5-json curl git && \
    apt-get clean && \
    update-rc.d apache2 defaults && \
    php5enmod mcrypt && \
    rm -rf /var/www/html && \
    curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer
```

安装 Apache、PHP 和其他扩展之后，我们需要配置 virtualhost 并指定我们的公共文件夹。让我们创建一个名为`000-default.conf`的文件，其中包含所需的配置。

```
<VirtualHost *:80> ServerAdmin webmaster@localhost
  DocumentRoot /var/www/laravel/public

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined <Directory  /var/www/laravel> Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted </Directory>  </VirtualHost>
```

### 复制

在上面的例子中,`COPY`指令很有用，我们只需要指定源文件和目标文件。

```
COPY 000-default.conf /etc/apache2/sites-available/000-default.conf
```

如果您想要将多个文件复制到映像中，也可以使用通配符。我们可以按如下方式移动多台主机配置。

```
COPY *.conf /etc/apache2/sites-available/
```

### WORKDIR

您可以使用`WORKDIR`移动到另一个目录继续创建图像。我们用它移动到`/var/www`目录，使用 Composer 安装 Laravel。

```
WORKDIR /var/www

RUN composer create-project laravel/laravel laravel $LARAVEL_VERSION --prefer-dist --no-interaction && \
    php laravel/artisan key:generate && \
    chown www-data:www-data -R laravel/storage
```

在转移到`/var/www`之后，我们安装 Laravel，并让 Apache 用户`www-data`可以写必要的文件夹。如果你注意到了，我们在 Composer 上使用了`--no-interaction`标志来避免任何安装问题。

### 揭露

因为我们正在创建小的服务容器，所以通过特定的端口公开这些容器以允许其他服务使用它是有意义的。

```
EXPOSE 80
EXPOSE 443
```

我们通过标准的 HTTP 端口 80 公开我们的服务器，我们还允许通过端口 443 进行 HTTPS 连接。

### 煤矿管理局

`CMD`指令可以用来初始化容器。在我们的例子中，我们将使用它在后台运行我们的 Apache 服务器。

```
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

虽然我们的映像现在已经准备好构建了，但是我们还需要完成一些其他有用的说明。

### 入口点

`ENTRYPOINT`指令让您指定当用户运行 Docker 映像时要执行的命令。我们可以用它来更新我们的 Composer 依赖项。

```
// setup-laravel.sh

#!/bin/bash
cd /var/www/laravel
composer update
```

```
COPY setup-laravel.sh /setup-laravel.sh # Copy the local file to the image
RUN ["chmod", "+x", "/setup-laravel.sh"] # Make sure it's executable
ENTRYPOINT ["/setup-laravel.sh"]
```

您可以浏览[文档](https://docs.docker.com/reference/builder/)中所有可用的说明。在下一节中，我们将看到如何使用 Docker Compose 来管理我们的容器。

## 复合坞站

如果您一直在关注这篇文章的第一部分，我们已经完成了安装过程，并且已经准备好了环境。如果没有，您可以查看[文档](https://docs.docker.com/compose/install/)了解更多安装细节。

我们首先创建一个`docker-compose.yml`文件来保存我们的配置。Docker Compose 使用 YAML 作为配置文件，如果你不熟悉的话可以查看官方的[文档](http://www.yaml.org/spec/1.2/spec.html)。
第一个节点定义服务名；这可能是任何事情。这种情况下就叫`web`。`build`子节点指定图像源，您可以从 Docker hub 或 Dockerfile 路径中选择一个图像来构建图像。

```
web:
  build: .
```

下一步是公开容器端口，使其可以通过主机访问。

```
web:
  build: .
  ports:
    - "80:80"
    - "443:443"
```

我们在第一部分谈到了卷的增加；我们也可以在我们的`docker-compose`文件中指定。

```
web:
  build: .
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - ./laravel:/var/www/laravel
```

因为大多数应用程序需要一些数据库交互，所以我们可以使用前面提到的链接来帮助容器相互通信。

```
web:
  build: .
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - ./laravel:/var/www/laravel
  links:
    - mysqldb:mysqldb
```

您以`<container name>:<env alias>`的形式指定链接。最后一部分是创建`mysqldb`容器。

```
mysqldb:
  image: mysql
  environment:
    - MYSQL_ROOT_PASSWORD=root
```

注意，我们使用了`environment`将`MYSQL_ROOT_PASSWORD`发送到图像。您可以使用这个选项来制作一个通用的 Laravel 映像，它可以安装任何 Laravel 版本。

如果您已经有一个想要链接的现有容器，您可以使用 [`external_links`](https://docs.docker.com/compose/yml/#external-links) 配置。

现在，我们准备构建我们的映像，并创建容器来测试一切是否按预期工作。在项目文件夹中运行`docker-compose up`命令来构建图像并创建容器。这可能需要一段时间才能全部安装完毕。您可以通过从终端运行`docker ps`来查看正在运行的容器，并使用`docker images`来查看构建映像。

## 结论

本文简要介绍了如何从您通常使用的开发环境迁移到 Docker，以及如何从使用轻量级容器而不是为小型服务创建完整的虚拟机中获益。

Docker Compose 是一个仍在开发和改进中的新工具，旨在消除管理和移植开发环境的痛苦。您可以查看[文档](https://docs.docker.com/compose/)以了解更多细节，以及 [Docker Compose YML 文件参考](https://docs.docker.com/compose/yml/#docker-compose-yml-reference)以查看支持的属性列表。一般 Docker 文档，见[此处](https://docs.docker.com/)。

如果你已经尝试过 Docker 和 Docker Compose，我们很想知道你对它们的看法！如果您有任何问题或意见，请在下面发布！

## 分享这篇文章