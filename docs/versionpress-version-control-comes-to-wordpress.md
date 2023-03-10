# 真正的版本控制来到了 WordPress

> 原文：<https://www.sitepoint.com/versionpress-version-control-comes-to-wordpress/>

虽然许多 WordPress 开发者将版本控制作为他们工作流程的一部分(例如，Mark Jaquith 的 [WordPress 框架](https://github.com/markjaquith/WordPress-Skeleton)非常受欢迎)，但在整个 WordPress 项目的简单协作和版本控制方面仍然存在差距。尤其是与数据库更改相关的问题。像 [WP 从 Delicious Brains 迁移 DB Pro](https://deliciousbrains.com/wp-migrate-db-pro/) 这样的插件和令人敬畏的 [WP-CLI](http://wp-cli.org/) 命令行工具在填补这一空白方面走了很长一段路，但是对于大众来说，完整的 WordPress 版本控制的挑战仍然没有答案。

直到一个叫做 [VersionPress](http://versionpress.net) 的创新项目出现。VersionPress 是一个 WordPress 插件，它会跟踪每一个变化，使用 [Git](http://git-scm.com/) 完全控制版本。

目前正在开发中，VersionPress 插件建立在 Git 的版本控制系统之上。VersionPress 将能够存储整个 WordPress 网站、数据库等等，完全在 Git 中进行版本控制。该插件由 Borek Bernard(创始人)和 Jan vora ek 开发，一旦推出，将获得 GNU 通用公共许可证的许可。

根据 VersionPress.net:

> VersionPress 是 WordPress 的版本控制插件。它将整个站点(包括文件和数据库)保存在 Git 中，实现了站点范围的恢复、安全更新和简单升级。

VersionPress 的核心功能包括:

1.  每次逻辑更改后创建备份
2.  从档案中恢复项目的任何历史版本
3.  有选择地进行变更，而不影响新变更的能力
4.  允许多个用户同时处理同一个项目的能力
5.  测试环境，其中测试环境和实时站点之间存在双向同步
6.  与第三方工具有效集成的节省空间的存储库

我有幸观看了伯雷克·伯纳德的版本出版示范。我的第一印象:VersionPress 对于所有开发者来说都将是一个开创性的插件。

![VersionPress Change History](img/d61903ceb4bbdd21b9200289ed95fbe3.png)

除了演示之外，我还有机会问了 Borek 几个关于他的项目的问题。

克里斯:【VersionPress 的想法从何而来？

作为一名软件开发人员，我几乎在每个项目中都使用版本控制系统。它们真的很有用——当出现任何问题时，总有一种简单的方法可以回到工作版本。这就是为什么我总是发现 WordPress 项目有点困难——它们通常也是严肃的软件项目，但是很难对它们进行适当的版本控制。因为一半的真相在文件里，一半在数据库里。所以和我的同事一起，我们开始寻找将这两个世界结合起来的方法，并在一个单一的 Git 存储库中发布所有版本。结果是 VersionPress。

Chris: 对于已经在工作流程中使用 Git 的开发人员来说，他们将如何从使用 VersionPress 中获益？

我见过许多开发人员使用 Git 对 WordPress 站点的文件部分进行版本控制，这本身肯定是有用的，我个人使用这种方法已经很长时间了。但是，我没见过多少开发者用 Git 做全站点版本控制，包括数据库。这是 VersionPress 非常独特的地方，虽然我并不是说没有解决方案或工作流来实现类似的结果，但我还没有见过像 VersionPress 那样将适当的版本控制与用户友好性结合起来的解决方案。

Chris: VersionPress 确实吸引了人们的兴趣，你的项目最近在黑客新闻上被讨论，WordPress 社区如何支持这一项目并为快速开发做出贡献？

是的，最初的反应非常热烈，我们对此深感荣幸。但这也是一个非常年轻的项目，我们知道它的目标非常远大。这就是为什么我们目前正在 versionpress.net[的](http://versionpress.net)开展一项活动，为最初的开发进行众筹，我想请所有喜欢 VersionPress 想法的人考虑支持它。没有社区的支持，我们无法成功，任何帮助都将不胜感激。

Chris: 读者怎样才能及时了解 VersionPress 的最新消息并获得更多信息？

我们在 versionpress.wordpress.com 的 [@versionpress](https://twitter.com/versionpress) 有一个 Twitter 账户，并在我们的博客[发布公告和其他事情。](http://versionpress.wordpress.com)

所以现在你有了它，在 WordPress 空间肯定是一个有趣的项目。在对这个项目有了更多的了解后，我决定早点加入并支持它(捐款从 5 美元开始)。我真的希望这个团队能尽快达到他们的目标，我真的希望我们能在不久的将来看到这个插件！如果一切按计划进行，它将在今年晚些时候上市。

## 分享这篇文章