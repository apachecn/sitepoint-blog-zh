# 冷聚变的状态

> 原文：<https://www.sitepoint.com/the-state-of-coldfusion/>

以下内容转载自《科技时报》第 151 期。

在《科技时报》第 145 期上，我提供了一些建议，告诉初学者应该花时间学习哪些网络技术。[本·福塔](http://www.forta.com/)，Adobe 的高级技术传道者，[觉得当我把 ColdFusion](http://www.forta.com/blog/index.cfm/2006/8/4/Message-To-SitePoint-Editor-Regarding-Column-Inaccuracies) 描述为“相对停滞不前”时，我曲解了它的意思做了一些功课后，我现在稍微改变了我的想法。

首先，福塔的反对意见如下:

> 关于平缓的学习曲线你是对的，但是你能澄清“相对停滞”吗？明确一下，韦伯斯特对停滞的定义是“不前进，不发展”。ColdFusion 于 1995 年首次发布，ColdFusion MX 7 于 2005 年发布，同年晚些时候发布了 7.0.1，2006 年 6 月发布了 7.0.2。此外，ColdFusion 团队正在努力开发 ColdFusion 的第八个主要版本(目前代号为“Scorpio”)，将于 2007 年发布。显然，我们都在推进和发展 ColdFusion，因此“停滞”这个词是完全不恰当的。因此，我必须要求你更新和更正你的声明。

Forta [继续指出 ColdFusion 位于 Java 基础之上，因此受益于该平台的全部功能和灵活性。我在这里的想法是，除了 ColdFusion 直接公开的内容之外，要利用 Java 平台，您需要学习 Java，这使得它成为初学者应该首先学习什么的讨论中的一个争论点。](http://www.forta.com/blog/index.cfm/2006/8/4/Message-To-SitePoint-Editor-Regarding-Column-Inaccuracies)

回到福塔的主要观点，当然，语义是无可争议的。如果衡量服务器端平台健康状况的标准是发布频率，那么 ColdFusion 仍然存在。但即使考虑到这个毫无意义的指标，与 Ruby on Rails、PHP 或令人眼花缭乱的 Java web 应用程序框架等竞争平台的发布时间表相比，ColdFusion 看起来“相对停滞不前”

我们还能如何衡量一个平台的健康状况？嗯，一种方法是查看图书销售情况(这是我们在 lot 网站 SitePoint 上做的事情)。2006 年[OSCON](http://conferences.oreillynet.com/os2006/)上，奥赖利展示了过去三年的图书销售结果。ColdFusion 甚至没有制作图表，但是[当被问及](http://radar.oreilly.com/archives/2006/08/programming_language_trends_1.html#c136788)时，蒂姆·奥雷利[回答](http://radar.oreilly.com/archives/2006/08/programming_language_trends_1.html#c136860):“如果我们制作图表，ColdFusion 将在底部显示为一条平线。”当然，如果一个平台有特别好的文档，它可能不需要书籍，ColdFusion 可能就是这种情况。或者可能只是 ColdFusion 的发展速度不够快，人们不需要关于它的新书。

或许衡量一个平台健康状况的方法是看它周围的就业市场。我看到[一些报道](http://kay.smoljak.com/archives/?ColdFusion-on-the-up-again/)说 ColdFusion 就业市场最近有所好转，所以让我们来看看。与 Ruby jobs 相比，ColdFusion 目前看起来是一个足够可靠的选择。

[![ColdFusion vs. Ruby job trend graph](img/569083231ceba47247654b39a35a28cf.png)](http://www.indeed.com/jobtrends?q=coldfusion%2Cruby)

请注意，“ColdFusion”和“ColdFusion”的数字大致相等，所以如果你不介意为一家不会拼写的公司工作，你可以有效地将 Cold Fusion 的数字翻倍。

当你加入其他更成熟的平台，如 PHP 和 ASP.NET，很明显，如果你是为了新的职业而玩数字游戏，ColdFusion 不是最好的选择。

[![ColdFusion vs. others job trend graph](img/1fb47559c59f8f20621cc636bd73c6cd.png)](http://www.indeed.com/jobtrends?q=coldfusion%2Cruby%2Cphp%2Casp.net)

即使你将图中的 ColdFusion 线翻倍，它仍然只是 PHP 就业市场的 2/3，而 ASP.NET 和 Java(没有显示，因为它压倒了其他)是更好的选择。

但是不，我说的不是这些形式的恶心。相反，我对 ColdFusion 的描述是基于一种印象，即该平台的开发速度已经慢到无法像前面提到的竞争对手那样响应现实世界 web 开发人员不断变化的需求。即使在 ColdFusion MX 7 这样的主要版本中，对平台的更改似乎也主要包括少数开发人员感兴趣的附加功能(如集成报告)或与 Macromedia(现为 Adobe)的其他属性(如 Flex)的搭配。

然而，基于福塔的观点，我认为最好重新审视一下这个前提。我阅读了许多 ColdFusion 博客，阅读了 ColdFusion 开发人员杂志，并与 ColdFusion 社区的一些活跃成员进行了交谈。我不得不说，在某些方面我感到惊喜。

我最近在 ColdFusion 相关博客上观察到的一个趋势是大量 ColdFusion 8 愿望列表。戴夫·卡拉贝塔发表了一篇特别全面的文章。这些列表很好地展示了 ColdFusion 的高级用户希望该平台走向何方。某些项目令人担忧:

> ColdFusion 问世已有 11 年，但它没有任何内置功能来真正处理任何有用级别的图像。

> ColdFusion 需要一个专业的 IDE。我最近一直在使用 Flex 2，看到 Flex Builder IDE 相对于 CFEclipse IDE 的专业水平简直令人震惊。

然而，我期望看到的列表中没有的东西是一个更强大的应用程序框架。这似乎是 ColdFusion 社区真正负责的一个领域:Fusebox、Model-Glue、Reactor、ColdSpring、unity 和许多其他项目是构建 ColdFusion 应用程序的不同方法的基础。

接下来，我坐在 2006 年夏季版的[融合权威](http://fusionauthority.com/)旁边，几周前，我偶然看到了一本免费的。首先，让我说，这是一个一流的出版物，大人物和热情的暴发户写及时和实用的文章恰到好处的混合。

通过这本杂志刷新了我对 ColdFusion 代码的了解，我对平台所体现的“隐藏硬东西”的方法有了新的认识。如果你是那种只想完成工作而不想陷入技术细节的人，ColdFusion 可能是唯一适合你的风格。

然而，上个月我在 [Web Directions South](http://webdirections.org/) 遇到的凯·斯莫雅克的一篇文章引起了我的注意。在“诚实地看待集成报告”中，她描述了她在实际使用 ColdFusion MX 7 中的一个旗舰新功能时遇到的问题。坦率地说，她描述的错误的严重性令人震惊，尽管听起来 Adobe 对她的错误报告做出了回应，在最近的 7.0.2 版本中纠正了许多问题，但人们不得不怀疑 a)Adobe 如何能够发布一个有如此根本缺陷的旗舰新功能，以及 b)除了 Kay Smoljak 以外，似乎没有人注意到这一点。

在我看来，这标志着 ColdFusion 的核心开发与其用户群的现实需求之间的脱节。ColdFusion MX 7 的集成报告功能中的错误没有引起公众的强烈抗议，原因是很少有人愿意使用该功能。当 Macromedia/Adobe 花费时间构建没有人使用的集成报告，而不是像动态图像生成这样基本有用的功能时，这是非常错误的。

好消息是，Adobe 已经宣布[cold fusion 8*将在*](http://ray.camdenfamily.com/index.cfm/2006/10/24/ColdFusion-8-Image-Support)*中内置动态图像生成功能。然而，我不得不问凯·斯莫亚克对这一切有什么看法。*

 *> 事实上，我在成为其他人之前很久就是 ColdFusion 的开发人员，而且我对 ColdFusion 的整体印象非常好。[……]不要误会我的意思，ColdFusion 集成报告是一堆冒着热气的狗屎。但我还是平台的粉丝。也许他们没有给更广泛的开发者社区留下正确的印象，但是有很多事情正在发生。[…]
> 
> 我对被 ColdFusion 吸引的开发人员的类型有一些想法，我认为这导致了“停滞不前的印象”——他们往往是“完成工作并转向下一个目标”类型的人，而不是编码爱好者——他们只是不像 Ruby On Rails、PHP 和。网络狂热者。

…就目前而言，这些都很合理。

我不断回到的问题是:当你选择为 ColdFusion 开发时，你到底在为什么付费？我们不要忘记，Adobe 的 ColdFusion 服务器的许可费用相当高，而你的雇主不会将这笔钱投入服务器硬件、开发工具或最终进入你的口袋。你得到了什么回报？它没有更快的错误修复，没有更大的就业市场，没有更丰富的功能集，也没有坚如磐石的可靠性。这可能是及时的支持，但其他平台也提供这种支持，而且没有前期成本。

最后，我所知道的今天选择 ColdFusion 的唯一坚实的理由是，如果你只是喜欢它的做事方式。但是，当我们将 ColdFusion 与竞争平台进行比较时，我真的相信 ColdFusion 的核心开发已经停滞了一段时间。随着明年 ColdFusion 8 的发布，这一切可能都将改变，多亏了 Adobe 实验室，我们应该能够在这个新版本上市之前很久就知道了。但是基于这个平台现在所处的位置，我必须坚持我的建议，web 开发的新手应该去别处看看，至少现在是这样。* 

## *分享这篇文章*