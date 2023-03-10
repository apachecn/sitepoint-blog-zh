# 颠覆搭上了末班车

> 原文：<https://www.sitepoint.com/subversion-gits-the-last-train/>

刚刚宣布 Rails-core [源代码版本控制库](http://en.wikipedia.org/wiki/Revision_control)将从 [Subversion](http://subversion.tigris.org/) (SVN)转移到 [git](http://git.or.cz/) ，他们将使用 [Lighthouse](http://www.lighthouseapp.com/) 进行错误跟踪，而不是 [trac](http://trac.edgewall.org/) 。这对你有影响吗？不直接(除非你喜欢玩 edge-rails)，但这是一个谈论源代码库和 subversion 与 git 之间的一些差异的好借口。

### 什么是源代码版本控制？

源代码存储库为您提供了一个存储代码的中心位置——它们通常会在维护您的“提交”历史(将代码保存回存储库的操作)的同时完成这一工作。这意味着您能够回滚您所做的任何更改。如果你曾经过分热衷于使用`rm -rf`命令，你可能会理解为什么这可能会很方便。

另一个很酷的特性是能够分支(或者复制)你的代码——这允许你保持，比如说，一个开发和一个稳定的版本。如果您在您的产品版本中发现了一个需要修补的 bug，您可以将变更合并回开发分支，从而避免[回归 bug](http://en.wikipedia.org/wiki/Regression_testing)。

有许多版本控制系统，subversion 成为近年来最流行的系统之一。不过最近，git 似乎获得了一些关注，尤其是在开源社区中，因为它具有本地存储库的特性。在 subversion 中，当您提交一个更改时，每个其他用户的本地“工作副本”都必须更新以反映这些更改，因此，如果您正在进行试验并想要保存一个版本，但这些更改还没有准备好，您就有点卡住了。然而，Git 允许您保留一个本地存储库，这不会影响公共存储库，除非您明确地告诉它这样做。

git 中使用的许多命令与 SVN 相同或相似(名称可能略有变化)，查看本指南，如果你喜欢冒险，你可以使用 [git-svn](http://www.kernel.org/pub/software/scm/git/docs/git-svn.html) 将 SVN 分支与 git 同步。

## 分享这篇文章