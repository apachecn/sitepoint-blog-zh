# 快速提示:通过命令行同步 GitHub Fork

> 原文：<https://www.sitepoint.com/quick-tip-synch-a-github-fork-via-the-command-line/>

当你在 GitHub 上派生某人的库时，你会希望用对原始库所做的任何改变来更新你的派生库。有各种方法可以做到这一点。在这个快速技巧中，Shaumik 描述了如何通过命令行更新 fork。另一个选择是[通过 GitHub web 界面](https://www.sitepoint.com/quick-tip-sync-your-fork-with-the-original-without-the-cli/)更新你的 fork。

* * *

Git 是一个分布式版本控制系统，这意味着存储库的每个副本都有自己完整的历史。另一方面，GitHub 是 Git 库的在线集合。GitHub 引入了分叉的概念，这涉及到制作主存储库的副本。

![GitHub Workflow](img/0ba368294413941c191acf9e9dcd4f97.png)

GitHub 工作流程

要理解更新 fork 的概念，首先必须知道为什么这是必要的。

一个组织不能授予每个潜在贡献者对其主存储库的访问权限，所以公众只能通过 T2 查看中央存储库。fork 是用户可以创建的这个存储库的副本。用户对自己的分支拥有读写权限。

通常，编程发生在本地机器(或 VM)上，而不是直接发生在 GitHub 接口上，所以通常会创建一个 fork 的克隆。

一旦贡献者提交了一个本地副本，它就需要被推送到 GitHub 上的 fork(由于写访问，这是可能的)。然后，创建一个从 fork 到中央存储库的 pull 请求。

### 保持你的叉子是最新的

当中央存储库被其他人的代码更新时(在 fork 被创建之后)，这些新的提交不会神奇地出现在 fork 上。必须首先下载这些更改并将其与本地存储库合并，然后将其推送到分叉处。

出于遗留原因，在我们的本地存储库中，我们将中央存储库 remote 命名为`upstream`，将 fork 命名为`origin`。

理想情况下，您不应该直接对 fork 的`master`分支或本地存储库进行任何提交。该分支必须仅用于保存来自`upstream`的更新代码。必须对新特性或 bug 分支进行所有更改，并将其推送到 fork 上同名的分支。

因此，以下步骤有助于使用来自中央存储库的最新提交来更新 fork:

*   从`upstream`的`master`分支拉到本地存储库的`master`
*   从本地存储库的`master`推送到分支的`master`

这些步骤假设您已经分叉了存储库，并在本地机器上克隆了分叉。

为了演示，我们将使用 GitHub 上的 [e-Cidadania 的存储库。](https://github.com/cidadania/e-cidadania)

## 步骤 1:派生存储库

要派生一个存储库，您需要单击 **fork** 按钮(屏幕截图的右上部分)。

![e-Cidadania's home page on GitHub](img/1423dced6fc9833262cf3675a2ea9c04.png)

e-Cidadania 在 GitHub 上的主页

## 步骤 2:克隆分叉的存储库

要克隆您的 fork，您首先需要从下拉列表中选择协议(如下图所示)并复制链接。在本演示中，我们将选择 SSH 协议:

![e-Cidadania's fork on your profile](img/936ad9addc2539f048bb6ea2a9951c3e.png)

您个人资料中的 e-Cidadania 分叉

打开终端并运行以下命令:

```
git clone git@github.com:sdaityari/e-cidadania.git
```

## 步骤 3:链接到中央存储库

然后，您需要将您的本地存储库链接到中央存储库，以便能够从中央存储库中提取变更。这是通过添加上游遥控器来实现的。首先，从中央存储库中复制 SSH 链接，并通过运行以下命令添加远程:

```
git remote add upstream git@github.com:cidadania/e-cidadania.git
```

要验证是否已添加遥控器，请运行以下命令检查遥控器列表:

```
git remote -v
```

输出应该如下所示:

```
origin  git@github.com:sdaityari/e-cidadania.git (fetch)
origin  git@github.com:sdaityari/e-cidadania.git (push)
upstream    git@github.com:cidadania/e-cidadania.git (fetch)
upstream    git@github.com:cidadania/e-cidadania.git (push)
```

## 步骤 4:从`upstream`(中央存储库)提取变更

当中央存储库的`master`中有新的提交，而您的 fork 中没有，您会在 fork 的页面上收到来自 GitHub 的消息。在截图中，你可以看到消息说<q>该分支在 cidadania:master</q> 之后 36 次提交:

![Fork’s page on GitHub](img/88caa8f1124b09b0474ac45ca5f3decc.png)

GitHub 上的 Fork 页面

要将这些更改加载到您的本地存储库中，请运行以下命令:

```
git pull upstream master
```

该命令从`upstream`遥控器更新您的`master`分支。

## 步骤 5:将更改推送到`origin` (Fork)

要将这些更新从中央存储库推送到 fork，只需运行以下命令:

```
git push origin master

Counting objects: 739, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (381/381), done.
Writing objects: 100% (739/739), 219.66 KiB | 0 bytes/s, done.
Total 739 (delta 408), reused 602 (delta 278)
To git@github.com:sdaityari/e-cidadania.git
   da546f3..2fc7f31  master -> master
```

要确认更改已经更新，请再次访问 GitHub 上的 fork 页面！

![Fork's updated page on GitHub](img/93c9aa4476ec2dd63ea6316d24ece254.png)

GitHub 上 Fork 的更新页面

消息<q>This branch even with cida dania:master</q>显示提交已经被添加到 fork 的`master`分支。

## 分享这篇文章