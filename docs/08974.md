# Git 简介，第 1 部分

> 原文：<https://www.sitepoint.com/introduction-to-git-1/>

Git 是一个(分布式)版本控制系统。那是什么？*版本控制系统*是允许程序员跟踪和管理项目历史的软件，其中这样的项目可以是单个文件、一组文件，或者是由来自世界各地的多个程序员参与的整个开源项目。

“分布式”的区别意味着没有一个中央服务器来存放项目的源代码和历史。当一个 Git 项目被克隆时(“clone”是 Git 对项目初始检验的说法)，您会收到从当前状态到时间开始的整个项目历史。将整个项目本地存储在您的计算机上，而没有一个中央服务器来协调一切，这使得合并、区分和日志历史查找速度极快。

您可能听说过的一些更常用的版本控制系统是 CVS 和 Subversion。本教程将采用“忘记你所知道的关于 CVS 或 Subversion 的一切”的方法。作为一个在专业领域使用过所有这三个系统的人，我可以证明 CVS 或 Subversion 的一些知识在使用 Git 时是有用的，但不是必需的。学习 Git 的最好方法是开始使用 Git。

## 为什么要用 Git？

这是一个完美的 cromulent 问题。如果我只是一个人在自己的网站上工作，我为什么要为版本控制而烦恼呢？我有很好的记忆力，我知道别人不会重写我的代码。

也许你有一套为客户开发的网站文件，看起来像这样。

```
sean@beerhaus:~/Workspace$ ls
images/  templates/  index.php.save1  index.php.save3
js/      index.php   index.php.save2  index.php.save4
```

这里，`index.php`是您当前的文件，`index.php.save#`是前几天工作的一组备份。

现在你正在编辑`index.php`。你刚刚添加了一个非常漂亮的功能，这是一个迟到但值得的灵感时刻。明智的做法是，对这个文件做一个名为`index.php.save5`的备份。第二天你回到`index.php`的工作中，你发现昨天的新功能并不实用，于是你把它从文件中删除了。几周过去了，此时你已经存了`index.php.save27`。项目的当前状态非常稳定，因此您不再需要之前保存的 27 个文件，因此您将它们全部删除。

在你清理的几天后，你觉得`index.php.save5`的那个特性值得第二次机会，但是现在它永远消失了。当然，你可能有一些额外的备份副本，你甚至可能有一个文件恢复工具。但是，这真的是管理源代码的最佳方式吗？

当你在你的项目中增加更多的人时，问题会变得更糟。即使两个人在同一个项目上工作，也会有一些无意中删除和覆盖的文件。也许你来回地用电子邮件发送源代码，你可以查找旧的电子邮件来获得你刚刚删除的文件的存档。祝你好运。

在这一部分，100 个教程中有 99 个会不出所料地重复“输入 Git”这句陈词滥调。

## 如何使用 Git

