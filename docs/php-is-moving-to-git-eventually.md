# PHP 正在(最终)转向 Git

> 原文：<https://www.sitepoint.com/php-is-moving-to-git-eventually/>

从过时的 CVS 迁移到 Subversion (SVN)才两年多， [PHP 又一次移动了](http://news.php.net/php.internals/55293 "php.internals: [RESULT] Choosing a distributed version control system for PHP"):这一次，迁移到 [Git](http://git-scm.com/ "Git - Fast Version Control System") 。好吧，最终。从 CVS 到 SVN 的迁移规模很大，花了好几个月。PHP 项目需要支持它的用户群、钩子脚本(提交邮件列表等)。)意味着版本控制软件的任何改变都意味着相当大的承诺。这就是为什么即使投票已经结束，尘埃落定，我们也不会在 Git 上看到 PHP，直到今年年底。

## 我为什么投 Git？

出于我自己的版本需求，我遵循了与 PHP 相同的迁移路线，从 CVS 开始，迁移到 Subversion，然后最近又迁移到 Git，我亲身体验了这些系统相对于它们的前辈所带来的好处。

Subversion(至少在我上次使用它时是 1.5 版本)的分支管理非常糟糕。对于现有的存储库，使用合并跟踪的能力以任何有意义的方式都是不存在的，甚至对于一个小团队，我们经常有大的、复杂的冲突要解决。虽然 Git 确实带来了一种全新的思维方式，但对我来说，仅仅是它的分支管理就值得远离 Subversion。

## 这对 PHP 意味着什么？

随着向 Git 的转移及其创建和合并分支的卓越能力，PHP 开发人员现在可以非常灵活地做出和接受贡献。而不是制定一个计划然后坚持下去的传统发展模式(哈！)当所有开发人员在同一个分支(即主管)中工作时，开发人员可以在他们自己的分支中以他们自己的速度工作，然后就他们的哪个分支将构成给定的构建达成共识。

这种分支合并可能是 PHP 开发周期中潜在的巨大转变，因为它为团队提供了许多选择。例如，他们可以像以前一样继续，或者他们可以选择切换到季度或两年一次的发布周期。他们甚至可以选择更频繁地发布，在特性变得稳定时立即推出。如果一个特性还没有准备好，就不要合并它。

此外，Git 为核心开发人员提供了更多的便利，他们中的许多人在世界各地旅行，并将从分布式版本控制系统提供的“在飞机上滞留时本地提交”场景中受益。让我们面对现实吧，我们能做的任何让核心开发人员生活更轻松的事情对项目都是有益的。

## 这对你意味着什么？

如果你还没有，你需要开始复习你的 Git 技能。我确信，如果不是正式的克隆，我们将很快在 GitHub T1 上看到 PHP 存储库，这将使存储库的分叉和修改变得更加容易。

随着开发周期的潜在变化，这也可能意味着新功能会更快地到达你的手中——警告大多数虚拟主机公司将会落后(任何人的特征？).

总而言之，我觉得从 Subversion 迁移到 Git 对 PHP 项目和整个社区来说都是一件好事。从投票结果来看，迁移到 Git 可能会有争议(虽然我认为更公平地说，从 Subversion 迁移是引发任何争议的原因)，但是有 78 票支持迁移，其中 52 票选择 Git，很明显 PHP 开发社区支持这个决定。

图片 via[marema](http://www.shutterstock.com/gallery-594931p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章