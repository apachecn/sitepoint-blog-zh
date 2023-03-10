# ColdFusion 的一周:5 月 21-27 日:迟到总比不到好

> 原文：<https://www.sitepoint.com/the-week-in-coldfusion-21-27-may-better-late-than-never/>

ColdFusion 8 再次获奖—[这次是 SIIA 的 Codie 奖](http://www.siia.net/codies/2008/winners.asp)(向[本·福塔](http://www.forta.com/blog/index.cfm/2008/5/21/ColdFusion-8-Wins-Codie-Award)致敬)。Ben 还指出，[英国的软件编辑杂志发表了一篇关于 ColdFusion 8](http://www.forta.com/blog/index.cfm/2008/5/21/Software-Editorial-Review-Encourages-Taking-ColdFusion-8-For-A-Spin) 的详细评论，他们鼓励企业试用 ColdFusion。

**开源**

艾伦·威廉姆森(Alan Williamson)本周发布了两篇关于官方开放蓝龙插件 API 的帖子——首先是对 API 本身的概述，其次是对如何开发标签的逐步概述。

Brian Rinaldi 的开源更新强调了五个新项目，包括 AnythingToXML，开放的 BlueDragon Web Admin，以及一个名为 templateListener 的有趣项目，以帮助简化可信缓存的使用。布莱恩还问【ColdFusion 的成本是否抑制了它的发展？-他收到了大量的评论，表达了各种观点，来自一些非常受尊敬的社区成员。非常值得一读！

名为 Sava 的新[开源 ColdFusion 内容管理系统已经发布。加里·吉尔伯特讨论了 ColdFusion 中内置的过多开源内容管理系统，并思考了在](http://www.brianmeloche.com/blog/index.cfm/2008/5/22/Found-a-new-open-source-ColdFusion-CMS-this-morning--Sava-CMS)[缺乏对国际化的广泛支持，而不是另一个 CF CMS](http://www.garyrgilbert.com/blog/index.cfm/2008/5/21/NACFCMS-Not-Another-CF-CMS) (引用 [FarCry 作为唯一内置国际化的开源 CMS](http://www.farcrycms.org/))。具有讽刺意味的是，又一个 CF CMS 在评论中公布了。

Andy Jarrett 暗示 cfCommerce 项目又开始了。我很想看看这一次的结果。

**代码**

数组和结构迭代哪个更快？本·纳德尔认为结构更快——但是[埃里克·斯蒂文斯证明了事实恰恰相反](http://www.bandeblog.com/2008/05/relative-cfloop-performance-for-various-loop-structures/)。需要牢记的好信息！Ben 还提供了一些使用 XML 文档的有用提示:[从 ColdFusion XML 文档中删除 XML 节点数组](http://www.bennadel.com/index.cfm?dax=blog:1236.view)。

斯科特·本内特问[我糟糕是因为我没有使用框架吗？](http://www.coldfusionguy.com/ColdFusion/blog/index.cfm/2008/5/23/Do-I-Suck-Because-I-Dont-Use-Frameworks)对那篇文章的有趣评论…本质上，在我听来好像斯科特确实使用了一个框架——他自己创造的一个框架。他说“任何理解 MVC 概念的人都很容易理解我的代码”，对我来说，这清楚地表明他有一个系统，一个听起来经过深思熟虑的系统。虽然使用广泛流行的公共框架有很多好处，但任何用于组织代码的系统都会给开发人员带来好处，尽管规模较小。

[Scott Stroz 打破了一些关于 ColdFusion 8 中包含的开源嵌入式 Derby 数据库引擎](http://www.alagad.com/go/blog-entry/derby--sqlite-and-other-derby-myths)的神话。我希望看到 Derby 做更多的工作——它是 Access 的一个很好的替代品，因为它的尺寸很轻，并且是嵌入式的，很容易移植——但它在功能上绝不是轻的。

同样在代码中:

*   Mike Schierberl 发布了 varScoper 的关键更新，该工具用于识别函数中未正确限定 var 范围的变量。如果你写 CFCs，你一定要看看 varScoper。
*   曾经需要在 ColdFusion 中动态编译 Java 类吗？托德·夏普做到了，并发布了一个解决方案。
*   Raymond Camden 分享了他遇到的一个复杂问题的解决方案，该问题涉及 [CFFEED 和日期值](http://www.coldfusionjedi.com/index.cfm/2008/5/24/CFFEED-and-Date-Values)。

**会议**

苏格兰威士忌加冰块将于下周举行，朱迪思·迪诺维茨与组织者凯文·麦凯布谈论为与会者准备的内容。

网络狂人是上周。虽然官方下载列表很大，但演讲者和与会者的博客上也出现了一大堆帖子和演示:

*   艾伦·韦斯特谈论由独一无二的本·福塔所做的主题演讲，马克·克鲁格也在谈论
*   Raymond Camden 分享了他在 CF8 中关于[动态图像处理的演示幻灯片](http://www.coldfusionjedi.com/index.cfm/2008/5/20/WebManiacs-ColdFusion-Images-Presentation-LectureHands-On)
*   Scott Stroz 分享了他在 Derby 和 CFExchange 上的两次演讲的材料
*   道格·休斯发布了他的[高级模型粘合会议](http://www.alagad.com/go/blog-entry/advanced-model-glue-presentation-materials)的幻灯片和样本代码
*   彼得·贝尔发布了他关于[实用代码生成](http://www.pbell.com/index.cfm/2008/5/21/Practical-Code-Generation-Presentation-from-WebManiacs)和 [RAD OO](http://www.pbell.com/index.cfm/2008/5/19/Presented-on-RAD-OO-at-Webmaniacs) 的演示幻灯片
*   [Terrence Ryan 在 AIR 和 SQL Lite 上演讲](http://www.numtopia.com/terry/blog/archives/2008/05/webmaniacs_2008_air_and_sqlite.cfm)
*   有趣的是，[看看 flickr feed 浏览器](http://www.infoaccelerator.net/webmaniacs2008/) ( [向 Andrew Powell](http://www.infoaccelerator.net/blog/post.cfm/webmaniacs-2008-flickr-stream-viewer) 致敬)

就在那一周。抱歉，这篇综述比通常的时间表晚了几天——有时现实生活会碍事。这是我做的第 20 次每周综述——我希望它对外面的人有用。

向 kay 发送建议，地址为 smoljak dot com，或者在 del.icio.us 中添加 for:kay.smoljak 的标签链接。我将在接下来的几天里发布一篇关于即将在悉尼举行的 WebDU 会议的帖子，请密切关注。

## 分享这篇文章