# 企业 SaaS 和 Coghead 的教训

> 原文：<https://www.sitepoint.com/coghead-lesson/>

![saas-boneyard](img/07c963add1fc286fa250bd4ac5ff844c.png) TechCrunch 最近[报道了](http://www.techcrunch.com/2009/02/18/coghead-grinds-to-a-halt-heads-to-the-deadpool/)网站上最受欢迎的 SaaS 企业解决方案之一 [Coghead](http://www.coghead.com/) 的消亡。Coghead 通过这篇文章提出，经济衰退是造成其困境的原因。然而，许多这样的发展有一个更基本和现实的缺陷。反对任何 SaaS 平台的最长久的论点是数据备份和安全性，这在任何经济形势下都是有害的。

在 ZDNet 的 Larry Dignan 最近的一篇文章中，数据备份和 SaaS 的经济方面都受到了质疑。虽然经济衰退可能是 SaaS 的一个担忧，就像数据备份一样，但另一个因素被忽略了。原始数据，无论如何“存储”(正如 Dignan 所指出的)，如果没有应用程序来运行，实际上是没有用的。仅在这一方面，基于网络的商业 SaaS 的开发者就犯了一个致命的错误。任何企业，无论多么小，都承受不起哪怕是极小的数据停滞不前的可能性。

为什么这些开发者中的一些人从来没有制作一个“独立”的版本，包括适当的文档，这是一个谜。我在这个领域的专业知识包括早期测试这些服务(特别是 Coghead)，并与 [Iceberg](http://www.geticeberg.com/) 合作，帮助他们设计新网站并发布新闻。冰山和[工艺制造商](http://www.processmaker.com/)的生存能力与这一论点特别相关，因为他们确实有“现场”服务。

下图显示了几种 SaaS 服务的对比，包括:Coghead， [Zoho](http://www.zoho.com/) ，Process Maker，QuickBase，SalesForce 和 Iceberg。

![Updated chart illustrating features](img/0554cf4b1ee6d83e570d98ce61456145.png)

说明特性的更新图表

##### 生存冗余

无论我们多么希望 SaaS 取得成功，生存能力会影响到从开发商到最终用户——企业或个人——的每一个人。正如他们所说，时间就是金钱，因此丢失数据甚至“扣留”数据对大多数人来说都不是一个选项。Coghead 作为一项技术的最终命运是不确定的，今天的[新闻](http://www.techcrunch.com/2009/02/19/sap-acquires-cogheads-technology-as-it-looks-towards-the-cloud/)说 [SAP](http://www.sap.com/index.epx) 收购了它。不管怎样，对于现在的 SAP 来说，应用程序和数据的冗余似乎是明智的。

##### 最终注释

Coghead 作为一个 Web 2.0(或者其他什么)开发是非常重要的。我联系了“冰山”的联合创始人韦恩·伯恩，听听他对这条新闻的看法。尽管他的开发在早期与 Coghead 进行了有些不公平的比较，但他的陈述揭示了 Coghead 作为一种创新的许多方面:

> Coghead 简化并传达了 SaaS 的信息，以一种其他玩家无法做到的方式，抓住了人们的想象力。

鉴于 SalesForce 的[广泛接受度](http://blogs.wsj.com/deals/2008/07/30/mean-street-salesforcecom-how-much-higher-can-it-go/)，我还询问了 Byrne 关于 sales force 的备份问题。Byrne 回答说:“Salesforce 的客户可以请求备份，但它是以 Excel 电子表格的形式给出的，公司需要几个月的时间来整理这些数据。”此外，我最初关于“无用的原始数据”的论点似乎也是正确的。这一领域的每一项发展都有其优势和劣势，但作为一家 SaaS 公司的核心，数据安全必须是重中之重。暗示没有这种“保证”的生存能力似乎是可笑的。

**作者注&披露:**正如我在本文正文中提到的，在网站开发早期，我曾向冰山咨询过网站设计、公关和 SEO 方面的问题。任何明示或暗示的观点都不是一种产品对另一种产品的支持，当然也不是 SitePoint 或其社区的建议。我建议网络社区作为一个整体，更全面地评估这些服务。这样做，我们可以更容易地保证开发的艺术性，并且最终用户不会疑惑发生了什么。

**更新:** Intuit 通过他们的公关与我联系，建议我提供工作流、Excel 集成和其他功能。我包含了工作流程(虽然仍然没有可视化的工作流程)，Excel 作为 givens。至于 API 建议实用程序，开放的 API 并不意味着你可以将自己的代码上传到应用程序中。他们还想知道什么是数据库集成。这允许一个平台直接从另一个数据库获取数据，鉴于 Coghead 的用户现在需要有人集成他们的数据，这似乎是不可或缺的。

## 分享这篇文章