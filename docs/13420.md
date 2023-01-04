# DevConnections 年春季第 2 天

> 原文：<https://www.sitepoint.com/devconnections-spring-2007-day-2/>

参见[第 0 天](https://www.sitepoint.com/devconnections-spring-2007-day-0-scottgu-ajax-entity-frameworks/)或[第 1 天](https://www.sitepoint.com/devconnections-day-1-wrap-microsoft-day-at-a-microsoft-conference/)。

又一次成功地避免了阳光直射，我成功地进行了另一系列良好的训练。

Paul Litwin 给出了一个很好的总结，介绍了让全新的 ASP.NET 2.0 数据控件在非演示环境中实际工作的基本但重要且记录不完整的提示和技巧。我不认为在复杂的场景中可以做很多事情来保存数据控件——抽象太容易泄漏，而且它们对数据访问代码强加了太多的要求，以至于无法在重要的应用程序中使用。保罗的演示虽然有用，但有助于巩固这一观点。会后应提供代码来说明细节。

接下来是罗布·霍华德(Rob Howard)本人，[社区服务器先生(Mr)](http://communityserver.org/),就构建高性能 ASP.NET 应用发表演讲。虽然他目前在 [Telligent Systems](http://telligent.com/) 工作，但他在微软时曾是 ASP.NET 团队的一员，对内部情况略知一二。他的一些要点是:

*   不要试图去思考框架。当前的实现非常高效，调整更深层次的设置可能只会带来麻烦。他指出，MySpace 团队没有改变任何默认设置，并且对结果非常满意。作为一个 corolarry，不要试图智取连接池。
*   如果您遇到了速度问题，从查看数据库连接问题开始，然后从那里开始。你可能有一些重要的问题。
*   是的，使用是从 ASP.NET 获得输出的最快方法。但是，仅仅使用内联命令来完成复杂的事情并不足以加快速度，以证明在极端情况之外所需要的努力是值得的。
*   Rob 和我一样，倾向于使用结合了 PODOs feeding Repeaters 的数据阅读器来将数据放到页面上。英雄所见略同。

我跳过了对[斯科特·格思里](http://weblogs.asp.net/scottgu/)的[网络摇滚](http://www.dotnetrocks.com/)采访，去赶[金伯利之旅](http://www.sqlskills.com/blogs/kimberly/)的[SqlSkills.com](http://www.sqlskills.com/)名气呈现“从孤立的灾难和人为错误中恢复”从很多层面来看，这都是一个很好的演示，展示了 Sql 2005 的一些非常闪亮的新特性。知识的瑰宝——查看 Sql tools 文件夹中的[tablediff.exe](http://weblogs.sqlteam.com/mladenp/archive/2007/03/03/60125.aspx)实用程序。它可以检查两个数据库，并生成一个 T-SQL 脚本来同步数据。我能想到一些人们购买的昂贵工具来做到这一点。无论如何，如果你有机会看到特里普女士在场，那就去看看吧——她有着令人敬畏的舞台表现力。至少您会觉得有趣，而且您可能会学到一些关于 Sql Server 的知识。

在当天的最后一场会议中，我参加了 Miguel Castro，网络达人，关于 Web 控件数据绑定的演讲。他制作了一个附加到各种数据源的日历控件——从强类型到使用自定义数据源控件——这是 ASP.NET 2.0 中声明性控件的一个非常有效的演示。现在，我可以试着总结一下，或者你可以后天(3 月 30 日)去 www.steelbluesolutions.com[下载源代码，自己看看。](http://www.steelbluesolutions.com/)

现在去一些令人害怕的阳光直射的地方参加一个泳池派对，这是 CoDe Magazine 和 T2 app dev 的礼节，明天再回来参加更多的网络极客会议。敬请关注更多内容。

## 分享这篇文章