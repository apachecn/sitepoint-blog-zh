# Java 9 入门的顶级资源

> 原文：<https://www.sitepoint.com/java-9-resources/>

### 目录 

*   [会谈](#talks)
*   [文章](#articles)
*   [博客](#blogs)
*   [书籍和课程](#booksandcourses)
*   [评论](#comments)

这是我们每隔一周的周五发出的 SitePoint Java Channel 时事通讯的社论。[在此订阅！](https://www.sitepoint.com/newsletter/)

您可以看出 Java 9 即将到来，因为最近几个月关于它的帖子和讨论数量激增。我想推荐一些现有的讲座和文章，您可以从中了解 Java 9 以及更多的资源，在这些地方会出现新的、高质量的内容。

## 会谈

如果你是那种喜欢看谈话的人，我可以推荐几个。要获得模块系统的高级概述和概念介绍，请观看 [*Java 9:为模块让路！*](https://www.youtube.com/watch?v=Bj_nUbIhJg8) (马克·莱因霍尔德；40 分钟)。为了更深入地了解模块系统，JDK 团队进行了一系列讲座:

*   [*模块化开发简介*](https://www.youtube.com/watch?v=eALw4P_0O4k) (艾伦·贝特曼；55 分钟)
*   [*高级模块化开发*](https://www.youtube.com/watch?v=WJHjKMIrbD0) (马克·莱因霍尔德，艾伦·贝特曼；60 分钟)
*   [*项目拼图:引擎盖下*](https://www.youtube.com/watch?v=fxB9cVNcyZo)(Mark Reinhold；60 分钟)

作为跟进，在 JFokus 有一个[*Ask Architect*会议，Mark Reinhold 回答了各种各样的问题，其中一些是关于 Java 9 的(传递依赖、版本冲突、JavaFX 的状态、提前编译；23 分钟)。](https://www.youtube.com/watch?v=sO1fumd8e4o)

随着 Java 9 的到来，人们开始展示 Java 9 必须提供的非模块化特性。Simon Ritter 快速浏览了《JDK 9》( 50 分钟)和中的 55 个新特性，在进入新的语言特性、一些新的 API(集合工厂、反应流(又名流 API)、堆栈遍历、多版本 JARs)和性能(50 分钟)之前，我先简单介绍一下模块系统。如果你想更深入地了解，我强烈推荐 Aleksey shipilv 的[演讲(60 分钟)。](https://www.youtube.com/watch?v=wIyeOaitmWM) [Monica Beckwith 解释了 G1](https://vimeo.com/181948157) 但是要注意，在尝试之前，你最好已经掌握了 GC 专业知识(55 分钟)。

也有一些更实际的伟大演讲。要了解 Maven 如何处理 Java 9，[观看 Robert Scholte](https://www.youtube.com/watch?v=Wef9p4ykNMM) 谈论 Unicode 编码、版本字符串、交叉编译、多版本 jar，当然还有 Jigsaw 对 Maven 工作方式的影响以及它所能提供的内容(50 分钟)。不要错过现场编码女王 [Trisha Gee 正在用 IntelliJ 进行 Java 9 项目](https://www.youtube.com/watch?v=96vce1qd0QY)，她演示了 JVM 和 IDE 的各种特性(30 分钟)。如果你有兴趣看看移植到 Java 9 模块会是什么样子，[观看 Rabea Gransberger](https://www.youtube.com/watch?v=hUZb4iOaizg) 实时重构一个小的演示项目(15 分钟)。当然，如果不提到文卡特·苏布拉马年，就无法谈论实时编码，他在一个长达 150 分钟的大型实时会议中展示了模块和 JShell [。](https://www.youtube.com/watch?v=8XmYT89fBKg)

对于较短的片段，Voxxed 的人录制了几个采访:

*   [Mark Reinhold 与](https://www.youtube.com/watch?v=R83xS0bNHTM)谈论强封装、开放模块、兼容性和`sun.misc.Unsafe` (10 分钟)。
*   [文卡特·苏布拉马年谈论](https://www.youtube.com/watch?v=OjJBau4ZNyA)兼容性、Flow API 以及对未来 Java 版本的期望(13 分钟)。
*   [我谈](https://www.youtube.com/watch?v=bZu6MGefHU0)关于迁移和命令行转义(还没有[新的`--permit-illegal-access`](http://mail.openjdk.java.net/pipermail/jigsaw-dev/2017-March/011763.html) ，虽然)(7 分钟)

![Java 9 Resources](img/48dc2f4910303328421bf3ccfcdbd86e.png)

## 文章

关于 Java 9 的文章数不胜数，所以根本没办法做到人人公平。以下是我尝试列出的比较重要的几个:

*   [终极指南](https://www.sitepoint.com/ultimate-guide-to-java-9/)
*   [模块化编程和项目拼图](https://www.infoq.com/articles/Latest-Project-Jigsaw-Usage-Tutorial)

*   [增加流 API](http://blog.codefx.org/java/dev/java-9-stream/) ，[新的流收集器](http://www.baeldung.com/java9-stream-collectors)
*   [流程 API:未来事物的形状](http://iteratrlearning.com/java/2017/03/12/java9-process-api.html)
*   [Java 时间(JSR-310)增强功能](http://blog.joda.org/2017/02/java-time-jsr-310-enhancements-java-9.html)
*   [并发更新](https://www.voxxed.com/blog/2016/10/java-9-series-concurrency-updates/)
*   [添加到可选的](http://blog.codefx.org/java/dev/java-9-optional/)
*   [深入了解堆栈审核 API](https://www.sitepoint.com/deep-dive-into-java-9s-stack-walking-api/)
*   [集合的便利工厂方法](http://www.baeldung.com/java-9-collections-factory-methods)

*   [应用`@Deprecated`增强功能](http://marxsoftware.blogspot.de/2016/08/applying-jdk-9-deprecated-enhancements.html)
*   [使用`sun.misc.Unsafe`](http://gregluck.com/blog/archives/2017/03/using-sun-misc-unsafe-in-java-9/)
*   [变量句柄](https://www.voxxed.com/blog/2016/11/java-9-series-variable-handles/)
*   [反射 vs 封装——在 Java 模块系统中脱颖而出](https://www.sitepoint.com/reflection-vs-encapsulation-in-the-java-module-system/)

*   [用 Maven 生成多发布 jar](http://word-bits.flurg.com/multrelease-jars/)，[用 Maven 构建多发布 jar](http://in.relation.to/2017/02/13/building-multi-release-jars-with-maven/)
*   [如果使用 Docker](https://www.infoq.com/news/2017/02/java-memory-limit-container) 运行，调整内存限制
*   [JShell–入门和示例](http://jakubdziworski.github.io/java/2016/07/31/jshell-getting-started-examples.html)

要获得更长的帖子列表，请查看 Baeldung 的 Java 9 网站。

我想用一个试探来结束 whit list:Java 9 模块化的一个特别有趣的部分是构建工具是否会生成模块声明。下周一，Apache Maven 项目主席、Maven Java 9 兼容性和特性的主要开发者 Robert Scholte 将告诉我们 Maven 是否能为你做到这一点。剧透:不好看。

## 博客

有一些博客和网站定期发布关于 Java 9 的内容。他们中的大多数人都很体面地给这些帖子贴上标签，所以你不必去搜索。公司博客/网站:

*   [甲骨文](https://blogs.oracle.com/java/)(没有 Java 9 标签)
*   [SitePoint](https://www.sitepoint.com/ultimate-guide-to-java-9/) (惊喜！)
*   [Voxxed](https://www.voxxed.com/blog/tag/java-9/) (包括一个[尼斯系列](https://www.voxxed.com/blog/tag/java-9-series/))

*   拜尔东
*   [迭代器学习](http://iteratrlearning.com/articles)(没有 Java 9 标签；拉乌尔-加布里埃尔·乌尔马和理查德·沃布顿)
*   [CodeFX](http://blog.codefx.org/tag/java-9/) (我的)
*   约达(斯蒂芬·科尔伯恩)

## 书籍和课程

如果你想真正深入，并为实际使用新东西做好准备，你可能会想去找一本书或在线课程。这些是我所知道的:

*   [*Java 9 模块化:先看*](https://www.pluralsight.com/courses/java-9-modularity-first-look) (Sander Mak 带 Pluralsight)
*   [*Java 9 模块化*](http://shop.oreilly.com/product/0636920049494.do) (桑德·麦和保罗·巴克与奥莱利)
*   [*Java 9 模块化 Jigsaw 项目和可扩展的 Java 应用*](http://www.apress.com/de/book/9781484227121)(Alexandru Jecan with a press)
*   [*精通 Java 9*](https://www.packtpub.com/application-development/mastering-java-9) (马丁·托舍夫与帕克特)
*   [*Java 9 中的模块化编程*](https://www.packtpub.com/application-development/modular-programming-java-9)(Koushik Kothagal with Packt)
*   [*Java 9 带 JShell*](https://www.packtpub.com/application-development/java-9-jshell) (Gastón C. Hillar 带 Packt)
*   [*Java 9 模块系统*](https://www.manning.com/books/the-java-9-module-system?a_aid=nipa&a_bid=869915cb) (我与曼宁)

因为这是我的社论，我将自由地确保你不会忽略列表上的最后一本书是我写的，这当然使它成为最好的一本书。😜早期访问是开放的，所以你现在就可以得到它！

这就是本周的全部内容，希望你玩得开心！

再见…尼古拉

PS:别忘了[订阅](https://www.sitepoint.com/newsletter/)！

## 分享这篇文章