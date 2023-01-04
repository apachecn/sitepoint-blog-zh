# JavaOne 大会 2016–Nucleus

> 原文：<https://www.sitepoint.com/javaone-2016-nucleus/>

JavaOne 2016 结束了！已经两个星期了，所以我的报告可能有点晚了。嗯，只是花了一些时间看了足够多的演讲，对正在发生的事情有了一个印象。但即使两周也不够——甲骨文从 JavaOne 2016 上传了大约一百个[视频！所以我必须选择一个主题，我决定保持接近核心:语言和核心库，标准版和企业版，他们自己。](https://www.youtube.com/channel/UCdDhYMT2USoLdh4SZIsu_1g/videos)

虽然很诱人，但我放弃了偏离 Java 驱动力的谈话。所以，不，我甚至没有看关于 JUnit 5 的讨论。

## Java SE

先说 [Java SE 主题演讲](https://www.youtube.com/watch?v=G5xw1lMKmvA)。老实说，我并不觉得导言或马自达报告特别激动人心。但这只是 30 分钟演示的前三分之一。

当马克·莱因霍尔德开始使用技术手段时，事情变得更有趣了。他谈到了 Java 9 的总体情况(我将在下面进一步讨论)，并特别谈到了 *jShell* 和 Jigsaw 项目。当 [Brian Goetz 和他一起出现在一个笨拙编排的入口时，事情变得更加有趣。](https://www.youtube.com/watch?v=G5xw1lMKmvA&t=28m40s)

想象一下，在未来的某个时刻，我们也许可以这样写:

```
public class Point(int x, int y) { } 
```

编译器生成字段、构造函数、访问函数、适当的 [`equals`](https://www.sitepoint.com/implement-javas-equals-method-correctly/) 、 [`hashCode`](https://www.sitepoint.com/how-to-implement-javas-hashcode-correctly/) 和`toString`。哇！

还有更好的！(或者更糟，取决于你对清晰性的偏好程度。)

```
var url = new URL("https://sitepoint.com/java");
var connection = url.openConnection(); 
```

Brian 说得很清楚，这并没有削弱 Java 的类型系统。这只是意味着编译器推断出比现在更多的类型。

最后但同样重要的是，布莱恩快速总结了项目[瓦尔哈拉](http://openjdk.java.net/projects/valhalla/)和[巴拿马](http://openjdk.java.net/projects/panama/)。

在进入更有针对性的讨论之前，我想推荐一下 JDK 建筑师 ，马克·莱因霍尔德(Mark Reinhold)、约翰·罗斯(John Rose)和布莱恩·戈茨(Brian Goetz)在这里回答了关于 Java 和 JVM 的随机问题。这是一个很好的大杂烩，解释了为什么 Java 是现在这个样子，以及它在未来会如何改变。

### Java 8

我发现有意思的是，有很多关于 Java 8 的演讲，其中很多仍然在介绍一些基础知识。其他的，对我来说更有意义的，是更先进的，帮助开发人员，他们大部分已经每天使用它一段时间了，以获得正确的细节。

Stuart Marks 的[*Collections fueled*](https://www.youtube.com/watch?v=LgR9ByD1dEw)([幻灯片](https://stuartmarks.files.wordpress.com/2016/09/collectionsrefueled-final.pdf))演讲的前半部分展示了 Java 8 中对 Collections 框架的一些扩展，由于围绕流的大肆宣传，这些扩展可能被忽略了。例如，我不知道`Collection::removeIf`、`List::replaceAll`，或者`List::sort`如何提高排序性能。在转向 Java 9 之前，他完成了`Comparator`工厂。

另一个确保你从 Java 8 中获得一切的演讲是文卡特·苏布拉马年的[*Java 8*](https://www.youtube.com/watch?v=GphO9fWhlAg)中的一些隐藏的宝藏，其中他涵盖了各种主题，如字符串连接、[默认方法](http://blog.codefx.org/java/everything-about-default-methods/)和并行流。

谈到并行流，如果您想了解更多关于如何创建良好并行化的解决方案的信息，请观看 Stuart Marks ' and Brian Goetz '[*并行思考*](https://www.youtube.com/watch?v=iDplU7mOocU) 。在这里，他们给出了如何设计好的解决方案和评估并行流性能的简洁心智模型。

但是我们不要因为 Venkat 的现场编码马拉松而分心。在 [*让我们变懒:探索溪流的真正力量*](https://www.youtube.com/watch?v=873E68cylNo) 他一头扎进了懒惰，以及为什么懒惰的评价是有益的。他在《用 Java 8 重构为函数式风格》中又做了一次——从 Java 8 的函数式编程特性到用 Java 8 进行函数式编程的旅程。为什么高阶函数、不变性、lambdas、streams 和懒惰是相关的，它们是如何结合在一起的？

我非常喜欢流 API 中的 [*旅程的终点:收集和还原，它深入研究了还原和收集器。正如我在其他地方写的，我*](https://www.youtube.com/watch?v=_rcRzIs4uBw)*[不是复杂流收集](http://blog.codefx.org/java/rebutting-5-common-stream-tropes/#Collector-Magic)的粉丝，但这并不意味着如果你需要知道如何做就不好。此外，Maurice Naftalin 也提供了许多有趣的背景信息。*

![Published by Hubble Heritage under CC-BY-SA 2.0](img/f7491c49bc82ada2c9d426a1e4cf9794.png)

[发表](https://www.flickr.com/photos/hubble-heritage/8497132547/ "Source on Flickr")由[哈勃遗产](https://www.flickr.com/photos/hubble-heritage/ "Author on Flickr")下 [CC-BY-SA 2.0](https://creativecommons.org/licenses/by-sa/2.0/ "Link to Licence")

### Java 9

Java 9 当然是一个大话题，有很多关于它的讨论。不过，在开始介绍新版本的一些细节之前，我想为那些还没有花很多时间学习 Java 9 的人推荐两个特别的讲座。

第一篇， [*JDK 9 的语言、工具和库特性*](https://www.youtube.com/watch?v=vKYzmIi_1LM) 由 Joseph Darcy 撰写，对 Java 9 进行了高层次的概述。给予或接受，这几乎是我的[Java 9](https://www.sitepoint.com/ultimate-guide-to-java-9/)终极指南的口语版本(但它也涵盖了一些仍未出版的第二部分)。

然后是 [*为 JDK 9*](https://www.youtube.com/watch?v=eU8hCCjGSbE) ( [幻灯片](http://openjdk.java.net/projects/jigsaw/talks/prepare-for-jdk9-j1-2016.pdf))，艾伦·贝特曼在其中探讨了我们的项目应该做些什么来为新版本做准备。他提到的一些事情是 Jigsaw 将使哪些内部 API 不可访问以及如何处理，新的运行时文件系统布局，以及即将到来的版本字符串格式。最后，他解释了非常酷的多版本 jar 是如何帮助项目保持兼容多个版本的。

#### 拼图项目

作为所有关于 Java 9 的帖子，我们必须谈谈 Jigsaw 项目和即将到来的 Java 平台模块化系统(JPMS)。拼图小组提出了一系列的谈话，每一个都建立在以前的基础上。

在 [*模块化开发简介*](https://www.youtube.com/watch?v=2Hmrn_r-uJA) ( [幻灯片](http://openjdk.java.net/projects/jigsaw/talks/intro-modular-dev-j1-2016.pdf))中艾伦·贝特曼对模块系统进行了介绍。他解释了如何创建模块以及 JVM 如何处理它们。关键词是*模块声明*、*可读性*和*可访问性*； [jlink 也被引入](https://www.youtube.com/watch?v=2Hmrn_r-uJA&t=9m53s)。

接下来的大部分演讲，Alex Buckley 和 Alan Bateman 的 [*高级模块化开发*](https://www.youtube.com/watch?v=WWbw8u5jaaU)([幻灯片](http://openjdk.java.net/projects/jigsaw/talks/adv-modular-dev-j1-2016.pdf))，讨论了如何将现有的库和应用迁移到模块系统(主要通过*未命名模块*和*自动模块*)。演讲以 jlink 的 [*延续结束，然后以一轮*](https://www.youtube.com/watch?v=WWbw8u5jaaU&t=30m15s)*[广泛的问题](https://www.youtube.com/watch?v=WWbw8u5jaaU&t=35m40s)结束——有些有趣，有些不那么有趣。*

将客户端从它们所依赖的接口的实现中分离出来的一个关键因素是第三方检测实现并使它们对客户端可用。这通常由 [DI 容器](https://en.wikipedia.org/wiki/Dependency_injection)来完成，它们也注入实现。另一种方法是[服务定位器模式](https://en.wikipedia.org/wiki/Service_locator_pattern)，其中客户必须访问中央注册中心来请求实现。Java 已经通过 [`ServiceLocator`类](https://docs.oracle.com/javase/8/docs/api/java/util/ServiceLoader.html)支持后者，正如 Alex Buckley 在 [*模块和服务*](https://www.youtube.com/watch?v=u8Hbdo-u-88) ( [幻灯片](http://openjdk.java.net/projects/jigsaw/talks/modules-and-services-j1-2016.pdf))中所介绍的，该类已经被扩展以允许模块轻松地发布和消费服务。

最后，在 [*项目拼图中:引擎盖下*](https://www.youtube.com/watch?v=Vxfd3ehdAZc) ( [幻灯片](http://openjdk.java.net/projects/jigsaw/talks/jigsaw-under-the-hood-j1-2016.pdf))亚历克斯·巴克利将观众带到了下一个层次。他解释道:

*   模块系统和类装入器的交互以及层的概念
*   如何使依赖性可传递(`requires transitive`)
*   封装和反射之间对抗的当前状态(`exports private`)
*   不同种类的模块(命名、未命名、自动)

除非你一直密切关注 Jigsaw，否则你会在这里学到很多新东西。强烈推荐！

如果你仍然不能满足，请观看艾伦·贝特曼、钟学心和马克·莱因霍尔德的 [*项目拼图黑客会议*](https://www.youtube.com/watch?v=w4lLd-JOyRU) 。

#### 蜜蜂

我有点惊讶地发现只有两篇关于 Java 9 给游戏带来的新 API 的演讲，都是由 Stuart Marks 发表的——也许我只是忽略了其他人？

第一个事实上是由斯图尔特的另一个自我，反对者博士本人持有的。在 [*增强弃用*](https://www.youtube.com/watch?v=T_O9merCgKw) ( [幻灯片](https://stuartmarks.files.wordpress.com/2016/09/enhanceddeprecation-final.pdf))中，医生深入探讨了 Java 弃用历史和生命周期背后的细节，Java 9 中对`@Deprecated`的补充，新工具( *jdeprscan* )，以及未来的弃用工作。

我已经提到了另一个， [*集合加油*](https://www.youtube.com/watch?v=LgR9ByD1dEw) ( [幻灯片](https://stuartmarks.files.wordpress.com/2016/09/collectionsrefueled-final.pdf))，因为它是从谈论 Java 8 开始的。第二部分从[开始到](https://www.youtube.com/watch?v=LgR9ByD1dEw&t=26m53s)的一半，解释了为什么没有集合文字(简而言之:集合是库的一部分，而不是语言的一部分，所以语言级的支持会很尴尬)。相反，我们得到了集合工厂方法，Stuart 深入介绍了这些方法，包括不变性、随机迭代顺序等细节。在最后的几分钟里，他制定了近期和远期的计划。

#### 工具作业

当谈到 Java 9 和 Jigsaw 项目时，构建工具可能是最有趣的话题。他们怎么样了？在 [*Java 9 和对 Maven 项目的影响*](https://www.youtube.com/watch?v=Wef9p4ykNMM) 中，Robert Scholte 讨论了他必须做的所有事情，以使 Maven 与 Java 9 一起工作。如果你用的是 Maven，你绝对应该看看这个！有趣的花絮是[对多版本 jar](https://www.youtube.com/watch?v=Wef9p4ykNMM&t=16m40s)的支持， [extra enforcer 规则*禁止重复类*](http://www.mojohaus.org/extra-enforcer-rules/banDuplicateClasses.html) 是检测分裂包的好方法，如果你想保持最新，[Java 9 的 Maven Wiki 页面](https://cwiki.apache.org/confluence/display/MAVEN/Java+9+-+Jigsaw)。一个不幸的细节是，目前还不支持从`pom.xml`生成`module-info.java`，这让开发人员不得不在两个地方维护依赖关系。

如果你看过文卡特·苏布拉马年的代码直播，你会和我一样相信 *jShell* ，Java 自己的全新的读取-评估-打印循环(简称 REPL)，是专为他制作的。于是在 [*用 Java 9 交互开发并快速反馈的 REPL*](https://www.youtube.com/watch?v=DHTVcq_fK2U) 他拿着它进行了试驾。您将了解什么是 REPL，它有什么用，以及如何使用 Java 的。

在[*JDK 故障排除简介 9*](https://www.youtube.com/watch?v=_JRNFu3Qbtg) ( [幻灯片](https://static.rainfocus.com/oracle/oow16/sess/14627958356770011JJj/ppt/JavaOne2016_CON3733.pdf))中，久保田佑司、末永保正和乔雄信治解释了 JDK 9 中引入或显著改进的服务工具 jhsdb、jcmd 和 HeapStats 代理。它们介绍了如何在 JVM 上线后配置它，获取线程/堆转储或类直方图，以及检测本机内存泄漏。

## Java EE

首先:Java EE 8 计划于 2017 年 9 月发布，一年后发布第 9 版。

企业版的愿景在 [*Java EE 主题演讲*](https://www.youtube.com/watch?v=ZqfjW-RQPOs) 中呈现。一个字:云。更多的话:云，微服务，云，hypewords，云。值得注意的是，Anil Gaur 特意强调甲骨文*正在*倾听(他的重点，不是我的)其合作伙伴和客户。他们中的一些人被邀请上台支持 Java EE 路线图。[最后](https://www.youtube.com/watch?v=ZqfjW-RQPOs&t=28m5s)，在一个可能比 Brian Goetz 更尴尬的条目之后，甚至出现了一些代码。

### Java EE 8

如果你对 Java EE 联盟的现状感兴趣，尤其是即将到来的第 8 版，琳达·德米希尔的 [*Java EE 8 更新*](https://www.youtube.com/watch?v=Th9faGLhQoM) 已经涵盖了你。不过，她不仅谈到了 8，还对重点领域和有趣的建议提出了见解，例如反应式编程、断路器和安全性。[最后](https://www.youtube.com/watch?v=Th9faGLhQoM&t=42m10s) Linda 列出了其他与 Java EE 相关的讲座。以下是我可以找到视频的非云产品:

*   [*JAX RS 2.1 for Java EE 8*](https://www.youtube.com/watch?v=0KArzAbaPWQ)Ed Burns 和 Pavel Bucek
*   [*Servlet 4.0:状态更新，HTTP/2 来到 Java*](https://www.youtube.com/watch?v=T9HPLLXjGzI)Ed Burns
*   [*Java EE Next–HTTP/2&休息机会*](https://www.youtube.com/watch?v=BwwR1C_Lvpc) 帕维尔·布切克和大卫·德拉巴西
*   [*JSF 2.3 社区接管*](https://www.youtube.com/watch?v=Fhv-qb47PNY) 艾德·伯恩斯和基托·曼
*   [*配置为 Java EE 8 和云*](https://www.youtube.com/watch?v=nWrx2SV82PM) 由 Dmitry Kornilov
*   *Kk Sriramadhesikan 的 Java EE 8 和云的安全性*(仅[幻灯片](https://static.rainfocus.com/oracle/oow16/sess/1471981490115001GrZ4/ppt/CON7978_Sriramadhesikan_JavaOne2016_SecurityForJavaEE8AndTheCloud.pdf)

### 云妈妈

正如我们在主题演讲中了解到的，云非常重要:

*   [*面向云的企业 Java*](https://www.youtube.com/watch?v=t7miysQP7Dg)作者 Rajiv Mordani、Josh Dorr 和 Dhiraj Mutreja
*   Rajiv Mordani、Josh Dorr 和 Joe Di Pol 的 [*可移植的云应用程序和 Java EE*](https://www.youtube.com/watch?v=nCqVSf5v37s)
*   [*Oracle Java 云服务上的 Java EE 应用*](https://www.youtube.com/watch?v=TW8ZVQvWtfU)Harshad Oak
*   [*将 Java EE 应用迁移到微服务和 Oracle Java Cloud*](https://www.youtube.com/watch?v=s95NonOYflk) 作者 Tilen Faganel 和 Matjaz B. Juric
*   [*云原生 Java EE*](https://www.youtube.com/watch?v=OtCNZGOMlU0) 作者麦克·克罗夫特和翁德雷·米哈里

我想知道这些谈话可能会有多少不同？我也是！(我没有看——不完全是一个 JEE 人。)

## 还有什么？

这是 Java SE 和 EE 对话的全部内容。但是还有更多。两个特别突出的主题是微服务和工具。如果你对这些感兴趣，可以看看 [JavaOne 2016 播放列表](https://www.youtube.com/playlist?list=PLPIzp-E1msrYicmovyeuOABO4HxVPlhEA)。

如果您想听听其他人对 JavaOne 的看法，请查看以下帖子:

*   [Groovy 播客#33，*来自 JavaOne 2016*T3 的现场直播](http://groovypodcast.podbean.com/e/groovy-podcast-ep-33-live-from-javaone-2016/)
*   [*JavaOne 2016 后续*](http://jj-blogger.blogspot.de/2016/09/javaone-2016-follow-up.html) 乔希·朱诺号
*   每日报告由[索莉塔](https://www.solita.fi/) : [第一天](http://dev.solita.fi/2016/09/20/javaone2016-day1.html)，[第二天](http://dev.solita.fi/2016/09/21/javaone2016-day2.html)，[第三天](http://dev.solita.fi/2016/09/22/javaone2016-day3.html)，[执行摘要](http://dev.solita.fi/2016/09/26/JavaOne-executive-summary.html)(如果你只看一个，就做这个吧)
*   更多每日报道，这次由[卡诺](http://www.canoo.com) : [第一天](http://www.canoo.com/javaone-2016-day-i/)，[第二天](http://www.canoo.com/javaone-2016-day-ii/)，[第三天](http://www.canoo.com/javaone-2016-day-iii/)
*   [*JavaOne 2016 概要*](http://wavelengths.freewave.com/featured/javaone-2016-recap/) 由 [FreeWave](http://freewave.com/)
*   [*甲骨文 JavaOne 2016*](http://blog.whitehorses.nl/2016/09/25/oracle-javaone-2016/) by [白马](http://whitehorses.nl)

不像我，他们甚至在那里。:)

## 分享这篇文章