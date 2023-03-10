# 跳转起点 Git:Git 中的分支

> 原文：<https://www.sitepoint.com/git-branching/>

![jsgit](img/9bfbd8758c7ef97681ace55a0205f366.png)

*以下是我们的书 [Jump Start Git](https://www.sitepoint.com/premium/books/jump-start-git) 的简短摘录，免费提供给 [SitePoint Premium 会员](https://www.sitepoint.com/premium/join)。印刷版在世界各地的商店都有出售，或者你可以在这里[订购](http://shop.oreilly.com/product/9780994182654.do)。我们希望你喜欢这个摘录，并发现它很有用。*

在第一章，我谈到了我曾经害怕在一个项目中尝试新事物。如果我尝试了一些雄心勃勃的东西，但它打破了之前一切正常的东西，那该怎么办？这个问题通过在 Git 中使用分支来解决。

## 什么是分支？

在一个项目中创建一个新的 **分支** 实质上意味着创建那个项目的一个新副本。你可以在不影响原件的情况下试验这个复制品。所以如果实验失败了，你可以放弃它，回到最初的——`master`分支。

但是如果实验成功，Git 很容易将实验元素合并到`master`中。如果在以后的某个阶段，你改变了主意，你可以很容易地回到合并之前的项目状态。

所以 Git 中的一个分支是独立的开发路径。您可以在一个分支中创建新的提交，而不会影响其他分支。这种使用分支的便利性是 Git 最好的特性之一。(虽然其他版本控制选项如 CVS 也有这个分支选项，但是在 [CVS](https://en.wikipedia.org/wiki/Concurrent_Versions_System) 上合并分支的体验是非常乏味的。如果您有过在其他版本控制系统中使用分支的经验，请确保在 Git 中使用分支是完全不同的。)

在 Git 中，默认情况下，您会发现自己在`master`分支中。“大师”这个名字并不意味着它有任何优势。只是习惯这么称呼而已。

### 注意:分支约定

尽管您可以自由地使用不同的分支作为 Git 中的基础分支，但是人们通常希望在`master`分支中找到特定项目的最新代码。

你可能会说，有了返回任何提交的能力，就不需要分支了。然而，想象一下这样一种情况，你需要向你的上级展示你的工作，同时还在开发一个新的、很酷的功能，这不是你已完成工作的一部分。由于分支被用来分离不同的想法，它使得你的库中的代码易于理解。此外，分支使您能够只保留`master`分支或主分支中的重要提交。

分支的另一个用途是让你能够同时处理多件事情，而不会互相干扰。假设您提交了`feature 1`进行审核，但是您的主管在审核之前需要一些时间。同时，你需要在`feature 2`上下功夫。在这个场景中，分支开始发挥作用。如果您在一个单独的分支上处理您的新想法，您总是可以切换回您先前的分支，以将存储库返回到其先前的状态，其中不包含任何与您的想法相关的代码。

现在让我们开始使用 Git 中的分支。要查看分支列表和您正在处理的当前分支，请运行以下命令:

```
git branch
```

如果您已经克隆了您的存储库或者设置了远程，您也可以看到远程分支。只需在上面的命令后面加上`-a`:

```
git branch -a
```

![Command showing the branches in the local copy as well as the origin branch](img/d0811d039f2578717f1f16ad6ce32850.png)

显示本地副本中的分支以及原始分支的命令

如上所示，红色的分支表示它们在遥控器上。在我们的例子中，我们可以看到出现在`origin`遥控器中的各种分支。

## 创建分支

在 Git 中创建分支有多种方法。要创建新分支并保留在当前分支中，请运行以下命令:

```
git branch test_branch
```

这里，`test_branch`是创建的分支的名称。但是，在运行`git branch`时，似乎活动分支仍然是`master`分支。要改变活动分支，我们可以运行`checkout`命令(如下所示):

```
git checkout test_branch
```

![Creating a new branch and making it active](img/2a818c7e1886231be06510050075e5cb.png)

创建一个新分支并激活它

您也可以将上面的两个命令结合起来，通过在`checkout`命令后添加`-b`来创建并检出一个新的分支:

```
git checkout -b new_test_branch
```

![Create and checkout to a new branch in a single command](img/7e2e2b6578194eb6264aaa27c205bd92.png)

我们刚刚创建的分支基于当前活动分支的最新提交——在我们的例子中是`master`。如果您想基于某个提交(比如`cafb55d`)创建一个分支(比如说`old_commit_branch`)，您可以运行下面的命令:

```
git checkout -b old_commit_branch cafb55d
```

![Creating a branch based on an old commit](img/d37e8ef00c46e2d76e128eccf9151709.png)

基于旧提交创建分支

要将当前分支重命名为`renamed_branch`，请运行以下命令:

```
git branch -m renamed_branch
```

## 删除一个分支

要删除分支，请运行以下命令:

```
git branch -D new_test_branch
```

![Deleting a branch in Git](img/7dd5a86bb5cbcc572ab96a2348fd9393.png)

在 Git 中删除分支

### 注意:除非必要，否则不要删除分支

由于保留分支并没有什么坏处，所以作为一种预防措施，我建议不要删除它们，除非存储库中的分支数量变得太大而无法管理。

上面使用的`-D`选项删除一个分支，即使它还没有与远程分支同步。这意味着，如果您的当前分支中有尚未推送的提交，`-D`仍然会删除您的分支，而不会提供任何警告。为了确保你不会丢失数据，你可以把`-d`作为`-D`的后缀。`-d`仅删除已与远程分支同步的分支。由于我们的分支还没有同步，让我们看看如果我们后缀`-d`会发生什么，如下所示:

![Deleting a branch in Git using the -d option](img/9fa168c4c02874493424e2552c5925ae.png)

如您所见，Git 向您发出警告并中止操作，因为数据还没有与分支合并。

### 分支和`HEAD`

既然我们已经有机会对分支的基础知识进行了实验，那么让我们花一点时间来讨论分支在 Git 中是如何工作的，并且引入一个重要的概念:`HEAD`。

如上所述，分支只是不同提交之间的链接，或者是提交之间的路径。需要注意的重要一点是，在处理分支时，分支的`HEAD`指向分支中最近的提交。在接下来的章节中，我会多次提到`HEAD`。在 Git 中，`HEAD`指向分支中最近的提交。换句话说，它指的是树枝的顶端。

分支本质上是一个指向提交的指针，它有一个父提交、一个祖父提交等等。这个提交链形成了我上面提到的路径。那么，你如何将一个分支和`HEAD`联系起来呢？嗯，`HEAD`和当前分支的尖端指向同一个提交。让我们看一张图来说明这个想法:

![Branches and HEAD](img/b975be82639dff4620bebd084d556a6f.png)

树枝和头

如上所示，`branch_A`最初是活动分支，`HEAD`指向提交 C。提交 A 是基础提交，没有任何父提交，因此`branch_A`中的提交(这也形成了我已经谈到的路径)是 C→B→A。`branch_B`中的提交是 E→D→B→A。`HEAD`指向活动`branch_A`的最新提交，这是提交 C。当我们添加一个提交时，它被添加到活动分支。提交后，`branch_A`指向 F，分支跟随 F → C → B → A，而`branch_B`保持不变。`HEAD`现在指向提交 f。类似地，当我们添加另一个提交时的变化如图所示。

## 高级分支:合并分支

如前所述，Git 最大的优势之一是合并分支特别容易。现在让我们来看看它是如何完成的。

我们将创建两个新分支——`new_feature`和`another_feature`——并添加一些虚拟提交。检查每个分支中的历史显示分支`another_feature`领先一个提交，如下所示:

![Checking the history in each branch](img/122c34ed1a57aeffddb39061099a569f.png)

检查每个分支机构的历史记录

这种情况可以如下图所示。每个圆圈代表一个提交，分支名称指向它的`HEAD`(分支的顶端)。

![Visualizing our branches before the merge](img/d67f0a04f407084af9fbaadf05d6b733.png)

在合并前可视化我们的分支

要将`new_feature`与`master`合并，运行以下命令(首先确保`master`分支处于活动状态之后):

```
git checkout master
git merge new_feature
```

结果如下所示:

![The status of the repository after merging new_feature into master](img/6480ae119390a855c5d6c2f73619f65a.png)

将 new_feature 合并到 master 中后存储库的状态

要将`another_feature`与`new_feature`合并，只需运行以下命令(确保分支`new_feature`处于活动状态):

```
git checkout new_feature
git merge another_feature
```

结果如下所示:

![The status of the repository after merging another_feature into new_feature](img/7807ff334f1ad50b335ddfd7020133f2.png)

将另一个 _feature 合并到新 _feature 后存储库的状态

### 重要提示:小心循环

上图显示了这种合并已经在您的项目历史中创建了一个跨越两次提交的循环，在这两次提交中，工作流分别发生了分叉和汇聚。当单独工作或在小团队中工作时，这样的循环可能不是问题。然而，在一个更大的团队中——从您脱离主分支的时候开始，可能已经有很多提交了——这样大的循环使得浏览历史和理解变更变得很困难。我们将在第 6 章中探索一种使用`rebase`命令合并分支而不产生循环的方法。

![The status of branch new_feature after the merge](img/127511f5be4c549621871abc5e11485e.png)

合并后分支新特征的状态

这次合并没有任何“冲突”。原因很简单，与分支`another_feature`相比，没有新的提交添加到分支`new_feature`中。Git 中的 **冲突** 发生在同一个文件在两个分支的非公共提交中被修改的时候。Git 会引发冲突，以确保不会丢失任何数据。

我们将在下一章详细讨论冲突。我前面提到过，分支可以通过提交的简单途径来可视化。当我们合并分支并且没有冲突时，比如上面，只有分支路径被改变并且分支的`HEAD`被更新。这就是所谓的 **快进** 式的合并。

另一种合并分支的方式是 **不快进** 合并，通过在`merge`命令后加上`--no-ff`来实现。这样，在基础分支上创建了一个新的 commit，其中包含来自另一个分支的更改。还会要求您指定提交消息:

```
git merge --no-ff new_feature
```

在上面的例子中，前者(将`new_feature`与`master`合并)是一个快速前进合并，而后者是一个带有合并提交的非快速前进合并。

虽然默认情况下是快进方式的合并，但是对于合并到主分支的合并，使用非快进方式通常是一个好主意。从长远来看，标识新功能合并的新提交可能是有益的，因为它在逻辑上将负责新功能的代码部分分离成一个提交。

## 结论

### 你学到了什么？

在本章中，我们讨论了什么是分支以及如何在 Git 中管理它们。我们看了创建、修改、删除和合并分支。

### 下一步是什么？

我已经谈到了 Git 对团队工作的开发人员的好处。下一章将更详细地讨论这个问题，以及在分布式团队中经常使用的特定 Git 动作和命令。

## 分享这篇文章