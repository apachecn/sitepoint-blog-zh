# 2006 年 Sun 开发者日:第二天

> 原文：<https://www.sitepoint.com/sun-developer-days-2006-day-two/>

上周在墨尔本举行的 Sun 2006 年开发者大会第二天的情况与第一天的情况类似。如果你错过了，一定要看看[我对第一天](https://www.sitepoint.com/sun-developer-days-2006-day-one/)的报道。

在当天的会议正式开始之前，Sun 组织者 [David Coldrick](http://blogs.sun.com/coldrick) 起身为 Java 开发人员推荐了两个有用的资源，以便他们在开发者日会议期间跟上最新的发展:[Java Posse 播客](http://javaposse.com/)(我也强烈推荐这个！)，还有 javapassion.com 的，我之前在这个博客里已经[报道过了。](https://www.sitepoint.com/learn-j2ee-programming-with-passion/)

## <cite>为移动世界打造精彩游戏</cite>，蔡牧恩·李(孙)

在[做了一些手机游戏](http://j2mebomber.sourceforge.net/)的工作之后，我希望能对这个平台上的游戏开发者面临的技术问题有所了解:特定设备的 API 碎片、应用程序大小和处理速度限制、不一致的控制方法等等。相反，我们得到的是一个非技术性的概述，关于开发手机游戏相对于基于 PC 和控制台的游戏平台的利弊。

简而言之， [Java Micro Edition (ME)](http://java.sun.com/javame/) 是一个轻量级平台，它可以让你用少至一个人工月的开发时间为数百万潜在用户制作一个完全实现的游戏。这个平台上的成功游戏应该考虑到这样一个事实，即玩家将会在短暂的时间内不停地玩游戏，并且可能会在任何时候停止游戏。鉴于此，将其他平台的旧游戏改编到手机上的流行做法并不是一个好方法。

## <cite>优化 Java SE 的性能& Java EE 应用</cite>，Simon Ritter (Sun)

再说一次，我对这次会议的期望有点偏离主题(我真希望会议日程包括会议摘要)。我们得到的不是编写更快代码的有用技术的集锦，也不是某些常见性能反模式的概述，而是关于使用各种未记录(或记录不良)的[命令行开关](http://java.sun.com/performance/reference/whitepapers/tuning.html)在 Sun Java 虚拟机(VM)中调优垃圾收集算法的教程。

我很喜欢对 VM 的堆管理技术的解释，以及它们如何考虑到 Java 应用程序广泛使用短命对象的趋势，这些对象必须快速有效地进行垃圾收集，以便 Java 能够工作，我还喜欢看为进一步提高 Java 5 运行时版本 1.5.0_06 的性能而引入的[偏向锁定](http://java.sun.com/performance/reference/whitepapers/tuning.html#section4.2.5)算法。但是所讨论的绝大多数性能调整实际上只适用于高负载、多处理器的服务器，所以对我来说只不过是技术上的好奇。

## <cite>SOA，JBI，BPEL:战略，设计&最佳实践</cite>，Rima Patel (Sun)

在他的第一天会议中，Ashwin Rao 展示了 Sun 为开发人员设计的一些工具，以解决基于离散的、专门的服务构建应用程序的独特挑战，这种实践被称为面向服务的架构(SOA)。Patel 女士在本次会议中回顾了 SOA 的动机和技术挑战，这些都是在开发工具出现之前必须考虑的问题。

虽然开发人员习惯于面对新的专有名词和流行词汇，一旦掌握，就会带来包括性能、可用性和开发简便性在内的技术优势，但 SOA 非常不同，因为驱动它的动机主要是以业务为中心的。

如今，许多大企业都承受着重叠遗留系统的重压，这些遗留系统是在相互冲突、通常是特定于供应商的平台上临时开发和连接的。解决这个问题的 SOA 方法是识别这些系统中存在的关键*服务*,或者重新实现它们，或者将现有系统包装在接口中，以便应用程序可以通过 XML 文档的交换公开、异步、有状态和安全地发现和访问它们，这些 XML 文档反映了将在相关业务流程的基于纸张的等价物中易手的业务文档。

Patel 女士继续简要概述了 [Java 业务集成(JBI)](http://java.sun.com/integration/) 规范，该规范以开放框架的形式为 SOA 应用程序提供了管道，该框架是厂商和平台中立的。如果你正在为一家大银行重新设计 IT 系统，这是一个不错的选择，但我不是。对我来说，这个会议主要是为了确认我对 SOA 的理解与 Sun 的大致相符。我不能保证它是否和你的一致。

## 爪哇 SE:老虎，野马和海豚，西蒙·里特(孙)

这是一个相对简单的会议，通过今年晚些时候的 [Java 6(代号 Mustang)](https://mustang.dev.java.net/) 中最引人注目的特性的概述。有很多关于此事的[好报道](http://blogs.sun.com/roller/page/dannycoward?entry=the_10_things_you_need)，特别是因为 Sun 对 Mustang 的开发非常开放，每周都有版本可供下载和测试。

Java 6 中最有可能影响 web 开发者的变化有:[完全支持 XML web services](http://blogs.sun.com/roller/page/dannycoward/20060227) 内置，包含 Rhino 脚本引擎，将允许 JavaScript 代码在 Java VM 上运行，并充分利用 Java 类库；JDBC 4.0，它将[简化和增强](http://weblogs.java.net/blog/lancea/)连接数据库的 Java 应用程序的主要 API 并且[更好的调试支持](http://blogs.sun.com/roller/page/dannycoward/20060310)，使得检测和诊断死锁、内存泄漏和其他运行时问题变得更加容易。

就我个人而言，我最感兴趣的是对基于桌面的 Java 应用程序的增强，但 Ritter 先生跳过了其中的大部分，而倾向于对平台进行更多的核心改变。

Ritter 先生确实花了几分钟时间给出了一些为 Java 7(代号 Dolphin)计划的事情的例子，包括支持 XML 代码原生出现在 Java 代码中，以及支持在 Java VM 上运行的动态语言(如脚本语言)的字节码更改，但几乎没有为 Dolphin 确定的事情，每一条信息都附有免责声明:“另一方面，我们可能根本不会这样做。”

## <cite>用 servlets 让 AJAX 更有效，JavaServer Pages 技术，【JavaServer Faces 技术</cite>，Doris Chen(孙)

这个混乱的标题应该把我吓跑了，但是我参加了这个会议，希望至少能看到一些在服务器端 Java 代码中支持 AJAX 应用程序的优雅例子。在看了大约 45 分钟的演讲者试图简单定义 AJAX *是什么*的混乱和困惑后，我放弃并离开了。我不是唯一一个离开的人。

我不想进一步指责演示者，但是当您不能让一屋子的开发人员对 AJAX 的演示感兴趣时，这确实是令人难过的一天。

## 分享这篇文章