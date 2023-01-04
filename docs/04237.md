# 优化 Jenkins 的 7 种方法

> 原文：<https://www.sitepoint.com/7-ways-optimize-jenkins/>

*本文由 [CloudBees](https://www.cloudbees.com/) 赞助。感谢您对使 SitePoint 成为可能的赞助商的支持！*

![The Jenkins Logo](img/279b5d4c9baa08198362cbf1dc70ffbc.png)

Jenkins 是持续集成和持续交付的伟大工具。在之前的文章中，我们已经了解了如何设置 Jenkins 进行质量保证，并且查看了一个特别有用的插件[来增强你的过程。在这篇文章中，我们将通过七种方法来指导你如何有效地使用 Jenkins。](https://www.sitepoint.com/supercharge-continuous-delivery-jenkins-workflow-plugin/)

*本文基于 Jenkins 撰稿人 Andrew Bayer 在 2014 年 Jenkins 用户大会上的发言，经许可后在此进行了改编。*

## 1.确保詹金斯是稳定的，工作正常

詹金斯频繁发布新版本。建议始终使用最新的长期支持(LTS)版本，并避免中间的更新。詹金斯项目对 LTS 版本进行了测试。

同样的规则也适用于插件。他们没有 LTS 版本，但可以进行许多更新。总的来说，你应该坚持这条规则:如果有效，就不要更新它。新的更新可能会使功能不稳定。此外，任何给定的插件的行为都可能与预期不同。

还要确保创建 Jenkins 服务器的备份。您可以备份所有数据，但这将耗费您大量的时间和资源。相反，您可以决定只通过像 [this](https://gist.github.com/abayer/527063a4519f205efc74) 这样的脚本来备份您的配置文件。

## 2.不要让詹金斯膨胀

你可以做几件简单的事情来避免成为臃肿的 Jenkins 设备的所有者:

1.  如果您有许多团队或项目，您可能会创建多个 Jenkins masters。例如，您可以为 PHP 项目创建一个母版，为 Android 项目创建一个单独的母版。这样做将确保更改不会影响所有项目，并且您只需要在每个项目的特定母版上安装插件。

2.  将你的工作分成多个更小的工作。没有什么比长时间的构建最终在某个地方失败更令人恼火的了。如果您将作业分成更小的作业，那么当其中一个作业失败时，您可以轻松地重新启动它。[工作流插件](https://wiki.jenkins-ci.org/display/JENKINS/Workflow+Plugin) [对于这个](https://wiki.jenkins-ci.org/display/JENKINS/Workflow+Plugin)目的是有用的。如果你对工作流插件的更深入的解释感兴趣，看看我们的[最近的产品评论](https://www.sitepoint.com/supercharge-continuous-delivery-jenkins-workflow-plugin/)。

## 3.自动化詹金斯

Jenkins 带有一个集成的脚本控制台，允许您在服务器上执行命令。这是调试 Jenkins 或查找所需信息的便捷工具。

一个有趣的插件是 [Scriptler](https://wiki.jenkins-ci.org/display/JENKINS/Scriptler+Plugin) 插件。有了这个插件，你可以保存你喜欢的脚本以备后用。您还可以在这里找到一个在线脚本目录，其中包含像清除作业队列这样的预定义脚本。

对于每个构建，你也可以用 [Groovy](http://groovy.codehaus.org/) [语言](http://groovy.codehaus.org/)编写你自己的构建步骤。这给你的工作带来了更多的灵活性，但也意味着你需要更加小心，因为这给了你更多的控制权。您还可以从 Scriptler 目录中运行脚本作为构建步骤。

如果你有很多项目，考虑自动生成工作。Jenkins 提供了一个很棒的 REST API 和一个 T2 CLI 工具来创造新的工作。你还可以使用插件，比如我们最近讨论过的[工作流](https://wiki.jenkins-ci.org/display/JENKINS/Workflow+Plugin)来自动化流程的某些部分，或者使用 Cloudbees 模板插件，让你轻松地从预定义的工作框架中生成工作。

## 4.仔细考虑你的插件

Jenkins 有丰富的插件集合；人们很容易迷失在大量的可用信息中。决定你是否真的需要一个插件是很重要的。插件可能会在你意想不到的地方破坏 Jenkins 的功能。它们还会减慢您的构建工作。确保为正确的工作选择正确的插件。如果不是真的需要，不要随便安装。

跟踪你的插件列表，卸载任何不再使用的插件。这将有助于保持詹金斯在良好的状态和自由膨胀。

还要记住，有些插件带有全局配置设置。安装插件后不要忘记检查全局配置，因为默认设置可能不是你想要的，并可能导致意想不到的行为。

## 5.与其他工具集成

Jenkins 提供了一个 REST API，您可以使用它将 Jenkins 与其他工具集成在一起。最好的集成之一可能是与源代码控制的集成。例如，你可以很容易地将 Jenkins 与 [Github](https://github.com/) 或 [Gitlab](https://about.gitlab.com/) 结合起来。每当开发人员创建一个对您的源代码的拉请求时，Jenkins 就会被触发来检查这个拉请求是否有任何缺陷。这样，您可以确保只接受符合您的标准的拉请求。

您还可以将 Jenkins 与代码审查工具集成，例如 [Gerrit](https://code.google.com/p/gerrit/) 。当在 Gerrit 中审查代码时，您会从 Jenkins 那里得到额外的反馈。您可以检查是否所有事情都按照您想要的方式完成，并自动将代码合并到几个分支中。

另一种方法是将 Jenkins 与 bug 跟踪系统集成在一起，比如吉拉的 T1 和 T2 的 red mine T3。Jenkins 可以在构建开发人员的修复时，在这些 bug 跟踪工具中发布问题的更新，允许团队中更高水平的可见性，并在问题修复时通知其他团队成员(例如:项目经理或 QA 测试人员)。另一方面，您也可以基于特定构建中所有报告的问题自动生成一个 changelog。

当然，更多的集成是可能的。例如，您可以轻松地在每个构建中部署您的应用程序。无论您做出什么决定，一个好的自动化集成将让您在其他事情上工作，而不是手动执行平凡的任务。

## 6.设置正确的从设备

确保你有易于管理的奴隶。如果你确定你可以毫不费力地替换它们或添加新的，你的生活会变得容易得多。如果一个从服务器崩溃了，确保只需要点击几下就可以让一个新的启动并运行。

你可以通过准备你可以用来安装的虚拟映像或者你可以通过[傀儡](http://puppetlabs.com/) ( [模块](https://forge.puppetlabs.com/rtyler/jenkins))或者[主厨](https://www.chef.io/) ( [食谱](https://github.com/opscode-cookbooks/jenkins))来管理你的完整配置。

你也可以选择让奴隶更通用。这意味着您可以在单个从属服务器上运行多个不同的作业，使从属服务器更加通用。然而，如上所述，你不应该让你的奴隶太复杂。

例如，通过将从设备放在云中，您可以决定用一个容器(比如 [Docker](https://www.docker.com/) )来自动激活它们。这样，您不会浪费任何资源，并且可以在需要更多从属服务器时自动扩展。

## 7.詹金斯社区

有效使用 Jenkins 的最后一个技巧是利用 Jenkins 社区。你需要融入社区。通过编写和贡献插件，或者报告/解决问题来参与进来，将有助于改进 Jenkins，并将改善您的 Jenkins 体验。此外，查看 [Jenkins Subreddit](http://www.reddit.com/r/jenkinsci/) ，加入[邮件列表](http://jenkins-ci.org/content/mailing-lists)，登录 IRC 参加[两周一次的 Jenkins 项目会议或在项目会议期间随时聊天](http://jenkins-ci.org/content/chat)或注册[时事通讯](https://www.cloudbees.com/jenkins/newsletter)。

## 结论

通过维护和一点打磨，每一个好工具都可以变得很棒。詹金斯也不例外。开箱即用，Jenkins 是持续集成和持续交付过程的一个很好的工具。但是如果你选择更稳定的 LTS 版本，避免膨胀，花一点时间自动化，选择正确的插件和正确的集成，并参与社区，你的 Jenkins 过程将真正开始发光。如果你的持续集成和交付工具为你努力工作，你的软件开发将会超负荷。

看完这篇文章后，你打算做些什么改变？你还有什么确保詹金斯顺利运行的技巧吗？

## 分享这篇文章