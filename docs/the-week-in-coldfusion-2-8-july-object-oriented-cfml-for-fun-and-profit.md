# ColdFusion 的一周，7 月 2-8 日:面向对象的 CFML，有趣且有益

> 原文：<https://www.sitepoint.com/the-week-in-coldfusion-2-8-july-object-oriented-cfml-for-fun-and-profit/>

在过去的几周里，ole CFBlogosphere 中与代码相关的博客帖子数量激增——这正是我喜欢的方式。这周我发现了很多关于编写面向对象代码的好材料。

这篇大综述将是我这个月发表的最后一篇文章——我要去欧洲波兰、斯洛文尼亚、意大利和德国度假三周。这是我近 7 年来第一次离开——但是当然，我会想念你们所有人……非常感谢迈克·亨克给我寄来一些在飞机上阅读的东西！

**代码-对象 ahoy**

Ben Nadel 继续他学习面向对象编程的冒险，在 ColdFusion 中对 MVC、OOP 和表单提交有了更多的思考，然后是 T2 OOP Prototype——在定义域模型之前理解接口。如果您对面向对象感兴趣，您可能会希望研究 ORM 或对象关系映射器框架，以消除创建对象的繁琐。Bob Silverberg 写了一系列关于如何使用 Transfer ORM 的文章。现在有六个部分，它们读起来非常棒(向[肖恩·科菲尔德](http://corfield.org/blog/index.cfm/do/blog.entry/entry/Bob_Silverberg_on_Transfer_ORM_and_Architecture)致敬):

*   [我如何使用转移-第一部分-简介](http://www.silverwareconsulting.com/index.cfm/2008/6/23/How-I-Use-Transfer--Part-I--Introduction)
*   [我如何使用转移–第二部分–模型架构](http://www.silverwareconsulting.com/index.cfm/2008/6/24/How-I-Use-Transfer--Part-II--Model-Architecture)
*   [我如何使用转移-第三部分-抽象对象](http://www.silverwareconsulting.com/index.cfm/2008/6/30/How-I-Do-Transfer--Part-III--Abstract-Objects)
*   [我如何使用转移-第四部分-我的抽象服务对象](http://www.silverwareconsulting.com/index.cfm/2008/7/3/How-I-Use-Transfer--Part-IV--My-Abstract-Service-Object)
*   [我如何使用 Transfer-Part V-一个具体的服务对象](http://www.silverwareconsulting.com/index.cfm/2008/7/8/How-I-Use-Transfer--Part-V--A-Concrete-Service-Object)
*   [我如何使用转移-第 5.1 部分-注释和回复](http://www.silverwareconsulting.com/index.cfm/2008/7/8/How-I-Use-Transfer--Part-V1--A-Comment-and-Response)

如果您已经在编写面向对象代码，或者正在使用大量使用 CFC 的框架，您可能已经受到 cold fusion 8/Java 6“class loader”错误的影响，这可能会影响启动时间。Brian Ghidinelli 在一个模型粘合应用程序上做了一些实验，并指出与“热”应用程序重新初始化相比，在“冷”重启(完全重启 CF)时，该错误不那么普遍。对任何有这个 bug 问题的人有用的信息！

但是等等，还有更多……更多:

*   cfSearching 博客包括一个方便的代码片段，用于[生成 JVM](http://cfsearching.blogspot.com/2008/07/what-fonts-are-available-in-myjvm.html) 中所有可用字体的样本图像，这意味着这些字体可用于 CFDOCUMENT、CFREPORT 和图像生成功能。很高兴知道！
*   对开源 Java 搜索引擎 Lucene 感兴趣？萨米·霍达已经写了两篇关于使用 Lucene 的导引头包装器的帖子——[cold fusion&Lucene:安装导引头](http://www.bytestopshere.com/post.cfm/coldfusion-lucene-installing-seeker)，上周它在雷达下溜走了，现在 [ColdFusion & Lucene:运行演示](http://www.bytestopshere.com/post.cfm/coldfusion-lucene-running-the-demos)。Lucene 是 Apache 的一个项目，它是 Verity 的一个很好的替代品，可以支持 CFML 站点搜索
*   Doug Boude(和 loud 押韵好吗？)探索了 Fusebox 5.5 中的基本安全性——没有 XML 控制器的版本。正如博客帖子经常出现的情况一样，帖子上有一些有趣的评论。
*   雷蒙德·卡姆登本周的“问一个绝地武士”系列包括关于对多维数组排序的问题的答案[、](http://www.coldfusionjedi.com/index.cfm/2008/7/5/Ask-a-Jedi-Sorting-a-2D-Array)[日期字段和掩码](http://www.coldfusionjedi.com/index.cfm/2008/7/2/Ask-a-Jedi-Issue-with-datefield-and-mask)以及 [ColdFusion。Ajax.SubmitForm](http://www.coldfusionjedi.com/index.cfm/2008/7/3/Ask-a-Jedi-Trouble-with-ColdFusionAjaxSubmitForm)
*   Nic Tunney 和 Mike Henke 都在上周发布了“未充分利用”的功能列表——本周 Simon Horwith 增加了一个额外的[两个未充分利用的 CFML 函数:setVariable 和 structAppend](http://www.horwith.com/index.cfm/2008/7/2/frequently-under-implemented-cf-functions)
*   Steve Bryant 已经发布了关于用 CFUnit 编写单元测试的介绍
*   Kai Koenig 经常撰写和介绍国际化问题——本周[他解释了一些德语特有字符背后的神秘](http://www.bloginblack.de/archives/000984.cfm)
*   迈克尔·沙曼提醒我们[不要忘记 ColdFusion 站点范围的错误处理程序](http://www.chapter31.com/2008/07/08/dont-forget-the-coldfusion-site-wide-error-handler/)
*   Sami Hoda 发布了关于为会话范围爱好者提供的新工具的信息。
*   [肖恩·科尔菲尔德解释结构复制](http://corfield.org/blog/index.cfm/do/blog.entry/entry/Explaining_StructCopy)

**社区**

Adobe ColdFusion 产品经理克里斯汀·施菲尔德关于 Adobe 每周举办的在线研讨会的提示。除了在美国办公室举办的在线研讨会， [Adobe 还在亚太办公室](http://events.adobe.co.uk/cgi-bin/main.cgi?country=pa)举办在线研讨会，任何人都可以参加。

最近几周，Raymond Camden 对 Adobe ColdFusion 认证流程发表了很多看法，而且大多不是正面的看法。本周他发布了他所说的关于这个话题的最后一篇文章:[我关于 Adobe 认证的最后一篇文章](http://www.coldfusionjedi.com/index.cfm/2008/7/2/My-last-post-on-Adobe-Certification)。[安迪·艾伦也发表了他作为 Adobe 认证讲师的观点](http://www.creative-restraint.co.uk/blog/index.cfm/2008/7/5/Adobe-ColdFusion-Certification-and-Training)。

更糟糕的是:生物 Gewlas 纹了一个老式的 ColdFusion 标志纹身——这就是承诺！

**替代 CFML 发动机**

来自开放蓝龙营地的 Matt Woodward 带来了一个关于如何将 BlazeDS 集成到 OpenBD 中以支持 Flex remoting 的截屏。

开源项目的许可是一个巨大的话题，也是最近才与 CFML 社区的大部分人相关的话题。Sean Corfield 指出了两篇有用的开源许可摘要，一篇是由 [Pete Freitag 不久前写的](http://www.petefreitag.com/item/533.cfm)，另一篇是由[Grant Skinner 写的更近(也更深入)的文章](http://www.gskinner.com/blog/archives/2008/07/source_code_lic_1.html)。关于 ColdFusion 8 中包含的 ExtJS 库的新许可有一些讨论。Sami [Hoda 已经发布了这个问题](http://www.bytestopshere.com/post.cfm/brewing-controversy-surround-extjs)——评论中有一些很好的解释。

然而，并不是每个人都跳到开源替代品上:大卫·塔克为奥莱利的 Inside RIA 写了一篇文章，讲述了为什么在评估了所有可用的选项后，他[选择购买 Adobe ColdFusion](http://www.insideria.com/2008/07/why-i-just-purchased-an-adobe.html) 的许可证(向[本·福塔](http://www.forta.com/blog/index.cfm/2008/7/2/David-Tucker-Bought-ColdFusion-And-Tells-Why)致敬)。他说得很有道理！

最后，如果我即将到来的旅行让你羡慕，来一次 ColdFusion 巡航怎么样？ [RIA Adventure](http://www.riadventure.com/) 被宣传为网络活动——与志同道合的 ColdFusion、Flex 和 RIA 开发人员在巴哈马群岛巡游，但没有演讲者或会议。由[乔舒亚·西尔](http://www.usefulconcept.com)于 2009 年 2 月组织的这次巡游看起来非常有趣——而[吉姆·普里斯特甚至写了一首主题曲](http://www.thecrumb.com/2008/07/07/riadventure-geek-cruise/)。

现在就这么多了，伙计们…继续编码，我会在八月和你们大家谈的。

## 分享这篇文章