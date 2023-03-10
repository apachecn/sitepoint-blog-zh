# 为本地 WordPress 开发引入主题果汁

> 原文：<https://www.sitepoint.com/introducing-theme-juice-for-local-wordpress-development/>

像大多数 WordPress 开发者一样，我使用 MAMP 作为本地开发环境。MAMP 对某些人来说工作得很好，但是它并不真正允许你创建可重复的开发环境。您的环境实际上取决于 MAMP 本身(即您的设置)，以及您正在开发的操作系统；这真的不是一件好事。

过了一段时间，MAMP 的工作流并不像它被吹捧的那样，尤其是当你开始需要在团队成员之间共享环境的时候。简单地说，一旦我们被介绍给无处不在的流浪者，我们就离开了 MAMP 的工作流程。我不会谈论那件事的细节，[因为已经有一篇很棒的文章写了发生在别人身上的事](https://www.sitepoint.com/wordpress-meets-vagrant-vvv/)。但是，当你如此习惯于你心爱的 MAMP 的工作流程时，你是如何让自己放松下来成为流浪者的呢？

进入，[主题果汁](http://themejuice.it/)(或`tj`)，现代 WordPress 开发的命令行实用程序。它通过利用 VVV 的[Apache 分支](https://github.com/Varying-Vagrant-Vagrants/VVV/) [VVV-Apache](https://github.com/ericmann/vvv-apache.git) 作为虚拟机，消除了本地开发的痛苦。它允许您生成无限数量的本地开发项目，管理它们的依赖关系，甚至处理它们的部署，*全部从命令行*进行。

## 要求

这个项目需要[流浪者](https://www.vagrantup.com/)和 [VirtualBox](https://www.virtualbox.org/) 能够为本地开发创建虚拟机。在阅读本文之前，请下载并安装这两个软件。

`tj`完全用 Ruby 编写，至少需要 1.9.3 版。，但我推荐 Ruby MRI 的最新版本之一(2.2、2.1、2.0)。如果你想知道你的方法是否得到了支持，那就去看看 Travis CI 的[版本吧，我在那里测试了一系列不同的 Ruby 解释器。](https://travis-ci.org/ezekg/theme-juice-cli)

最后，建议您为 GitHub 设置 [SSH-keys。在内部，`tj`将`git clone`与 SSH URLs、](https://help.github.com/articles/generating-ssh-keys/)[一起使用，所以如果您没有正确设置密钥的话，事情可能会出错](https://github.com/ezekg/theme-juice-cli#help-it-wont-let-me-git-clone-anything)。

*如果你使用的是 Windows，请放心**你是受支持的**！一定要检查一下[文档](https://github.com/ezekg/theme-juice-cli#is-windows-supported)，因为你可能需要做一些额外的事情来确保它能很好地与你的操作系统兼容。*

## 装置

它就像在您的终端中运行下面的命令一样简单:

```
gem install theme-juice
```

*根据您的 Ruby 环境的设置，您可能需要使用`sudo`来运行它。*

## 介绍

社区已经在 VVV 投入了大量的工作，但是如果有一件事不是很好，那就是创建一个新项目的过程。使用 VVV 几周后，我发现自己一遍又一遍地重复着同样的步骤:

1.  在`www/`目录下创建一个新的项目文件夹
2.  在`config/`中为`.dev`域创建一个新的 conf 文件(可以使用 Nginx 或 Apache，取决于您的 VVV 风格)
3.  在`database/init-custom.sql`中创建一个新条目，以建立一个新数据库和一个新用户
4.  在一个`Customfile`中创建一个新条目，将本地项目文件夹同步到 VM 文件夹中(我不喜欢将我的实际项目放在 VM 中，因为这样删除站点实际上需要您删除项目文件夹)
5.  在一个`Customfile`中创建一个新条目，自动将您的新域添加到您的`/etc/hosts`(在我们的例子中，使用 [Landrush](https://github.com/phinze/landrush) 设置一个本地 DNS 服务器)
6.  使用 [xip.io](http://xip.io/) 在`Customfile`中创建一个新条目，允许我从另一个设备(即我的手机和平板电脑)访问我的本地项目
7.  建立一个本地`wp-cli.local.yml`文件，允许我在项目上快速执行 [WP-CLI](http://wp-cli.org/) 命令(实际上不必 SSH 到 VM，转到项目目录，然后从那里执行命令)

我真的会一周做好几次。说说重复！好消息是`tj`位于 VVV 之上，自动化了所有的重复；允许您在几秒钟内快速搭建(和管理)新的开发项目。

## 特征

我们一会儿会深入到本质细节，但首先让我快速展示一下`tj`提供的所有功能:

*   新的本地开发项目生成(通过执行上述步骤)
*   现有项目的管理，包括项目拆除
*   构建工具和依赖管理(稍后将详细介绍)
*   多阶段单命令部署，包括数据库迁移

但是，不是已经有一些工具可以完成大部分工作了吗？当时(即去年年中)，只有 [VVV 站点向导](https://github.com/aliso/vvv-site-wizard)，并没有真正满足上述步骤提出的所有要求；更不用说，我们将 WordPress 安装在一个子目录中，这个脚本不支持我们需要的方式。

与 VVV 网站向导和可变 VVV 等项目不同的一点是，`tj`位于成熟的开源技术之上，如用于部署的 [Capistrano](http://capistranorb.com/) ，以及用于管理插件的[和实际 WordPress 安装的](https://github.com/ezekg/theme-juice-starter/blob/master/composer.json) [Composer](https://getcomposer.org/) 。围绕着 [WPackigist](http://wpackagist.org/) 有一个巨大的生态系统，所以我们相信利用如此巨大的努力是显而易见的。

这意味着`tj`不提供选择你的 WordPress 版本或者你想安装哪个插件的选项，因为所有这些都可以(也应该)通过一个 starter 主题在`tj`之外管理。如果你有一系列你喜欢在每个项目中使用的插件，那太好了！只需为您的初学者主题创建一个存储库，瞧！您可以在`tj`中使用它，方法是[请求将它添加到 CLI 本身](https://github.com/ezekg/theme-juice-cli#can-i-add-my-starter-theme-________)，或者只是通过提示传递它(下面讨论)。

*有一点值得注意:当我谈论“开始主题”时，我指的不是像[下划线](http://underscores.me/)这样的东西；相反，我说的是一个完整的 WordPress-installation-starter，就像 [WP-Skeleton](https://github.com/markjaquith/WordPress-Skeleton) 、[基岩](https://github.com/roots/bedrock)或[我们的 starter 主题](https://github.com/ezekg/theme-juice-starter)。所以，如果你想使用一个自定义的启动主题，确保它是一个完整的 WordPress 安装，而不仅仅是一个主题。在未来的版本中，这可能会更改为类似“kit”的内容；它一直保持这种状态，所以我们可以“直接发货！”也就是说，尽快将这个项目交到社区手中。*

## 创建您的第一个项目

既然我们已经讨论了需要自动化的步骤，让我们看看如何利用`tj`所提供的优势。要开始，我们只需运行以下命令:

```
tj create
```

这个命令有很多可用的标志，但是我们今天不会讨论它们。

![Creating your first project](img/35709a2e4d11b00f9b130cc9de4cf535.png)

这将引导我们通过一系列的提示来设置我们的项目，包括项目的名称和路径(记住，路径可以在系统的任何地方！)、您想要使用的起始主题、数据库信息等。利用所有这些信息，`tj`将创建必要的文件，让您尽快启动并运行。在一系列提示之后，将要求您确认:

![Confirming](img/f1fd84ecc98f149a27321252398a0d91.png)

在您的第一个`create`上，它将继续将 VVV 安装到`~/vagrant`目录中(除非通过[全局标志](http://themejuice.it/#GLOBAL-OPTIONS)指定了另一个路径，或者已经安装了 VVV)，然后它将供应虚拟机，如果您以前从未这样做过，这可能需要几分钟。

![Success](img/20fddb4143db8905964d7c5d5e1e1428.png)

在所有这些都完成之后，`tj`将完成它的工作，在几分钟之内(基本上，这是为 VM 提供新的更改所需的时间)，您将拥有一个全新的开发项目。我提到过你可以从网络上的任何设备自动访问这个新项目吗？

## 设置现有项目

从事现有项目？为了方便起见，您可以运行下面的命令将现有项目设置到 VM 中:

```
tj setup
```

提示将与`create`非常相似(在内部，它实际上是相同的命令)，省略了一些提示，比如询问开始主题。像`create`一样，这个命令也有[大量可用的标志](http://themejuice.it/setup#GLOBAL-OPTIONS)。

## 移除项目

我想确保用`tj`可以自由地从 VM 中删除一个项目，而不用真的删除我所有的文件。为什么？因为我不需要在我的虚拟机中运行 20 个项目，而我只积极地处理其中的 2-3 个。这允许您轻松地创建、删除和重新创建项目。

既然我已经提到了`tj`如何处理删除，要从 VM 中删除一个项目，只需运行下面的命令并遵循提示:

```
tj delete
```

默认情况下，当删除一个项目时，您的数据库是*而不是*删除的(以便以后更容易恢复)，但是万一您想删除它，您可以使用`--drop-db`标志。[查阅文档，查看标志的完整列表。](http://themejuice.it/delete#GLOBAL-OPTIONS)

## 利用`Juicefile`

`Juicefile`是一个 YAML 配置文件，可以用来存储常用的构建脚本和部署配置。每个命令列表都可以映射到单个项目的构建工具，从而允许在使用不同工具的多个项目中使用精简的命令集。为了更好地理解这一点，看看[我们的首发主题的`Juicefile`](https://github.com/ezekg/theme-juice-starter/blob/master/Juicefile#L1-L37)；它包含所有构建工具和依赖项管理器的命令映射:

```
commands:
  install:                         # Run application install scripts
    - composer install
    - bundle install
    - npm install
    - bower install
    - grunt build
  watch:                           # Manage build tools
    - grunt %args%
  assets:                          # Manage front-end dependencies
    - bower %args%
  vendor:                          # Manage back-end dependencies
    - composer %args%
  wp:                              # Manage WP installation
    - wp ssh --host=vagrant %args%
  backup:                          # Create a backup of the local database
    - mkdir -p backup
    - wp ssh --host=vagrant db export backup/$(date +'%Y-%m-%d-%H-%M-%S').local.sql
```

通过查看上面的配置可以看出，我们能够使用参数编写一些小的脚本。这并不算大，但是可以让你更好地利用你的别名。例如，通过运行`tj install`,我们能够以正确的顺序快速运行整个构建过程；或者另一个例子，我们可以运行`tj vendor update`来更新我们所有的 [Composer](https://getcomposer.org/) 依赖项。

这个特性显然并不适合所有人，尤其是当你不怎么使用依赖管理器或者构建工具的时候。也就是说，它对我们来说工作得很好，特别是因为我们的构建堆栈充满了像 Haml、Sass 和 CoffeeScript 这样的预编译语言。

有兴趣了解更多关于如何设置和使用`Juicefile`的信息吗？查看[文档](http://themejuice.it/)中所有可用的选项。

## 部署项目

没有什么比 FTP 部署更让我讨厌的了。通过利用流行的工具 [Capistrano](https://github.com/capistrano/capistrano) ，加上令人敬畏的 [rsync 扩展](https://github.com/Bladrak/capistrano-rsync) , `tj`能够为您的所有项目创建快速可靠的单命令部署，而不需要在您的远程服务器上安装所有的构建工具。设置部署阶段就像这样简单:

```
stages:
  production:                      # Stage name
    server: 192.168.50.4           # Server IP or domain
    path: /var/www/production      # Path to the web root
    user: deploy                   # User to deploy as
    url: example.com               # URL for stage
    uploads: app/uploads           # Path to uploads directory from project root
    tmp: tmp                       # Path to temp directory
    shared:                        # Files to share between deployments
      - .env.production
    ignore:                        # Files to ignore on deployments
      - robots.txt
    roles:                         # Stage roles (for Capistrano)
      - :web
      - :app
      - :db
```

一旦您在项目的`Juicefile` ( [查看我们的配置以获得灵感](https://github.com/ezekg/theme-juice-starter/blob/master/Juicefile#L39-L152))中设置了您的部署配置，您只需运行:

```
tj deploy <stage>
```

你可以走了！`tj`也可以通过`tj remote <stage> db:pull`和`tj remote <stage> db:push`用一个命令处理数据库迁移。

以几乎相同的方式，您可以在阶段之间迁移`uploads/`文件夹和`.env`文件。想开始第一次部署吗？点击[这里](http://themejuice.it/deploy#SETUP)，按照快速安装指南轻松安装并运行。

## 结论

我希望你喜欢这篇文章。我们研究了`tj`如何帮助您摆脱过时的 MAMP 工作流程，同时让您能够利用它的许多功能。有兴趣了解更多信息吗？让我们知道！如果有需求，我很乐意为创建一个新项目，或者建立和部署你的第一个项目做一个截屏。

如果你对我们今天讨论的内容有任何问题，请在下面的评论中告诉我，或者在 [Twitter](https://twitter.com/ezekkkg) 上联系我，我会尽我所能帮助你。

## 分享这篇文章