# 马克·曼德尔在调动表上的 10 个问题

> 原文：<https://www.sitepoint.com/10-questions-for-mark-mandel-on-transfer-orm/>

在下周的 WebDU 会议之前，Mark Mandel 昨天发布了 1.0 版本的 Transfer ORM。如果你想知道什么是地狱转移 ORM，为什么你应该关心，我请马克回答几个问题，向我们解释这一切。

这篇文章与 FarCry 网站上的杰夫·鲍尔斯和 COOP 网站上的约翰·法勒是同一个系列(使用相同的问题)。

你好，马克！给我们你的电梯营销:用一两句话总结转移的本质。

Transfer 是 ColdFusion 的对象关系映射器。

它生成并填充 CFC，CFC 是基于 XML 配置文件表示应用程序中的数据的对象。从那里，它能够自动地在数据库中插入、更新和删除数据，而无需您编写任何 SQL 或 CFML 来完成这些工作。

好的，听起来很酷。让我们再深入一点:告诉我们更多关于主要特性的信息。

在顶层，Transfer 为您生成通常被称为业务对象的东西，而无需您编写任何 CFML 或构建 CFC。对于业务对象，我指的是表示系统中实体的对象，比如用户、产品或订单。

这些生成的业务对象(在框架之后通常称为 TransferObjects)也可以被扩展和增强，以允许您编写您可能需要的任何额外的定制功能。

作为一个对象关系映射器，Transfer 能够接受这些 TransferObjects 并自动为您生成所有的创建、读取、更新和删除 SQL。

例如，如果我希望 Transfer 从数据库中检索一个特定的用户，并返回给我一个用户对象，我需要做的就是:

`user = transfer.get("User", 1);`

我有一个用户对象，它包含用户记录的所有相关数据，主键为“1”，然后可以从用户对象中检索到这些数据，例如

 `The user name is #user.getFirstName()# #user.getLastName()#` 

然后可以编辑用户对象上的信息，并将其传递回 Transfer 以将其保存回数据库，例如

`user.setFirstName("Mark");
user.setLastName("Mandel");`

`transfer.save(user);`

现在，数据库中的用户记录的“名字”和“姓氏”列分别设置为“Mark”和“Mandel”。

如果我想从数据库中删除这个特定的用户对象，只需简单的操作，

`transfer.delete(user);`

并且用户记录已经从数据库中删除，无需编写任何 SQL 语句。

Transfer 所做的远不止这些，包括处理对象组合、生成列表查询以及提供高度可配置的缓存层。

