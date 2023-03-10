# 从 GitHub 部署到服务器

> 原文：<https://www.sitepoint.com/deploying-from-github-to-a-server/>

![Chinchilla and Octocat](img/67d73564c5f595f047800e689a5aee86.png)

GitHub 和它所基于的 Git 版本控制系统是管理和协作项目的极好工具——无论是基于代码的还是其他的。

在本文中，我们将看看如何让 Git 和 GitHub 项目更好地适应开发人员的工作流程，从而实现一个平稳的<q>放手的</q>部署过程。

我将把这些选项分成不同类型的可用工具集——这些工具集允许从自动运行测试和代码检查到将您的代码部署到服务器的选项。

## 为什么要这么做？

随着这些自动化流程的启动和运行，您和您的团队可以专注于编码、批准和合并代码，而不是在每次新的构建或更改就绪时花费数小时进行部署和重复任务。

### 自动化的缺点

自动部署变更的主要问题是变更是自动部署的。你必须相信你的团队和他们写的代码。这就是为什么自动部署通常与自动测试结合在一起，下面介绍的工具反映了这一点。

当然，这也意味着任何小问题都能同样快速地解决。自动化应该与通信相结合。如果推进到一个存储库的主分支可以触发实时构建，那么就需要弄清楚什么时候会发生这种情况，以及谁可以让它发生。

自动化流程的初始设置可能需要一些时间来适应。重要的是要权衡你的团队或工作流程是否真的需要它。把你花在测试和部署新版本上的时间加起来——如果每次都超过几分钟，那么这是值得的。

## 去吧胡克斯

Git 有一套可以用于自动化的内置钩子,在特定的 Git 操作之后，这些钩子通常是我们处理任务的第一站。这些被分为服务器端和客户端挂钩。

服务器端挂钩用于监听网络操作等事件——例如，当存储库接收到推送时。客户端钩子是在开发人员的机器上发生的动作上触发的，比如提交和合并。

Git 的文档中有一个完整的钩子列表。我将在这里看几个例子来帮助您开始。希望您将开始看到它们在您自己的项目和当前(手动)工作流中是如何有用的。钩子是可以包含主机系统可以运行的任何语言的命令的文件，允许很大的能力和灵活性。

### `pre-commit`

这个客户端挂钩在任何其他挂钩之前运行，并且在任何更改提交之前运行。这是对代码进行测试或其他检查的绝佳场所。

让我们为我们的小项目添加一些基本的 JavaScript(是的，这里有一个故意的错误):

```
document.onload = function() {
    alert("Hello World")
};
```

