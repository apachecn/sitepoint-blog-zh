# 如何安装 MySQL

> 原文：<https://www.sitepoint.com/how-to-install-mysql/>

**几乎所有的网络应用都需要基于服务器的数据存储， [MySQL 仍然是最常用的数据库解决方案](https://insights.stackoverflow.com/survey/2019#technology-_-databases)。本文讨论了开发过程中在本地系统上使用 MySQL 的各种选项。**

MySQL 是一个免费的开源关系数据库。 [MariaDB](https://mariadb.com/) 是数据库的一个分支，创建于 2010 年，起因是对甲骨文收购 MySQL 的担忧。(它的功能是相同的，所以本文中描述的大多数概念也适用于 MariaDB。)

虽然 NoSQL 数据库近年来激增，但对于大多数应用程序来说，关系数据通常更实用。也就是说，MySQL 也支持类似 NoSQL 的数据结构，比如 [JSON 字段](https://www.sitepoint.com/use-json-data-fields-mysql-databases/)，这样你就可以享受两个世界的好处。

下面几节研究了在本地开发环境中使用 MySQL 的三种主要方式:

1.  基于云的解决方案
2.  使用 Docker 容器
3.  在您的电脑上安装。

这篇受欢迎的文章于 2020 年更新，以准确反映当前安装 MySQL 的过程。关于 MySQL 的更多信息，请阅读[快速启动 MySQL](https://www.sitepoint.com/premium/books/jump-start-mysql?utm_source=blog&utm_medium=articles) 。

## 基于云的 MySQL

MySQL 服务由 AWS、Azure、Google Cloud、Oracle 和许多其他专业托管服务提供。即使是低成本的共享主机也提供带有远程 HTTPS 或隧道 SSH 连接的 MySQL。因此，您可以在本地开发中远程使用 MySQL 数据库。好处:

*   无需安装或管理数据库软件
*   您的生产环境可以使用相同的系统
*   不止一个开发人员可以轻松访问相同的数据
*   它非常适合那些使用基于云的 ide 或 Chromebooks 等低规格设备的用户
*   可能包括自动扩展、复制、分片和备份等功能。

缺点是:

*   设置仍然需要相当长的时间
*   不同主机之间的连接库和进程可能略有不同
*   实验风险更大；任何开发人员都可能意外地删除或修改数据库
*   当你没有互联网连接时，开发将会停止
*   可能会有令人眼红的使用成本。

基于云的选项对于那些对数据库要求最低的人或者处理相同复杂数据集的大型团队来说可能是实用的。

## 使用 Docker 运行 MySQL

Docker 是一个允许你在*容器*中构建、共享和运行应用程序的平台。可以把容器想象成一个独立的虚拟机，拥有自己的操作系统、库和应用程序文件。*(实际上，容器是在主机上共享资源的轻量级进程。)*

Docker 映像是可以作为容器运行的文件系统的快照。Docker Hub 为流行的应用程序和数据库提供了广泛的图像，包括 T2 MySQL T3 和 T4 Maria db T5。好处:

*   所有开发人员都可以在 macOS、Linux 和 Windows 上使用相同的 Docker 映像
*   MySQL 安装配置和维护是最少的
*   相同的基础映像可以在开发和生产环境中使用
*   开发人员保留了本地开发的好处，可以毫无风险地进行试验。

Docker 超出了本文的范围，但是需要注意的要点是:

*   Docker 是一个客户端-服务器应用程序。服务器负责管理图像和容器，可以通过 REST API 使用命令行界面进行控制。因此，您可以在任何地方运行服务器守护进程，并从另一台机器连接到它。
*   您的 web 应用程序需要的每种技术都应该使用单独的容器。例如，您的应用程序可以使用三个容器:一个支持 PHP 的 Apache web 服务器、一个 MySQL 数据库和一个 Elasticsearch 引擎。
*   默认情况下，容器不保留状态。下次容器重新启动时，保存在文件或数据库中的数据将会丢失。持久性是通过在主机上装载卷来实现的。
*   每个容器都可以在自己的隔离网络中与其他容器通信。必要时，可以将特定端口暴露给主机。
*   Docker 的商业企业版已经推出。本文引用了开源社区版，但是同样的技术也适用。

### 安装 Docker

在 **Linux** 上安装最新版本 Docker 的说明可以在 [Docker 文档](https://docs.docker.com/install/linux/docker-ce/ubuntu/)上找到。你也可以使用官方的存储库，尽管它们可能有旧版本。例如，在 Ubuntu 上:

```
sudo apt-get update
sudo apt-get remove docker docker-engine docker.io
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker 
```

在其他版本的 Linux 上，安装会有所不同，所以请在网上搜索适当的说明。

[Docker CE Desktop for MAC OS Sierra 10.12 及以上](https://hub.docker.com/editions/community/docker-ce-desktop-mac)和[Docker CE Desktop for Windows 10 Professional](https://hub.docker.com/editions/community/docker-ce-desktop-windows)作为可安装包提供。您必须在 [Docker Hub](https://hub.docker.com) 注册并登录下载。

Windows 10 上的 Docker 使用 Hyper-V 虚拟化平台，您可以从控制面板中的**程序和功能**访问的**打开或关闭 Windows 功能**面板来启用该平台。Docker 还可以使用 Linux 2 的 Windows 子系统(wsl 2——目前处于测试阶段)。

为了确保 Docker 可以访问 Windows 文件系统，从 Docker 托盘图标菜单中选择**设置**，导航到**共享驱动器**窗格，并检查允许服务器使用哪些驱动器。

![Docker shared drives on Windows](img/e28bb65343165753baa2f9312b976874.png)

在命令提示符下输入`docker version`,检查 Docker 是否已成功安装。可选地，尝试使用`docker run hello-world`来验证 Docker 可以像预期的那样拉取图像并启动容器。

### 运行 MySQL 容器

为了使 Docker 容器更容易通信，创建一个名为`dbnet`或任何您喜欢的名称*的桥接网络(如果您只想从主机设备访问 MySQL，可以跳过这一步)*:

```
docker network create --driver bridge dbnet 
```

现在在您的系统上创建一个`data`文件夹，其中将存储 MySQL 表——比如`mkdir data`。

最新的 MySQL 8 服务器现在可以通过以下方式启动:

```
docker run -d --rm --name mysql --net dbnet -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysecret -v $PWD/data:/var/lib/mysql mysql:8 
```

使用的参数:

*   `-d`将容器作为后台服务运行。
*   `--rm`停止运行时移除容器。
*   `--name mysql`为容器指定一个名称`mysql`，以便于管理。
*   `-p 3306:3306`将容器端口转发给主机。如果您想在主机上使用端口`3307`，您应该指定`-p 3307:3306`。
*   `-e`定义了一个环境变量，在这个例子中默认的 MySQL root 用户密码被设置为`mysecret`。
*   `-v`挂载一个卷，这样容器中的`/var/lib/mysql` MySQL 数据文件夹将存储在主机上当前文件夹的`data`子文件夹中。

`$PWD`是当前文件夹，但这只在 macOS 和 Linux 上有效。Windows 用户必须使用正斜杠符号指定完整的路径，例如`/c/mysql/data`。

第一次运行这个命令时，MySQL 将需要几分钟的时间来启动，因为 Docker 映像已经下载，MySQL 容器也已经配置好了。假设您没有删除或更改原始图像，后续的重新启动将是即时的。您可以使用以下工具随时检查进度:

```
docker logs mysql 
```

### 使用容器 MySQL 命令行工具

启动后，使用以下命令打开 MySQL 容器上的 bash shell:

```
docker exec -it mysql bash 
```

然后以 root 用户身份连接到 MySQL 服务器:

```
mysql -u root -pmysecret 
```

`-p`后面是上面所示 Docker 的`-e`参数中设置的密码。不加空格！

现在可以使用任何 MySQL 命令——比如`show databases;`、`create database new;`等等。

### 使用 MySQL 客户端

任何 MySQL 客户端应用程序都可以通过主机的端口`3306`连接到服务器。

如果你没有安装 MySQL 客户端， [Adminer](https://www.adminer.org/) 是一个轻量级的 PHP 数据库管理工具，它也可以作为 Docker 容器运行！

```
docker run -d --rm --name adminer --net dbnet -p 8080:8080 adminer 
```

启动后，在浏览器中打开`http://localhost:8080`，输入`mysql`作为服务器名称，`root`作为用户名，`mysecret`作为密码:

![Adminer](img/ef6d53d7942b454722232b794041a8db.png)

现在可以添加、编辑或删除数据库、用户、表和相关设置。

### 停靠码头集装箱

可以用`docker stop`命令后跟一个或多个容器名来停止(并自动移除)容器。例如:

```
docker stop mysql adminer 
```

与虚拟机相比，Docker 映像和容器是轻量级的，但是您可以使用以下方式擦除所有数据:

```
docker system prune -a
docker volume prune 
```

### 使用`docker-compose`管理容器

可以管理任意数量的容器，而无需输入冗长的 Docker 命令。在当前文件夹中创建一个`docker-compose.yml`文件:

```
version: '3.7'

services:

  mysql:
    environment:
      - MYSQL_ROOT_PASSWORD=mysecret
    image: mysql:8
    container_name: mysql
    volumes:
      - ./data:/var/lib/mysql
    networks:
      - dbnet
    ports:
      - "3306:3306"

  adminer:
    image: adminer
    container_name: adminer
    depends_on:
      - mysql
    networks:
      - dbnet
    ports:
      - "8080:8080"

networks:
  dbnet: 
```

然后运行:

```
docker-compose up 
```

MySQL 和 Adminer 容器都使用与上面相同的配置启动。按下`Ctrl` + `C`停止并移除容器。

## 在您的操作系统上运行 MySQL

如果您希望在多个项目中使用单个实例，或者要求服务在引导时运行，那么在本地开发机器上安装 MySQL 可能是实用的。

### 一体式包装

有一些优秀的一体化 macOS、Linux 和 Windows 发行版，它们在单个安装包中包含 Apache、PHP、MySQL、phpAdmin、SSL 证书、框架和其他应用程序。选项包括:

*   [XAMPP](http://www.apachefriends.org/en/xampp.html)
*   wamp server
*   [EasyPHP](https://www.easyphp.org/)
*   [安培数](https://www.ampps.com/)
*   [WPN-XM](https://wpn-xm.org/)
*   [Wnmp](https://github.com/wnmp/wnmp)

大多数都是快速 PHP 和 MySQL 开发设置的理想选择，并且可能提供每个服务的多个版本。然而:

*   不保证更新。许多软件包仍然提供旧版本的 MySQL。
*   使用不同操作系统或调整配置的开发人员可能会在共享代码库中引入兼容性问题。
*   开发和生产环境可能会有很大的不同。

一体化包最适合只有一个程序员的项目，或者那些刚接触 web 开发的人。

### 在 Linux 上安装 MySQL

在各种 Linux 发行版上安装 MySQL 的方法有很多:

*   一些发行版如 Ubuntu Server 默认提供 MySQL。
*   官方文档提供了使用 APT、Yum 和 SLES 包管理器以及 RPM 和 Debian 包进行安装的细节。
*   不同版本的 MySQL 可以从 [snap store](https://snapcraft.io/mysql) 获得。

### 在 macOS 上安装 MySQL

MySQL 可以通过[下载原生包安装程序](https://dev.mysql.com/downloads/mysql/)安装在 macOS 10.13 及以上版本上。dmg 磁盘映像。双击挂载镜像然后双击`.pkg`文件启动[安装向导](https://dev.mysql.com/doc/refman/8.0/en/osx-installation-pkg.html)。

如果您选择不自动启动 MySQL，可以从“系统偏好设置”面板中的图标启动它:

![MySQL in macOS System Preferences](img/77aeb246f7daa47329a5d7d89b7e2660.png)

也可以在 macOS 上安装 MySQL，使用:

*   自制:`brew install mysql`
*   [DBngin](https://dbngin.com/) 管理工具，或
*   [通用二进制文件](https://dev.mysql.com/doc/refman/8.0/en/binary-installation.html)

### 在 Windows 上安装 MySQL

MySQL 可以安装在 64 位版本的 Windows 10 和 Windows 2012 Server R2 及更高版本上。您还需要以下运行时:

*   [。NET 4.5.2](https://www.microsoft.com/en-us/download/details.aspx?id=42643)
*   [Visual c++可再发行版](https://www.microsoft.com/en-us/download/details.aspx?id=52685)

MSI 安装程序提供了服务器和工具，如 MySQL Workbench。(较小的*“web”*MSI 安装程序会在选中时下载软件包。)

双击 MSI 文件启动安装向导，并参考 [MySQL 文档](https://dev.mysql.com/doc/refman/8.0/en/mysql-installer.html)了解具体的配置选项。

默认情况下:

*   程序二进制文件和文档被安装到`%PROGRAMFILES%\MySQL\MySQL Server V.v\`(例如`C:\Program Files\MySQL\MySQL Server 8.0\`)
*   数据库数据存储在`%PROGRAMDATA%\MySQL\MySQL Server V.v\`(如`C:\ProgramData\MySQL\MySQL Server 8.0\`)

还有一个不安装的 [ZIP 存档文件](https://dev.mysql.com/downloads/mysql/)。这可以复制到 USB 驱动器或 Windows 系统上的任何地方。

首先，将 ZIP 文件的内容解压到`C:\mysql`或您选择的文件夹中。

然后创建一个存储数据库数据的文件夹，比如`C:\mysqldata`。使用`C:\mysql`之外的文件夹更安全，并允许您升级应用程序文件。

现在在`C:\mysql`中创建一个`my.ini`文件，指定应用程序和数据文件夹。例如:

```
[mysqld]
# installation path
basedir=C:/mysql
# data directory
datadir=E:/mysqldata 
```

*(前向路径`/`斜杠是必需的，尽管双`\\`反斜杠也可以使用。)*

现在，通过在控制台提示符下输入以下命令，初始化数据文件夹并创建一个默认的`root`用户(没有密码):

```
C:\mysql\bin\mysqld.exe --initialize-insecure --user=mysql 
```

现在可以使用以下命令启动服务器:

```
C:\mysql\bin\mysqld.exe --console 
```

通过在另一个控制台中输入`C:\mysql\bin\mysql.exe -u root`来使用 MySQL 客户端。您现在可以发出 SQL 命令，如`show databases;`，并通过键入`exit`退出。

可以通过以下方式关闭服务器:

```
C:\mysql\bin\mysqladmin.exe -u root shutdown 
```

或者，MySQL 可以安装为自动启动的 Windows 服务:

```
C:\mysql\bin\mysqld.exe --install 
```

该服务将在重启时启动，或者您可以输入`net start mysql`立即启动它。可以使用`net stop mysql`停止或者使用 **Windows 管理工具**中的【T2 服务】面板进行管理:

![Windows MySQL service](img/4696eb62f0018479ab4b010fd851418b.png)

使用以下方法可以完全删除该服务:

```
net stop mysql
C:\mysql\bin\mysqld.exe --remove 
```

## 压倒性的 MySQL 选项

无论您使用什么操作系统，都有多个 MySQL 安装选项。

对于那些学习 MySQL 的人来说，云提供商或一体化包可能是最简单的开始方式。随着您的应用程序开始增长，考虑在您的开发工作流中采用 Docker 或管理本地 MySQL 安装。随着多个开发人员加入项目，Docker 变得越来越实用。最终，随着成功产品的发展，您可以返回到基于云的选项。

## 分享这篇文章