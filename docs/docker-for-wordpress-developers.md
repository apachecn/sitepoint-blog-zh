# WordPress 开发者 Docker 简介

> 原文：<https://www.sitepoint.com/docker-for-wordpress-developers/>

到目前为止，我们已经看到了许多不同的开发环境选项。我们已经见识了[流浪汉 VVV](https://www.sitepoint.com/wordpress-meets-vagrant-vvv/) 和[底盘](https://www.sitepoint.com/using-chassis-for-wordpress-development/)的能耐；两者都是很棒的解决方案，并且都建立在[流浪者](https://www.vagrantup.com/)的基础上。如果你已经开始使用流浪者解决方案进行开发，那么这对你有好处。但是等等，还有一个选择。在这篇文章中，我们将看到什么是 Docker ，以及如何在你的 WordPress 项目中使用它。

## 为什么是 Docker？

![Docker](img/d5c8684e976b8ce4642feb45c652e8e8.png)

Docker 是一个开源平台，可以帮助你构建、运行和部署应用。在这篇文章中，我们将开始使用 Docker 和 [WordPress](https://wordpress.org/) ，但是它真的很容易和任何其他的 CMS 或框架以及任何语言一起使用。Docker 已经成为 DevOps 社区中的一个[流行语](http://opensource.com/business/14/7/why-docker-new-craze-virtualization-and-cloud-computing)有一段时间了。考虑一下:在其第一个 1.0 稳定版本中，许多云提供商开始采用 Docker。如果你使用过 [Heroku](https://www.heroku.com/) 或其他 [PaaS](http://en.wikipedia.org/wiki/Platform_as_a_service) 一段时间，你可能知道在它们上面开发很容易。对于大多数 PaaS 提供商，您有一个 web 容器、一个数据库容器、一个保存文件的容器等等。每个容器都是不同的，通过 IP 和端口相互通信。PaaS 平台也使用容器技术。

Docker 也是如此。您可以创建一个运行代码的容器(可能是运行 PHP 脚本的 Apache 和 [PHP](http://php.net/) ，以及一个保存数据库的容器( [MySQL](https://www.mysql.com/) 、 [MongoDB](https://www.mongodb.org/) 等等)。但是你也可以使用流浪者做到这一点，我必须承认流浪者在这方面做得很好。它解决了在开发阶段通常与在我的机器上工作相关的问题，并且它也接近了生产环境。但另一方面，Docker 让当地的环境和生产变得一样。

我首选的流浪汉配置是[宅地](http://laravel.com/docs/homestead)。是的，著名的[拉腊维尔](http://laravel.com)包厢。它对大多数项目来说非常好，但它也附带了两个数据库和许多其他你可能不需要的软件程序。Docker 比传统的[虚拟机管理程序](http://en.wikipedia.org/wiki/Hypervisor)技术表现更好。如果你用的是 Linux，Docker 对于本地开发来说是超级快的。它还可以比传统的虚拟机管理程序更快，即使您没有将 Linux 作为主要操作系统运行。

如果你已经使用了一段时间，并想学习一些新的和酷的东西，那么这篇文章是给你的。

## Docker 简介

Docker 是用容器的概念构建的。这不是一个新的想法。我们已经使用容器很多年了。Android 应用也是建立在这个概念之上的。Docker 由一个 [Docker CLI](https://docs.docker.com/reference/commandline/cli/) 和一个守护进程组成。您将只使用 CLI。CLI 与守护程序通信，守护程序执行处理。

容器是从图像构建的，带有一些额外的配置。您也可以从其他图像创建图像。假设你想用 Ubuntu 14.04 镜像。你也想用 Apache 和 PHP。你可以使用 Ubuntu 镜像，然后安装 Apache 和 PHP。通过这种方式，您可以创建另一个图像，并与他人分享此图像。图像就像容器的蓝图。容器是根据这些图像构建的。相比之下，你有一个 Ubuntu 的基本操作系统，然后使用像 [Puppet](https://puppetlabs.com/) 这样的工具，然后指示它安装 Apache 和 PHP。然后你“流浪起来”，你就准备好去发展你的地方了。

在开始这篇文章之前，我强烈推荐你尝试一下[互动教程](https://www.docker.com/tryit/)。

## 使用 Docker 变得实用

首先，确保你的系统上安装了 Docker。理想的解决方案是安装 Linux 作为您的主要操作系统，特别是考虑到 Docker 使用 Linux 内核。但是可以在 Windows 和 Mac 上运行 Docker。有一个叫做 [Boot2Docker](http://boot2docker.io/) 的工具，Mac 和 Windows 都有。有关安装说明，请阅读他们的[安装指南](http://docs.docker.com/installation/)。

要确保 docker 正确安装在您的系统上，运行`docker -v`，Docker 会告诉您系统上安装的是什么版本。

关于 Docker，你需要知道两件主要的事情:图像和容器。图像就像容器的蓝图。图像告诉 Docker 容器应该是什么。您不能运行图像；相反，你用它们来建造容器。在 [Docker Hub](https://registry.hub.docker.com/) 上，你可以看到一个图片列表。有来自 Docker 团队的图片，也有来自社区的图片。

![Docker Hub](img/686c1eaa788c3def433ced8fa4f90f8f.png)

要查看系统上当前可用的映像，请执行:

```
docker images 
```

这将为您提供系统上下载的图像列表。如果您过去没有使用过 Docker，您将会收到系统上没有图像的结果。

让我们从 Docker Hub 下载一张图片。下载最多的是 Ubuntu 镜像。要下载图像，您应该执行`docker pull [imagename]:[tag]`或`docker pull [imagename]`。

```
docker pull ubuntu:12.04 
```

或者

```
docker pull ubuntu 
```

现在使用`docker images`再次检查您的图像。现在有两个图像。标记名可以用作版本控制，但不能单独使用。例如，在这个 PHP 镜像中，有相同的 PHP 版本，有 Apache、nginx，也有没有其中任何一个。你可以在每个图片页面(Docker Hub)上找到标签名称。如果没有指定标签名，那么它将提取标签名为`latest`的图像。

您可以使用`docker run [imagename]:[tag]`或`docker run [imagename]`构建容器。如果不指定标签名，它将运行标签为`latest`的图像。然后您可以使用`docker start [name_of_the_container]`开始设置您的容器。如果你不为容器指定一个名字，Docker 会给容器一个随机的名字。让我们构建一个容器:

```
docker run ubuntu 
```

要运行容器，您必须执行:

```
docker ps 
```

但是为什么没有运行容器呢？因为容器已经完成了它的过程。要查看到目前为止已创建的容器列表，您需要运行`docker ps -a`。这将为您提供所有现有容器的列表和一些附加信息，如容器 ID、容器启动时将执行的命令、映像名称、其状态、端口和名称。当第一次创建容器时，我们没有给它命名。因此，容器 ID 是它的唯一标识符。在某些情况下，它可以代替容器的名称。端口是容器外部的公共端口(端口可以在 Docker 容器和您的系统之间映射)。您可以通过以下方式删除已创建的容器:

```
docker rm [container name]
docker ps -a 
```

为了更好地理解容器是如何工作的，让我们分析一下它的 Dockerfile。在 Docker Hub 上的 Ubuntu 页面，你可以看到链接到 Docker 文件的不同标签名。我们来看看 Ubuntu 12.04 是什么样子的。

![Ubuntu 12.04 Dockerfile](img/21221047e1607206fa8605d9491d8196.png)

`FROM`是告诉 Docker 使用基础图像的一种方式，在本例中是刮花。你也可以有其他使用 Ubuntu 图像的图像。在他们的 other 文件中，你会看到`FROM ubuntu:14.04`或者其他版本的 Ubuntu 图片。在这种情况下，scratch 几乎不做任何事情。

然后它下载 Ubuntu 的内容，运行一些命令，就像我们在终端上做的一样(在 nix 系统上)。如果你仔细看，当我们执行`docker ps -a`时，有一个命令文件。在 [Dockerfile](https://docs.docker.com/reference/builder/) 的结尾，容器创建时以及每次启动时将执行的命令是，`/bin/bash`。这没有任何作用。

现在，让我们创建另一个容器，但是这一次使用不同的名称和命令，在每次容器启动时运行。

```
docker run --name ubuntucontainer ubuntu echo something 
```

你看到这在终端上有什么回响吗？如果您再次输入它，`docker ps -a`您将看到一个具有给定名称的新容器，这次使用了不同的命令。通过在图像名称后添加一些东西，Docker 将其作为命令接受。

但这真的很无聊。当我们运行一个容器时，我们希望它停留一段时间。但这实际上取决于它在启动时(或首次创建时)执行的命令。从基本 Ubuntu 映像创建另一个容器。

```
docker run ubuntu ping google.com 
```

这将表明它无法找到 ping。这是因为默认情况下没有安装 ping。但是我们可以做些别的事情:构建一个运行`apt-get install -y ping`命令的容器，然后从中创建一个图像。

```
docker run ubuntu apt-get install -y ping 
```

然后你必须找到建造的容器。之后执行:

```
docker commit [containername] 
```

你会发现新图像是这样的:

```
docker images 
```

您的新图像没有名称，但有一个图像 id。我们可以用这个 id 来创建一个容器。使用前四个字符就足够了。

```
docker run [id] ping google.com 
```

这里的问题是它不会停止。我们可以让它在后台模式下运行。

```
docker run -d [id] ping google.com 
```

现在执行`docker ps`来查看正在运行的容器。要查看 ping 实用程序的输出，请使用:

```
docker logs [container_name] 
```

这将永远运行(或者直到您关闭计算机)。要停止正在运行的容器，可以使用:

```
docker stop [name_of_the_container] 
```

如果您输入`docker ps`，您将看到不再有运行的容器。

## 结论

在本文中，我们发现了 Docker 是什么。但是我们仅仅触及了它所有功能的表面。但是不要绝望:接下来还有三篇文章。在下一篇文章中，我们将看到如何为 WordPress 手动设置 Docker。之后，我们将看到如何通过官方的 WordPress 映像更容易地设置 Docker，以及如何使用 Docker Compose。所以，请确保您继续关注更多。

我们想听听你的意见。你觉得 Docker 怎么样？你会在下一个项目中考虑它吗？如果您有任何问题，请在下面留下您的评论。