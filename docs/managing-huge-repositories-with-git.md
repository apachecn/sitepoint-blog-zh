# 用 Git 管理大型存储库

> 原文：<https://www.sitepoint.com/managing-huge-repositories-with-git/>

Linus Torvalds 在 2000 年代中期创建了 Git，以解决当时其他开源版本控制系统无法解决的问题——分布式、可靠和快速。

正如他在谷歌关于 Git 的技术演讲中提到的，Git 是出于 Linux 项目的需要而创建的。在演讲开始的时候，Git 还很年轻，人们已经习惯了它。它似乎解决了版本控制软件面临的所有问题，这有助于它的迅速崛起。

## Git 的缺点

几年后，人们开始注意到 Git 的第一个真正缺陷:很难处理非常大的存储库。我们这里说的是多大？这是[脸书大](https://code.facebook.com/posts/218678814984400/scaling-mercurial-at-facebook/)。脸书的团队预计，几年后，一个简单的`git status`将需要半分钟来显示结果，因为脸书每天都会增加来自数千名开发人员的大量提交。脸书将其整个代码库转移到 [Mercurial](https://mercurial.selenic.com/) ，其团队开始积极地为 Mercurial 做贡献，以满足脸书的需求。

Git 哪里失败了？Mercurial 撰稿人 Prasoon Shukla 在比较 Git 和 Mercurial 的伸缩性时说，这可以归因于 Mercurial 和 Git 存储提交的方式。Mercurial 为存储库中的每个文件管理几个对象(或文件)，而 Git 为每次提交创建一个对象。因此，随着提交次数的增加，Mercurial 中的对象数量保持不变，这与 Git 中的线性增加形成对比。因此，当您运行一个简单的`git status`命令时，Git 必须筛选所有这些对象，这需要相当长的时间(尽管 Git 的效率很高)。

Git 可能不足的另一个领域是管理存储库中的大型二进制文件。因为 Git 跟踪文件中的变化，所以它不能解释二进制文件内容的变化。并且存储库的大小随着每次提交而增加，因为 Git 必须存储准确的二进制文件，而不是上一个版本的更改。

多年来，Git 的开发人员试图解决这些问题。每个第三方服务都提出了解决方案，使 Git 能够管理更大的存储库——例如 GitHub 的[大文件存储](https://www.sitepoint.com/guide-versioning-large-files-with-git-lfs/)扩展。

这篇文章着眼于可以用来处理 Git 中的大型存储库的技术——在大型历史和/或大型二进制文件方面。

## 有大量提交的项目

我将首先看一些更有效地管理具有大量历史的存储库的方法。

### 仓库的浅层克隆

如前所述，拥有大量历史记录的项目变慢的主要原因是大量的提交。在像 Git 这样的分布式系统中，当您克隆一个存储库时，它的完整项目历史会被下载。然而，Git 提供了一种方法来指定您希望在项目克隆中拥有的提交数量。这被称为**浅层克隆**。当提交数量减少时，Git 操作会运行得更快。

要执行浅层克隆，您需要在`clone`命令中添加`--depth`选项，以及我们想要的提交次数:

```
git clone --depth [number_of_commits] [url_of_remote]
```

在 Git 的早期版本中，对浅层克隆的支持是有限的。如果你被截断的历史不够长，你就不能推或拉。然而，随着 Git 1.9.0 的发布，对浅层克隆的支持显著增加。

### 克隆单个分支

克隆存储库时，远程中的所有分支都会被下载。(如果您在一个新克隆的存储库中运行`git branch`，它只显示`master`分支。您应该运行`git branch -a`来列出作为远程的一部分的所有分支。)很可能其他分支中出现的许多提交与一个开发人员的工作无关。因此，您可以只克隆`master`或者与您的开发相关的分支。这样做可以大大减少构成克隆版本历史的提交数量，特别是如果存储库中的分支有不同的历史。

要仅克隆远程的单个分支，您可以运行以下命令:

```
git clone [url_of_remote] --branch [branch_name] --single-branch
```

这个命令指示 Git 只从远程克隆`branch_name`分支。

## 具有大文件的项目

下一个出现的问题是大型二进制文件(不是传统的文本文件)的存在。Git 不跟踪二进制文件中的更改，这就是为什么二进制文件中的任何更改都存储为二进制文件本身。如果二进制文件很大(比如 3D 模型，或者图形设计)，那么存储库的大小会随着每次提交的改变而显著增加。

### 使用子模块

解决大文件问题的一种方法是使用子模块，它使您能够在一个 Git 存储库中管理另一个。您可以创建一个子模块，其中包含所有的二进制文件，将其余的代码单独保存在父存储库中，并仅在必要时更新子模块。这在逻辑上将项目的核心部分与大文件分开，有助于分别管理它们。

### 使用第三方扩展

Git 有许多扩展，由其他开发人员构建，用于处理大文件。一种选择是使用 [git-annex](https://git-annex.branchable.com/) ，它允许您在 git 中管理文件，而无需将文件内容签入 Git。另一个扩展(其开发现已停止)是 [git-bigfiles](http://caca.zoy.org/wiki/git-bigfiles) 。

GitHub 最近推出了 [Git 大文件存储](https://git-lfs.github.com/)，这是 Git 的一个开源扩展，用于管理 Git 中的大型二进制文件。LFS 将这些大文件存储在 GitHub 这样的远程服务器上，而只有文本指针存储在 Git 存储库中。SitePoint 最近[发布了一个关于如何开始使用 Git LFS](https://www.sitepoint.com/guide-versioning-large-files-with-git-lfs/) 的教程。

在很短的时间内，Git LFS 变得流行起来，这表明它提供了一种在 Git 中处理如此大的二进制文件的好方法。

## 最后的想法

脸书宣布转向 Mercurial 已经有很长一段时间了(尽管它仍然继续使用 Git 进行类似于 [ReactJS](https://github.com/facebook/react) 的附带项目)。很高兴看到 Git 开发人员和第三方开发人员都对它做出了积极的反应，并提出了解决手头问题的创新解决方案。如果你正在考虑学习版本控制，我建议你选择 Git——因为未来肯定是光明的！

* * *

如果你想了解更多关于 Git 及其惊人的能力，请查看 Shaumik 的新书 [Jump Start Git](https://www.sitepoint.com/premium/books/jump-start-git) ，就在 SitePoint 上发布！

[![Jump Start Git cover](img/1ed95ef4db0c41e2766c3e70f5857dd6.png)](https://www.sitepoint.com/premium/books/jump-start-git)

*   理解 Git 的核心理念。
*   Git 入门:安装它，学习基本命令，并设置您的第一个项目。
*   作为协作团队的一员与 Git 一起工作。
*   使用 Git 的调试工具来获得最大的调试效率。
*   使用 Git 的高级特性进行控制:reflog、rebase、stash 等等。
*   使用 Git 和基于云的 Git 存储库主机服务，如 Github 和 Bitbucket。
*   了解 Git 如何有效地用于大型开源项目。

## 分享这篇文章