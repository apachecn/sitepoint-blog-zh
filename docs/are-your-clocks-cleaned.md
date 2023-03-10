# 你的钟干净了吗？

> 原文：<https://www.sitepoint.com/are-your-clocks-cleaned/>

在美国，我们大多数人每年都会例行地调整两次时钟，希望能获得更多的日光。这种做法被称为[夏令时](http://en.wikipedia.org/wiki/Daylight_savings_time)，据我所知，世界其他地方也在实行这种做法。美国政府最近改变了夏令时的起止日期。这在一些地方引起了类似 y2k 的恐慌，人们担心他们的软件会突然开始报告错误的午休时间。与 y2k 非常相似，软件制造商开始为他们的软件和操作系统发布补丁，以应对这些变化。

对于那些运行自己的 ColdFusion 服务器的人，你需要在这里记下，因为为了为下周末做好准备，你需要做一些事情，因为三月的第二个周末是更改生效的时间。

要“修复”ColdFusion，您需要实际更新 ColdFusion 用来处理代码的 Java 虚拟机(JVM)。根据 Adobe 网站的消息，SUN JVM 版本 1.4.2_11 修复了夏令时问题，并通过了 CFMX 7.02、CFMX 6.1 Updater 1 和 JRun 4 Updater 6 的认证(适用于不独立运行的版本)。

现在获取合适的 JVM 有点棘手。当然，你可以直接去 SUN 的 java 网站下载最新的版本，但是这些新版本还没有被 Adobe 认证，不能与当前版本的 CF 一起工作，这可能毫无意义，或者它肯定会给我带来一些不可靠的代理网站！

下面是我为了获得 SUN JVM 1.4.2_11 而采取的步骤

1.  参观[http://java.sun.com/products/archive/index.html](http://java.sun.com/products/archive/index.html "http://java.sun.com/products/archive/index.html")
2.  向下滚动页面，直到看到以下粗体字**J2SDK/J2RE–1.4**
3.  在它的右边，你会看到一个下拉选择器，旁边有 Go 这个词。单击此下拉选择器并选择“1.4.2_11”版本，然后单击“Go”按钮
4.  在下一个屏幕上，您需要选择 j2SDK 1.4.2_11 或 J2RE 1.4.2_11。我选择 j2SDK 只是为了让我知道我拥有一切:)。
5.  下一个屏幕应该是下载屏幕。不过在下载之前，您需要选择所有版本表格上方“ **Accept** License Agreement”文本旁边的单选按钮
6.  接受许可协议后，页面将会刷新，您可以选择操作系统的版本。如果您有多台运行不同操作系统版本的服务器，您可能需要在所需版本旁边的每个框中打勾，然后单击“使用 Sun Download Manager 下载选定内容”链接。对于运行 Windows 32 位的用户，我建议选择“离线安装”。它有点强大，但是你不必担心安装程序试图下载它需要的部分等等。

下载完成后，您需要将它安装在运行 ColdFusion 的同一台计算机上。安装很简单，只需双击安装文件，并按照提示。当被问到你想把文件放在哪里时，我建议把它们放在驱动器的根目录下，例如 C:或 d:以保持路径名尽可能的短。当它询问应该注册哪些浏览器时，如果你还没有安装 JRE，你可以选择已经注册的浏览器。大多数机器都已经安装了一个版本，因此在这种情况下，您需要取消选中您使用的浏览器，以避免任何可能的冲突。

一旦安装了 J2SDK / JRE，您将需要升级 ColdFusion 附带的 JVM 以使用 1.4.2_11 版本。您可以按照 [Adobe Technote 2d547983](http://www.adobe.com/cfusion/knowledgebase/index.cfm?id=2d547983) 中概述的步骤来完成。

如果您正在独立运行，这是一个超级简单的步骤，只需要您登录 ColdFusion administrator 并进行简单的更改。

请确保重新启动 ColdFusion，以便所有更改生效。

如果你想了解更多关于这个问题的信息，请查看来自 weblogs.Macromedia.com[MXNA 聚合器](http://weblogs.macromedia.com/mxna/index.cfm)的[这个搜索](http://weblogs.macromedia.com/mxna/index.cfm?categoryId=1&languageId=1&startDate=-1&searchSortBy=date&searchTerms=daylight+AND+savings&query=byAdvancedSearch)。

## 分享这篇文章