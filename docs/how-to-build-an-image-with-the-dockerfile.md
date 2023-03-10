# 如何使用 Dockerfile 文件构建图像

> 原文：<https://www.sitepoint.com/how-to-build-an-image-with-the-dockerfile/>

构建应用程序、安装依赖项和服务、自动化部署等等，一切都从 docker 文件开始。让我们回顾一下语法，从基本到详细，以及在构建 Docker 图像时的一些最佳实践。

在本指南中，我们将编写一个 Docker 文件，指导 Docker 为我们将要交付的应用程序选择一个最小的 Linux(基本映像),并附带一套我们选择的工具和一定的配置，有效地构建我们自己的 Linux 发行版，它*正好*用于运行我们的应用程序。

![Building an image with Dockerfile](img/d0a6e76f027c199c576fdd99fb029464.png)

## 为什么是码头工人

![docker_logo](img/e6e170066b6b4c48fc75ec80044c06bb.png)

有了 Docker，你可以“在任何地方构建、发布和运行任何应用”。也就是说，您可以将所有二进制文件和运行时库、后端工具、操作系统调整，甚至您的应用运行所需的特定服务打包到您的应用中，并使其随时可用于即时交付和自动部署。

Docker 实现的**软件容器**技术使这成为可能。虽然我不会在这里涉及它背后的细节，你可以阅读更多关于 Docker，什么是软件容器，以及它们如何在[理解 Docker，容器和更安全的软件交付](https://www.sitepoint.com/docker-containers-software-delivery/)。

### 安装 Docker

在开始之前，您需要安装 Docker，无论它是在您的本地机器上还是在远程服务器上。

幸运的是，Docker 的最新版本(撰写本文时为 1.12)使安装过程变得非常顺利，并且你有了易于遵循的指南，适用于 [Windows](https://docs.docker.com/docker-for-windows/) 、 [MacOS](https://docs.docker.com/docker-for-mac/) 和 [Linux](https://docs.docker.com/engine/installation/linux/) 。

## 文档文件

为了在 Docker 中构建一个映像，首先需要在一个名为`Dockerfile`和*的纯文本文件中设置这个构建的指令，一个上下文*(稍后会详细介绍)。该文件的语法类似于 [Apache 配置文件](https://httpd.apache.org/docs/current/configuring.html#syntax)的语法——每行一条指令及其各自的参数，所有指令都按顺序一个接一个地处理。注释前面有`#`字符和一个空格。最后，一旦你有了 docker 文件，命令`docker build`将构建图像，我们将在后面看到更多细节。

在开始编写 Dockerfile 文件之前，我们将设置工作空间。我们将在主目录中创建一个名为`my_image`的目录，将其用作工作目录，并将`Dockerfile`放在那里:

```
mkdir ~/my_build
cd ~/my_build
touch Dockerfile 
```

现在我们准备开始构建图像。

### 选择基础图像

大多数情况下，当创建一个图像时，您会使用一个起点，即另一个图像。这可以是官方的 [Ubuntu](https://hub.docker.com/_/ubuntu/) 、 [MySQL](https://hub.docker.com/_/mysql/) 、 [WordPress](https://hub.docker.com/_/wordpress/) ，或者任何其他可以从 [Docker Hub](https://hub.docker.com/explore/) 获得的图片。您也可以使用之前自己创建的图像。

***注*** :你可以用自己的核心工具和目录结构创建自己的基础映像，使用 Docker 保留的、极简的映像，名为`scratch`。这是一个我不会在这里介绍的过程，但是你可以参考 Docker 网站上关于[创建基础图像](https://docs.docker.com/engine/userguide/eng-image/baseimages/)的指南。

例如，如果您想从一个最小的 Debian 发行版开始，您可以将以下内容添加到`Dockerfile`:

```
# set the base image
FROM debian 
```

[`FROM`](https://docs.docker.com/engine/reference/builder/#/from) 一定是你写 Dockerfile 时使用的第一条指令。注意，您也可以使用基础图像的特定版本，方法是在图像名称的末尾添加`:`和`version_name`。例如:

```
# set the base image
FROM debian:sid 
```

在上面的代码中，我们使用的是“sid”Debian(不稳定分布)。当你想要一个特定版本的 Ruby 或 Python 解释器，MySQL 版本，或者你所拥有的，当你为这些工具中的任何一个使用一个官方的基础映像时，这也是相关的。现在，我们将坚持使用本指南的默认(稳定)`debian`图像。

### 指定维护者并添加元数据

可选地，您可以指定谁是 [`MAINTAINER`](https://docs.docker.com/engine/reference/builder/#/maintainer) ，用您的名字或负责构建的人或团队替换`Lucero del Alba`:

```
# author
MAINTAINER Lucero del Alba 
```

这不是必须的，但我们也可以使用 [`LABEL`](https://docs.docker.com/engine/reference/builder/#/label) 指令添加一些元数据，这些信息将在以后使用 [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect/) 命令检查图像时可用:

```
# extra metadata
LABEL version="1.0"
LABEL description="First image with Dockerfile." 
```

有关该功能的更多信息，请参考 [Docker 对象标签](https://docs.docker.com/engine/userguide/labels-custom-metadata/)。

### 制作自己的发行版

![Linux logo](img/36226336c143a3bc9e82bb4819d44baa.png)

在这一点上，我们将选择一些工具和库来包含在我们的映像中，以便我们的容器拥有我们想要它做的一切。在本教程的最后，我们将做一些非常接近实际构建 Linux 发行版的事情。

一些容器，比如运行 PostgreSQL 数据库的容器，应该在后台运行。但是我们经常需要一个控制台来对容器执行一些操作，所以我们很可能需要一些额外的工具，因为基础映像只捆绑了最小的一组 GNU 工具。

#### 处理缓存问题

几乎可以肯定的是，当你试图在你的映像上安装额外的包时，你会遇到缓存问题。这是因为基本映像带有缓存的元数据，而您从中提取数据的实时存储库经常发生变化。

在基于 Debian 的发行版中，您可以在安装新的软件包之前添加以下命令来处理这个问题:

```
# update sources list
RUN apt-get clean
RUN apt-get update 
```

#### 安装基本工具

代码编辑器、语言环境、工具，如`git`或`tmux`——这是安装您以后需要的所有东西的时候了，所以它们被捆绑在映像中。

我们将每行安装一个:

```
# install basic apps, one per line for better caching
RUN apt-get install -qy git
RUN apt-get install -qy locales
RUN apt-get install -qy nano
RUN apt-get install -qy tmux
RUN apt-get install -qy wget 
```

我们可以在一行中安装所有的软件包，但是如果我们以后想要添加或删除一个软件包，我们需要重新运行整个过程。所以这里的最佳实践是**每行安装一个包**，这样你就可以从 Docker 的缓存中获益。

还有，**保持紧绷**。您不希望“以防万一”地安装工具，因为这可能会增加构建时间和映像大小。

#### 为应用程序安装运行时库

我们也将以这张图片发布我们的应用程序。你需要特定版本的 PHP、Ruby 或 Python，以及特定的模块吗？现在是时候交付我们的应用程序需要的所有程序和运行时了。

尽可能具体，因为该容器旨在仅运行*您的*应用程序:

```
# install app runtimes and modules
RUN apt-get install -qy python3
RUN apt-get install -qy python3-psycopg2
RUN apt-get install -qy python3-pystache
RUN apt-get install -qy python3-yaml 
```

对于这个例子，我们将安装 Python 3 以及包 Psycopg 2(连接到 PostgreSQL 数据库)、Python 的 Mustache 模块和 YAML 模块。(在编写自己的 docker 文件时，您自然会安装所需的特定依赖项。)

#### 编译和下载包

也有可能您的发行版中没有您需要的某个模块或程序的包。但是你不需要在你的运行容器中手动安装它！相反，您可以使用 [`RUN`](https://docs.docker.com/engine/reference/builder/#/run) 指令(一行一个)来批量下载、编译和设置您的应用程序需要的库。

您甚至可以在一个单独的文件上编写一个脚本，将这个文件添加到构建中并运行它，我们将在后面的“发布您自己的应用程序”一节中看到。

#### 清理

为了保持你的图像整洁和尽可能小，在安装序列的最后做一个清理*也是一个好主意:*

```
# cleanup
RUN apt-get -qy autoremove 
```

同样，请注意我们使用了`apt-get`，因为我们选择了 Debian，但是使用适当的命令来分发您的基本映像。

### 运送您自己的应用程序

构建这个环境的全部目的是让您可以平稳地交付您的应用程序并准备好运行。要将文件、目录甚至远程 URL 的内容添加到图像中，我们将使用 [`ADD`](https://docs.docker.com/engine/reference/builder/#/add) 指令。

然而，在添加文件之前，我们需要将它们放在适当的*上下文*中。为了使事情变得简单，我们将把所有东西都放在前面提到的`my_build`目录中，和`Dockerfile`放在一起。

假设有了应用程序和我们想要放入图像的所有东西，我们在`~/my_build`中有以下文件(其中`app.py`和`lib.py`在子目录`app/`中):

```
.bashrc
.profile
app/app.py
app/lib.py
Dockerfile
```

我们将把`.bashrc`和`.profile`脚本添加到容器中的`/root`目录，这样每当我们在容器上启动 shell 时它们就会执行，我们将把`app/`的内容复制到容器中的`/app/`目录。

我们添加以下说明:

```
# add scripts to the container
ADD .bashrc /root/.bashrc
ADD .profile /root/.profile

# add the application to the container
ADD app /app 
```

### 设置您的环境

最后，我们将设置一些在系统和应用程序级别需要的环境变量。

你们中的许多人使用默认的 Debian 字符集就可以了，但是因为我们的目标是国际观众，所以让我们看看如何拥有一个 UTF-8 终端。我们之前安装了`locales`包，所以我们现在要做的就是生成字符集并设置适当的 Linux 环境:

```
# locales to UTF-8
RUN locale-gen C.UTF-8 && /usr/sbin/update-locale LANG=C.UTF-8
ENV LC_ALL C.UTF-8 
```

您可能还需要为您的应用程序设置一些环境变量，用于交换密码和路径。Dockerfile 提供了 [`ENV`](https://docs.docker.com/engine/reference/builder/#/env) 指令来实现这一点:

```
# app environment
ENV PYTHONIOENCODING UTF-8
ENV PYTHONPATH /app/ 
```

请注意，您还可以在启动容器时从命令行传递环境变量，这对于共享一些敏感信息(如密码)可能很方便。

## 完整的文档

当然，您必须根据自己的需要来修改 docker 文件，但是希望您能够了解各种可能性。

以下是完整的文件:

```
# author
MAINTAINER Lucero del Alba

# extra metadata
LABEL version="1.0"
LABEL description="First image with Dockerfile."

# set the base image
FROM debian

# update sources list
RUN apt-get clean
RUN apt-get update

# install basic apps, one per line for better caching
RUN apt-get install -qy git
RUN apt-get install -qy locales
RUN apt-get install -qy nano
RUN apt-get install -qy tmux
RUN apt-get install -qy wget

# install app runtimes and modules
RUN apt-get install -qy python3
RUN apt-get install -qy python3-psycopg2
RUN apt-get install -qy python3-pystache
RUN apt-get install -qy python3-yaml

# cleanup
RUN apt-get -qy autoremove

# add scripts to the container
ADD .bashrc /root/.bashrc
ADD .profile /root/.profile

# add the application to the container
ADD app /app

# locales to UTF-8
RUN locale-gen C.UTF-8 && /usr/sbin/update-locale LANG=C.UTF-8
ENV LC_ALL C.UTF-8

# app environment
ENV PYTHONIOENCODING UTF-8
ENV PYTHONPATH /app/ 
```

### 建立形象

在`my_build`目录中，我们将使用 [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) 命令，传递`-t`标志来为新图像“标记”一个名称，在本例中是`my_image`。`.`表示`Dockerfile`在当前目录中，以及所谓的“上下文”——即可能在该位置的其余文件:

```
cd ~/my_build
docker build -t my_image . 
```

这将生成一个很长的输出，其中每个“步骤”都是 docker 文件中的一个指令。这是一个截断的输出:

```
Sending build context to Docker daemon  5.12 kB
Step 1 : FROM debian
 ---> 7b0a06c805e8
Step 2 : MAINTAINER Lucero del Alba
 ---> Running in d37e46e5455d
 ---> 2d76561de558
Removing intermediate container d37e46e5455d
Step 3 : LABEL version "1.0"
 ---> Running in 904dde1b4cd7
 ---> a74b7a492aaa
Removing intermediate container 904dde1b4cd7
Step 4 : LABEL description "First image with Dockerfile."
 ---> Running in 9aaef0353256
 ---> 027d8c10e966
Removing intermediate container 9aaef0353256
Step 5 : RUN apt-get clean
 ---> Running in bc9ed85dda16
 ---> a7407036e74a
Removing intermediate container bc9ed85dda16
Step 6 : RUN apt-get update
 ---> Running in 265e757a7563
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Get:4 http://deb.debian.org jessie Release [148 kB]
Get:5 http://security.debian.org jessie/updates/main amd64 Packages [402 kB]
Get:6 http://deb.debian.org jessie-updates/main amd64 Packages [17.6 kB]
Get:7 http://deb.debian.org jessie/main amd64 Packages [9064 kB]
Fetched 9843 kB in 10s (944 kB/s)
Reading package lists...
 ---> 93fa0a42fcdc
Removing intermediate container 265e757a7563
Step 7 : RUN apt-get install -qy git
 ---> Running in c9b93cecd953
(...) 
```

### 列出图像

我们可以用 [`docker images`](https://docs.docker.com/engine/reference/commandliimg/) 命令列出我们的图像:

```
docker images 
```

这将输出我们新创建的`my_image`以及我们已经下载的其他基本图像:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
my_image            latest              e71dc183df2b        8 seconds ago       305.6 MB
debian              latest              7b0a06c805e8        2 weeks ago         123 MB
debian              sid                 c1857cb435d7        3 weeks ago         97.77 MB 
```

…就这样，我们的映像已经**准备好交付并运行**！

### 启动容器

最后，为了启动我们新创建的图像的交互终端，我们将使用 [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 命令:

```
docker run -ti my_image /bin/bash 
```

## 下一步做什么

我没有涵盖 Dockerfile 文件的所有可能性。特别是，我还没有回顾如何使用 [`EXPOSE`](https://docs.docker.com/engine/reference/builder/#/expose) 端口来运行服务，甚至在它们之间链接容器；如何向 [`HEALTHCHECK`](https://docs.docker.com/engine/reference/builder/#/healthcheck) 容器验证它们是否还在工作；或者甚至如何指定一个 [`VOLUME`](https://docs.docker.com/engine/reference/builder/#/volume) 来存储和恢复来自主机的数据…以及其他有用的特性。

我们将在以后的文章中讨论这些内容。现在，您可能想查看以下资源。

来自 Docker 网站:

*   [docker 文件参考](https://docs.docker.com/engine/reference/builder/)
*   [编写 docker 文件的最佳实践](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)

从站点点:

*   [了解 Docker、容器和更安全的软件交付](https://www.sitepoint.com/docker-containers-software-delivery/)
*   [Docker 子通道](https://www.sitepoint.com/web/docker/)
*   [所有与 Docker 相关的文章](https://www.sitepoint.com/?s=docker)

## 分享这篇文章