# 哪个 Java 日志框架的性能最好？

> 原文：<https://www.sitepoint.com/which-java-logging-framework-has-the-best-performance/>

### 目录 

*   [测井垫片](#loggingshims)
*   [日志框架](#loggingframeworks)
*   [日志框架性能](#loggingframeworkperformance)
*   [基准的结构](#structureofthebenchmark)
*   [稻草人的观点](#thepointofthestrawman)
*   [基准测试结果](#benchmarkresults)
*   [无处记录](#loggingtonowhere)
*   [Java Util 日志输出格式](#javautilloggingoutputformat)
*   [Log4j 2.x 默认输出格式](#log4j2xdefaultoutputformat)
*   [回退默认输出格式](#logbackdefaultoutputformat)
*   [给日志框架开发者的消息](#amessagetologgingframeworkdevelopers)
*   [结论](#conclusions)
*   [评论](#comments)

Java 有幸拥有三大 Java 日志框架:Java Util 日志、Log4j 2 和 Logback。当你为你的项目挑选一个的时候，你有没有想过他们的表现？毕竟，仅仅因为选择了一个缓慢的日志框架或一个次优的配置就降低应用程序的速度是愚蠢的。

每当 Twitter 上出现用 Java 登录的话题，你可以肯定你会受到很多指责。丹·艾伦(Dan Allen)的这条推文或许最好地概括了这一点:

> [@ connolly _ s](https://twitter.com/connolly_s)[@ rmannibucau](https://twitter.com/rmannibucau)Java 的日志情况还是这么乱。在这个可笑的问题上损失了数百万美元。
> 
> —丹·艾伦·✊(@ mojavelinux)[2017 年 1 月 28 日](https://twitter.com/mojavelinux/status/825144263293943808)