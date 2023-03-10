# 在云中开发:简介

> 原文：<https://www.sitepoint.com/developing-in-the-cloud-an-introduction/>

![people developing in the cloud](img/fea6eef6335bbd037b7ff0e2303b6d9e.png)

每个 web 开发人员都需要建立一个*开发环境*——一套工作工具，例如

*   代码编辑器
*   版本控制系统(如 Git 或 SVN)
*   构建工具和/或包管理器
*   DevOps 和供应工具(码头工人、木偶、Ansible、流浪者)

…等等。良好的设置对于最大限度地提高生产率至关重要。

开发堆栈因开发人员而异，并且在很大程度上取决于开发人员的工作领域。然而，有一些非常典型的设置。SitePoint 最近发布了用于 Linux、T2、Windows 和 T4 Mac 的本地开发环境的例子。

在本文中，我将介绍第四种开发环境——完全基于云的环境。这通常会通过第三方服务来实现，而且有相当多的服务可供选择——比如 [Cloud9](https://c9.io/) 、 [Codenvy](https://codenvy.com/) 和 [Nitrous](https://www.nitrous.io/) 。

## 在云中开发的优势

当使用云开发环境时，您实际上是一个**开发平台即服务** (dPaaS)的客户——它提供的不仅仅是一个在线代码编辑器。

**更快的设置**
首先，在云中开发减少了设置时间。通常你可以使用你选择的框架，或者使用样板代码，只要点击一些按钮就可以了。

**从任何地方访问**
你可以从任何一台电脑上编写代码，只要你有互联网连接——无论你使用的是最新的 iMac、旧的上网本，甚至是平板电脑。

**远程配对**
我喜欢这些环境的一个特点是**远程配对编程**。云 ide 允许多个开发人员同时访问源代码。可以实时看到对方写代码，测试，聊天。

**多重安装**
我最近在使用 [Scala](http://scala-lang.org/) 应用时遇到了一个问题。依赖项就是不能安装。原来是因为我装了 Java 8，而有些依赖项需要 Java 7。我不想降级，因为它可能会影响其他应用程序，所以我决定继续使用一个流浪配置。好吧，如果你在云中，你不必这样做。您可以为您开发的每个应用程序要求一个容器，容器将会是您想要的样子(在这种情况下是 Java 7)。

## 使用云 9

在本文的其余部分，我将通过几个例子向您展示您可以使用 [Cloud9](https://c9.io/) 做些什么，这是开发人员可以使用的众多 dPaaS 服务之一。

Cloud9 提供 [5 个计划](https://c9.io/web/site/pricing)，其中一个是免费的。每个计划都使用运行在 Ubuntu Linux 上的 Docker 容器，提供完整的终端访问、多个合作者访问和无限的开源项目。

### 合作

[![Collaboration](img/e4869c34bc8ab0fc4366fffd910af1db.png)](https://www.sitepoint.com/wp-content/uploads/2015/09/1443508202c9_collaboration-1024x188.png)

多个开发人员可以在同一个源代码上工作，这真的很方便。每个协作者都有一个活动光标，他们可以同时编写代码。

在上面的截图中，你可以看到两个用户，我和`jamesbond006`，同时访问同一个控制器代码。请注意行号左侧用不同颜色标记的行。每种颜色标识一个用户。第 8 行的`@message`用对应于`jamesbond006`的颜色加了下划线——意思是他是写代码的人。这个功能非常有用，随时胜过 TeamViewer！你可以用它来帮助朋友，从朋友那里获得帮助，甚至远程测试或教授一些学生/求职者。

### 多种浏览器

![Multiple Browsers](img/70d06aa13237af29412c3eff289cb240.png)

如果你是一个前端 web 开发人员或设计人员，你有时会不得不切换浏览器来检查你所写的是否是你期望看到的。你可能会安装 Chrome、Firefox、Opera，还可能安装 IE 和/或 Edge。

Cloud9 让你不用安装一大堆网络浏览器。您可以在主流浏览器上远程测试您的布局，包括桌面和移动浏览器。你也可以在 iPad、iPhone、Android 和 Kindle 上测试你的应用。

### 使用语言

像其他服务一样，Cloud9 支持广泛的编程语言和框架，包括 Ruby on Rails、各种 PHP 框架、Node.js 以及 MySQL、PostgreSQL、Cassandra、MongoDB 等数据库。

![Cloud9 templates](img/10185b43f84ac24f7618b663f71d9309.png)

即使你想用上面没有列出的语言开发，比如 Scala，你也可以选择**自定义**模板，通过终端安装你选择的语言。

#### 轨道

你可以通过选择 **Rails** 模板，或者选择**自定义**模板并在终端执行`rails new`来创建一个 Rails app。

您可以通过在终端上执行以下命令来启动应用程序:

```
rails s -p $PORT -b $IP
```

然后，您可以通过导航到`<workspace>-<username>.c9.io`来访问您正在开发的应用程序。

要安装 gems，只需将它们添加到您的`Gemfile`中，并在终端中运行`bundle install`——就像在本地环境中一样。`rails generate`和`rake db`保持不变，迁移在默认的`db/development.sqlite3`文件中创建表。如果您不想使用 SQLite，请阅读关于如何[建立数据库](https://docs.c9.io/docs/setup-a-database)的文档。

#### 进一步的例子

为了获得更多的语言设置示例，我在 [GitHub](https://github.com/sitepoint-editors/cloud9-lang-support) 上放了一些演示——包括 C 和 C++，PHP 和 MySQL，Gradle 等等。

### 其他功能

当然，像 Cloud9 这样的在线代码编辑器提供了许多你在编辑器中所期望的好特性。其中包括:

*   大纲视图和代码完成(包括 Emmet 集成)
*   调试工具
*   拆分视图
*   主题
*   按键绑定
*   流行编辑器的模式(VIM、Emacs、Sublime)
*   内置图像编辑。

## 部署到服务器

无论是在本地开发还是在云中开发，您最终都希望将代码部署到服务器上。一些云开发服务将托管作为一个选项。但是您也可以将代码部署到其他地方。

### 文件传送协议

在 Cloud9 上，你可以通过进入**文件>挂载 FTP 或 SFTP 服务器**来挂载 FTP 或 SFTP (S 代表 SSH/Secure)服务器:

![Mounting an FTP server](img/b1355eab28858697f6899fb64698941f.png)

在上图中，您可以看到 FTP 服务器是如何安装的。我推荐使用 SFTP，它更安全，并且使用 SSH 连接托管服务器。使用 SFTP，您不必指定密码；默认情况下，使用工作区中的 SSH 密钥。(如果您对这个概念不熟悉，我建议您阅读一下关于设置 SSH 密钥的内容。)

### 推动部署

FTP 的替代方案称为*推送部署*。大多数云服务——包括[谷歌应用引擎](https://cloud.google.com/tools/repo/push-to-deploy)、 [Heroku](https://www.heroku.com/) 和[Azure](https://azure.microsoft.com/en-us/documentation/articles/web-sites-publish-source-control/)——允许你使用 Git(或类似的 VCS)将源代码推送到云虚拟机进行部署。因为 Git 已经包含在每个 Cloud9 工作空间中，所以您可以通过将它推送到生产服务器来使用它部署您的应用程序。

## 脱机工作

如果你想继续离线工作，那不成问题。Cloud9 允许你下载源代码作为一个`.zip`档案，然后你可以在你的机器上进行本地开发。它还支持 FTP。您可以将项目直接推入开发或生产服务器。

## 总结

云 ide 试图让开发者尽可能的移动。整个环境远离您的机器，这使您即使使用旧计算机或坐在别人的机器旁也能使用它。它附带了 Git，因此您可以使用它将项目置于版本控制之下和/或与开发团队合作。

我建议在这样的时候使用云 IDE:

*   您的电脑功能不够强大，并且您的互联网连接稳定(不会经常断开)。
*   当你们中的一个人在校外工作时，你想和另一个开发人员练习结对编程。
*   您不介意使用功能不如本地安装的代码编辑器强大的代码编辑器。
*   您希望快速启动一个替代环境，而没有本地安装的所有麻烦。
*   你想向某人展示你正在开发的网站/网络应用程序/Wordpress 主题的演示。(你只需**运行**应用程序并共享网址。)

在本文中，我主要介绍了 Cloud9 的在线 IDE 的特性，但这并不是唯一的一个。有很多替代品，包括[亚硝酸](https://www.nitrous.io/)、 [Codeanywhere](https://codeanywhere.com/) 和 [Codenvy](https://codenvy.com/) 。当然，您选择哪一种将取决于您的特定需求和偏好。

*你曾经在云中开发过吗？如果有，你是怎么找到的？你喜欢什么服务，为什么？*

## 分享这篇文章