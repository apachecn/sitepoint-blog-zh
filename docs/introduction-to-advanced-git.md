# Git 简介-第二轮(高级)

> 原文：<https://www.sitepoint.com/introduction-to-advanced-git/>

Git 是一个非常强大、灵活和有能力的分布式版本控制系统。不幸的是，它也可能令人不快，文档也有点简洁，这是人们在很多场合都提到过的。

你可能已经读过 Sean Hudgston 的 Git 系列[介绍。如果没有，我真的建议你这样做。顾名思义，它不仅是一个很好的介绍，而且也是本文的一个很好的入门。](https://www.sitepoint.com/series/introduction-to-git/ "Introduction to Git")

在这里，我将讨论一些高级主题，这些主题在您的正常开发工作流程中可能会遇到，也可能不会遇到——这些功能允许您比其他系统(如 SVN)做得更多。其中包括:

*   导出您的存储库
*   基本重置基础
*   对一组提交重新排序
*   拆分一组提交
*   更改提交消息
*   合并一组提交

如果你花时间专门研究版本控制系统，而不是 Git，并且按照他们的理念工作，那么其中的一些概念对你来说可能是陌生的(甚至是禁忌)。但是不要害怕；我将解释如何执行这些操作，并向您展示使用它们可能会有好处的时候。

## 导出您的存储库

让我们轻松愉快地开始吧。导出可能不是那么高级，但它确实是一个非常方便的功能。导出允许我们在没有我们通常保留的所有版本控制信息的情况下向其他人交付我们代码的发布或快照。

### 来自本地回购的档案

利用 Phing 和 Capistrano 之类的工具，或者 Hudson 和 Xinc 之类的持续集成工具，归档可以成为我们部署过程中方便的一部分。(可选地，我们可以触发它来响应提交挂钩，但是这可能有点过分。)

从本地存储库创建归档的选项非常简单。这里有一个例子:

```
$ git archive --format=tar --prefix=sitepoint-git-advanced/ 
    HEAD > sitepoint-git-advanced.tar | 
    gzip > sitepoint-git-advanced.tar.gz
```

在上面的例子中，我希望归档文件的格式是 TAR(如果您愿意，也可以指定 ZIP)。然后，我指定了前缀值“sitepoint-git-advanced”。如果结尾斜杠没有被附加，则该名称将被赋予归档中的所有文件。在这种情况下，它只提供给整个存档。

现在让我们看另一个例子，它的输出是一个 ZIP 文件。我们可以使用`--output`参数并为其提供一个名称，而不是指定格式，然后通过管道将输出传递给`gzip`。zip 作为文件扩展名。

```
$ git archive --output=sitepoint-git-advanced.zip  
    --prefix=sitepoint-git-advanced/ -9 HEAD
```

`git archive`自动将存储库导出到 ZIP 存档文件中，而不是将结果发送到 stdout。使用 ZIP 时，您还可以使用开关`-0`到`-9`来指定压缩级别，如果生成的文件相当大，这将非常方便。

### 来自远程 Repos 的存档

不过，本地回购并不是我们可以存档的唯一类型的存储库。使用`--remote`开关，我们能够从 Gitosis 和我们团队可能用来存储集体代码的其他 Git 服务器中归档存储库。

下面的示例试图克隆 Joind。在项目中。不幸的是，老实说，它不起作用(从搜索论坛来看，我认为 Github 故意禁用了它，以帮助他们确保更好的用户体验)，但我在这里包含了它，以向您展示该命令的一个示例。

```
$ git archive --remote=git://github.com/settermjd/joind.in.git 
    --format=tar --prefix=joind.in/ HEAD | 
    gzip > joindin.tar.gz
```

## 基本重置基础

现在让我们进入使用 Git 的一些更有趣的方面。根据 Git SCM 手册，使用 rebase 命令，我们可以“获取一个分支上提交的所有更改，并在另一个分支上重放它们。”简而言之，这允许我们以一种强大而灵活的方式合并分支和变更。也正是在这里，一场争论开始了。如果你熟悉其他版本控制工具，比如 CVS、SVN 和 Mercurial，那么你可能已经习惯了一种一旦提交就不能再更改的模式。使用 Git，情况就不一定是这样了。

### 重新排序提交

假设我们的提交顺序并不完全符合它应有的顺序。我们已经进行了一系列的提交，但是按顺序进行会更实际。也许它们都与同一个代码模块相关，中间有一个不相关的模块发生了变化。这不是一个节目停止，但当几个月后回顾历史时，让他们在一起可能会有所帮助。

```
$ git rebase -i HEAD~5
```

使用上面的命令，我启动了一个包含最近五次提交的 rebase 会话，由`git rebase`以相反的顺序显示:

```
pick da4fc8e changing default to add iteration
pick 003ab61 adding even iteration script
pick 701e132 reworking directory structure
pick c722b9f removing old files
pick 3a10e1c Added in files that were forgotten in the previous commit
```

假设提交 5 (3a10e1c)应该在提交 2 (003ab61)之后。我们可以在编辑器中更改提交顺序，如下所示:

```
pick da4fc8e changing default to add iteration
pick 003ab61 adding even iteration script
pick 3a10e1c Added in files that were forgotten in the previous commit
pick 701e132 reworking directory structure
pick c722b9f removing old files
```

保存缓冲区并退出编辑器，我们会收到以下内容:

```
Successfully rebased and updated refs/heads/testing.
```

运行`git log`现在会产生以下输出:

```
commit ab97ffb2fe87c25ca48116ed03cdbe486dc0abb5
Author: Matthew Setter 
Date:   Fri Jan 18 12:54:00 2012 +0000

    removing old files

commit 3b5d6c5b0c6a866f426ff5bf412508ce9d9ae864
Author: Matthew Setter 
Date:   Fri Jan 18 12:53:30 2012 +0000

    reworking directory structure

commit d9ab87677f8e5692b649b650a6de6ce836c72531
Author: Matthew Setter 
Date:   Mon Jan 21 10:11:33 2012 +0100

    Added in files that were forgotten in the previous commit

commit 003ab6124b7f7a59be4fe152df28b9de706383ed
Author: Matthew Setter 
Date:   Fri Jan 18 12:44:08 2012 +0000

    adding even iteration script
```

通过新的提交顺序，我们可以看到重新排序是成功的。Git 返回到指定的第一次提交，然后用我们刚刚指定的更改重放历史。

### 更改提交消息

如果您习惯于以一种特殊的方式编写提交消息，例如，您从前一份工作中学到的，但是现在您在一个新的组织中，他们以不同的方式编写提交消息，该怎么办？您刚刚提交了一个变更，高级开发人员在查看它时，看到了这条消息，并希望对它进行更改以符合公司标准。使用 Git，您可以返回并更改消息。

首先，我们必须通过发出以下命令来启动 rebase 过程:

```
$ git rebase -i HEAD~2
```

我们要求在`HEAD`之前的两次修订中重新调整基础。终端在编辑缓冲区中显示以下内容:

```
pick c722b9f removing old files
pick bcc1dff adding some files

# Rebase 701e132..bcc1dff onto 701e132
#
# Commands:
#
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for ammending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash" but discard the commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# If you remove a line here THAT COMMIT WILL BE LOST.
# However, if you remove everything, the rebase will be aborted.
#
```

我们在顶部看到两个提交。在下面的命令列表中，我们有`reword`选项，它允许我们更改提交消息。

这里我把`pick`改成了`reword`:

```
pick c722b9f removing old files
reword bcc1dff adding some files

# Rebase 701e132..bcc1dff onto 701e132
#
# Commands:
#
...
```

保存并退出缓冲区将允许我们重新指定提交消息。

### 合并一组提交

在提交实践中，您可能非常勤奋，确保提交仅包括单个变更或出于一个目的的一组相关变更。但是总有一些东西会打乱我们正常的节奏。

假设我们提交了一个变更，做了一些工作，又提交了一个，然后又做了一些工作，又提交了一个。在第三次提交之后，我们发现提交 1 和 3 实际上与同一个更新相关，应该一起提交。

如果我们与 SVN 或 CVS 合作，那就是了；修订历史将被修复。使用 Git，我们可以打开历史记录，在提交 2 之前将提交 1 和 3 合并成一个提交。让我们走一遍流程。

我们从查看最后五个提交开始重定基础过程，如下所示:

```
$ git rebase -i HEAD~5

pick da4fc8e changing default to add iteration
pick 003ab61 adding even iteration script
pick d9ab876 Added in files that were forgotten in the previous commit
pick 3b5d6c5 reworking directory structure
pick ab97ffb removing old files
```

然后我们改变顺序，使 3 在 2 之前，如下所示:

```
pick da4fc8e changing default to add iteration
pick d9ab876 Added in files that were forgotten in the previous commit
pick 003ab61 adding even iteration script
pick 3b5d6c5 reworking directory structure
pick ab97ffb removing old files
```

然后，在新的提交号 2 上，我们将`pick`更改为`squash`:

```
pick da4fc8e changing default to add iteration
squash d9ab876 Added in files that were forgotten in the previous commit
pick 003ab61 adding even iteration script
pick 3b5d6c5 reworking directory structure
pick ab97ffb removing old files
```

然后我们再次进入编辑器模式，显示之前的提交消息。我们添加了一个新的提交消息，在两个提交合并之前对它们进行总结。因此，我更改以下内容:

```
# This is a combination of 2 commits.
# The first commit's message is: 

changing default to add iteration

# This is the 2nd commit message:

Added in files that were forgotten in the previous commit
```

有了新消息:

```
# This is a combination of 2 commits.
# The first commit's message is: 

Merging two changes together as they should be together.
```

退出编辑器后，重定基础过程完成。

通过再次非破坏性地启动 rebase 过程，我们看到两个提交已经被合并。我们现在有五个提交，第一个是前一个之前的一个:

```
pick f5cac8d adding top documentation
pick e50970f changing default to add iteration
pick 3c2b3a5 adding even iteration script
pick 3b136c1 reworking directory structure
pick 01ad46f removing old files
```

### 拆分一组提交

但是如果我们想反方向走呢？如果有一个年轻的开发人员可能有点粗心，或者如果你自己有点不守纪律呢？我知道我时不时会遭受这种痛苦。

比方说，我们有一个提交，其中包含一些我们刚刚因为时间晚了或我们很急而加入的更改。现在我们是新来的，想收拾残局。我们如何做到这一点？

```
$ git commit -m "need to finish quickly"
[testing e6a98bb] need to finish quickly
 0 files changed
 create mode 100644 about.php
 create mode 100644 contact-us.php
 create mode 100644 default.php
 create mode 100644 login.php
```

在上面的例子中，我提交了一组文件，三个业务模块页面(`about.php`、`contact-us.php`和`default.php`)和一个用户模块页面(`login.php`)。让我们把他们分开，把事情做好。

从最后 3 次提交开始 rebase，我们看到以下内容:

```
pick 3b136c1 reworking directory structure
pick 01ad46f removing old files
pick e6a98bb need to finish quickly
```

在最后一次提交(e6a98bb)时将`pick`更改为`edit`,并退出编辑器以接收以下输出:

```
Stopped at e6a98bb... need to finish quickly
You can amend the commit now, with

    git commit --amend

Once you are satisfied with your changes, run

    git rebase --continue
```

使用`git reset HEAD^`我们回卷提交，取消文件的暂存。现在我们能够正确地提交它们了，在两次单独的提交中添加业务和用户页面。

```
$ git add about.php contact-us.php default.php
$ git commit -m "Adding new business pages"
[detached HEAD a189c9b] Adding new business pages
 0 files changed
 create mode 100644 about.php
 create mode 100644 contact-us.php
 create mode 100644 default.php

$ git add login.php
$ git commit -m "adding new user login page"
[detached HEAD 9efcab9] adding new user login page
 0 files changed
 create mode 100644 login.php
```

然后，通过用`--continue`开关运行`git rebase`来结束:

```
$ git rebase --continue
Successfully rebased and updated refs/heads/testing.
```

## 一句警告

现在引用一句经常被引用的话:

> 权力越大，责任越大

请记住，仅仅因为你能做某事，并不意味着你应该做。特别是如果您在一个团队环境中协作，如果您在您的存储库中进行这些更改，然后与主存储库合并，您很可能会与您的团队混淆。如果有正当的理由，而且不经常发生，那就没问题。但如果这种情况反复发生，我相信你不会受欢迎太久。

此外，您还需要了解这些类型的更改可能会给代码带来的潜在冲突。虽然这些功能可能很有趣，甚至令人兴奋，但要意识到你所做的事情的后果。

## 结论

我希望在阅读完这篇文章之后，您能够看到 Git 提供了多么强大的功能和灵活性，但是您也应该明白，在拥有这种功能的同时，您还必须了解何时以及何时不应该应用它。

Git-time 快乐，在评论中分享你的想法和观点，这样我们都能受益。要了解更多信息，这里有一些供进一步阅读的链接:

*   [去网站](http://git-scm.com/ "Git")
*   [Github 帮助](https://help.github.com/ "github: help")
*   [AlBlue 的博客——Git 本周提示:重置基础](http://alblue.bandlem.com/2011/06/git-tip-of-week-rebasing.html "AlBlue's Blog - Git Tip of the Week: Rebasing")
*   斯科特·沙孔著
*   Travis Swicegood 的使用 Git 的实用版本控制

## 分享这篇文章