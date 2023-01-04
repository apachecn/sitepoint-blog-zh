# 用 Java 玩 MP3

> 原文：<https://www.sitepoint.com/playing-mp3s-with-java/>

在业余时间，我正在为一个开源的多用户 MP3 点播服务器 [JJukebox](https://web.archive.org/web/20060118115538/https://www.jjukebox.com:80/) 构建一个非常棒的网络界面。在 SitePoint 总部，我们非常认真地对待我们的音乐，现在是我们生锈的、陈旧的、[基于 Perl 的 MP3 自动点唱机软件](https://cardhouse.com/otto/)升级的时候了。

如果说 JJukebox 服务器实现中有一个弱点，那就是音频回放——特别是对 MP3 文件的处理。尽管 Java 具有尖端的生命力，但它对多媒体的支持确实处于一种可悲的状态。

按照常规，[Java Media Framework API](https://java.sun.com/products/java-media/jmf/)(JMF)是捕捉和回放各种流媒体的一站式商店。尽管在 JMF 2.1.1a 之后，由于同样的[专利问题](https://www.hydrogenaudio.org/forums/index.php?showtopic=24082)影响了其他开源 MP3 软件，MP3 支持被删除，但 JMF 2.1.1c 的 Windows 版本通过使用 Windows 中的 MP3 支持恢复了 MP3 播放，并且自 11 月起，JMF 2.1.1e 现在有了一个用于所有支持平台(Windows、Linux、Solaris 和跨平台版本)的 [MP3 插件](https://java.sun.com/products/java-media/jmf/mp3/download.html)。

不幸的是，据报道 JMF 与 Java 5 不兼容(或者至少有问题), API 唯一的生命迹象是 Java Client Group 的 Swing 团队经理在 4 月 12 日留下的[评论](https://weblogs.java.net/blog/mason/archive/2005/04/jmf_wherefor_ar.html#7321),大意是他们已经接管了 JMF 的责任，并将很快向[宣布一些事情](https://www.jargon.net/jargonfile/r/RealSoonNow.html)。就我个人而言，我甚至无法让 JMF 2.1.1e *安装程序*在我的 Windows 系统上运行。

在《JMF，你在哪里？》中，可以找到一些关于如何对待 JMF 的至理名言。在梅森·格拉维斯的博客上，[没有平庸的 Java 媒体](https://weblogs.java.net/blog/invalidname/archive/2005/04/java_media_with.html)在克里斯·阿达姆松的博客上。执行摘要:停止“伯尼的周末”式的骗局，让 JMF 去死，为更合格的选择腾出空间。

不管怎样，基于上述原因，JJukebox 在相当长的一段时间前放弃了 JMF 的 MP3 播放(CVS 在移除 JMFPlayer 类后的评论:“JMF 死了。JMF 万岁。”)，并采用了 [JavaZoom](https://www.javazoom.net/) 的 [JavaLayer](https://www.javazoom.net/javalayer/javalayer.html) 代替。如果你需要用 Java 播放 MP3，那么 JavaLayer 是目前最好的选择。对于大多数实际应用来说，你实际上想要使用 JavaZoom 的 [BasicPlayer API](https://www.javazoom.net/jlgui/api.html) ，它与 Java 的内置 [Java Sound API](https://java.sun.com/products/java-media/sound/) 对话，后者可以使用 JavaZoom 的[MP 3 SPI](https://www.javazoom.net/mp3spi/mp3spi.html)(JavaLayer 的一个包装器)来播放 MP3，使用 JavaZoom 的[VorbisSPI](https://www.javazoom.net/vorbisspi/vorbisspi.html)(JOrbis 的一个包装器)来播放 Ogg Vorbis(另一种 MP3)。

![](img/7572a9c523a9bec25387989103350322.png)

困惑了吗？我也是，但是安装了所有必需的库的最新版本，它实际上工作得很好。这并不是说没有改进的余地。[据 JJukebox 作者之一](https://www.codetoad.com/java_mp3_player.asp#2583):

> 我是 JJukeBox 项目([www.jjukebox.com](https://web.archive.org/web/20060118115538/https://www.jjukebox.com:80/))的一部分。我们渴望找到一款像样的 ogg/mp3 播放器，不是基于 JMF 或 JavaLayer (JlGui)的。这两个都完全是垃圾，我们提交给 JavaZoom (JavaLayer/JlGui)的任何补丁都被忽略，我们不得不大量重写 JlGui 的部分内容，使其可以远程使用。我对项目中如此糟糕的部分感到非常惊讶，例如，在(不存在的)事件系统方面。

> 如果你知道一个项目，将做 ogg/MP3 可靠(打破和 knackered MP3/oggs，与适当的事件系统和多线程的能力)，那么请让我们知道！

## 分享这篇文章