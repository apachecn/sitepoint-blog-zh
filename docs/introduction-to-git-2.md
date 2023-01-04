# Git 简介，第 2 部分

> 原文：<https://www.sitepoint.com/introduction-to-git-2/>

在本系列的第 1 部分中，我介绍了使用 Git 的基础知识——创建存储库、添加文件、提交文件，以及使用 Git 的 log 和 diff 工具查看变更的时间表。这一部分将继续讨论一些更高级的主题:恢复变更，创建分支，以及将变更从一个分支合并到另一个分支。

## 搞砸后恢复更改

我们举一个经典的例子，涉及一些无意删除的作品。你已经编辑了一个源文件，做了一个简单的修改，你正准备点击`Ctrl` + `S`(假设你没有使用 vi)来保存你的工作，这时你的手掌划过笔记本电脑的触摸板，导致文件的一大块被选中。然后你无意中按了其他键，导致选择清除了你的工作，在你知道发生了什么之前，`Ctrl` + `S`保存了你的工作。如果您使用 Git，这很容易恢复。

### 将文件恢复到以前提交的版本

这种情况假设您尚未提交损坏的文件。使用第一部分中的`config.php`文件，假设您已经清除了大部分数据库配置数组，使其看起来像这样:

```
<?php
$database = array(
    "database"  => "new_project");
```

您可以运行`git diff`来查看发生了什么变化；驱动程序、主机、用户名和密码值都不见了。

```
-    "driver"   => "mysql", 
-    "host"     => "locahost", 
-    "username" => "Foo", 
-    "password" => "pass",
```

如何恢复文件？用`git checkout config.php`。撤消某些更改的最简单方法是将文件恢复到上次提交时的状态。

有时，您会想要撤消一些更改，并将文件恢复到许多次提交之前的状态。如果您只想恢复单个文件而不是提交中的所有文件，您可以使用`git checkout`,后跟包含您想要恢复的文件版本的提交散列和文件名。

假设您运行了`git log config.php`，结果发现提交`c1d55debc7be8f50e363df462f84672ad029b703`包含了您想要恢复的`foo.php`的版本。现在您可以运行:

```
sean@beerhaus:~/new_project$ git checkout c1d55debc7be8f50e363df462f84672ad029b703 config.php
sean@beerhaus:~/new_project$ git commit config.php -m "Reverted to previous revision."
[master b125806] Reverted to previous revision.
 1 files changed, 1 insertions(+), 1 deletions(-)

```

您的工作副本`config.php`将被先前提交的版本覆盖。但是在运行 checkout 命令时要小心，因为它会覆盖文件，并且您不能恢复工作副本…您只能恢复已经提交并被 Git 跟踪的版本。

### 将所有内容恢复到以前的提交

恢复 Git 中更改的另一种方法是将所有内容恢复到特定的提交。当您恢复提交时，您将恢复其中的每个文件，无论提交包含一个还是一百个文件。

让我们创建一个假更改的时间表来说明恢复整个提交。在您的测试存储库中创建一个名为`foo.php`的文件。

```
<?php
// this is our original, pristine version
```

添加新文件并提交:

```
sean@beerhaus:~/new_project$ git add foo.php
sean@beerhaus:~/new_project$ git commit -am "This is the original version."
[master 4d7add0] This is the original version.
 1 files changed, 2 insertions(+), 0 deletions(-)
 create mode 100644 foo.php
```

对`foo.php`进行第二次修改，如下所示:

```
<?php
// this is our original, pristine version
// this is a version that might be useful
```

再次提交更改。

```
sean@beerhaus:~/new_project$ git commit -am "Added a comment."
[master 9e87750] Added a comment.
 1 files changed, 1 insertions(+), 0 deletions(-)
```

再做一个你不想保留的改变:

```
<?php 
while (true) {
    exec("yes");
}
```

```
sean@beerhaus:~/new_project$ git commit -am "Added a really bad version of the file."
[master d4cb9dd] Added a really bad version of the file.
 1 files changed, 3 insertions(+), 2 deletions(-)
```

一旦提交了这三个更改，请查看日志。因为这里您主要关注提交散列，所以您可以向`git log`传递一个格式化选项来删除一些额外的信息。

```
sean@beerhaus:~/new_project$ git log --pretty=oneline foo.php
d4cb9ddbcb72cccf61e8682ecc2485a9a7a57a29 Added a really bad version of the file.
9e877501c2ad74d177a77b88eb66bfa8be7c18bd Added a comment.
4d7add037b4b38faba3392be988e0e9813daa8a7 This is the original version.
```

要撤销上一次提交，可以使用`git revert`。该命令需要一个引用需要还原的错误提交的参数；因为您只想撤销最近的提交，所以可以使用`HEAD`作为参数。在这个上下文中，`HEAD`仅仅是对最后一次提交记录的引用。

```
sean@beerhaus:~/new_project$ git revert HEAD
```

