# Sun 开发者日 2006:第一天

> 原文：<https://www.sitepoint.com/sun-developer-days-2006-day-one/>

Sun Microsystems 即将结束澳大利亚和新西兰之旅，今天和明天在墨尔本举行的 Sun Developer Days 2006 会议是其最后一站。SitePointer 的同事 Lachlan Donald 和我今天在那里感受了 Java 的氛围，和大多数免费会议一样，我们参加的会议质量有很大的差异。

## <cite>Sun 开发者工具</cite>，Bob Brewin (Sun)

在他的主题演讲中，Brewin 先生主要谈到了 Sun 在整个 Java 平台和 Sun 开发人员工具集上对易开发性(EoD)的推动。这方面的主要例子包括 Java SE 5.0 中的[新语言特性，](http://java.sun.com/developer/technicalArticles/releases/j2se15/) [Java Studio Creator](http://developers.sun.com/prodtech/javatools/jscreator/) 的免费下载版本，以及 [Java Persistence API](http://java.sun.com/j2ee/persistence/faq.html) ，它将取代 EJB 3.0 中的实体 beans。然而，这都是旧闻了，布鲁因看起来像是已经发表了五十次这样的主题演讲。尽管对 Java 前景的一些改进可能是鼓舞人心的，但这次会议却不是。

## <cite>改变软件开发的前景</cite>，Simon Ritter (Sun)

Ritter 先生的主题演讲基本上涵盖了 Sun 用 Java 促进开源开发的所有方式。再说一次，这次谈话对房间里的人来说很少是新闻，里特似乎对这个事实感到有点尴尬(甚至对“他的老板”在为他组装幻灯片时选择的一些图像大声感到惊讶)。会议的结尾描述了 Java 与 T1 一样好的所有方面，如果不是开源的话，似乎完全是在为自己辩护…这尤其奇怪，因为对观众的快速调查表明只有一小部分人认为应该这样。

## 使用 Sun Java Studio Creator 快速开发 Web 应用程序

非常快速的介绍，这个会议通过 [Java Studio Creator](http://developers.sun.com/prodtech/javatools/jscreator/) 快速旋转，这是一个使用 [JavaServer Faces (JSF)](http://java.sun.com/javaee/javaserverfaces/) 的拖放式 web 应用程序开发工具。我在过去看过几个类似的介绍，虽然我称赞 Sun 用 Creator 在使快速组合 Java web 应用程序成为可能方面取得的成就，但我仍然不相信它在产生最佳实践代码方面的价值(至少不是现成的)。

与微软的 Visual Studio 的 [Visual Web Developer](http://msdn.microsoft.com/vstudio/express/vwd/) 版本非常相似(Creator 显然是为了与 Visual Studio 竞争而设计的)，Creator 允许您通过将现成的组件拖到设计视图上来快速生成 Web 用户界面。每当我想到天真地使用这种开发机制(特别是在默认的“网格”布局模式下，它利用绝对像素坐标来定位所有页面元素)的可访问性后果时，我都会不寒而栗。

也就是说，Creator 基于的 JSF 技术肯定能够生成符合标准的、可访问的网站。我想知道的是，Creator 似乎准备好产生的快速而肮脏的结果是工具的错误(在这种情况下，我谦卑地建议 Sun 应该回到绘图板)，捆绑的组件(在这种情况下，一个专门的开发人员小组可以产生一组更“负责任”的组件以在 Creator 中使用)，还是只是开发人员选择使用它们的方式(在这种情况下，SitePoint 可能应该写一本书，类似于其最近的 Dreamweaver 标题。

## <cite>SAP 技术会议:以</cite>Taib lok man(SAP)的身份在 SAP Web 上运行基于开源的 Java EE 应用程序

虽然我确实见过名称 [SAP](http://www.sap.com/) 四处流传，但我并不真正理解它如何融入 Java 领域。原来，SAP 已经在 [Java 企业开发业务](https://www.sdn.sap.com/)有一段时间了，现在它提供自己的 Java EE 服务器套件和基于 Eclipse 的开发工具。SAP 赞助了这次会议，所以我期待在这次会议中有一次推销。相反，我得到的是意想不到的，非常令人困惑。

在他的演讲中，Lokman 先生介绍甚至演示了许多流行的开源 Java 企业技术(Struts、Axis、Ant 等。)，似乎认为它们是我们都应该使用的好东西，然后解释说 SAP 有自己或多或少的专有替代方案，如果您被要求参与依赖 SAP 平台的项目，您将不得不处理这些替代方案。就这样。说真的，就像“谁会用 Struts？太好了！是不是很酷？但如果你使用 SAP，你需要学习 NetWeaver，这是完全不同的，”冲洗并重复。

## Java SE 的持久性& Java EE ，Rima Patel (Sun)

作为《掌握 EJB》一书的合著者，第三版。 ( [免费 PDF 下载](http://www.theserverside.com/books/wiley/masteringEJB/index.tss))，显然第四版即将推出，也将免费提供下载，帕特尔女士在企业 JavaBeans (EJB)方面真的很在行。这转化成了一个充满活力和启发性的谈话，谈论我们可以从即将发布的 EJB 3.0 中期待什么。

EJB 3.0 最大的变化是对实体 beans 的彻底反思和简化。实体 bean 用于将数据库记录(和其他持久数据项)表示为可以参与事务并使其状态与数据库同步的 Java 对象，长期以来一直是 EJB 的一个严重问题。它们要求开发人员编写大量重复的样板代码，并且在许多方面受到限制，而 Java 对象通常不会受到限制。在 EJB 3.0 中，实体 bean 已经被抛弃，取而代之的是 [Java 持久性 API](http://java.sun.com/j2ee/persistence/faq.html) ，它允许您使用标签普通旧 Java 对象(POJOs)作为实体，它可以做实体 bean 可以做的所有事情，但没有所有繁琐的编码开销，也没有实体 bean 的所有限制。

一段时间以来，Java 开发人员已经以 [Hibernate](http://www.hibernate.org/) 的形式受益于许多 Java 持久性 API，这是一个将关系数据库记录映射到 Java 对象的开源库。然而，有了 Java Persistence API，这些强大的功能不仅将成为 Java EE 平台的标准部分，而且它们将能够在 EJB 体系结构中工作，具有 EJB 设计提供的所有资源和事务管理。

## <cite>解决复杂性&提高开发 SOA 的生产力</cite>，Ashwin Rao(孙)

我对面向服务的体系结构(SOA)没有太多的了解，SOA 是一种新兴的企业开发愿景，其中离散的、专门的服务可以组装和编排在一起，以产生具有高度可伸缩性和灵活性的有用的应用程序。这当然是大企业前进的方向，但是在我们观众中大多数开发人员居住的基层，这不是一个很大的因素。这个会议是一个检查 SOA 状态的机会。

Rao 先生简要概述了当今 SOA 的实际情况，然后总结说，管理由此产生的复杂性意味着要拿出一些工具，以一种有用的方式解决编排不同服务的独特挑战。这为演示 Java Studio Enterprise 8(T1)的 UML 建模特性以及 Sun 最近在发布的 Netbeans 5.5 Enterprise Pack(T3)预览版中首次亮相的业务流程执行语言可视化设计器(BPEL)提供了一个方便的切入点。

BPEL 设计器允许您将多个 web 服务组装在一起以产生组合服务，本质上是通过绘制一个流程图来在幕后生成所需过程的 XML 描述。尽管该工具生成的流程图不可否认地很流畅，但是每当我们浏览生成的 XML 代码时，我觉得我们可以用比通过向导以图形方式生成代码更少的时间来编写代码。假设 SOA 起飞，复合服务将开始涉及比我们看到的简单演示更复杂的交互，我可以看到可视化这些交互的价值，而不是苦读 XML 代码；然而，给我留下的印象是，需要解决的 SOA 问题仍在不断被发现，因此，这些工具至少还需要一代人的时间才能在野外变得真正有用。

## NetBeans Extreme:马蒂斯，协作，剖析和插件

在这场长达 90 分钟的马拉松式会议中，凯瑟多女士展示了 Sun 免费 Java IDE 的最新版本 NetBeans 5.0 的各个方面，该版本在最新版本中得到了增强。

NetBeans 5.0 通过引入 [Matisse](http://form.netbeans.org/) 在桌面应用程序领域引起了轰动，这是一个出色的新 GUI 布局工具，它消除了 Java 开发人员从 Java 的 Swing GUI API 中接受的大量复杂性，而没有牺牲成品的灵活性或质量。Matisse 没有依赖 Swing 现有的`LayoutManager`类(比如臭名昭著的`GridBagLayout`，而是使用了自己的`GroupLayout`类，该类已被列入即将发布的 [Java SE 6.0](http://java.sun.com/javase/6/) 。马蒂斯在这个会议上的演示很吸引人，如果不是完全说服像我这样的资深用户的话。它可能远远领先于 Java 过去拥有的任何可视化 GUI 布局工具，但这个演示遇到了几个小的布局问题，我可以用`GridBagLayout`避免这些问题，我很好奇马蒂斯是否提供了这些问题的解决方案。

协作功能无疑是一个惊喜，我不知道在围绕 NetBeans 5.0 发布的激动人心的时刻，我怎么会错过他们的提及。类似的特性在去年的 JBuilder 2006 中形成了主要的增强，但是随着 IDE 的未来处于严重的危险中，很高兴看到这些特性成为像 NetBeans 一样健康的 IDE。本质上，IDE 包括一个即时消息客户端，它不仅允许您与其他开发人员聊天，还允许您共享本地项目代码，通过共享视图协作浏览和编辑代码，甚至运行共享调试会话来一起定位问题。我曾假设，为了实现这一点，两个开发人员都需要在他们的系统上有他们自己的项目副本，但是演示展示了这样一个事实，即共享编码会话实际上是围绕一个开发人员的项目副本进行的，而所有其他开发人员都需要一个 NetBeans 副本来参与。

接下来，Caicedo 尝试了一下 NetBeans 中的分析功能，确定了负责挂起简单 Swing 应用程序接口的线程，然后确定了 web 应用程序中的一个慢速方法。与其他更受欢迎的免费 Java IDE Eclipse 相比，新的分析器是 NetBeans 最大的优势之一，后者缺乏内置的分析功能。虽然不像专门的分析产品如 [YourKit Java Profiler](http://www.yourkit.com/) 那样强大，但 NetBeans 5.0 中的工具肯定是值得尊敬的，将有助于解决除最棘手的性能问题之外的所有问题。

最后，但同样重要的是，我们看到了一个完整的 NetBeans IDE 插件在我们眼前出现。随着 Java 开发跨越如此多不同的应用领域，一个 IDE 不可能捆绑给定开发人员可能需要的所有工具。这就是为什么现在所有的大 ide 都非常强调插件架构的可扩展性。NetBeans 也不例外，通过集成的向导和打包工具，Caicedo 能够在几分钟内将 Google 搜索字段添加到 NetBeans 工具栏中。IDE 插件开发，至少对于 NetBeans 来说，肯定没有我之前想象的那么可怕。

那么，我明天会转用 NetBeans 吗？恐怕不行。尽管 NetBeans 5.0 已经取得了一些令人印象深刻的进步，而且该工具中的新功能也确实令人印象深刻，但许多基本功能仍然有一种“玩具 IDE”的感觉，我就是无法摆脱这种感觉。缺乏像多重运行配置和项目组这样的基础设施对我来说不太合适。尽管如此，新功能和旧功能还是有很多值得喜欢的地方(我特别欣赏 Ant 在所有构建任务中的使用)，所以我一定会密切关注 NetBeans。

总的来说，在一个疲软的开始后，随着话题从一般的旋转转向向下和肮脏的编码，这一天变得有趣多了。希望明天会有更多同样的事情发生！

## 分享这篇文章