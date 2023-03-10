# 如何手动构建 WordPress 的 Docker 容器

> 原文：<https://www.sitepoint.com/how-to-manually-build-docker-containers-for-wordpress/>

在我的上一篇文章中，[我们介绍了什么是 Docker](https://www.sitepoint.com/docker-for-wordpress-developers/)以及如何用几个命令启动并运行它。然而，我们还没有做任何有用的事情。使用 Docker 有很多方法可以获得 WordPress 环境，在这篇文章中，我将向你展示如何手动设置 Docker 容器来使用 WordPress。如果你想快速了解 Docker，你可以在这里跳回到[的第一篇文章](https://www.sitepoint.com/docker-for-wordpress-developers/)。

## 设置 MySQL

每个 WordPress 安装都需要一个 MySQL 数据库。为此，我们前往 [Docker Hub](https://registry.hub.docker.com) 并找到一个 MySQL 映像。

Docker 团队已经准备好了一个 MySQL 镜像供我们使用。在终端上运行任何命令之前，请务必阅读此映像的文档[。撰写本文时的最新版本是 5.7。但是，最新的标签名称是 5.6。映像的最新版本可以是任何以前的版本，但只能是稳定状态的版本。](https://registry.hub.docker.com/_/mysql/)

![Docker Image](img/919a4b950881dc0dbb08e166ffd33b38.png)

使用此映像设置容器的基本命令是:

```
 docker run --name wordpressdb -d mysql:5.7 
```

![MySQL docker image](img/279fe3a8b6e1dd99455569508a987de7.png)

如果您在本地还没有该图像的副本，Docker 会从 Docker Hub 为您提取。到目前为止，我们知道`--name`给了我们的容器一个名字，`-d`确保我们的容器在后台运行。

如果您运行`docker ps`，您将看到`wordpressdb`容器没有运行。它应该还在运行。运行`docker logs wordpressdb`，你会看到这样一条消息:

```
 error: database is uninitialized and MYSQL_ROOT_PASSWORD not set
  Did you forget to add -e MYSQL_ROOT_PASSWORD=... ? 
```

这是为什么呢？这是因为我们在第一次构建容器时没有将 root 密码作为参数传递。所以我们就这么做吧。首先，我们需要使用`docker rm wordpressdb`删除我们创建的名为 **wordpressdb** 的容器。这是因为新容器将使用相同的名称，并且不能有两个同名的容器。

所以让我们再次创建我们的容器。当我们第一次创建容器时，我们需要传递一个环境变量。它应该是这样的:

```
 docker run --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -d mysql:5.7 
```

`-e MYSQL_ROOT_PASSWORD=password`是环境变量。当从映像构建容器时，它读取这个变量并将 root 用户的密码设置为指定的值，在本例中是`password`。

如果你现在检查`docker logs wordpressdb`，你会看到一条很长的消息，但是不要担心，它正在工作。再次运行`docker ps`，您将看到一个名为`wordpressdb`的容器处于活动状态并正在运行。

你也可以将其他环境变量传递给你的容器，你可以在 MySQL 图片文档中找到完整的列表。这是另一个例子:

```
 docker run --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -d mysql:5.7 
```

如果您试图删除名为 **wordpressdb** 的前一个容器，可能会失败。这是因为容器仍然在后台运行。您可以首先停止正在运行的容器，然后删除它，或者只是强制删除它:

```
 docker rm -f wordpressdb 
```

如果我们使用`MYSQL_DATABASE`，它会确保创建一个同名的数据库。这样，我们就可以确切地知道数据库的名称和 roots 密码。您还可以使用密码和数据库创建另一个用户。给你一个快速测试，看看他们的[文件](https://registry.hub.docker.com/_/mysql/)，试着自己做。

如果你想知道更多这个容器是如何建造的，看看 [Dockerfile](https://github.com/docker-library/mysql/blob/95a8d25a230dda44709b8942c46c63595b93b73a/5.7/Dockerfile) 。它使用 **debian wheezy** 并使用 bash 命令构建容器。它从存储库中取出它，然后启动`mysqld`。当*从这个映像构建*你的容器时，它将第一次执行构建文件的命令。当*使用*容器时，它只会执行`mysqld`。

![Docker - MySQL buildfile](img/833061d3d015ab0b234ce9ef234b46de.png)

现在我们有了一个正在运行的 MySQL 容器，我们可以运行一个运行 WordPress 的容器。

## 构建一个 WordPress 容器

对于这个容器，我们将使用 PHP 图像。有三种类型的 PHP 图像，我们只需要 Apache 附带的 PHP 图像。

```
 docker run --name wordpress php:5.6-apache 
```

如果没有`-d`选项，它不会在后台运行，而是会显示容器输出的所有内容(和`docker logs [container_name]`一样)。

从输出中，您可以看到它已经自动为该容器分配了一个 IP。我的情况是`172.17.0.35`。如果你使用浏览器访问这个地址，你会得到一个禁止的错误。这是为什么呢？这是因为`/var/www/html`文件夹(在容器文件系统上)中什么也没有，它是空的。

那么我们如何把文件放在那个文件夹里呢？默认情况下，该文件夹位于容器内，是不可见的。然而，好景不长(别忘了`docker rm wordpress`)。首先，创建一个文件夹并在里面导航(不要忘记移除旧的 **wordpress** 容器)。

```
 docker run --name wordpress -v "$PWD/":/var/www/html php:5.6-apache 
```

`-v`用于映射两个文件夹。第一部分是操作系统上的文件夹，第二部分是容器文件系统中的文件夹。在类似 Unix 的系统上，`"$PWD"`返回命令运行时终端所在的位置。当您第一次启动终端时，您将位于您的主目录中。在 Windows 上相当于`cd`。更多关于 **PWD** 的信息可以在[这里](http://en.wikipedia.org/wiki/Pwd)找到。

所以在我们的例子中，第一部分是“$PWD/”，这是本地目录，第二部分是`/var/www/html/`。`-v`要求两者都是完整路径。然而，如果我们查看我们的工作目录，我们可以看到那里不存在任何文件。创建一个名为`index.php`的文件，包含以下内容:

```
<?php 

phpinfo();

?>
```

请在您的浏览器中再次检查。这一次，您会注意到 IP 地址发生了变化，因为我们创建了一个新的容器。每当我们创建一个新的容器，它就改变它的 IP。如果您在浏览器中看到该消息，那么您已经做了所有正确的事情。

让我们看看如果我们把 WordPress 文件放在那里会发生什么。使用`docker stop wordpress`停止容器。从`wordpress.org`获取 WordPress 的最新版本，并将文件放入项目文件夹中。使用`docker start wordpress`再次启动容器。另外，请注意，您最初需要使文件可读。可以在*nix 系统上运行`chmod -R 777 projectfolder`。如果你重新加载页面，你的浏览器会告诉你:

```
 Your PHP installation appears to be missing the MySQL extension which is required by WordPress. 
```

默认情况下，PHP 镜像没有安装 MySQL 扩展，但是我们可以解决这个问题。这次我们将通过 Dockerfile 文件构建一个容器。我们已经看到了 docker 文件是如何工作的。它们从一个基础映像构建，进行一些处理，然后最终执行一个命令。

创建一个名为 Dockerfile 的新文件:

我们想使用`php:5.6-apache`图像。

```
 FROM php:5.6-apache 
```

然后我们将安装`mysqli`扩展。

```
 RUN docker-php-ext-install mysqli 
```

接下来，我们需要像 PHP 映像一样执行`apache2-foreground`(我们只需要安装 MySQL 扩展)。

```
 CMD ["apache2-foreground"] 
```

使用构建文件，我们可以构建图像。使用这个图像，我们构建容器。

```
 docker build -t phpwithmysql . 
```

`-t`用于给出一个存储库名称。`.`告诉 Docker Docker 文件所在的位置。由于 docker 文件位于工作目录中，`.`告诉 docker 它在工作目录中。

如果您用`docker images`检查图像，您现在会看到一个带有标签 latest 的新图像(因为我们没有为这个图像指定标签)。现在用这个图像构建这个容器，就像我们用 php5.6-apache 图像一样。

```
 docker run --name wordpress -v "$PWD/":/var/www/html phpwithmysql 
```

检查您的浏览器中的容器 IP，您会看到类似这样的内容:

![Welcome WordPress Screen](img/812950ba97edaa6c0464d8e8b372fa29.png)

如果你走到这一步，那么你做的一切都是对的。现在我们必须把 WordPress 和数据库连接起来。这与著名的 WordPress 的 5 分钟安装相差甚远，也更复杂，但从长远来看，你会看到 Docker 的好处。

那么我们如何把 WordPress 和数据库联系起来呢？首先，我们需要将 **wordpress** 容器与一个数据库容器( **wordpressdb** )链接起来。这可以通过链接两个容器来完成。更多链接可以在[这里](http://docs.docker.com/userguide/dockerlinks/)找到。

```
 docker run --name wordpress --link wordpressdb:mysql -v "$PWD/":/var/www/html phpwithmysql 
```

新的论据是`--link`。第一部分`wordpressdb`是我们想要链接的容器的名称，第二部分`mysql`是别名。Docker 修改了`wordpress`容器的主机并将`wordpressdb`的 IP 设置为`mysql`。因此，当我们在 WordPress 配置上填写数据库信息时，我们会将主机设置为“mysql”。

现在使用容器的 IP(新 IP)转到您的浏览器。填写数据库信息并登录管理员面板。如果您尝试安装一个新的主题(它将尝试对文件系统进行更改)，您将会看到类似这样的内容:

![WordPress not writable access on filesystem](img/883fd84c9abadb0e62c2f91b7a00128c.png)

这是为什么呢？这是因为运行 Apache 的用户对文件系统没有写权限。这就是事情变得有点困难的地方。我们需要建立一个新版本的`phpwithmysql`图像。转到 docker 文件，将其修改为如下所示:

```
 FROM php:5.6-apache

RUN docker-php-ext-install mysqli

COPY entrypoint.sh /entrypoint.sh 

RUN chmod 777 /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

CMD ["apache2-foreground"] 
```

我们还没有创建 entrypoint.sh 文件，但是我们很快就会这样做。`COPY`将`entrypoint.sh`复制到`/`容器内。`chmod 777 /entrypoint.sh`使该文件可执行。最后`ENTRYPOINT`执行那个文件。现在在 Dockerfile 文件所在的目录中创建`entrypoint.sh`文件。

```
#!/bin/bash

chown -R www-data:www-data .

exec "$@"
```

这是官方 WordPress 映像的简化解决方案，但将确保我们对容器文件系统有写权限。我们现在可以构建新的图像:

```
 docker build -t phpwithmysql:v2 . 
```

确保移除旧容器并创建新容器:

```
 docker rm -f wordpress
docker rm -f wordpressdb 
```

```
 docker run --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -d mysql:5.7 
```

```
 docker run --name wordpress --link wordpressdb:mysql -v "$PWD/":/var/www/html phpwithmysql:v2 
```

另外，删除旧的`wp-config.php`文件。

现在在浏览器中检查您的`wordpress`容器的 IP。这一次您可以安装主题和插件，并对容器文件系统进行修改。

上面的一些步骤可能看起来相当神秘和复杂。这就是为什么许多不同的框架和语言都有官方图像。每个框架或语言都有不同的工作规范。默认情况下，Docker 不允许应用程序在文件系统上写。这是坏事还是好事？我觉得这是好事。我们可以创建第三个只存放文件的容器。应用程序可以在那里写文件。这样我们就会有一个更加模块化的架构。但是对于那些不能改变的框架(比如 WordPress)，还是有变通办法的。

## 最终调整

我们要做的最后一件事是解决当你停止 **wordpress** 容器，并再次启动它时出现的问题。问题是 WordPress 将最后一个 IP 保存为它的“主页”和“网站”URL。停止**容器并再次启动。这次它将有一个新的 IP。如果你在你的浏览器中尝试，你会看到图片，css 和 javascript 文件没有被正确的包含。解决方案很简单，只需通过添加以下代码行来修改`wp-config.php`:**

```
define('WP_HOME',$_SERVER['SERVER_ADDR']);
define('WP_SITEURL',$_SERVER['SERVER_ADDR']);
```

请注意，如果您在`wp-config.php`文件中定义了这些值，您以后就不能在常规设置中更改它们。

## 结论

在本文中，我们介绍了如何为 WordPress 构建容器。我们用一种相当神秘的方式做到了这一点，使用了很难记住的长命令。应该有更简单的方法，而且有！Docker 团队已经建立了一个 WordPress 图片，你可以在几分钟内轻松设置。毕竟，谁想记住设置 WordPress 的每一个命令呢？

在本系列的下一篇文章中，我将“向您展示如何使用官方的 WordPress 图片，我们还将学习如何使用 Docker Compose 使事情变得更加简单。

如果有更简单的方法，我为什么要写这篇文章呢？本质上，这是为了更好地理解 Docker 是如何工作的，要做到这一点，您必须亲自体验底层的复杂性。这更像是一个个人规则，所以当我明天开始使用 Docker 时，我会更了解它是如何工作的，以及如何根据我的需要调整它。我希望您现在也对 Docker 的幕后工作有了更深的理解。请继续关注本系列的第三篇文章，在那里我们将从 Docker 和 WordPress 中获得更多的乐趣。

到目前为止你对 Docker 有什么看法？你会在下一个项目中考虑它吗？请在下面的评论中告诉我。

## 分享这篇文章