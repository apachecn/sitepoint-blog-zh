# ColdFusion 的一周:4 月 9 日至 15 日:疯狂代码

> 原文：<https://www.sitepoint.com/the-week-in-coldfusion-9-15-april-code-crazy/>

出于某种原因，博客圈在本周绝对爆炸了。即使你订阅了 MXNA，Fullasagoog 或者其他 CF 博客聚合器，看看这个列表，看看你是否错过了什么…

对 ColdFusion 8.0.1 版本的大肆宣传已经平息了一些，本周有几个项目讨论了新内容。Raymond Camden 讨论了 CF 8.0.1 中对 CFEXECUTE 标记的[更改。随着 CF 许可的变化，John Beynon 在 Flex 中整合了一个小的](http://www.coldfusionjedi.com/index.cfm/2008/4/9/ColdFusion-801-change-to-CFEXECUTE) [ColdFusion 许可证计算器](http://john.beynon.org.uk/index.cfm/2008/4/14/CF-License-Calculator)，根据 CF 版本、CPU 数量和虚拟机数量计算出需要多少许可证(及其成本)。

不甘示弱，替代 CFML 引擎 Railo 也发布了一个更新，到 2.0.1 版本。新版本包含大量的错误修复和新功能。

在开放的蓝龙阵营中，[新的指导委员会成员已经宣布](http://alan.blog-city.com/bluedragon_steering_committee.htm)。艾伦·威廉姆森发布了对委员会成员[安迪·艾伦](http://alan.blog-city.com/interview_andyallan.htm)、[迈克·布鲁特](http://alan.blog-city.com/interview_mikebrunt.htm)和[肖恩·科菲尔德](http://alan.blog-city.com/interview_seancorfield.htm)的采访，更多肯定会跟随。

说到开源，Greg Cerveny 发布了对 Raymond Camden 关于开发开源 ColdFusion 应用程序的采访。这很有趣，因为 Greg 似乎对什么是开源，什么是开源有一些强烈的看法。从 [Brian Rinaldi 的开源综述](http://www.remotesynthesis.com/blog/index.cfm/2008/4/14/ColdFusion-OpenSource-Update--April14-2008)，许多新东西已经发布，包括 OpenID 集成组件、门户框架、日志应用程序、Robin Hilliard 的单元测试项目、文件管理器等等。咻！

简短而甜蜜的代码片断:

*   雷蒙德·卡姆登已经开始开发他的新版[YouTube ecfc 包装器](http://www.coldfusionjedi.com/index.cfm/2008/4/15/First-draft-of-new-YouTube-API)，它现在使用更新的 YouTube API。
*   Brian Rinaldi 发布了一个新版本的恶意命名的 Illudium PU-36 代码生成器。
*   Brian Love 分享了一个用户自定义功能[轻松管理图片上传](http://blog.brianflove.com/articles/2008/04/11/safely-uploading-photos-in-cf)。

如果你想要更重的东西，这里有一些扩展灰质的话题:

*   Raymond Camden 从现实世界和理论角度讨论了在 CFCs 中处理会话和 cgi 变量的最佳实践。
*   Mike Brunt 在关于这个主题的后续文章系列中讨论了垂直和水平集群。
*   Peter Bell 讨论了如何将一个对象作为一个结构来对待——包括你将如何做以及你为什么想要这样做。
*   Nathan Strutz 告诉我们, [ColdFusion 是一种领域特定语言](http://www.dopefly.com/techblog/entry.cfm?entry=246),旨在很好地解决一个问题:“ColdFusion 是 web 应用程序的梦想语言。它最初的目标是使您的 HTML 网站支持数据库和动态。这个目标在 1.0 版和 cfquery 标签中得到了基本完善。”

ColdFusion 之所以伟大，其中一个原因是社区。Jim Priest 发布了一个由社区驱动的 CFML IDE 调查 …他将结果公之于众。已经有超过 300 个回复，所以请填写并发表您的意见。ColdFusion 产品经理克里斯汀·施菲尔德在 Adobe 网站上发布了一些 [ColdFusion 8 客户案例研究](http://www.webbschofield.com/index.cfm/2008/1/29/ColdFusion-8-Customer-Success-Stories-one-could-be-yours)。在 Fusion Authority，我从本周 CF-Talk 邮件列表的讨论中挑选了[五个有趣的话题:数据库模式比较工具、按字母顺序排列数字查询列、高级分页、开源购物车系统和 Application.cfm 与 Application.cfc](http://www.fusionauthority.com/news/4746-coldfusion-talk-roundup-2.htm)

ColdFusion 会议似乎在一年中分布得很好。在 CFUnited conference 博客上，他们发布了参加即将到来的 CFUnited 的[十大理由。然而，这些相同的原因可以适用于几乎任何会议——走出去并在学习新技能的同时结识志同道合的 CF 开发人员真是太棒了！我很可能会错过今年的 WebDU(Geoff 已经发布了一些](http://cfunited.com/blog/index.cfm/2008/4/14/Top-10-reasons-to-attend--CFUnited) [OSX 仪表板小部件，以倒计时几天](http://blog.daemon.com.au/index.cfm?objectid=501D7DFA-13D4-B1F1-488CFE96744C1DBE)，直到南半球的 CF 会议)，但我对明年抱有很高的期望。Nick Tong 宣布将于 9 月在英国召开[新的 ColdFusion 会议，会议名称仍为 CFDevCon。更多详情即将发布！](http://www.succor.co.uk/index.cfm/2008/4/14/CFDevCon-2008--Old-conference-name-but-new-vibe)

最后，一些与框架相关的公告:

*   几周前，Geoff Bowers 在 FarCry 5.0 上举办了一次 Adobe 电子研讨会，并宣布长达一小时的会议录音现已发布。如果您错过了现场会议，[之前在线研讨会的录音](http://www.adobe.com/cfusion/event/index.cfm?event=list&loc=en_us&type=ondemand_seminar&product=ColdFusion&interest=)可从 Adobe 网站“点播”获得。杰夫·库格林为 FarCry CMS/framework 发布了 [jcFarcryFlvPlayer 插件，该插件在 FarCry 网站中嵌入了一个基于 Flash 的视频播放器。](http://www.jeffcoughlin.com/blog/index.cfm/2008/4/10/New-FarCry-Plugin-jcFarcryFlvPlayer)
*   Nathan Strutz 为 Fusebox 5.5 更新了他的 [Fusebox XML 备忘单](http://www.dopefly.com/techblog/entry.cfm?entry=247)。
*   Matt Quackenbush 讲述了如何在 ColdBox 中使用映射视图——查看多个应用程序共用的文件。
*   Sean Corfield 解释了如何在集群中使用 Transfer ORM。说到 Transfer，Dan Wilson 透露说 Mark Mandel 正在为 CF.Objective conference 上[发布的 Transfer 1.0 完成文档。](http://www.nodans.com/index.cfm/2008/4/14/TransferORM-gets-closer-to-10-release)

这星期到此为止！像往常一样，留下评论，给我发邮件(kay 在 smoljak DOT com)告诉我任何建议，或者标记你的链接:kay.smoljak 在 del.icio.us

## 分享这篇文章