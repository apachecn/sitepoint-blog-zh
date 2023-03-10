# 搬到 Laravel Homestead 的 6 个理由

> 原文：<https://www.sitepoint.com/6-reasons-move-laravel-homestead/>

注:要快速使用 Homestead，请参阅此[快速提示](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。关于它是什么的解释，请阅读下面。

[Laravel Homestead](http://laravel.com/docs/homestead?version=4.2) 简单地说就是:

> 一个官方的，预打包的流浪者“盒子”，它为你提供了一个美妙的开发环境，而不需要你在本地机器上安装 PHP，web 服务器，以及其他任何服务器软件。

换句话说，它自动地做了我们之前在类似[这些](https://www.sitepoint.com/blog/)的文章中手动通过 travel 和 PuPHPet 所做的事情。

那么，它与你的普通 Vaprobash/Vagrant/PuPHPet 设置有什么不同呢？让我们看看。

### 1.它工作了

不像其他流行的简化流浪文件设置的解决方案，Homestead 很少启动失败，如果失败了，它会在几分钟内修复。如果你处理过 GUI 流浪解决方案，你可能已经注意到在第一个`vagrant up`之后就全部启动并运行是多么罕见。它总是像一个过时或过度更新的木偶，过时的 Ubuntu repos，或其他一些神秘的错误，需要大力“foruming”来解构。家园只是…工作。

家园安装在 Ubuntu 14.04 上。PHP 5.5，Nginx(因为我们现在都已经放弃 Apache 了，对吗？)、MySQL 和 Postgres，因此您也可以立即准备好 Heroku(它们的默认设置是 Heroku Postgres)、Node(用于所有静态资源编译需求、后台任务和其他业务不太重要的事情)、Redis、Memcached 和 Beanstalkd(用于所有缓存和队列需求)、Laravel Envoy(用于所有远程服务器任务需求)和 Fabric + Hipchat 扩展，因此您可以通过 Hipchat(也称为 chatops)进行应用程序部署。

### 2.这是奥特威尔批准的

宅地是官方的，就像在，由泰勒·奥特威尔，拉勒韦尔的父亲，意味着它自动被认为是符合某些标准的。虽然 Laravel 社区也不是没有戏剧性的事件(谁在乎一个类叫什么？如果一个革命性的新框架将一个模型称为土豆、门面或火箭，只要土豆/门面/火箭能够工作，就完全没问题)，正是泰勒参与其中的排他性赋予了它简单和高质量的特点，这在当今的 PHP 世界中是非常需要的。我们已经看到太多的开源项目被团队利己主义者、热心的贡献者或无能的万事通毁掉了，我个人认为，泰勒几乎完全参与了 Laravel 的改进，这使它在其他项目中脱颖而出，并真正允许它在质量上与 Phalcon 竞争。

Homestead 是他自己的项目，被称为“官方的本地开发环境”，他将尽一切努力保持它的高质量，并确保它始终有效。如果做得不够，那就是他名誉上的污点。如今，在 PHP 中，没有比(大部分)由一个如此关心质量的人拥有更好的项目长期可靠性保证了。

### 3.设置起来很快

建立家园是小菜一碟。按照文档页面上的说明，您需要做的就是将 homestead box 添加到您的流浪者中(如果您还没有的话)并克隆 repo。

![](img/f5cb9a55ebee322aab2552ece80e6425.png)

还有一个我们不习惯的额外步骤——设置 SSH。这也很容易做到，并且需要在克隆 repo 之后编辑 Homestead.yaml 文件。只需将相关的行指向您的 SSH 密钥，您就可以开始了。对我来说，我必须改变这一点:

```
--- ip:  "192.168.10.10" memory:  2048 cpus:  1 authorize:  /Users/me/.ssh/id_rsa.pub

keys:  -  /Users/me/.ssh/id_rsa

folders:  - map:  /Users/me/Code to:  /home/vagrant/Code sites:  - map: homestead.app
      to:  /home/vagrant/Code/Laravel/public variables:  - key: APP_ENV
      value:  local
```

到

```
--- ip:  "192.168.10.10" memory:  2048 cpus:  1 authorize: C:\Users\Bruno\.ssh\id_rsa.pub

keys:  - C:\Users\Bruno\.ssh\id_rsa

folders:  - map: D:\VM\vagrant_boxes\homestead\Homestead
      to:  /home/vagrant/Code sites:  - map: homestead.app
      to:  /home/vagrant/Code/Laravel/public variables:  - key: APP_ENV
      value:  local
```

在我们运行`vagrant up`之后，一切都将水落石出:

![](img/864b41933064704852ae40a4e6b60981.png)

如您所见，我的过程产生了一些错误，但最终仍然正常工作:

![](img/5dd2344b2c7a556e92afb6bb008a681c.png)

### 4.港口

默认情况下，Homestead 开放了某些重要的端口，这使得从主机维护和管理您的数据库和其他安装在虚拟机上的软件变得轻而易举。例如，要连接到安装了 MySQL Workbench 的 MySQL 数据库(在我的例子中是 Windows)，只需在连接窗口中输入所需的凭证:

![](img/712d6fa27f4e9446932fd5e0ab52844b.png)

默认的 MySQL 和 Postgres 端口只是附加了一个零(即 MySQL 的端口是 33060，而不是 3306)，这允许您连接到本地主机(127.0.0.1:33060)并授予对虚拟机上的数据库的访问权限。

这样做的潜在缺点是，如果不因为冲突而改变端口，你就不能同时运行多个机器。如果数据库连接是开放的就更好了，人们可以像往常一样连接到虚拟机的 IP，但如果需要的话，这很容易修复——只要看看我以前的一些[流浪文件](https://www.sitepoint.com/client-server-dart-app-getting-started/)就知道是什么样子了。

### 5.最佳实践和共同点

由于 Homestead 是官方的，你可以放心，如果你遇到任何问题，你会有一个巨大的社区随时提供任何帮助。你将和其他使用 Homestead 的人有相同的起点，问题会自动变得更容易诊断。

### 6.易于添加站点

由于配置文件的简单性，人们可以在微调 Homestead 时进行调整，添加新站点(vhost)是一件轻而易举的事情——您甚至不必处理 nginx 文件中的单个 vhost 配置。

默认情况下，Homestead.yaml 文件注册了一个名为“homestead.app”的 vhost，该文件充当虚拟机服务器配置的默认站点。你可以在你的浏览器中直接访问虚拟机的 IP，在常规端口 80: [http://192.168.10.10](http://192.168.10.10) 。但是，您可能希望运行其他应用程序或项目，并且可以通过您的主机浏览器按名称访问它们。这就是“站点”块的用武之地。通过像这样定义另一个站点:

```
sites:  - map: homestead.app
      to:  /home/vagrant/Code/Laravel/public  - map: homestead2.app
      to:  /home/vagrant/Code/H2/public 
```

你注册一个新的虚拟主机。然后，如果你将新网站的名称添加到你的`hosts`文件中(在任何平台上)，你就可以通过你的浏览器按名称访问新网站，现在你只需要通过端口 8000 来这样做:[http://homestead 2 . app:8000](http://homestead2.app:8000)。

这个过程允许您向 VM 添加任意数量的 vhosts，根据需要从一个盒子中为多个项目提供服务。

## 缺失功能

在一些缺失的功能中，我想列举如下:

*   Homestead 缺少 global composer 安装，这意味着您必须为每个项目手动获取它
*   前面提到的端口问题——如果端口是开放的就更好了，这样就可以通过特定的端口连接到虚拟机的 IP，而不是本地主机的 IP。这将避免端口冲突，并允许同时运行多个 Homestead 虚拟机
*   没有拉弗尔。Homestead 最好在默认 vhost 中默认包含基本的 Laravel 项目，这样就可以立即开始开发，而不必从头创建一个新的项目
*   本着这种精神，HHVM 的支持将是整齐划一的

## 结论

Laravel Homestead 是迄今为止最好最稳定的 PHP 流浪环境之一。它启动速度极快，包含很少会在`up`时间内中断的依赖项，并且配置了一个现代的、最新的 PHP 环境，可以让人立即开始入侵。

你在用吗？让我们知道。

## 分享这篇文章