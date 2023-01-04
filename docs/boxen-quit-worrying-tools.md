# 别担心你的工具了

> 原文：<https://www.sitepoint.com/boxen-quit-worrying-tools/>

![hero](img/976229db864c3a4d99755fdae163c624.png)

你有多少次感受到设置开发环境、安装依赖项、配置数据库、处理环境变量以及开发人员在开始新项目的第一天*(或一周，lol)* 应该做的所有常规任务的痛苦？

如果你的回答是“不止一次”，那么继续阅读这篇文章，也许，你可以试试 **Boxen** (仅限 OSX)。

正如[博闻网站](http://boxen.github.com/)所说:

> > Pain in automated development environment. Boxen will install your dependencies so that you can focus on getting the job done.

## 它从哪里来的？

**博闻**始于 [Github 项目](https://github.com/blog/1345-introducing-boxen)，原名*“The Setup”*。Github 开始开发它是因为他们意识到他们花了多少时间来建立开发环境，以及不兼容的环境会产生多少麻烦。

他们认为开发*就是*生产，因此他们重视一致性、可预测性和可再现性，而不是一次性的手工调整的开发环境。将此与帮助员工从第一天开始发货的目标保持一致，然后*瞧*。

一旦那个 **Boxen** 成为一个众所周知并被使用的内部项目，他们决定提取特征并包装一切，以便更容易在*任何公司*工作。

## 手动安装是一个错误

这个想法是设置您的项目可能需要的一切，并进行您想要的任何定制，因此除了开始开发之外，没有必要担心任何其他事情。它不仅可以设置您的环境，还可以自动处理所有重要的更新。

> > Boxen can clone your project, install your tool chain, apply security policies, configure your favorite music player, set your screen saver, clone your dot files, and call for help when something goes wrong.

已经有很多很多的[库](https://github.com/boxen)与 Boxen 合作，也有一个很好的社区支持它。如果你找不到你要找的东西，你可以按照这些[简单的步骤](https://github.com/boxen/puppet-template)轻松定制一个现有的库或者创建你自己的库。

## 秘密武器

所有这些酷功能背后的大秘密是 **Boxen** 使用[傀儡](https://puppetlabs.com/)，一个配置管理工具，来做所有困难的工作！而且非常简单！

为了使用它，您或您团队中的任何人编写一个[项目清单](https://github.com/boxen/our-boxen/tree/master/modules/projects#project-manifests)。之后，您需要做的就是运行一个命令，您将得到一个包含所有依赖项的项目。

## 让我们开始吧

在我们开始一些真正的代码示例之前，您需要了解一些细节以避免愚蠢的错误:

*   Boxen 只在 MAC 上运行
*   Boxen 需要安装 Xcode 命令行工具。
*   Boxen 不能与现有的 rvm 安装一起使用。
*   Boxen 可能无法很好地使用包含破折号(-)的 GitHub 用户名
*   Boxen 可能不适合现有的 rbenv 安装。
*   Boxen 可能无法与现有的 chruby 安装兼容。
*   Boxen 可能不适合现有的自制软件安装。
*   Boxen 可能不适合现有的 nvm 安装。
*   博闻建议安装完整的 Xcode。

在 [Boxen 库](https://github.com/boxen/our-boxen)上有一个初学者教程，它将指导你完成初始设置，并帮助你向同事分发。一旦你完成了它，你就可以根据你的需要定制它了。别忘了仔细看看那些现成的[库](https://github.com/boxen)！

### 木偶档案

`Puppetfile`位于你的 **Boxen** 库的根目录下。将所有依赖项放在这个文件中，类似于 Gemfile。

```
# Core modules for a basic development environment.

github "repository", "2.0.2"
github "dnsmasq",    "1.0.0"
github "gcc",        "1.0.0"
github "git",        "1.2.2"
github "homebrew",   "1.1.2"
github "hub",        "1.0.0"
github "inifile",    "0.9.0", :repo => "cprice404/puppetlabs-inifile"
github "nginx",      "1.4.0"
github "nodejs",     "2.2.0"
github "ruby",       "4.1.0"
github "stdlib",     "4.0.2", :repo => "puppetlabs/puppetlabs-stdlib"
github "sudo",       "1.0.0"

# Optional/custom modules. There are tons available at
# https://github.com/boxen.

github "java",     "1.1.0"
```

### 显示

通过包含您需要的模块，`manifests`文件夹保存了所有的节点定义(节点[的概念](http://docs.puppetlabs.com/references/glossary.html#agent)实质上是运行 Puppet 的机器)。

**博闻**默认运行`manifests/site.pp`里面的一切。默认的“site.pp”文件如下所示:

```
node default {
  # core modules, needed for most things
  include dnsmasq

  # more...
}
```

**Boxen** 最酷的部分是令人惊叹的开源[模块](https://github.com/boxen)，它们配置了你可能需要的最常用的工具。现在，您不必在清单中编写大量代码。

一旦你配置好你的`Puppetfile`和`manifest`，你所要做的就是运行 **Boxen**

```
script/boxen
```

**Boxen** 会问几个问题，比如你的 github 账户凭证和 sudo 的 root 密码。有了这些问题的答案，它将起飞和安装一切。

## 包裹

长话短说，如果你在 Mac 上设置工作环境时遇到问题，你应该试试 **Boxen** 。它的配置非常简单，可以安装您需要的所有依赖项。

### 快速游览

1.  在 git 友好的计算机上

    *   派生并克隆 [our-boxen](https://github.com/boxen/our-boxen) 库
    *   将你需要的所有 github 模块添加到`Puppetfile`
    *   将所有要安装的模块添加到`manifests`文件夹中
2.  在新的 Mac 上

    *   [安装 Xcode 和命令行工具](http://railsapps.github.io/xcode-command-line-tools.html)
    *   在`/opt`文件夹中克隆您的 our-boxen 库
    *   跑箱子〔t0〕

## 终极工具包

经过多年从事不同项目的工作，我很兴奋地看到开发人员发明了令人难以置信的有用工具来使我们的生活变得更容易。

使用其中的一些新工具，我已经用 **Boxen** 和**vagger**为我正在处理的任何项目构建了(我认为是)最终的默认包。

使用**博森**设置新机器，安装 [VirtualBox](https://www.virtualbox.org/ "Virtual Box") 、[流浪汉](http://www.vagrantup.com/ "Vagrant")、你喜欢的编辑器，以及你需要的任何东西。一旦完成，使用**travel**下载项目框并设置一个完整的环境。没有大惊小怪，没有混乱。

事情是这样的， **Boxen** 是一个伟大的项目，它解决了一个最常见的环境问题。它是由最进步和最有远见的公司之一支持和创建的。我相信一旦你尝试了，它会帮助你的！

## 分享这篇文章