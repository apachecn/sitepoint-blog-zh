# Git 2.0 有什么新特性？

> 原文：<https://www.sitepoint.com/whats-new-git-2-0/>

五月底，Git 的主要开发者 Junio C Hamano 在他们的邮件列表上宣布了期待已久的 Git 2.0 版本。如果你浏览一下[的发布说明](https://git.kernel.org/cgit/git/git.git/tree/Documentation/RelNotes/2.0.0.txt)，你会发现有相当多的变化受到了社区的欢迎。

![Git 2.0](img/1890d6efe665c191f70ac287091d5f48.png)

在本文中，我将概述这些变化。我们将会看到两个巨大的变化正在掀起波澜，接下来是一些其他的变化。

## `git push`默认行为

如果您在项目中使用 Git，您应该知道推送代码的一般格式如下:

```
git push [remote_name] [local_branch]:[remote_branch]
```

如果您在习惯上遵守纪律并使用这种语法，您甚至不会注意到 Git 中的这一变化。但是，如果您很懒，只是执行了`git push`，您可能会收到以下消息:

```
warning: push.default is unset; its implicit value is changing in 
 Git 2.0 from 'matching' to 'simple'. To squelch this message 
 and maintain the current behavior after the default changes, use: 

   git config --global push.default matching

 To squelch this message and adopt the new behavior now, use: 

   git config --global push.default simple
```

在 Git 1.x 中，默认行为被设置为`matching`。这意味着您的所有本地分支(在远程上有匹配的分支)都将被推送。然而，在 Git 2.0 中，缺省值被更改为`simple`，这将**仅将您当前的分支**推送到同名的远程分支。如果你想继续使用`matching`，就像在 1.x 中一样，你可以改变默认设置。

## 对`git add`的更改

Git 2.0 在`git add`命令中带来了一些变化。

### 取消跟踪删除的文件

当您删除文件时，您通常会在`git status`上发现以下输出:

```
# On branch master
  # Changes not staged for commit:
  #   (use "git add/rm <file>..." to update what will be committed)
  #   (use "git checkout -- <file>..." to discard changes in working directory)
  #
  #   deleted:    my_file1
  #   deleted:    my_file2
```

在 Git 1.x 中，`git add`忽略从您的存储库中删除的文件，您必须[分别删除它们](http://stackoverflow.com/questions/6004453/how-to-remove-multiple-deleted-files-in-git-repository)。在 Git 2.0 中，`git add dir/`将会注意到被删除的文件，并为您的提交准备变更。如果您只想存放新的或修改过的文件，可以添加`--ignore-removal`。

### `git add -u`

普通 Git 用户经常使用`git add -u`命令。该命令将所有跟踪文件中的更改转移到提交中。然而，在 Git 1.x 中，commit 的一个缺点是它在运行命令的目录中的文件上工作。对于 Git 2.0，这个命令是一个树范围的操作，这意味着它在整个存储库中搜索更改的文件并添加它们，而不管您在哪里运行这个命令。

如果这令人困惑，让我试着用一个例子来解释。假设您的存储库中有两个有变更的文件— `README`和`src/my_file`。如果您将目录更改为`src`，然后运行`git add -u`，在 Git 1.x 中只会添加`my_file`。在 Git 2.0 中，两个文件都被添加。如果从父目录运行该命令，则无论版本如何，都会添加这两个文件。

## 杂项变更

最后，我们将在此总结一些您可能希望了解的其他变化。

### `git pull`

Git pull 现在有了一个新的配置变量`pull.ff`，它可以被设置为只接受[快进分支](https://confluence.atlassian.com/display/BITBUCKET/Git+fast+forwards+and+branch+management)，这消除了合并，因此不会导致冲突。

### `git svn`

如果您在其他版本控制系统中使用 Git，您可能会遇到命令`git svn`，它将作为所有 Subversion 相关函数的基本命令[。](http://git-scm.com/book/en/Git-and-Other-Systems-Git-and-Subversion)

在 Git 1.x 中，`git svn`的远程跟踪分支是在`refs/remotes`下创建的。但是，它们将在`refs/remotes/origin`下创建，这可以通过`--prefix`选项进行更改。`git svn`并不是一个受欢迎的特性，因为大多数组织已经转移到 Git，这可能不会影响很多人。

### `.gitignore`文件

文件中的尾随空格被警告并被忽略，除非它们被引用。这种改变不是向后兼容的。

### `git grep`

Git 中的`grep`命令使您能够在所有分支中进行搜索。当给定了`-h`(无标题)和`-c`(计数)选项时，你现在可以让它表现得像一个本地 grep。

### 远程助手接口的移除

Mercurial 和 Bazaar 的远程助手接口位于`contrib/`下，不再是 Git 的一部分，现在作为第三方插件作为独立的存储库维护。

## 展望未来

Git 无疑是最流行的版本控制管理系统，HackerNews 上关于发布说明的讨论证明了这一点。Git 2.0 有相当多的新特性，但 Git 1.x 的每个版本也是如此。有一些向后不兼容的变化，但它们可能不会影响您。如果使用 Git，未来看起来很光明。如果没有，你什么时候加入俱乐部？

## 分享这篇文章