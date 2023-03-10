# PHP 和与 Travis CI 的持续集成

> 原文：<https://www.sitepoint.com/php-continuous-integration-travis-ci/>

持续集成(CI)允许团队快速提交他们的工作，这意味着每天将有多个提交到存储库。每次提交时，都会被自动构建(包括测试)验证，以便立即提醒您任何构建或测试错误。一旦将您的工作集成和验证到主文档中的过程变得自动化，您就能够快速开发内聚的软件。

## 什么是特拉维斯 CI？

[Travis CI](http://travis-ci.org) 自动建立 CI 环境，让任何人都能轻松测试和部署他们的应用。他们的构建系统支持许多不同的语言，你只需要定义这个项目是哪种语言，Travis CI 会处理剩下的事情。你可以在[他们的文档](http://docs.travis-ci.com/)中找到关于他们提供的所有其他环境的更多信息，但是对于本文，我们将重点关注将 Travis CI 添加到 GitHub 上的 PHP 存储库中。

Github 上的公共存储库可以免费使用 Travis CI，但它们共享社区框并排队。要在 Travis CI 中使用私有存储库，您可以订阅他们的计划之一，无需等待即可构建您的项目，此外，您还可以拥有并发作业。

***注:*** *开源站点结束于’。org '([https://travis-ci.org/](https://travis-ci.org/))并且私有存储库站点以'结尾。' com '([https://travis-ci.com/](https://travis-ci.com/))。*

 <video loop="" autoplay="" width="640" height="360"><source src="<a%20href=%22https://dl.dropboxusercontent.com/u/7827818/Watching%20Travis.mov.mp4%22>https://dl.dropboxusercontent.com/u/7827818/Watching%20Travis.mov.mp4</a>%E2%80%99%20type=%E2%80%99video/mp4%E2%80%99>%20<br>%20%0A<source%20%20src=" href="https://dl.dropboxusercontent.com/u/7827818/Watching%20Travis.mov.webmhd.webm">https://dl . dropboxusercontent . com/u/7827818/Watching % 20 Travis . mov . web MHD . webm ' type = ' video/webm '></video> 

## Travis CI 入门

要继续学习，只需分叉这个示例存储库。请随意更改它的文件，看看设置做什么。

*   [GitHub 回购示例](https://github.com/clouddueling/travis-ci-php-example)
*   [Travis CI 构建页面示例](https://travis-ci.org/clouddueling/travis-ci-php-example)

### 第一步:登录

![](img/4b2e42580b88d0dde0f370d648ef5318.png)

点击右上角的**登录**链接，使用您的 GitHub 帐户登录 [Travis CI](https://travis-ci.org/) 。

在这个页面上，你会在左边看到一个正在构建的公共库列表。观看他们的建设现场只需点击其中之一。

### 第二步:同步你的 GitHub Repos

![](img/b4a13865dd6ee6edde7b90512183527b.png)

在您登录 Travis 后，CI 会将您的公共存储库同步到您的[个人资料页面](https://travis-ci.org/profile)下的帐户。然后，您需要将开关按钮按到打开位置。现在，每次提交到这个存储库时，GitHub 都会调用 Travis CI 并自动启动构建过程。

如果您创建了一个存储库，但没有在列表中看到它，请在您的个人资料页面上单击**立即同步**，您新创建的存储库将会出现在那里。

### 步骤 3:将. travis.yml 文件添加到存储库中

![](img/f7bce8ed58524ed36f9a46b7023bd860.png)

要告诉 Travis CI 您希望在什么环境下构建和测试您的存储库，您需要在存储库的根目录下添加一个`.travis.yml`。Travis CI 将根据您的配置的所有可能组合矩阵运行您的测试，使用:

*   您在`php`下指定的运行时间
*   用`env`定义的环境变量
*   排除、包含和允许的故障

[了解有关构建矩阵的更多信息](http://docs.travis-ci.com/user/build-configuration/#The-Build-Matrix)

```
# Required to run your project under the correct environment. language: php # Versions of PHP you want your project run with. php:  -  5.4  -  5.5  -  5.6  - hhvm # Commands to be run before your environment runs. before_script:  - composer self-update - composer install --prefer-source --no-interaction --dev # Commands you want to run that will verify your build. script: phpunit # allow_failures: Allow this build to fail under the specified environments.  # fast_finish: If your build fails do not continue trying to build, just stop. matrix: allow_failures:  - php:  5.6  - php: hhvm
  fast_finish:  true  # Customize when the notification emails are sent. notifications: on_success: never
    on_failure: always
```

**示例配置**

*   [Laravel 框架](https://github.com/laravel/framework/blob/master/.travis.yml)
*   GushPHP
*   [大吃大喝](https://github.com/guzzle/guzzle/blob/master/.travis.yml)
*   [碳](https://github.com/briannesbitt/Carbon/blob/master/.travis.yml)

[了解更多关于. travis.yml PHP 选项的信息](http://docs.travis-ci.com/user/languages/php/)

### 步骤 4:用 Git 推送触发构建

您的第一个构建必须由 GitHub 的提交和推送来触发，而不是重启构建按钮。

每当您向存储库的一个分支提交时，Travis CI 将启动构建过程。一旦有工人可用，它将开始构建您的项目。您可以导航到`https://travis-ci.org/GITHUB_USERNAME/REPO_NAME`来实时观看它的构建进度。

如果你想重启你的构建，点击**重启构建**按钮，你会看到一旦有工人可用，你的项目就开始构建。

![](img/018cf3352c8aaf1690ad1c2062228d9d.png)

### 步骤 5:调整构建配置

在示例存储库中，您将找到一个`.travis.yml`文件，其中包括一些基本内容，以及 Travis CI 的一些附加内容。如果您想要定制您的配置，您可以配置几乎所有关于您的构建环境的内容。

[了解更多关于 Travis CI 的 PHP 配置](http://docs.travis-ci.com/user/languages/php/)。

### 第六步:测试它

要查看您是否正确配置了存储库，请在 GitHub 上编辑您的 README.md 文件并提交您的编辑。转到该存储库的 Travis CI 页面， **Job** 下的所有圆圈应该变成黄色，并且一旦构建开始运行，持续时间应该开始增加。

一旦构建完成，灯变红，单击它并在控制台中读取输出。您还可以设置 Travis CI 通过`notifications`选项向您发送电子邮件，让您知道发生了什么。
如果您的构建没有开始，尝试用 [YAML 验证器](http://yaml-online-parser.appspot.com/)进行验证，以确保您的配置文件中没有错误。

*   [常见的构建问题](http://docs.travis-ci.com/user/common-build-problems/)
*   [邮件列表](https://groups.google.com/forum/#!forum/travis-ci)
*   [IRC](irc://irc.freenode.net#travis)
*   [堆栈溢出](http://stackoverflow.com/questions/tagged/travis-ci)

## 了解更多信息

Travis CI 工作人员提供许多不同的服务，您可能会依赖这些服务，并且有针对所有这些服务的指南。

*   [文档](http://docs.travis-ci.com/)
*   [命令行客户端](https://github.com/travis-ci/travis.rb#readme)
*   [构建环境](http://docs.travis-ci.com/user/ci-environment/)
*   [数据库和其他服务](http://docs.travis-ci.com/user/database-setup/)
*   [加速构建](http://docs.travis-ci.com/user/speeding-up-the-build/)
*   [Travis CI 状态](http://status.travis-ci.com/)

### 构建状态图像

Travis CI 提供了一个状态图像，您可以将它放在项目的自述文件或一般文档中。然后访问者可以立即看到它的构建状态。

这是来自示例存储库的状态图像。希望现在是绿色的。

[![Build Status](img/51be54621be3adaa0aca15e7ae534b2d.png)](https://travis-ci.org/clouddueling/travis-ci-php-example)

![](img/4d6d50334cc683096ac00d71531c6cd7.png)

```
Travis CI PHP Example  ===========  [![Build  Status](https://travis-ci.org/clouddueling/travis-ci-php-example.svg?branch=master)](https://travis-ci.org/clouddueling/travis-ci-php-example)
```

图片网址格式:
`https://travis-ci.org/USERNAME/REPO_NAME.svg?branch=BRANCH_NAME`

## 快速概述

### 目前的

您用配置选项定义的所有构建都将出现在这里，旁边有一个彩色图标，表示它们的构建状态。

*   空白屏幕:您还没有运行您的第一个构建
*   黄色:进行中
*   红色:错误，检查控制台
*   绿色:成功！

![](img/760b68aa4af10aafa85df3557d7871d2.png)

### 创造历史

每次运行构建时，它都会收到一个构建号，并创建一个历史记录，您可以看到是什么提交触发了构建，GitHub 上提交的链接，构建的持续时间，以及发生的时间。

![](img/bf203d1e92349740406e7318faddb42d.png)

### 拉取请求

为了让项目经理很好地了解一个拉取请求是否会破坏您的构建，Travis CI 自动使用该拉取请求构建您的项目，并在 PR 上显示其状态。

*   [拉取请求变得更加令人敬畏](http://blog.travis-ci.com/2012-09-04-pull-requests-just-got-even-more-awesome/)
*   [测试拉取请求](http://docs.travis-ci.com/user/pull-requests/)

![](img/3b803e54b39917a7eac3b7b7e271d37c.png)

### 分支摘要

Travis CI 不仅构建您的主分支，还构建所有其他分支，分支摘要告诉您每个分支的当前构建状态。

![](img/bcd153f9c71d767442705dda934d9942.png)

## 结论

正如你所看到的，Travis CI 是一个强大的、功能丰富的服务，旨在保持你的项目活跃，并通过任何和所有的贡献。你在你的项目中实现它了吗？有诀窍分享吗？让我们知道！

## 分享这篇文章