默认编辑器将打开，允许您输入消息。在我的例子中，已经提供了一个建议的消息。

```
Revert "Added a really bad version of the file."

This reverts commit d4cb9ddbcb72cccf61e8682ecc2485a9a7a57a29.

# Please enter the commit message for your changes. Lines starting 
# with '#' will be ignored, and an empty message aborts the commit. 
# On branch master 
# Changes to be committed: 
#   (use "git reset HEAD ..." to unstage) 
# 
#       modified:   foo.php 
# 
```

一旦`git revert`完成，再看看你的日志。

```
sean@beerhaus:~/new_project$ git log --pretty=oneline foo.php
264208850a690ba1dc6de13c701390d395706830 Revert "Added a really bad version of the file."
d4cb9ddbcb72cccf61e8682ecc2485a9a7a57a29 Added a really bad version of the file.
9e877501c2ad74d177a77b88eb66bfa8be7c18bd Added a comment.
4d7add037b4b38faba3392be988e0e9813daa8a7 This is the original version.
```

进行了新的提交，其中`foo.php`的内容显示为错误提交之前的内容。

现在让我们再试一次，只是这次你将把你的代码库恢复到`9e877501c2ad74d177a77b88eb66bfa8be7c18bd`。

```
sean@beerhaus:~/new_project$ git revert 9e877501c2ad74d177a77b88eb66bfa8be7c18bd
```

编辑器再次打开，让您指定提交消息。完成后，再次显示日志。

```
sean@beerhaus:~/new_project$ git log --pretty=oneline foo.php
522ef8c7b70b1dcb9c21ecb51f1f025323b18da2 Revert "Added a comment."
264208850a690ba1dc6de13c701390d395706830 Revert "Added a really bad version of the file."
d4cb9ddbcb72cccf61e8682ecc2485a9a7a57a29 Added a really bad version of the file.
9e877501c2ad74d177a77b88eb66bfa8be7c18bd Added a comment.
4d7add037b4b38faba3392be988e0e9813daa8a7 This is the original version.
```

## 分支和合并

分支和合并是许多版本控制系统共有的概念。这个想法是你有你的发展主线，有时被称为*主干*或*稳定*。这是您希望将所有源代码保持在工作、可发布、稳定状态的地方。你不希望在这条主线上强加各种各样的实验性代码，不是因为你想阻止新的想法或实验，而是因为你希望这条线干净可靠。应该有另一行用于实验目的，这就是分支。

### 使用分支

一个*分支*就像是你主要的、稳定的开发路线的副本。在你自己的分支范围内，你可以做任何你想做的事情，它不会影响主线中的任何东西，直到你将你的分支代码合并回主分支。

在 Git 中，从第一次创建存储库开始，您就已经在使用分支了。这个分支叫做“主”，你一直在用它。不相信我？运行`git branch`并观察它的输出。

```
sean@beerhaus:~/new_project$ git branch
* master
```

星号指向您当前正在处理的分支。

大部分情况下，master 是作为我之前说的稳定或者主要发展线，但也不一定。您可以轻松地创建自己的名为“stable”的分支，并将其指定为主分支。创建分支最简单的方法是使用`git branch <name>`。

让我们在存储库中创建一个名为“experimental”的分支。

```
sean@beerhaus:~/new_project$ git branch experimental
sean@beerhaus:~/new_project$ git branch
  experimental
* master
```

注意星号仍然显示主服务器是活动分支。您只创建了新的分支；你还没有切换到它。要切换到新的分支，使用`git checkout`。

```
sean@beerhaus:~/new_project$ git checkout experimental
Switched to branch 'experimental'
sean@beerhaus:~/new_project$ git branch
* experimental
  master
```

或者，您可以使用`git checkout -b experiment`来创建新的分支，并一次性切换到它。我更喜欢使用这个快捷方式，而不是我自己的两个命令过程。

由于您在创建实验分支时使用的是主分支，因此实验分支将是主分支创建时的直接副本。

现在在编辑器中打开`foo.php`,添加一条注释，如下所示:

```
<?php
// this is our original, pristine version
// comment from the experimental branch
```

然后提交更改。

```
sean@beerhaus:~/new_project$ git commit -am "Added branch comment."
[experimental 8632135] Added branch comment.
 1 files changed, 1 insertions(+), 0 deletions(-)
```

一旦您提交了变更，切换回主分支。

```
sean@beerhaus:~/new_project$ git checkout master
Switched to branch 'master'
```

看一看`foo.php`,您会注意到您在实验分支中所做的更改并没有出现。这是因为两个分支是两种不同的工作环境。在合并之前，一个分支的更改不会出现在另一个分支中。

### 合并分支

`git merge`命令采用`git merge <source>`的形式，其中`<source>`是您合并变更的分支。运行合并命令的分支被暗示为目标。

所以让我们把你的分支从实验分支带到主分支。

