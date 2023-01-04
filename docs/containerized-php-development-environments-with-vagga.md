# 使用 Vagga 的容器化 PHP 开发环境

> 原文：<https://www.sitepoint.com/containerized-php-development-environments-with-vagga/>

这偶尔会发生在我们所有人身上。

我们克隆一个项目，然后尝试运行它。然而，有些东西不管用。

![Broken car image](img/864cb0a3f838aec36a4ec4aa27eee641.png)

可能是我们版本的 NGINX 或者 Apache。可能是 npm 做得不对。也许项目需要一个扩展，但是我们没有安装，现在我们必须从源代码中构建扩展，因为在我们的发行版的存储库中不存在依赖关系。不管是什么原因，设置越复杂，失败的概率就越高。

当我第一次知道[流浪者](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)的时候，那就像天堂一样:终于有机会摆脱 Windows 的缺点，同时不必处理 Linux 中存在的可访问性问题。

我很开心。一段时间。然后，将虚拟机作为开发环境的局限性打击了我。很难。

想象一下这个场景:现在是下午 4:30。开发星号系统的开发人员已经离开了公司。支付逻辑中的某些东西(用 PHP 编写，使用已废弃的 [PHPagi](http://PHPagi.sourceforge.net/) 项目)不起作用，你必须尽快修复它。您和一位同事一直在敦促每个人都采用面向服务的架构，因此，为了能够建立一个开发环境，您必须具备以下条件:

*   处理呼叫的星号机器的实例。它必须有 Asterisk 和 PHP 5.5.X 运行。
*   实际处理付款的另一个星号机器的实例。因为您正在工作的银行有一个关闭所有从大楼外部访问其 API 的策略，所以这是上面机器的一个副本，但是在其上运行不同的代码。
*   PHP 后端 API 完成繁重的工作，用 [PhalconPHP](https://www.sitepoint.com/phalconphp-yet-another-php-framework/) 编写。

所以你把一些[木偶](https://puppetlabs.com/)脚本粘在一起准备好你的箱子，你`vagrant up`三台机器，准备进入[区](http://programmers.stackexchange.com/questions/20542/how-do-you-get-into-the-zone-how-long-does-it-take-what-steps-do-you-take-befo)！

如果这是一部电影，场景会放大到您的主机，戏剧性地显示大量数据以光速在系统的不同部分之间传输，填满您的内存和 CPU，然后嘎然而止。然而，由于这不是一部电影，可以说您的开发机器可能能够在 Windows 上处理一两台 8 GB 内存的机器，但是当第三台机器启动时，您的开发环境将会慢到您无法启动 IDE 的程度。

当然，你很聪明，足智多谋，知道这东西是怎么运作的。你可以降低你的`vagrantfile`的内存和 CPU，但这导致 Asterisk 抱怨内存太小，当你用[软电话](https://en.wikipedia.org/wiki/Softphone)(相当于网络世界的浏览器)手动呼叫你的电话系统进行测试时，音频中断并失真。你需要比你已经拥有的多得多的内存，而你没有访问它的权限，现在是晚上 10:30。

那你会怎么样？

没什么好事。那天晚上你没能解决问题，不得不面对一个愤怒的老板，他没有技术背景来理解你在说什么，明天回来工作，告诉你的两个同事在他们自己的机器上运行上面列表中需要的一台机器，你把这些虚拟机串在一起，让代码运行。哦，让我们不要忘记你在构建所有依赖项时遇到的问题，因为它们在任何地方都没有记录。那就太难看了。

但是所有的问题都会带来新的机会:进入 Vagga，这是一种用一个简单的命令就可以设置你的项目和它的依赖项的方法，使用的资源要少得多。

## 什么是 vagaa

Vagga 是一个[容器](https://en.wikipedia.org/wiki/Operating-system-level_virtualization)引擎，就像 [Docker](http://www.Docker.com) 一样，它的创建是为了更容易地构建开发环境。因为它是一个完全用户空间的容器引擎，所以它的加载速度比 vagger 快得多，占用的内存也少得多，并且允许您在几秒钟内完成一些令人惊叹的事情，比如在不同的环境中运行您的应用程序，而无需等待完全虚拟化的机器启动。

Vagga 目前正处于测试阶段，你可能会面临一些问题，但这个概念显示出巨大的前景，这就是为什么我们决定尝试一下。

### 等等！Docker 和 Docker-compose 怎么了？

Docker-compose 是一个通过配置文件配置和创建多个 Docker 容器的工具。我个人对 Docker 和 Docker-compose 没有任何经验。但是，Vagga 的文档中有一页[比较了 Vagga 和 Docker](https://vagga.readthedocs.org/en/latest/compare_to_docker.html) 。有兴趣可以去看看。如果你有使用 Docker 的经验，请在评论中告诉我们它们的区别和相似之处！

## 要求

由于[家园改进](https://github.com/Swader/homestead_improved)是我们在 SitePoint 这里推荐的东西，如果我们可以用 Vagga 复制那个设置，有一个真实世界的方法来评估它的易用性、优点和缺点，那就太好了。因此，我们在本文中想要的是一个 web 服务器(NGINX ),结合 PHP-fpm 来提供一个由三行代码组成的`index.php`文件:

```
<?php
phpinfo();
?> 
```

## 装置

Vagga 非常新，没有时间和人力落后于流浪者和码头工人。您可以在下面概述的安装步骤中看到这一点。我劝你咬紧牙关挺过去；我保证，如果你像我一样习惯用流浪狗，回报会很惊喜。

首先，我们为我们的项目创建一个目录。它可能在任何地方，但是在我们的例子中，我们将其命名为`vagga`。

### Linux 操作系统

> **注意** : PHP 和 composer 支持最近已经被添加到 Vagga 中，所以我们将使用`*-testing`包。当它进入稳定分支时，您可能希望使用下面不带`*-testing`后缀的 URL。

如果你使用的是 Linux，你将从速度和特性上获得 Vagga 的全部好处。您所需要做的就是启用用户名称空间，这取决于您的发行版，默认情况下可能启用也可能不启用。

如果您运行的是 Ubuntu 14.04 (Trusty)或更高版本，安装 Vagga 就像在您的终端中运行以下命令一样简单:

```
curl http://files.zerogw.com/Vagga/Vagga-install-testing.sh | sh 
```

如果您正在使用另一个发行版，我建议您使用 Vagga 手册的[安装页面来解决问题，或者获取更多最新的和更深入的步骤来继续。](http://Vagga.readthedocs.org/en/latest/installation.html)

### Windows 和 Mac OS X

如果你用的是 Windows 或者 Mac，实际上你得装上 Vagga，因为 Vagga 目前在 Linux 下工作。基于我在本文开头所说的真实故事，这可能对你来说是一种讽刺。但是，请记住，出于以下原因，您仍然可以使用 Vagga，即使您必须在虚拟机中使用它:

*   使用不同的配置同时运行不同的软件(数据库、web 服务器、PHP-fpm 实例),而不会占用大量的内存和 CPU
*   无需熟悉编排软件(如 puppet 或 chef ),也无需处理 Docker 等容器守护进程，即可快速配置您的设备
*   记录建立开发环境所需的步骤
*   如果项目依赖关系发生变化(比如向您的`composer.json`中添加一个新的包，或者改变一个已经存在的包的版本)，那么重新构建容器

下面是准备在 Vagga 内部安装 vag ga 的环境的步骤:

*   安装[流浪汉](http://www.Vagrantup.com)和[虚拟工具箱](http://www.virtualbox.org)。
*   通过打开命令提示符并运行`vagrant plugin install Vagrant-Vagga`来安装`vagrant-Vagga`插件。
*   创建一个新文件夹来存放你的项目，并运行`vagrant init ubuntu/wily64`来创建一个`vagrantfile`，使用`ubuntu/wily64`作为基础图像。

安装`vagrant-Vagga`已经给你的流浪者安装添加了一个新的[置备者](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)。如果没有它，你将无法使用 Vagga，因为虚拟文件系统 virtualbox 用来在你的主机和客户机器之间共享文件(如果你不知道我在说什么，请查看[这篇文章](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)以获得更多背景知识)。

下一步是在每次运行`vagrant up`来启动您的虚拟机时，告诉 vagger 运行这个 provisioner。怎么会？简单地打开你的`vagrantfile`，移动到末尾，在`end`的上面加上这两行:

```
 config.Vagga.testing = true
  config.vm.provision :Vagga, run: 'always' 
```

第一行告诉 Vagga 安装 Vagga 的测试版本。这是必需的，因为目前安装 composer 和 PHP 的特性只存在于测试二进制文件中。

第二行相当不言自明；它只是运行 Vagga provisioner，将您的`.Vagga`目录链接到虚拟机内的另一个目录，防止将该文件夹放在`vboxfs`同步文件夹中带来的问题。

唷！您已经完成了虚拟机的设置！您现在可以运行`vagrant up`来调出您将用来运行 Vagga 容器的环境。流浪者将需要一段时间来下载盒子图像，所以给自己拿一杯咖啡，拍拍自己的背，因为已经走了这么远-从这里开始只会变得更容易！

## 准备:Vagga.yaml

就像我们有`composer.json`、`vagrantfile`、`Dockerfile`和类似的文件一样，我们也有`Vagga.yaml`，它包含容器和命令的定义。

## 设置我们的容器

容器是轻量级的沙盒环境，它允许您安装和运行应用程序，而无需接触系统的其他部分。这意味着你可以在一台机器上使用 PHP，当你用 Vagga 创建一个容器时，它们不仅不会冲突，而且会彼此独立存在，允许你拥有一个包的不同版本，具有不同的配置，并排存在于它们自己的隔离容器中。

使用 Vagga，通常最好为每个服务创建一个容器。这就是为什么在我们的例子中，我们将为 Nginx 定义一个容器，为 PHP 定义另一个容器。

在`vagga`目录中创建一个名为`Vagga.yaml`的文件，这是我们项目的根目录，让我们定义第一个草图

```
containers:
  nginx:
    setup:
      - !Ubuntu trusty
      - !Install [software-properties-common]
      - !Sh add-apt-repository ppa:nginx/stable -y && apt-get update
      - !Install [nginx]

  php:
    setup:
      - !Ubuntu trusty
      - !Install [software-properties-common]
      - !Sh add-apt-repository ppa:ondrej/php -y && apt-get update
      - !Install [php7.0, php7.0-fpm]
      - !ComposerConfig
        install_runtime: false
      - !ComposerInstall 
```

在我们告诉 Vagga 构建这些容器之前，让我们看看这个文件中包含了什么。

> **注意**:如果你不熟悉 YAML，可以看看这篇有点过时的文章，名为[在你的 PHP 项目中使用 YAML](https://www.sitepoint.com/using-yaml-in-php-projects/)。从那篇文章中，您只需要知道 YAML 语法是如何映射到 PHP 数据类型的。

一目了然，有一个名为`containers`的一级键，它是一个键-值对的数组。关键是我们的容器名(在这个例子中是`nginx`和`php`)，值是每个容器的配置。

每个容器的配置*必须*定义一个设置密钥。它由[构建步骤](http://Vagga.readthedocs.org/en/latest/build_steps.html)组成，使用这些步骤你可以设置容器并准备好运行你的软件。上面的`Vagga.yaml`示例中使用了三种类型的构建步骤:

*   引导命令:首先是这些构建步骤，将操作系统安装到容器上。在我们的例子中，我们已经使用了`!Ubuntu`构建步骤在我们的容器上安装了一个预定义的版本(14.04，又名`trusty`)。
*   分发命令:Vagga 将这些命令转换成特定于分发版的命令。例如，当上面的`!Install`构建步骤在 Ubuntu 容器中运行时，它被转换为`apt-get install -y <package name>`命令。
*   通用命令:这些是您可以在任何您喜欢的发行版上运行的命令。这些是低级命令，比如用 shell 运行命令的`!Sh`、`!Download`将文件下载到容器中、`!tar`和`!TarInstall`用于提取和安装 tar 归档文件，等等。我们在上面的`Vagga.yaml`例子中使用了`!Sh`命令来添加`nginx/stable`和`ondrej/PHP` PPAs。

因此，您可能知道，为了设置我们的容器，我们:

1.  安装 Ubuntu Trusty
2.  安装`software-properties-common`以访问`add-apt-repository`命令
3.  将`nginx/stable`添加到 NGINX 容器，将`ondrej/php`添加到 PHP 容器，然后运行`apt-get update`从新添加的存储库中获取包列表
4.  安装软件包:`nginx`在 NGINX 容器中，`php7.0`和`php7.0-fpm`在`php`容器中
5.  使用`!ComposerConfig`将 composer 配置为不安装 PHP 包本身(如果您不这样做，它将安装`php`包，在撰写本文时该包的版本是 5.5.9)，然后使用`!ComposerInstall`安装 composer 本身

如果您在此时运行`Vagga _build nginx`或`Vagga _build php`，您将会得到一些错误消息，表明无法写入文件。这是因为出于安全原因，Vagga 不允许容器内的应用程序写入文件系统，除非您明确允许写入某些目录。您可以通过添加卷来实现这一点。

## 添加卷

卷主要是允许容器上的应用程序写入文件系统的一种方式。`Vagga.yaml`所在的目录在容器内自动添加为`/work`(就像 Vagga 在虚拟机内添加包含`vagrantfile`的目录为`/Vagrant`)，但除此之外，Vagga 也添加`/tmp`、`/run`、`/run/shm`为卷。

要了解和阅读有关不同卷类型的更多信息，请查看 Vagga 文档中的[卷页面。](http://Vagga.readthedocs.org/en/latest/volumes.html)

如果您搜索互联网或继续尝试构建您的容器，您将会对运行包所需的目录有所了解。对于我们的例子，NGINX 需要`/var/lib`、`/var/lib/nginx`、`/var/log`和`/var/log/nginx`。类似地，PHP 需要访问`/run/php`和`/var/log`。让我们将它们添加到我们的容器中:

```
containers:
  nginx:
    setup:
      - !Ubuntu trusty
      - !Depends runtime/nginx/default
      - !Install [software-properties-common]
      - !Sh add-apt-repository ppa:nginx/stable -y && apt-get update
      - !Install [nginx]
    volumes:
      /var: !Tmpfs
       mode: 0o766
       subdirs:
        lib:
        lib/nginx:
        log: # default mode is 0o766
        log/nginx: { mode: 0o1777 }

  php:
    setup:
      - !Ubuntu trusty
      - !Install [software-properties-common]
      - !Sh add-apt-repository ppa:ondrej/php -y && apt-get update
      - !Install [php7.0, php7.0-fpm]
      - !ComposerConfig
        install_runtime: false
      - !ComposerInstall
    volumes:
      /run: !Tmpfs
       mode: 0o766
       subdirs:
        php:
      /var/log: !Tmpfs
       mode: 0o766 
```

如您所见，我们添加了一级目录作为卷，并在每个卷的`subdirs`部分定义了该目录中的所有子目录。当在父目录上设置模式时，默认情况下所有子目录也会继承它。

> **注**:如果看不懂每个卷的`mode`参数，可以这样想:第一个值(0 或 1)是[粘滞位](https://en.wikipedia.org/wiki/Sticky_bit)，`o`是八进制之后的值，后面三个数字(如`777`)是权限。

## 配置 NGINX 和 PHP-fpm

如果您曾经手工安装过 NGINX 或 PHP-fpm，那么您可能知道，在 Linux 机器上安装这些应用程序之后，您必须对它们进行配置。因为我们想让这个自动化，我们可以自己编写配置文件，并使用`!Copy`构建步骤在构建时将它放入容器。这一步我通常做的是:

*   通过运行`Vagga _run <container name> bash`连接到容器(即登录到容器)。这只是在容器中运行 bash，并允许您键入命令。
*   将我想要编辑的配置文件复制到`/work`目录，这样我就可以在我的机器上访问它。我通过运行一个类似于`cp nginx.conf /work`的命令来做到这一点。记住，`/work`目录是在你的机器和容器之间共享的。

因此，要配置 NGINX 和`PHP-fpm`,您需要编辑以下文件:

*   /etc/nginx/sites-可用/默认
*   /etc/php/7.0/fpm/php-fpm.conf
*   /etc/PHP/7.0/fpm/pool . d/www . conf

将配置文件放入项目的惯例似乎是放在项目内部的`runtime`目录中。导航到您的项目文件夹(在我们的例子中是`vagga`)并创建一个`runtime`目录。在其中创建一个名为`nginx`的目录来存储你的 NGINX 配置，另一个名为`php`的目录来保存另外两个文件。您可以从您的容器中复制配置文件来感受一下这个过程，或者只是将以下内容复制并粘贴到每个文件中。

### 运行时/nginx/默认

在这个文件中，我们告诉 NGINX 监听端口 8000，将服务器名设置为`example.com`和`www.example.com`，并给出我们的根文件夹和 PHP-fpm 实例的路径(它将监听`127.0.0.1:9000`):

> **注意**:由于 Vagga 运行时没有 root 权限，运行在容器内部的进程无法监听 1024 以下的端口。这是 Linux 操作系统强加的限制。

```
server {
        listen   8000;

        root /work/;
        index index.php index.html index.htm;
        server_name  example.com www.example.com;

        location / {
                try_files $uri $uri/ /index.html;
        }

        error_page 404 /404.html;
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
              root /usr/share/nginx/www;
        }

        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
} 
```

### runtime/php/php-fpm.conf

我们编辑这个文件的原因是，由于 Docker 和 Vagga 监控每台机器内部的进程，它们要求您的应用程序在前台运行，这意味着它们不应该是守护进程。默认情况下，PHP-fpm 作为守护进程运行，我们正在编辑配置，将守护进程配置值设置为`no`。

```
daemonize = no 
```

另一种可能更简洁的方法是创建一个名为`no-daemonized.conf`的文件，将上面的一行放入其中，然后将其放入我们的`runtime/php`目录，并复制它。

### runtime/php/www.conf

我们更改这个文件的原因是让它监听端口 9000，而不是创建一个套接字文件。改变数值`listen`:

```
listen = 9000 
```

## 将配置复制到容器中

构建容器的最后一步是将这些配置文件复制到适当的位置。您可能还记得，在本文前面我说过，如果项目的依赖关系发生变化，Vagga 将重新构建您的容器。因此，由于我们希望对这些配置文件的修改在每次运行命令时生效，我们还需要使用`!Depend`命令将它们添加为依赖项。这是我们`Vagga.yaml`的最终版本:

```
containers:
  nginx:
    setup:
      - !Ubuntu trusty
      - !Depends runtime/nginx/default
      - !Install [software-properties-common]
      - !Sh add-apt-repository ppa:nginx/stable -y && apt-get update
      - !Install [nginx]
      - !Copy 
        source: /work/runtime/nginx/default
        path: /etc/nginx/sites-available/default
    volumes:
      /var: !Tmpfs
       mode: 0o766
       subdirs:
        lib:
        lib/nginx:
        log: # default mode is 0o766
        log/nginx: { mode: 0o1777 }

  php:
    setup:
      - !Ubuntu trusty
      - !Depends runtime/php/php-fpm.conf
      - !Depends runtime/php/www.conf
      - !Install [software-properties-common]
      - !Sh add-apt-repository ppa:ondrej/php -y && apt-get update
      - !Install [php7.0, php7.0-fpm]
      - !ComposerConfig
        install_runtime: false
      - !ComposerInstall
      - !Copy
        source: /work/runtime/php/php-fpm.conf
        path: /etc/php/7.0/fpm/php-fpm.conf
      - !Copy
        source: /work/runtime/php/www.conf
        path: /etc/php/7.0/fpm/pool.d/www.conf
    volumes:
      /run: !Tmpfs
       mode: 0o766
       subdirs:
        php:
      /var/log: !Tmpfs
       mode: 0o766 
```

> **注意**:注意当复制时，源路径和目的路径都在容器内。这与 Docker 不同，Docker 的源地址是在主机内部给出的。

成功！您现在可以运行`Vagga _build nginx`和`Vagga _run php`来构建您的容器了！下载软件包需要一些时间，所以要做好准备工作，并为最后一步做好准备:添加`run`命令将所有东西捆绑在一起。

## 命令

其他开发人员使用命令来挖掘这个新设置的强大功能。有两种类型的命令:

*   标有`!Command`的命令:这些命令正在运行，然后退出。这类命令的例子有`ls`、`php -v`等等。
*   用`!Supervise`标记的命令:这些命令有一些`children`，如果它们监视的任何命令退出，它们将执行一个动作(由它们的`mode`属性定义)。这些命令的例子包括`nginx`和`PHP-fpm`，这正是我们将要使用的。

首先，让我们添加一个名为`php`的命令。它将把我们想要的文件传递给安装在我们的`php`容器中的 PHP 解释器。把这个加到你的`Vagga.yaml`的末尾:

```
commands:
  php: !Command
    description: passes the given parameters to the PHP interpreter
    container: php
    run: [php] 
```

正如您可能猜到的，`description`是在没有参数的情况下运行`vagga`时打印的帮助文本，`container`是运行命令的容器，`run`是要运行的命令。如果我们没有将参数作为数组提供给`run`，它将不会接受参数，并在没有参数的情况下运行`php`。然而，通过以数组的形式提供值，我们允许用户向 php 传递参数，比如一个文件。

为了测试这一点，你可以简单地做`Vagga php -v`。如果使用的是流浪汉，可以`vagrant ssh`进入流浪汉机器，cd 进入`/Vagrant`目录，运行`Vagga php -v`。或者你可以在你的流浪者之家外面的主机上运行`vagrant Vagga php -v`。

最后，我们准备运行我们的 NGINX 和 PHP 堆栈。首先，更改`Vagga.yaml`中的命令部分:

```
commands:
  php: !Command
    description: passes the given parameters to the PHP interpreter
    container: php
    run: [php]
  run: !Supervise
    description: Run the NGINX and PHP stack
    mode: stop-on-failure
    children:
      nginx: !Command
        container: nginx
        run: nginx -g "daemon off;"
      php: !Command
        container: php
        environ:
          DATABASE_URL: postgresql://Vagga:Vagga@127.0.0.1:5433/test
        run: php-fpm7.0 
```

如您所见，我们没有将`run`属性的值作为数组提供，这有效地防止了用户传入任何参数。

最后，让我们运行`Vagga run`(或`vagrant Vagga run`)并观察输出:

```
[19-Mar-2016 15:00:22] NOTICE: fpm is running, pid 3
[19-Mar-2016 15:00:22] NOTICE: ready to handle connections
[19-Mar-2016 15:00:22] NOTICE: systemd monitor interval set to 10000ms 
```

继续在您的`vagga`目录中创建`index.php`文件，如下所示:

```
<?php
phpinfo();
?> 
```

如果您还没有，打开您的`vagrantfile`并取消注释该行，这样您将能够使用静态 IP 访问您的虚拟机:

```
 config.vm.network "private_network", ip: "192.168.33.10" 
```

然后，将`example.com`添加到主机上的`hosts`文件中。在 Unix 系统上，这个文件位于`/etc/hosts`，在 Windows 上，它位于`C:\Windows\System32\drivers\etc\hosts`。下面是将`example.com`域映射到`192.168.33.10`的方法，T5 是虚拟机的 IP 地址:

```
192.168.33.10 example.com 
```

现在只需打开浏览器，浏览到`example.com:8000`或`www.example.com:8000`。你应该看到 PHP 信息页面。

恭喜你！您已经用 Vagga 创建了一个开发环境，现在您可以允许其他用户在非常短的时间内创建一个开发环境！

## 结论

Vagga 非常适合容器化，并且创建了一个关于应用程序开发环境的动态文档。它还有一个好处是允许开发人员快速建立开发环境，而不需要知道过程的本质细节。在我看来，这在开源世界中是一个巨大的优势，因为这使得贡献者在几分钟内就能高效工作。

当然，Vagga 也有它的缺点:

*   不像 Docker 那样跨平台。Docker 本身也不是跨平台的，但是它的贡献者已经创建了一些工具，使得在 Windows 上安装非常容易。
*   安装过程不像它的竞争对手那么简单。您可能需要根据您使用的发行版和版本进行一些配置。
*   Puppet、Ansible、Docker 和其他人相对于 Vagga 的一个优势是，构建开发和生产环境不需要你掌握两种语言——你只需要用一种语法构建环境。有可能部署用 Vagga 构建的容器，但是，这个过程还没有很好的文档记录。
*   与 Docker 一样，Vagga 要求您知道如何安装和配置特定的软件包，而诸如 Puppet 之类的编排软件具有为您配置流行软件的模块。像 [NGINX](https://forge.puppetlabs.com/puppetlabs/NGINX) 、 [PHP](https://forge.puppetlabs.com/mayflower/PHP) 和 [PHP-fpm](https://forge.puppetlabs.com/Slashbunny/PHPfpm) 这样的例子浮现在脑海中。
*   你必须从头开始做每件事。与 Docker 或 vagger 不同，没有基础映像可供构建。

您是否使用其他软件来让您的团队或您的贡献者从零开始快速构建开发环境？你是否认为在 2016 年快节奏的开发环境中，设置开发环境的自动化方式不值得付出努力？请在评论中告诉我们你的经历！

## 分享这篇文章