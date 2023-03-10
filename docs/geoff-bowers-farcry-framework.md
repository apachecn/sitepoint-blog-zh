# 杰夫·鲍尔斯谈 FarCry 框架

> 原文：<https://www.sitepoint.com/geoff-bowers-farcry-framework/>

**虽然 FarCry 以开源内容管理系统而闻名，但它实际上是构建 ColdFusion 应用程序的强大框架。总部位于悉尼的[守护进程](http://www.daemon.com.au/)的首席执行官杰夫·鲍尔斯自封为“仁慈的独裁者”，他让我们了解了 FarCry 的成功之道。**

Geoff，你能告诉我们你的电梯间推介吗？用一两句话概括 FarCry 的精髓。

FarCry Core 是一个基于 ColdFusion 语言的 web 应用程序框架。FarCry CMS 是一款流行的内容管理解决方案，采用 FarCry Core 构建。作为一个社区，我们提供对框架、CMS 和一系列社区插件的支持，包括谷歌地图库、图像处理、自由文本搜索等等。FarCry 社区成立于 2003 年 4 月，当时 Daemon 开源了底层的 FarCry 核心框架和相关的 FarCry CMS 解决方案。从那时起，FarCry 已经发展成为最大的 ColdFusion 代码库之一，在全球拥有活跃的开发人员社区。

这个社区由各种各样的个人、网络开发公司、大型企业、政府和教育机构组成。尽管代码贡献来自社区的很大范围，但主要特性的开发是由 Daemon 的一个紧密结合的团队和一小群额外的核心提交者驱动和管理的。

让我们再深入一点:告诉我们更多的主要特性。
我来说说 FarCry 核心——框架。通过扩展，FarCry CMS 拥有所有这些功能以及更多功能，这些功能都包含在一个开箱即用的示例解决方案中。

FarCry Core 是一个用于构建 web 应用程序的工具箱，其中包含基于角色的安全模型、对象关系映射器(ORM)、用于对象和视图的内存缓存代理、完全可配置的管理 webtop、用于快速构建用户界面的“formtools”引擎、完全灵活的模板环境以及用于共享代码的可扩展插件架构。很难用一两句话来解释，但是这些特性都代表了开发人员可以利用的多年工作成果。

我个人最喜欢的一个是“formtools”，这是一个基于你附加到底层组件的属性来动态构建你的 ui 的引擎。我们不是在谈论基本的 HTML 表单——它是一个非常丰富的 ui 库，可以无情地调整，以你想要的方式工作。Formtools 让我的生活变得简单，但同时，它也让我能够为我遇到的那些一次性的或独特的 ui 元素编写自己的界面。

对于那些不能满足需求的人，我们从商业(相对于技术)角度出发，整理了一份更完整的功能列表。

**site point:FarCry 是怎么来的，创作它的原因是什么？你是怎么卷进来的？**

早在 2002 年，Daemon 就必须决定如何处理我们的代码库:卖给另一家厂商，加强营销并作为商业厂商竞争，还是围绕开源代码库建立一个以服务为中心的业务？我们不想冒险增加营销所需的金融投资，尤其是在“网络炸弹”时期我们真的更喜欢我们的代码库，而不是那些向我们寻求收购的供应商，我们不能忍受看到我们的解决方案因为一个次等产品而被扔进垃圾堆。2003 年 4 月，我们在公共许可证下发布了 FarCry CMS，并尝试建立一个开源社区。

当我们将内容管理解决方案从底层框架中分离出来，并添加了一个复杂的插件架构时，FarCry Core 成为了 4.0 版本的一个独特的框架代码基础。CMS 本身现在是一个非常流行的，但是可选的插件。

有趣的是，FarCry 这个名字来源于古英语术语，指的是被派往王国最偏远地区发布皇家公告的哭喊者或信使。这似乎是一个适合 web 应用交付平台的名字！

至于我是如何参与其中的，我可能就是你所说的“仁慈的独裁者”我不再是该项目的首席开发人员，但如果社区中有任何异议，我仍然是最终决定功能更改和增强的最终仲裁者。我定期涉足代码库，但大部分时间都花在了照看社区、编写文档和管理 bug 数据库上。最近，我收集了一堆很棒的开源插件，用于博客和 Picnik 集成，仅举两个例子。

SitePoint:开发者应该在什么时候寻找 FarCry？它有什么好处呢？

大多数开发人员将 FarCry CMS 作为一种手段:他们想要一个全面的内容管理解决方案。只有在他们玩了一会儿并构建了自己的扩展之后，他们才开始意识到底层框架实际上是多么强大。

开发人员应该考虑 FarCry Core，这个框架，用于任何 web 应用程序——它不“仅仅是一个 CMS”事实上，FarCry 正被广泛用于构建复杂的网络应用，包括 CRM 系统、电子商务解决方案、在线电视指南、内部网、目录服务等等。随着可用插件列表的不断增加，几乎“开箱即用”的复杂解决方案越来越容易投入使用。

**SitePoint:使用 FarCry 的前提条件是什么？有什么对新用户有帮助的先验知识吗？它是否使用了其他可以简化或复杂化事情的框架？**

FarCry 没有真正的先决条件，尽管知道如何在 ColdFusion 中编程显然有所帮助。与任何框架一样，您需要以“框架的方式”做事。FarCry Core 描述了一种组织代码的方法，利用代码库中可用的底层服务，解决不同种类的问题。如果你反对“FarCry 方式”，你会感到沮丧。这个框架为你做了大量的工作，如果你能让自己“放手”，你可能会突然发现自己变得非常高效。和 FarCry 一起工作绝对是一种享受。

不同于 ColdFusion 社区中的其他框架，它们相互协作以形成更完整的产品，FarCry 可以说是无所不为。有 ORM、全面的缓存引擎、基于角色的安全模型、嵌套树模型、模板引擎、分类引擎、插件架构等等。虽然我们认为我们自己的代码库很酷，但在其他更具体的框架中，如 Transfer ORM 和 ColdSpring，也提供了一些很棒的功能。为了更好地利用这些库，我们正在计划一系列插件来连接第三方框架，使它们在 FarCry 环境中更容易访问。

SitePoint:是什么让 FarCry 有别于其他框架？

ColdFusion 框架的当前趋势是迫使开发人员采用非常面向对象的思维方式。在本质上，FarCry 非常依赖于面向对象的概念，但它尽最大努力为开发人员提供一个不那么令人生畏的界面——一种更“ColdFusion 式”的做事方式。

以 webskins 为例；这些类似于经典 MVC 模式中的视图。如果你习惯于用 HTML 构建，你可以构建一个 FarCry webskin。只需将你的模板放入 webskins 目录，它就会自动注册。它包含您正在渲染的特定对象的所有数据、对父内容类型的所有功能的访问、帮助构建您的用户界面的自定义标记库，并自动缓存在内存中。然后是“formtools”，这是一个无需一行代码就能动态构建丰富的用户界面来添加和更新内容的引擎。当然，在所有情况下，如果您想亲自动手，用更面向对象的风格编码，您可以这样做。

site point:FarCry“在野外”有什么很棒的例子吗？

太多了！我认为 Daemon 最近参与的三个项目非常特别:

*   [梅奥诊所](http://www.mayoclinic.com/)——这个巨大的医疗网站是著名的梅奥诊所面向公众的视图，平均每天约有 200 万次页面浏览量。它是在 FarCry 中端到端构建的，是 ColdFusion 的一个神话般的放大示例。来自 Mayo 团队的 Phil Truesdale 将于 6 月在悉尼的 webdu 开发者大会上发表演讲。
*   [blue scope Steel](http://www.bluescopesteel.com.au/)–blue scope Steel 是澳大利亚前 20 大公司之一，在其各种网络资产中使用 FarCry。澳大利亚产品网站可能是我见过的最好的制造/工业网站之一。它有巨大的内容深度，丰富的交叉引用，美丽的图像，互动工具，视频，谷歌地图集成，并直接连接到他们的呼叫中心。它也是几个品牌子网站的中心，这些子网站利用完全相同的内容，但呈现方式完全不同。Bluescope 非常重视自己的在线形象。
*   select v–select v 是澳大利亚付费电视提供商 FoxTel 极具竞争力的替代选择。该公司的网站是用 FarCry 构建的，并与后端 Websphere 客户端订阅引擎紧密集成。我们最近推出了一个完整的在线电视指南，包括个性化指南、印刷和 PDF 交付选项，以及短信/电子邮件提醒。FarCry 框架从 XML 频道提要的导入一直使用到成员服务和指南本身的呈现。

SitePoint:许可、社区、支持和文档呢？

FarCry 是根据[通用公共许可证 1.0](http://www.opensource.org/licenses/cpl1.0.php) 授权的。你可以创作衍生作品，随你喜欢分享或不分享。与其他开源许可证不同，这是一种商业友好的东西。

我们一直在尽最大努力让人们参与到 FarCry 社区中来，为此，我们投入了大量资金让 FarCry 可以在各种系统上使用:

*   FarCry 运行于 Adobe ColdFusion、New Atlanta Bluedragon 和 Railo 上，集成了一系列 ColdFusion 应用服务器，从商业选项到社区免费使用的应用服务器。
*   FarCry 支持 Linux、Solaris、Windows 和 OSX(基本上是任何可以运行 ColdFusion 服务器的系统)。
*   FarCry 可与 mySQL、Postgres、Oracle 和 MS SQL 数据库服务器配合使用。

社区本身是一个充满活力和友好的地方。我们提供尽可能多的服务来帮助人们学习、参与和了解代码库中每天发生的事情:

*   [论坛](http://www.farcrycms.org/go/support/community-support)
*   [bug 跟踪](http://bugs.farcrycms.org/)
*   [用于代码跟踪的鱼眼](http://fisheye.farcrycms.org/)
*   同行代码评审的坩埚(在鱼眼下运行)
*   [文档维基](http://docs.farcrycms.org/)

我们最近在知识共享下发布了 Daemon 的整个 [FarCry 开发者课程](http://docs.farcrycms.org/display/FCDEV40/Developer+Training+Course) (130 多页)。我们希望这将有助于开发者快速熟悉这个平台。

当然，Daemon 为投资 FarCry 作为其在线业务平台的公司提供一系列商业支持、开发和培训服务。

FarCry 的未来会是怎样的？

大新闻:“堡垒”，FarCry v5.0 版本，将于 2008 年 3 月发布——我们只是在 beta 测试的最后阶段。堡垒岩石！《堡垒》中有如此多的新特性，其中最大的一个是安全主题，与它同名。我们将在这次大规模发布后推出一系列插件，这些插件将利用和展示堡垒构建的许多新的底层服务。

SitePoint:人们在哪里可以找到更多关于 FarCry 的信息？

最好的起点总是在[社区网站](http://www.farcrycore.org/)和[开发者论坛](http://groups.google.com/group/farcry-dev)。但是如果你想放松一下，听听 FarCry 的概述，有几个即将到来的演示可能会让你感兴趣:

[FarCry 核心框架](http://www.cfframeworks.com/)
cf.frameworks 在线研讨会【2008 年 3 月 27 日下午 6-7 点(悉尼时间)
在线研讨会

[将 FarCry 与 ColdFusion 一起使用](http://events.adobe.co.uk/cgi-bin/register.cgi?country=pa&eventid=6503&venueid=6855)
Adobe Pacific 电子研讨会【2008 年 4 月 4 日下午 2-3 点(悉尼时间)
在线研讨会

[研讨会:FarCry 5.0 快速启动](http://www.webdu.com.au/go/session/workshop-farcry-5-0-jump-start)
webDU 开发者大会【2008 年 6 月 11 日，澳大利亚悉尼
全天研讨会

## 分享这篇文章