```
sean@beerhaus:~/new_project$ git merge experimental
Updating 522ef8c..8632135
Fast-forward
 foo.php |    1 +
 1 files changed, 1 insertions(+), 0 deletions(-)
```

当你这次看`foo.php`时，你会看到实验部门发生的变化。

现在让我们从另一个方向开始。当你还在 master 中时，对`foo.php`做一个改变，你将合并到实验分支中。

```
sean@beerhaus:~/new_project$ git commit -am "Added function to master."
[master cbe5cba] Added function to master.
 1 files changed, 4 insertions(+), 0 deletions(-)
```

切换到您的实验分支，合并来自 master 的更改。

```
sean@beerhaus:~/new_project$ git checkout experimental
Switched to branch 'experimental'
sean@beerhaus:~/new_project$ git merge master
Updating 8632135..cbe5cba
Fast-forward
 foo.php |    4 ++++
 1 files changed, 4 insertions(+), 0 deletions(-)
```

看一下`foo.php`看到你在 master 里面加的功能现在在实验分支。

## 处理合并冲突

最终你会在合并时遇到冲突。当您将变更从一个分支合并到另一个分支时，如果没有人工干预，Git 无法完成合并，就会发生*合并冲突*。当您在一个分支中编辑了文件的第 80 行，Fred 在另一个分支中编辑了同一文件的第 80 行，然后您试图将 Fred 的更改与您的更改合并时，就会发生这种情况。Git 知道代码的相同部分已经被更改，但是由我们人类来决定应该保留哪个更改。

你可以很容易地创建一个合并冲突的情况来证明这一点。用 Git 切换到主分支，在编辑器中打开`foo.php`。

```
sean@beerhaus:~/new_project$ git checkout master
Switched to branch 'master'
```

在第 9 行添加这个函数:

```
function master() {
    print "This is the master branch.n";
}
```

保存文件并提交您的更改。

```
sean@beerhaus:~/new_project$ git commit -am "Added master function."
[master 4f97280] Added master function.
 1 files changed, 4 insertions(+), 0 deletions(-)
```

切换回实验分支。

```
sean@beerhaus:~/new_project$ git checkout experimental
Switched to branch 'experimental'
```

在编辑器中再次打开`foo.php`,并将该函数添加到第 9 行:

```
function experimental() {
    print "This is the experimental branch.n";
}
```

提交更改。

```
sean@beerhaus:~/new_project$ git commit -am "Added experimental function."
[experimental c5358cc] Added experimental function.
 1 files changed, 4 insertions(+), 0 deletions(-)
```

现在，当你还在实验分支中时，合并来自 master 的更改。您已经在两个分支中编辑了相同文件的相同行，所以您可以预料到某种冲突。

```
sean@beerhaus:~/new_project$ git merge master
Auto-merging foo.php
CONFLICT (content): Merge conflict in foo.php
Automatic merge failed; fix conflicts and then commit the result.
```

现在，您必须打开文件并手动解决冲突。在`foo.php`中，你会看到这样的内容:

```
<<<<<<< HEAD
function experimental() {
    print "This is the experimental branch.n";
=======
function master() {
    print "This is the master branch.n";
>>>>>>> master
}
```

以`<<<<<<< HEAD`开始并以`=======`结束的第一部分是您的最后一次提交(`HEAD`)。从`=======`到`>>>>>>> master`的部分是来自主分支的变更。

让我们假设您已经决定保留来自 master 的更改，并丢弃实验性的更改。你要做的就是删除冲突符号和来自实验的线条。`foo.php`的那个部分现在应该只有主函数了。

```
<?php
// this is our original, pristine version
// comment from the experimental branch

function bar() {
    print "This is bar.n";
}

function master() {
    print "This is the master branch.n";
}
```

完成后，保存文件并提交解决的冲突。

```
sean@beerhaus:~/new_project$ git commit -am "Fixed a merge conflict from master to experimental."
[experimental 3871a1d] Fixed a merge conflict from master to experimental.
```

这只是一个简单的例子。在专业软件开发的真实世界中，你可以有 20 个不同的开发人员，他们都将各自分支的变更合并到主开发中。每次发生这种情况时，您都希望将最新的更改从主分支合并回您的单独分支。冲突是会发生的，尽管 Git 是一个很好的工具，但是在这种情况下，它不能解决所有的问题。有时候，良好的老式人际沟通是避免这种规模冲突的最佳工具。

## 摘要

这就是怎么做的。您已经看到了如何从过程中犯下的任何错误中恢复过来，如何创建分支，以便您可以与同一项目的其他开发人员并行工作，或者只是尝试新的想法而不危及主线的稳定性，以及最终如何将其他人的编辑合并到您自己的分支中。本文涵盖的三个主题——恢复错误、分支和合并——以及本系列的第一部分为您提供了开始使用 Git 管理项目的知识。如果你以前从未使用过版本控制软件，我希望我已经说服你开始了，如果你还在使用 Subversion 的 CVS，也许你可以给 Git 一个尝试。

## 分享这篇文章