如果这些信息还不够的话，[下面是更完整的转让概述](http://docs.transfer-orm.com/wiki/Overview.cfm)。

迁移是如何产生的，产生迁移的原因是什么？你是怎么卷进来的？

三年多以前，当我写了第一篇关于 Transfer 的博文时，我实际上是在寻找一种自动的方法来动态生成我的 CFC。这实际上只是对不想整天显式编写 getters 和 setters 的一种回应！

一旦我开始实现这一点，我就开始考虑如何将它与将这些对象中的数据存入和取出数据库的自动化过程结合起来使用。

当时，ColdFusion 领域没有任何其他持久性框架，所以我开始研究其他技术中的 ORM 技术，并开始用 Transfer 的第一个版本重建我的博客 [Compound Theory](http://www.compoundtheory.com/) 。事实上，复合理论是第一个用 Transfer 开发的网站！

至于我是如何参与的，如前所述，我是 Transfer 的创建者和主要开发者。我也倾向于管理很多社区方面的事务，但是随着迁移社区变得越来越大，我不需要参与其中，因为我们有相当多非常活跃的成员，他们在需要的地方做了很好的帮助。

开发商应该何时寻求转让？它有什么好处呢？

当开发人员尝试过手工开发面向对象、数据库驱动的应用程序时，他们就开始寻求迁移。

他们厌倦了单调乏味的工作，厌倦了编写代表其业务对象的所有 CFC、执行所有创建、读取、更新和删除语句的所有 SQL 以及在对象和 SQL 语句之间来回移动信息的 ColdFusion 代码所耗费的时间。

当他们发现 Transfer 时，他们很快意识到他们不必担心这些平凡的细节，并可以更密切地关注如何设计和建模他们的应用程序数据层，因为 Transfer 会为您处理这一切，同时还提供大量工具和生成的代码，可以立即加以利用。

**使用转账的前提条件是什么？有什么对新用户有帮助的先验知识吗？它是否使用了其他可以简化或复杂化事情的框架？**

在开始转移之前，对面向对象编程有一个大致的了解是非常有用的，因为它确实从一个非常面向对象的角度来看待开发。

Transfer 本身不使用任何其他框架，但它集成了许多其他框架，例如像 Model Glue、Mach II、Fusebox 或 ColdBox 这样的 MVC 框架，以及 ColdSpring 或 Lightwire 这样的反转控制框架。

这使您只需将几个 ColdFusion 社区项目组合在一起，就可以非常容易地构建一些非常成熟和复杂的应用程序基础，这可以大大减少项目的开发时间。

Transfer 与其他框架的区别是什么？

虽然 Transfer 确实为你做了很多事情，但它也试图在很多方面“摆脱你的方式”。它并没有为您的开发设定一个规定的方法，所以您在构建应用程序的方式上有很大的自由度。

Transfer 有几个关键的扩展机制，可以为您的实现提供非常大的灵活性，这意味着您不会局限于单一的应用程序开发方式。事实上，我一只手就能数出我在邮件列表或文档中说过的地方的数量'这是用 Transfer 做 X 的唯一方法！'。

有没有很棒的“在野外”转移的例子？

据我所知，两个最大的商业转让实施是:

*   [NGA.NET](http://www.nga.net/)，澳洲领先的电子招聘公司。自 2000 年 NGA。NET's 一直在打造自己的电子招聘产品，并为 500 强企业和联邦政府提供解决方案，NGA.NET 在这些领域拥有 100%的市场份额。他们的应用程序经历了许多发展步骤，2008 年测试版中有一个基于 Mach-ii、ColdSpring 和 Transfer architecture 的全新版本。
*   [Broadchoice](http://www.broadchoice.com/) 是一家按需提供集成网络营销解决方案的提供商，该解决方案独特地将社交网络、内容管理、营销活动和分析结合起来，根据实际的客户、合作伙伴和潜在客户行为提供商业智能。Broadchoice 通过让公司参与其企业社区来提高营销效率，从而消除了营销中的浪费和猜测。

Luis Majano 和我最近也宣布了一个基于 Transfer、ColdBox 和 ColdSpring 的开源 wiki，名为 CodexWiki。它目前处于私人测试阶段，但请关注 www.codexwiki.org 的[了解更多细节。](http://www.codexwiki.org/)

许可、社区、支持和文档呢？

转让是在[通用公共许可证 1.0](http://www.opensource.org/licenses/cpl1.0.php) 下发布的，从商业和开源的角度来看，这允许在使用转让时有很大的自由度。

两个主要的转移支持和社区渠道是[转移邮件列表](http://groups.google.com/group/transfer-dev)，它有大约 340 名成员，非常活跃，以及达尔内 IRC 网络上的[#转移 IRC 聊天室](http://www.dal.net/)。

Compound Theory 最近也宣布了商业支持计划，因此社区和商业支持的全部范围都可以转移。

文档可以在 [Transfer Wiki](http://docs.transfer-orm.com/) 上找到，其中包括演示文稿、示例应用程序和各种关于 Transfer 的博客帖子的链接。

可以在[转移跟踪器](http://tracker.transfer-orm.com/)上找到 Bug 和增强票证跟踪。

所有这些细节都可以在[转会网站](http://www.transfer-orm.com/)找到。

**未来会有什么样的转移？**

Transfer 昨天刚刚发布了 1.0 版本，这是一个非常激动人心的版本，已经等了很长时间了！

一个全新的迁移网站正在建设中，它将整合已经实现的 wiki 和 tracker，为社区提供更多学习和发展迁移的方法。

我也已经完成了将所有可能的增强功能输入到 Tracker 中的工作，将会有一个过程允许社区帮助决定哪些功能将进入 Transfer 1.1

转移的专业方面也在扩展，目前正在编写培训模块，以提供现场和在线培训选项，[从在](http://www.webdu.com.au/go/session/transfer-training-workshop) [webDU](http://www.webdu.com.au) 的全天培训开始！

这是一个非常激动人心的转移时刻！

人们在哪里可以找到更多关于转让的信息？

[转会邮件列表](http://groups.google.com/group/transfer-dev)和[文档](http://docs.transfer-orm.com/)是获取更多转会信息的最佳途径。

维基上还有一个完整的[转移演示列表](http://docs.transfer-orm.com/wiki/Presentation_Recordings.cfm)，涵盖了入门和高级转移概念，以及[一系列示例应用](http://docs.transfer-orm.com/wiki/Example_Code.cfm)，这是一个很好的入门方式。

太好了，谢谢你配合马克！

## 分享这篇文章