我们将使用 JSHint 来检查 JavaScript 中的错误。(你可以在找到[的安装说明。)](http://jshint.com/install/)

将`hooks/pre-commit.sample`重命名为`hooks/pre-commit`，并将文件内容改为:

```
#!/bin/sh
jshint index.js
```

尝试提交更改:

```
git commit -m "adding Javascript file"
```

您将看到以下错误消息:

```
index.js: line 5, col 25, Missing semicolon.

1 error
```

请添加缺少的分号，然后重试。现在，提交将顺利进行。

### `post-receive`

当对远程 Git 存储库的推送完成时，这个服务器端钩子触发。在这个例子中，我们将一个简单网站的最新版本签入到我们的 webserver 目录中，这实际上是一个(基本)部署。

我有一个现有的网站，它由一个`index.html`页面和一些我们将在后面的例子中使用的其他页面组成。你可以创建自己的或者使用[这里设置的库](https://github.com/sitepoint-editors/GitHub-Auto-Deploy)。

克隆存储库，指定`--bare`标志来创建一个只包含版本控制信息而不包含我们的代码库的存储库:

```
git clone --bare https://github.com/sitepoint-editors/GitHub-Auto-Deploy.git GitHub-Auto-Deploy.git
```

现在我们将创建我们的钩子:

```
cd GitHub-Auto-Deploy.git/hooks
vi post-receive
```

将这些行添加到文件中:

```
#!/bin/sh
git --work-tree=/var/www/html --git-dir=/var/repo/GitHub-Auto-Deploy.git checkout -f
```

*注意:这些位置与 Ubuntu 安装相关，所以记得改变路径以适应你的设置*。

该命令将当前存储库签出到定义的工作目录中，但不包含任何版本控制数据。

我们需要使钩子可执行:

```
chmod +x post-receive
```

在您的本地机器上，使用您选择的工具正常克隆存储库，并为实时服务器添加一个新的远程服务器(记住将服务器详细信息更改为您的 web 服务器和用户详细信息):

```
git remote add prod ssh://user@domain.com/var/repo/GitHub-Auto-Deploy.git
```

要部署到我们的生产服务器而不是存储库，请输入:

```
git push prod master
```

如果你查看`var/www/html`文件夹，你会发现`index.html`文件被自动复制到你的网络文件夹中。

如果您使用自己的 Git 存储库，您可以将它与您的应用程序放在同一个服务器上，并且部署现在是自动化的。如果您正在使用 GitHub 或其他外部 Git 服务，那么这个钩子并没有完全自动化您的工作流程，而是将它简化为一个步骤。这可以进一步简化。

一种选择是使用 GitHub 上`post-receive`钩子中的 [rsync](http://linux.die.net/man/1/rsync) 或 [scp](http://www.hypexr.org/linux_scp_help.php) 命令。另一个选择——特别是如果你的应用在上线前需要一个构建过程(GitHub 限制可能的命令)——是使用`post-receive`钩子来触发你的应用服务器上的脚本，该脚本从 GitHub 中签出你的代码库(使用`-f`选项)并运行任何其他必要的命令。这开始变得复杂，这很好地引出了我们的下一套工具。

## 直接从 GitHub 自动部署

GitHub 有自己的文档来自动部署到集成平台，其中一些是托管提供商。

老实说，我查阅的大多数文档都是不正确、不准确或没有帮助的，所以我做了一些搜索，链接到一些流行的主机提供商的官方文档，对于其他任何文档，我建议你使用`post-receive`或持续集成方法:

*   [Heroku](https://devcenter.heroku.com/articles/github-integration#automatic-deploys)
*   [AWS](https://blogs.aws.amazon.com/application-management/post/Tx33XKAKURCCW83/Automatically-Deploy-from-GitHub-Using-AWS-CodeDeploy)
*   [蔚蓝色](https://azure.microsoft.com/en-us/documentation/articles/web-sites-publish-source-control/)

## 持续集成(CI)服务

有无数的服务可以监视您的 GitHub repos 的变化，不仅为您部署它们，还可以执行其他功能，例如为您运行测试和构建流程。

转到一个新的更复杂的例子，我们可以使用 CI 来自动化项目的构建过程。首先，提取存储库的`Master`分支，触发一个 bash 脚本来运行构建和部署过程，然后在 tweet 上发布更新。CI 和 web 服务可以在同一台服务器上，也可以在不同的服务器上，这取决于您的偏好。

让我们快速浏览一些最受欢迎的。

### 詹金斯

你需要建立自己的 Jenkins 服务器，这意味着你可以完全控制，但需要维护。幸运的是，它支持许多平台，包括 Docker(如果你只是想先试验一下的话)。

Jenkins 通过插件实现了它的大部分功能，由于它的历史、开源性质和受欢迎程度，它有很多插件。比如有 [Git](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin) 、 [GitHub](https://wiki.jenkins-ci.org/display/JENKINS/GitHub+Plugin) 和 [Twitter](https://wiki.jenkins-ci.org/display/JENKINS/Twitter+Plugin) 的插件。

Jenkins 需要大量的配置，有时将构建所需工作流所需的指令拼凑在一起可能需要大量的研究。

### 特拉维斯

同样，在 GitHub 的文档中，将 Travis 与 GitHub 集成的说明已经过时。现在更简单了:阅读 Travis docs 了解更多信息。

Travis 不需要任何主机或服务器设置，所以如果您热衷于尝试 CI 而不投入太多设置时间，这是一个很好的起点。然而，将它扩展到(全面的)默认集成之外将涉及一些额外的配置工作。例如， [Tweeting](https://docs.travis-ci.com/user/notifications/#Webhook-notification) 需要访问 webhooks。

Travis 有一个习惯，就是很难注意到你的 repos 中的更新，尤其是在它自己的配置文件中。这些问题可能很难解决，因为您无法访问 Travis 服务器本身。

### 其他商业服务

持续集成越来越受欢迎，因此出现了大量新的服务和应用——许多是由您可能已经在使用的工具的创建者发布的，它们将紧密地融入现有的工具链和工作流。以下是一些例子:

*   [https://buddy.works/](https://buddy.works/)
*   [https://www.atlassian.com/software/bamboo/](https://www.atlassian.com/software/bamboo/)
*   [https://www.jetbrains.com/teamcity/](https://www.jetbrains.com/teamcity/)
*   [https://codeship.com/](https://codeship.com/)
*   [https://circles . com/](https://circleci.com/)
*   [https://saucelabs.com/](https://saucelabs.com/)
*   [https://about.gitlab.com/gitlab-ci/](https://about.gitlab.com/gitlab-ci/)
*   [http://deploybot.com/](http://deploybot.com/)

## 包裹

希望这篇简短的介绍已经为您澄清了一些关于这种部署如何工作的事情。从把你的文件传输到你的服务器的时代开始，我们已经走过了漫长的道路！

如果你对上述过程有任何疑问，请在评论中告诉我。

## 分享这篇文章