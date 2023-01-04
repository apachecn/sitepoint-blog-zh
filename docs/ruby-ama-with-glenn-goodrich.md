# 与专家对话:格伦·古德里奇

> 原文：<https://www.sitepoint.com/ruby-ama-with-glenn-goodrich/>

![Screen Shot 2016-11-24 at 9.30.02 am](img/192348481500145c07ab9f6c608ebe17.png)

这是我们最近与 Glenn Goodrich 的[AMA](https://www.sitepoint.com/community/t/ruby-ama-with-glenn-goodrich-16th-nov-1pm-pst/243160)的回顾，Glenn Goodrich 是我们的 Ruby 频道编辑和 SitePoint 上 [Rails:新手到忍者](https://www.sitepoint.com/premium/books/rails-novice-to-ninja)的作者。我们的 1 小时 AMA 是您与专家直接对话的机会。

## 概述 Ruby on Rails

大多数人都听说过 Ruby on Rails (RoR)，即使您自己并不精通。Ruby 是一种非常强大的面向对象的脚本语言，Rails 是用 Ruby 构建 web 应用程序的主要框架。Ruby on Rails 是众所周知的——许多人甚至在真正学习 Ruby 之前就学习了它——它可以作为各种应用程序的平台。

将所有这些与对 Ruby 和 RoR 开发者的大量需求结合起来，你就有足够的理由去参与和学习 Ruby！Glenn 在 AMA 上讲述了很多关于 Ruby 和 Rails 的内容——让我们来看看其中的一些问题和答案吧！

#### 嘿 Glenn，如果你现在从头开始构建一个全新的网站，你还会用 Rails 来构建它吗？

格伦:嘿，谢谢你的问题。答案是“可能”。我是一个非常喜欢“使用合适的工具”的程序员，所以如果 Rails 是一个很好的选择，是的。比如我上一个大项目有微服务等等。我们在 Rails 中提供服务，但在 Node/Express 中提供 UI。有道理吗？我认为 Rails 对许多应用程序来说都是优秀的，但不是所有的。

#### 你使用过 Action Cable 吗？这似乎是一个令人兴奋的特性，但是我还没有任何项目有这么多的用例。

最近，我在一个客户的原型中使用了 ActionCable。用例是一个“Kudos”类型的应用程序，看起来有点像 Slack。如果你不介意 Redis 的依赖性，它很容易使用，也很酷。

#### Action Cable 的完美用例是什么？

格伦:任何需要实时体验的东西，比如聊天应用。顺便说一下，我们在 Ruby 频道上有一个关于[的教程，正是这个东西](https://www.sitepoint.com/rails-and-actioncable-adding-advanced-features/)。如果你想看看什么时候有人上线或者有人提/发消息给你等等。诸如此类的东西。

#### 你的书的目标读者是谁？更有经验的开发人员会学到新东西吗？

**Glenn:** 目标读者群肯定是那些 Rails 新手。然而，我会告诉你，写这本书让我学到了很多。所以，我认为任何人都会学到新的东西！

#### 写书是什么感觉？

格伦:对喜欢写作的人来说，这很棒。工作量很大，但我们很幸运，上一版(由 Patrick Lanz 编写)做得非常好。我使用了相当数量的他的内容和例子，这让我可以更专注于升级方面。我喜欢它。

#### 你的测试设置是什么？

**格伦:**我喜欢 RSpec，所以我几乎总是伸手去拿。`let`和`context`语法确实允许我使我的规范更有表现力。Minitest 很棒，但我已经使用 RSpec 很多年了，它就像一个最受欢迎的工具。

#### 你会建议刚接触 Ruby/Rails 的人先学习一些 Ruby 吗？或者你认为直接进入 Rails 是可以的吗？

**Glenn:** 我在书中稍微谈到了这一点，但快速版是:很多人(包括我)通过 Rails 来到 Ruby。你可以高效地完成一些事情，而不必一头扎进 Ruby。然而，如果你对 Rails 开发很认真，你必须学好 Ruby。你是如何走到这一步的并不重要，重要的是你最终要做到面面俱到。

#### 我是一名前端开发人员，对编程也很感兴趣……你认为有比 RoR 和 Python 更合适的语言和工具吗？

格伦:我认为学习 Ruby 或 Python 肯定能增加你的技能。我最近写了为什么我认为 Rails 仍然是一个值得学习的好东西，其中一个原因是它教会了你 web 的惯例。当您研究 Rails 的约定时，您会了解它们为什么存在以及它们解决了什么问题。这告诉你网络的挑战。然后你可以学习使用其他工具(宝石)来解决它们或者自己制作。我当然会鼓励你学习后端框架/语言，当然，我会说用 Rails…

### 包装它

再次感谢 Glenn 参加我们的 AMA，感谢所有对 Ruby 和 Rails 有疑问和讨论的人！如果你想看一看整个 AMA，请前往 [SitePoint 论坛主题](https://www.sitepoint.com/community/t/ruby-ama-with-glenn-goodrich-16th-nov-1pm-pst/243160)。

格伦为 Skookum Digital 工作，同时管理我们在 T2 的 SitePoint Ruby 频道。此外，他还是 [Rails:忍者新手](https://www.sitepoint.com/premium/books/rails-novice-to-ninja)的作者。这本书值得任何 Ruby on Rails 新手阅读！

## 分享这篇文章