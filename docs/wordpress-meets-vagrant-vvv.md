# 当 WordPress 遇到流浪者:VVV

> 原文：<https://www.sitepoint.com/wordpress-meets-vagrant-vvv/>

流浪者改变了我的工作方式。在这篇文章中，我讨论了一个创建和管理虚拟环境的工具，如何帮助我更有效地工作。然后我将介绍 Varying vagger(VVV ),这是一个为 WordPress 开发者设计的进化中的 vagger 配置。

## 你还在用 WAMP/MAMP 吗？

如果你正在阅读这篇文章，你很有可能在过去使用过本地开发。我们都经历过这个。WAMP/WampServer，LAMP 或者 MAMP，我们都有过和他们一起开发的噩梦。大部分时间我使用 WAMP (Windows)进行开发，后来我将开发环境改为 Linux。这是我做过的最好的事情。

如果你曾经在团队中工作过，你可能会遇到有人说“它在我的机器上工作”的情况。或者，当您更换计算机或购买新的笔记本电脑时，您可能已经失去了开发环境。

从事不同的项目，通常需要不同的 PHP 版本和不同的扩展。有时与扩展有冲突。东西可能会坏掉。更别说操作系统的差异了，那是另一个层次的噩梦。

但我最讨厌的是，扰乱我的主要系统。我不想我的系统变脏。我不想安装一些东西，然后忘记它在那里。项目完成了，但是(通常)扩展和配置停留在那里，然后你必须重新配置一切。

但是别担心，我想那些日子已经过去了。我已经尝试了一段时间，它永久地改变了我的工作方式。

## 向流浪者问好

