# 跟踪您部署的文件

> 原文：<https://www.sitepoint.com/tracking-your-deployed-files/>

*…发帖人* [大卫·梅德洛克](https://www.sitepoint.com/forums/member.php?u=24385) *:*

如果您没有使用某种版本控制，那么您应该使用。你会问“为什么”？“为什么不，”我的回答是。

我们大多数人都无法访问专用的 ColdFusion 服务器。如果你像我一样，你在家里(或者任何你开发的地方)的电脑上有一个开发人员的 CFMX 副本。我还在机器上安装了 CVS 来跟踪我的代码。它不允许我轻松地将它部署到我的服务器上，因为我实际上必须将它 FTP 到我的主机上，但至少我可以在我的机器上跟踪它。

此外，我倾向于记录我部署的文件以及我将它们部署到实时服务器的日期。这样，我可以一目了然地查看我的日志，看看哪里有问题。

关于如何在你的网站上使用 CVS 的简单教程，我推荐 [Harry Fuecks 的 CVS 教程](https://www.sitepoint.com/article/harness-power-cvs-site)。

对于不想处理命令行 CVS 的 Windows 用户，您可以使用 CVS 的许多图形界面之一:

[WinCVS](http://www.wincvs.org/)

[CVS Web 客户端](http://cvswebclient.sourceforge.net/)(开发者不再支持)

[乌龟 cv](http://www.tortoisecvs.org/)

您可以在 [CVS 网站](http://www.cvshome.org/)上找到更多的 GUI 和工具。

跟踪代码的每一个版本可以让你在突然需要快速恢复到旧版本的时候省去很多麻烦。

## 分享这篇文章