使用 Git 的第一步是获取 Git。如果你正在使用各种各样的 Linux，这是相当容易的。(相关事实:Git 最初是由创建内核的同一个人 Linus Torvalds 为 Linux 内核上的分布式开发而设计和开发的。)无论您运行的是什么版本的 Linux，您将需要的包很可能被称为“git”或“git-core”。如果你使用的是 Windows 或 Mac，或者只是想从源代码构建 Git，参见[www.git-scm.com](http://www.git-scm.com/)获取下载和安装选项。

## 创建存储库

一旦你安装了 Git，你会想要添加一些关于你自己的信息。当查看 Git 日志中的历史信息时，这将有助于确定谁提交了什么代码。

```
sean@beerhaus:~$ git config --global user.name "FirstName LastName"
sean@beerhaus:~$ git config --global user.email "your@email"
```

现在是创建存储库的时候了。有两种方法可以做到这一点，但本质上是一个命令。如果您从零开始一个新项目，并且您希望 Git 在它的整个生命周期中照看它，那么您可以创建一个包含所有项目文件的目录，并初始化该目录以便与 Git 一起使用:

```
sean@beerhaus:~/$ mkdir new_project
sean@beerhaus:~/$ cd new_project/
sean@beerhaus:~/new_project$ git init
Initialized empty Git repository in /home/sean/new_project/.git/
```

现在，您已经有一个空的 Git 存储库可以使用了。如果您运行新目录中所有隐藏文件的列表，您会注意到隐藏目录`.git`已创建:

```
sean@beerhaus:~/new_project$ ls -al
total 12
drwxr-xr-x 3 sean sean 4096 2011-11-19 16:09 .
drwxr-xr-x 5 sean sean 4096 2011-11-19 16:09 ..
drwxr-xr-x 7 sean sean 4096 2011-11-19 16:09 .git
```

`.git`目录是 Git 存储跟踪存储库所需的所有信息和历史的地方，所以最好不要管它。

如果你有一个现有源代码的目录，也可以使用。简单地输入您的目录并运行`git init`,就像我在这个例子中做的那样。

## 添加/暂存文件

您应该还在刚刚为 Git 初始化的`new_project`目录中。在版本控制术语中，这现在被称为一个*工作目录*，意味着它是一个在一些版本控制系统监视下的目录。

让我们创建一些文件供 Git 跟踪，从一个配置文件开始。在您选择的编辑器中创建包含以下内容的文件`config.php`:

```
<?php
$database = array(
    "driver"   => "mysql",
    "host"     => "locahost",
    "username" => "user",
    "password" => "pass",
    "database" => "new_project");
```

一旦您将文件保存为`config.php`，我们就可以运行另一个 Git 命令`git status`。

```
sean@beerhaus:~/new_project$ git status
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	config.php
nothing added to commit but untracked files present (use "git add" to track)
```

Git 中的默认分支被称为 *master* 。这和其他版本控制系统中的“trunk”是一个思路。一个分支仅仅是 Git 正在跟踪的一条开发线。

*未被跟踪的文件*是存在于你的工作目录中，但是还没有被 Git 跟踪的文件，因为你还没有告诉 Git 去跟踪它们。

`git status`的输出也提供了有用的消息，比如下面一行的消息:

```
(use "git add <file>..." to include in what will be committed)
```

继续将`congif.php`文件添加到 Git 的跟踪文件列表中。

```
sean@beerhaus:~/new_project$ git add config.php
```

现在，如果你再次运行`git status`，你会看到发生了什么变化。

```
sean@beerhaus:~/new_project$ git status
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#	new file:   config.php
#
```

`config.php`现在正在被 Git 跟踪，你可以看到从“未跟踪文件”到“新文件”的变化。

使用 Git，以这种方式添加文件被称为 *staging* 。临时区域是添加文件之后但提交文件之前的步骤。术语*添加*不一定指新文件。它还可以表示对现有文件所做的一组更改，这些更改将包含在下一次提交中。staging area 允许我们在提交之前挑选要提交的文件。

## 提交文件

现在您可以提交文件了。提交文件就是告诉 Git 记录项目当前状态的“快照”。每次提交时，提供一条提交消息是很重要的。这些信息有助于提醒你自己(或其他人)每次改变的目的是什么。你应该保持简短，但内容丰富。

提供提交消息的最快方法是在`git commit`命令中使用`-m`选项。如果您在命令中不使用`-m`选项，将会弹出一个编辑器窗口，您可以在那里输入提交消息。

要配置将使用哪个编辑器，您可以运行`git config --global core.editor vim`。在我的例子中，我使用 vim，但 emacs、pico 或任何其他编辑器也可以。

```
sean@beerhaus:~/new_project$ git commit config.php -m "Initial commit. Added a configuration file."
[master (root-commit) c1d55de] Initial commit. Added a configuration file.
 1 files changed, 7 insertions(+), 0 deletions(-)
 create mode 100644 config.php
```

现在，如果您再次运行`git status`，您将得到一条消息，告诉您自上次提交以来没有新的更改。

```
sean@beerhaus:~/new_project$ git status
# On branch master
nothing to commit (working directory clean)
```

## 查看项目历史记录

`git log`命令是查看项目时间线的非常有用的工具。您可以看到每个提交的详细信息，包括日期和提交者。

```
sean@beerhaus:~/new_project$ git log
commit c1d55debc7be8f50e363df462f84672ad029b703
Author: FirstName LastName <your@email>
Date:   Sat Nov 19 16:45:35 2011 -0400

    Initial commit. Added a configuration file.
```

这里有几点需要注意:

1.  姓名和电子邮件地址是您通过`git config`提供的信息。
2.  提交消息是您在第一次提交中提供的消息。
3.  单词“commit”后面是一个 40 位的十六进制字符串。

这个由 40 个字符组成的十六进制数字字符串被称为*提交哈希*，这是指用于生成它的 SHA1 加密哈希函数。与提交相关的所有信息都存储在提交对象中。提交散列是应用于对象内容的散列函数的输出，然后用作该特定提交的唯一引用。

如果您想要查找单个提交的日志条目，可以提供提交哈希:

```
sean@beerhaus:~/new_project$ git log c1d55debc7be8f50e363df462f84672ad029b703
```

要查看提交中涉及了哪些文件，可以使用`--stat`选项:

```
sean@beerhaus:~/new_project$ git log --stat c1d55debc7be8f50e363df462f84672ad029b703
```

日志消息现在包含:

```
 config.php |    7 +++++++
 1 files changed, 7 insertions(+), 0 deletions(-)
```

## 查看提交差异

现在让我们对`config.php`做一些修改，看看`git diff`能提供什么。从一个简单的改变开始；将数据库用户名改为“Foo”。当您保存文件并运行`git status`时，这次您会看到`config.php`现在处于修改状态:

```
sean@beerhaus:~/new_project$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   config.php
#
no changes added to commit (use "git add" and/or "git commit -a")
```

要查看修改了什么，请使用`git diff`。

```
sean@beerhaus:~/new_project$ git diff
diff --git a/config.php b/config.php
index ab6e11d..02d30f8 100644
--- a/config.php
+++ b/config.php
@@ -2,6 +2,6 @@
 $database = array(
     "driver"   => "mysql",
     "host"     => "locahost",
-    "username" => "user",
+    "username" => "Foo",
     "password" => "pass",
     "database" => "new_project");
```

你的改变很容易被发现。前导减号告诉您该行已被删除，前导加号告诉您该行已被添加。

默认情况下，命令`git diff`会将项目的当前状态与之前的提交进行比较。您可以向`git diff`命令传递参数，比如提交散列或特殊指针`HEAD`，它指向您当前项目的最近一次提交。

继续提交更改后的文件，这样您就可以比较一些提交散列。

```
sean@beerhaus:~/new_project$ git commit -am "Changed database username to Foo."
[master e69a7f9] Changed database username to Foo.
 1 files changed, 1 insertions(+), 1 deletions(-)
```

当你这次运行`git log`时，你会看到一个变化的时间表。

```
sean@beerhaus:~/new_project$ git log
commit e69a7f9b3b55c116a5c2edf730cd03df7e093eda
Author: FirstName LastName <your@email>
Date:   Sun Nov 20 14:16:43 2011 -0400

    Changed database username to Foo.

commit c1d55debc7be8f50e363df462f84672ad029b703
Author: FirstName LastName <your@email>
Date:   Sat Nov 19 16:45:35 2011 -0400

    Initial commit. Added a configuration file.
```

当您使用两个提交散列运行`git diff`时，输出与您在最后一次提交之前查看的 diff 相同。

```
sean@beerhaus:~/new_project$ git diff c1d55debc7be8f50e363df462f84672ad029b703 e69a7f9b3b55c116a5c2edf730cd03df7e093eda
diff --git a/config.php b/config.php
index ab6e11d..02d30f8 100644
--- a/config.php
+++ b/config.php
@@ -2,6 +2,6 @@
 $database = array(
     "driver"   => "mysql",
     "host"     => "locahost",
-    "username" => "user",
+    "username" => "Foo",
     "password" => "pass",
     "database" => "new_project");
```

使用`--color`选项再试一次。这使得变化更容易被看到。

## 摘要

在习惯了 Git 和它的文件编辑周期`git add`和`git commit`之后，你将永远不会回到使用文件备份或者你最喜欢的编辑器的撤销特性来替代正确的版本控制的日子。在本文中，我向您展示了开始使用 Git 所需的基本命令。其他主题包括分支、合并和远程存储库也将随之而来。

## 分享这篇文章