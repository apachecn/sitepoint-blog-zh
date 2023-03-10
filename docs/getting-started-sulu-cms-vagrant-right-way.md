# 在《正确的流浪方式》上开始使用 Sulu CMS

> 原文：<https://www.sitepoint.com/getting-started-sulu-cms-vagrant-right-way/>

在本教程中，我们将学习如何以正确的方式开始使用[Sulu CMS](http://sulu.io)*——也就是说，我们将使用 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 部署一个 Sulu“Hello World”实例，并注意性能问题和配置值。我们还将涵盖一些常见的陷阱，都是为了给未来的 Sulu 教程打下一个良好的基础。建议你按照这篇文章中的说明去做，并对你可能遇到的任何问题发表评论。*

 *非常感谢丹尼尔·罗特和 T2 帮助我完成了这个过程！

*注意，强烈建议在出发前熟悉[家园改善](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。如果你还没有达到那个水平，[你应该买一本关于 PHP 环境基础知识的好书](http://bit.ly/phpenv-sp)。*

* * *

### [Sidenote]输入您的项目名称

本教程是动态的，因为它将用您在本段下的字段中定义的项目名称替换下面文本中的所有占位符。这样，命令变得非常易于复制/粘贴。

<label>项目名称:<input type="text" name="project" id="project_id_input" placeholder="My Project"></label> 
<input onclick="updateProject(event)" type="submit" class="button" name="Save" value="Save">

生成的 slug: my_sulu_project

* * *

## OS X 流浪文件夹共享问题

当在 OS X 主机上使用 NFS 文件夹共享模式时，[流浪者-绑定文件](https://github.com/gael-ian/vagrant-bindfs)插件将是必要的。用`vagrant install plugin vagrant-bindfs`把它安装在你的流浪装置旁边。如果 OS X 是你的主要操作系统，这是一次性的事情，它将防止以后许多许多令人头痛的事情。

其余的都是自动的，已经在 [Homestead 改进的](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)实例中配置好了，你不需要做任何其他的事情。

## 流浪起来

当然，我们首先要做的是克隆 HI repo。

```
git clone https://github.com/swader/homestead_improved my_sulu_project
cd my_sulu_project 
```

接下来，让我们配置共享文件夹:

```
bin/folderfix.sh 
```

这使得当前工作目录与虚拟机中的`/Code`目录共享。这样，在此文件夹中所做的更改将在虚拟机中得到反映，反之亦然。

像任何 Symfony 应用一样，Sulu 需要为 Nginx 定制一个[虚拟主机配置](http://docs.sulu.io/en/latest/cookbook/web-server/index.html)。我们在 Homestead Improved 中将它变成了一个“项目类型”,从而使事情变得更简单，因此您只需对`Homestead.yaml`做如下修改:

*   添加`nfs`文件夹共享类型(在 OS X 和 Windows 10 上)
*   添加`sulu`项目类型，并将其文档根子文件夹更改为`web`

相关部分最终应该是这样的:

```
...

folders:
    - map: /Users/swader/vagrant_boxes/homestead/my_sulu_project
      to: /home/vagrant/Code
      type: nfs

sites:
    - map: my_sulu_project.app
      to: /home/vagrant/Code/my_sulu_project/web
      type: sulu 
```

最后，让我们启动虚拟机。

```
vagrant up; vagrant ssh 
```

> Protip:为将来使用而设置的有用别名:
> 
> ```
> alias vh='vagrant halt; cd ..'
> alias vush='vagrant up; vagrant ssh' 
> ```

## 设置苏禄

### 创建项目

让我们安装苏鲁的标准版(`minimal`版现在实际上是“标准版”，而旧的“标准版”已被弃用——他们正在为此重新命名)。

```
cd Code
composer create-project sulu/sulu-minimal my_sulu_project 
```

请注意，[文档](http://docs.sulu.io/en/latest/book/getting-started.html)目前建议在[作曲](https://www.sitepoint.com/re-introducing-composer/)命令的末尾添加一个`-n`标志，意思是“没有交互式问题”。我喜欢安装者问我应该配置什么，所以我省略了。

由于 Symfony 有很多依赖项，安装需要一段时间(参见下面陷阱和常见问题部分的 Polyfills 小节)。

下载完软件包后，安装程序会询问一些关于数据库、电子邮件和其他用户可填写数据的问题。您应该在那里设置的唯一值将是与数据库相关的值，如果您熟记的话，可能还有秘密令牌或邮件程序设置:

```
Some parameters are missing. Please provide them.
database_driver (pdo_mysql):
database_host (127.0.0.1):
database_port (null):
database_name (sulu): homestead
database_user (root): homestead
database_password (null): secret
database_version (5.5):
mailer_transport (smtp):
mailer_host (127.0.0.1):
mailer_user (null):
mailer_password (null):

... 
```

您总是可以在事后通过编辑`app/config/parameters.yml`文件来手动修改这些。

### 网络空间

是时候建立一个网络空间了。一个网络空间就像你的 Sulu 设备中的一个网站。例如，把 SitePoint 的免费、面向公众的一面想象成一个网络空间，而把[高级的一面](https://www.sitepoint.com/premium/)想象成另一个。

有一个`app/Resources/webspaces/example.com.xml`文件应该被重命名以匹配您的项目:`app/Resources/webspaces/my_sulu_project.xml`。

在该文件中，需要更新两个值(`name`和`key`)来匹配项目:

```
<?xml version="1.0" encoding="utf-8"?>
<webspace xmlns="http://schemas.sulu.io/webspace/webspace"
          xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://schemas.sulu.io/webspace/webspace http://schemas.sulu.io/webspace/webspace-1.1.xsd">

    <name>{{My Project}}</name>
    <key>my_sulu_project</key>

    <!-- ... -->
</webspace> 
```

文件名和键值不一定要匹配，但是如果匹配的话通常会更清楚。

谨记医生的话:

> **稍后更改 webspace 的`<key>`会导致复杂性。我们建议在下一步构建数据库之前决定使用什么密钥。**

### 创建数据库

如果还没有，您应该创建数据库。 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 附带了一个默认的`homestead`数据库，用户`homestead`可以用密码`secret`访问，所以如果你正在使用它，你可能不需要做任何事情。

### 建设

作为最后的准备步骤，我们需要运行(可能很难记住)命令:

```
php bin/adminconsole sulu:build dev 
```

这将提示我们确认以下内容:

```
Options:

  - nodeps: false
  - destroy: false
  - help: false
  - quiet: false
  - verbose: false
  - version: false
  - ansi: false
  - no-ansi: false
  - no-interaction: false
  - shell: false
  - process-isolation: false
  - env: 'dev'
  - no-debug: false 
```

在确认之前，让我们快速解释一下可以传递到构建命令中的每一个标志。事实上，这些是 Symfony 控制台组件的标志——为什么它们像这样出现在这里还不清楚。

*关于术语的说明:a `build target`是一个应用程序的“配方”。为使应用程序到达目标而执行的一组指令。对于`dev`(这里是构建目标，而不是环境)，这包括构建资产、发布路线、创建用户等等。另一个目标，`prod`，做完全相同的事情，但是没有生成默认用户(一个安全风险，因此`prod`用于生产)。要查看哪些目标可用，只需运行普通的`bin/adminconsole sulu:build`。*

1.  `nodeps`:构建时，默认情况下也会构建目标的依赖关系。如果您希望防止这种情况，可以使用`--nodeps`标志。依赖项可以是另一个目标、数据库中的特定设备等等。
2.  `destroy`:销毁应用程序中的现有数据。有用的全面重启，工厂重置排序。
3.  `help`:如果像这样添加到命令中:`bin/adminconsole sulu:build dev --help`，解释构建命令做什么。
4.  `quiet`:构建过程不产生任何输出。如果此标志存在，则下一个标志`verbose`无效。
5.  `verbose`:流程输出多少信息。可以有 1 到 3 的值，像这样:`--verbose=3`。
6.  `version`:显示应用程序的版本。如果使用，取消命令的`sulu:build dev`部分。
7.  `ansi`:如果设置为 true，*用 [ANSI](https://en.wikipedia.org/wiki/ANSI_escape_code) 装饰*(颜色和格式)输出消息。
8.  `no-ansi`:禁用 ANSI 装饰，在类似[这些](https://github.com/symfony/symfony/issues/17499)的情况下很有用
9.  `no-interaction`:将尝试自动确认所有选择的默认值/假定值，而不是要求用户输入。
10.  `shell`:启动 shell 模式，该模式使控制台具有交互性，例如，在中，您可以像登录 MySQL 时一样使用它。然后，您可以执行所有的控制台命令，而不用在它们前面加上`bin/adminconsole`。如果使用的话，取消所有其他命令，shell 优先，`sulu:build dev`必须从 shell 内部重新发出。
11.  `process-isolation`:启动将要作为单独进程执行的命令
12.  `env`:当前环境的名称(默认为`dev`)。随`--env=prod`而变。
13.  `no-debug`:禁用调试模式。换句话说，它禁止收集开发调试数据，如性能瓶颈、日志等。，如果你想要`prod`的性能，但不想从`dev`切换环境，这很有用。

确认命令。

### 登录

要检查应用程序的前端，请在浏览器中访问`my_sulu_project.app/`。以管理员身份登录，访问`my_sulu_project.app/admin/`，用`admin` / `admin`登录。

![Front end working](img/92710381116bd76fa110eebfa5c0759c.png)

没什么好看的，但是嘿，它真的有用！

![The back end works](img/2a626f20882b41066c56e685a506c8ea.png)

后端漂亮多了。在这个界面中随意玩吧——在下一篇文章中，我们将开始用苏禄语开发东西！

## 陷阱和常见问题

在这之后，有几个问题可能会在你的脑海里打转，就像我的一样，所以让我们开始吧。当您发布其他人的帖子时，我们将确保使用 Sulu 团队自己的答案更新此列表。

### 苏鲁适合做新闻网站吗？

默认情况下没有，考虑到它是一个“企业 CMS ”,这确实很奇怪。默认的内容设置是一个*树*结构，其中每个页面都可以有子页等等。这对于文件的平面列表来说不太好。然而，真正有用的是[SuluArticleBundle](https://github.com/sulu/SuluArticleBundle)——一个正在开发的捆绑包，非常适合定期发布文章。

在 Sulu 上发布高发布频率的网站时，请记住这一点。

### Polyfills？

![Different polyfills get installed](img/81427fbb285b8950f1563309485368b1.png)

您可能已经注意到，在安装过程中会安装各种过去 PHP 版本的不同 polyfills。这减慢了过程，并且膨胀了项目。这是因为他们是 Symfony/symfony 的[属地，而 Symfony/Symfony](https://github.com/symfony/symfony/blob/3.3/composer.json)是苏鲁的属地。Symfony 包含了这些，因为 PHP 的最低要求是版本 5.5.9(目前)，并且这些 polyfills 需要到位以支持只有较新版本的 PHP 才具有的功能。

关于在安装过程中去除这些问题的讨论正在进行中。

### 苏鲁会跟随 Symfony 进入 Symfony 4 和 Flex 吗？

是的，Sulu 团队正在关注 Flex 项目，并关注 Symfony 4 的进展。目前还不清楚 Flex 和 Symfony 的新设置会对 Sulu 产生什么影响，以及需要多长时间才能让它们同步——这都取决于新版本带来的变化的类型和幅度，但即使是现在，你也可以测试 Sulu 1.6.0。RC1 使用最新的 Symfony 3.3。你需要做的只是稍微修改一下`create-project`命令:

```
composer create-project sulu/sulu-minimal=1.6.0-RC1 my_sulu_project 
```

## 结论

Sulu 作为一个平台已经成熟，并且变得越来越 DX 友好。我的希望是，通过这样的教程，我将使这些概念更接近你，并帮助你理解这种基于 Symfony 的 CMS 所拥有的力量，并且它作为你的许多项目的起点是有意义的，无论大小。

如果这篇文章的任何内容没有得到适当的解释，或者你在设置方面遇到了任何困难，请不要犹豫，在下面留下评论，告诉我们这些问题。

## 分享这篇文章*