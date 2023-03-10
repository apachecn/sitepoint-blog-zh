# ColdFusion 的一周:4 月 30 日-5 月 6 日:ColdFusion 是一种编程语言吗？等等等等…

> 原文：<https://www.sitepoint.com/the-week-in-coldfusion-30-april-6-may-is-coldfusion-a-programming-language-blah-blah-blah/>

本周博客界有几条大新闻，但最引人注目的是 cf.Objective()会议。我不打算在这里链接到那些博客帖子——我打算等几天尘埃落定，让每个人都回家写下他们的想法，并在下周初做一个大的总结。就我目前所见，有幸参加的人度过了一段美好的时光，学到了很多东西。

蒂奥贝·马拉其

争议比比皆是:TIOBE 编程社区指数决定将 ColdFusion 从其最受欢迎的编程语言列表中删除，理由是它不是一种编程语言，而是一种类似。NET 和 Ruby on Rails。很明显，博客和邮件列表上引起了极大的愤怒——太多的厌恶倾泻而出，无法全部联系起来！经过重新考虑后，TIOBE 更新了他们的常见问题，指出 CFML——cold fusion 标记语言，与应用服务器产品 Adobe ColdFusion 不同——实际上是一种图灵完全编程语言，将被包括在未来的列表中。

有趣的是，这个事件让社区开始讨论我们使用的术语。CFML 本身就是一种语言，不仅由 Adobe 为其 ColdFusion 服务器产品实现，还由 BlueDragon 和 Railo 中的 New Atlanta 以及 Smith 项目在其各自的产品中实现。也许我需要把我的每周一轮的标题改为“CFML 一周”！

**框架**

Doug Boude 基于他在 Reactor 上的经历，在[上发表了一篇关于他为什么不喜欢 ORM(对象关系映射)框架](http://www.dougboude.com/blog/1/2008/05/Why-I-Hate-ORMs-a-solicited-rant.cfm)的有趣的咆哮。其他一些人已经指出，基于对一种产品的体验而谴责所有的 ORM 是不公平的。我想，就像 web 应用程序开发中的所有事情一样，您的收获可能会有所不同！

本周早些时候，我发布了一项调查——你使用框架吗？我将让它再开放几天，所以如果你还没有投票，请发表你的意见。

**社区**

Macromedia 的古老博客聚合服务 MXNA 最近显示出了它的年龄，它被放了出来，新的 [Adobe Feeds](http://feeds.adobe.com/) 也推出了。事实上，代码基础是相同的，但[根据本·福塔的说法，进行了一些优化](http://www.forta.com/blog/index.cfm/2008/5/5/Goodbye-MXNA-Hello-Feeds)，新服务器现在运行 ColdFusion 8.0.1 以实现额外的速度注入。

Charlie Arehart 继续他的系列文章，用[博客工具](http://carehart.org/blog/client/index.cfm/2008/4/30/tools_resource_list_part3)、 [CFML 认证准备资源](http://carehart.org/blog/client/index.cfm/2008/5/5/tools_resource_list_part4)和[Bug/缺陷跟踪工具](http://carehart.org/blog/client/index.cfm/2008/5/5/tools_resource_list_part5)展示他的大量资源列表。

**开源**

在万众期待之后， [Open BlueDragon 已经在 cf.Objective()会议之前发布了](http://www.openbluedragon.org/)——所以如果你想看看到底是怎么回事，请下载并尝试一下。

[Brian Rinaldi 的开源更新](http://www.remotesynthesis.com/blog/index.cfm/2008/5/5/ColdFusion-OpenSource-Update--May-5-2008)包括两个新版本和四个更新，包括一个过滤 SQL 注入和跨站点脚本攻击的新项目，以及对 Raymond Camden 的许多项目的许多更新。Brian 还链接了许多关于框架的新文章和教程。

如果你无法现场观看，你现在可以在开源网站和 ColdFusion 上观看绝地大师雷蒙德·卡姆登的演讲录音。

**代码**

一些 CFML 快动作:

*   Justin Mclean 展示了如何使用一小段 Java 代码[找出一个日期是否受到夏令时](http://blog.classsoftware.com/index.cfm/2008/5/2/ColdFusion-and-Daylight-Saving)的影响
*   Ben Nadel 讲述了如何使用 CF8 中新增的“删除”属性自动删除 CFMAIL 附件
*   Raymond Camden [演示了如何创建一个简单的定制标签](http://www.coldfusionjedi.com/index.cfm/2008/4/30/Ask-a-Jedi-Does-ColdFusion-have-a-htmlfoot-tag)来复制 CFHTMLHEAD 标签的功能，但是对于页脚，使用 onRequest 函数

我想这足够用一周了。像往常一样，如果你有任何建议，给我发电子邮件(kay 在 smoljak dot com)，留下评论或在 delicious 中标记一些东西:kay.smoljak(没有人会这样做！).直到下一次！

## 分享这篇文章