那么为什么要用游民呢？[vagger](http://vagrantup.com)是一个让配置虚拟系统变得更加容易的工具。您还可以在协作时使用单一配置，以确保您的所有团队拥有相同的开发环境。这样你就可以花更多的时间来构建东西，而不是修复系统。毕竟我们是开发人员，不是系统管理员。

正如我们所知，每个项目都有自己的库、依赖项和配置。有了 page，我们可以为每个项目建立一个专用的环境，并共享这个配置，这样当我们在一个团队中开发时，我们都在同一个页面上。

流浪汉是成熟的。您可以自由地从头开始创建配置或使用预定义的配置。有很多工具和脚本可以帮助你创建和管理你的漫游配置，其中之一就是 [PuPHPet](https://puphpet.com/) 。使用 PuPHPet，您可以为本地系统、数字海洋、AWS 和 Rackspace 配置环境。我不是系统管理员，但是我已经能够非常成功地为这些环境创建配置。

![PuPHPet](img/21fc7fad8c7bc361272b00198042ea08.png)

也有很多专门针对 WordPress 的配置。最广为人知的是[百变游民](https://github.com/Varying-Vagrant-Vagrants/VVV)或简称( **VVV** )。

## 什么是 VVV？

VVV 对于 WordPress 开发者来说是一个流浪的配置。该环境非常灵活，易于编辑，侧重于高流量的生产环境。通过使用 VVV，您可以立即在自己的系统上使用生产就绪的配置。

### VVV 带来了什么？

以下是你安装 VVV 时得到的所有好处:

*   Ubuntu 14.04
*   WordPress 开发
*   WordPress 稳定
*   WordPress 主干
*   WP-CLI
*   nginx 1.6.x 版
*   mysql 5.5.x
*   php-fpm 5.5.x
*   memcached
*   PHP 内存缓存扩展 3.0.8
*   PHP xdebug 扩展 2.2.5
*   PHP imagick 扩展 3.1.2
*   PHPUnit 4.0.x
*   2 点 04 分
*   去吧，1.9.x
*   subversion 1.8.x
*   ngrep
*   dos2unix
*   设计者
*   phpMemcachedAdmin
*   phpMyAdmin 4.1.14(多语言)
*   Opcache 状态
*   Webgrind
*   NodeJs 当前稳定版本
*   grunt-cli 当前稳定版本

您可以在一个 VVV 配置上使用许多不同的站点。这是开发主题和插件的完美环境，但这并不是全部，它使得对 WordPress core 的贡献更加容易。它还预装了 Composer 和 Grunt，帮助您更快、更智能地工作。

VVV 帮助你使用最佳实践，以正确的方式开发 WordPress。

## 安装 VVV

要安装 VVV，您需要在您的计算机上安装 VirtualBox 和 vagger:

*   [虚拟盒](https://www.virtualbox.org/wiki/Downloads)
*   [流浪者](http://www.vagrantup.com/downloads.html)

安装后，你可以从你的终端使用它。我这里用的是 Debian，在 Windows 和 Mac 上应该是一样的。

您现在可以从 GitHub 库获取 VVV:

```
git clone https://github.com/Varying-Vagrant-Vagrants/VVV.git wordpress-vvv 
```

接下来，导航到 **wordpress-vvv** 文件夹并执行`vagrant up`:

```
cd wordpress-vvv
vagrant up 
```

该命令将读取配置并为您构建整个系统。第一次它会从[流浪云](http://vagrantcloud.com)下载“Ubuntu 14.04 可信 64 位”镜像。下次您为依赖于该图像的另一个项目执行`vagrant up`时，它将从缓存中加载它。您可以通过执行以下命令来检查缓存的图像:

```
vagrant box list 
```

如果安装过程变得有点慢，不要担心，给它时间。

![Installing VVV on Debian](img/983755d9350cf18404822b5dbb3e0d24.png)

如上所述，VVV 安装了许多组件。 [git](http://git-scm.com/) ， [composer](https://getcomposer.org/) ， [wp-cli](http://wp-cli.org/) ， [grunt-cli](https://github.com/gruntjs/grunt-cli) ， [nodeJs](http://nodejs.org/) 等一大堆工具。但是引起我注意的是 VVV 包含了许多调试和分析工具。默认安装 [Webgrind](https://github.com/jokkedk/webgrind) 、 [Opcache Status](https://github.com/rlerdorf/opcache-status) 和 xdebug。VVV 也默认使用 nginx。

成功安装后，在 web 浏览器中导航到默认 IP 地址`192.168.50.4`。你会看到一个简单的界面，让我们可以访问各种工具和不同的 WordPress 安装。

![VVV Dashboard](img/16723daec91ee41de0a5f17af5d287f0.png)

你现在可以去任何装置和实验。你可以在 www 目录中找到所有这些文件。www 内有很多文件夹，但只关注 **wordpress-default** 和 **wordpress-develop** 。通常，你会想要使用**文件夹。如果你觉得有点古怪或者喜欢冒险，你可能会有兴趣尝试一下最新的开发版本。**

如果你想创建一个新的主题或者插件，你将会用到这个**安装。要登录管理仪表板，默认用户名是`admin`，密码是`password`。前往 [VVV 文档](https://github.com/Varying-Vagrant-Vagrants/VVV)获取关于每个安装的默认 MySQL 账户和数据库名称的更多信息。最后， **wordpress-develop** 安装是你想要贡献给 wordpress 核心的文件夹。**

## 额外插件

我推荐安装一个流浪插件来帮助管理你的主机更容易。这个插件叫做`vagrant-hostsupdater`，当你执行`vagrant up`、`reload`和`resume`时，它会更新你的主机文件。

该插件读取虚拟机的 **IP** 地址和**主机名**的配置，并相应地更新主机文件。要安装此插件，请执行:

```
vagrant plugin install vagrant-hostsupdater 
```

## 配置

当 avanger 开始安装时，它首先查看`Vagrantfile`。这个文件包含所有的配置。我不打算涵盖每一行，只是一些我认为重要的价值观。

第一个值是虚拟机的 RAM。默认情况下，它是 512 MB。如果需要更多，只需将该值更改为更高的值:

```
v.customize ["modifyvm", :id, "--memory", 512] 
```

我发现第二个重要的价值是 IP 配置。这是虚拟机默认使用的 IP 地址。如果您遇到任何网络问题，您可以在这里更改 IP 地址的默认值:

```
config.vm.network :private_network, ip: "192.168.50.4" 
```

最后一个值是 config 文件夹。该配置映射 2 个文件夹，其中**配置**文件夹在主机上， **/srv/config** 在虚拟机上:

```
config.vm.synced_folder "config/", "/srv/config" 
```

在 config 文件夹中，你可以找到 PHP，WordPress，MySQL，nginx 等等的配置。更新流浪者文件后，确保使用`vagrant provision`进行设置。这样，所有的更改都将被应用。

## 进一步阅读

如果你有兴趣了解更多关于流浪者和 VVV 的信息，这里有几个链接可供进一步阅读:

*   [VVV 官方 GitHub 页面](https://github.com/Varying-Vagrant-Vagrants/VVV)
*   [在你的工作流程中给流浪者一个位置](https://www.sitepoint.com/give-vagrant-a-place-in-your-workflow/)
*   [使用 PuPHPet](https://www.sitepoint.com/build-virtual-machines-easily-puphpet/) 轻松构建虚拟机

## 结论

如你所见，Varying vagger 专注于为主题和插件开发者，以及 WordPress 核心贡献者提供完美的开发环境。VVV 提供的工具是强大的，适合建设高性能的网站。

对于初学者来说，VVV 可能有点难懂，但是它可以节省 WordPress 开发者的时间，非常值得学习。

你觉得 VVV 怎么样？你有使用它的经验吗？有没有推荐另一个流浪汉配置？请在下面的评论中告诉我们。

## 分享这篇文章