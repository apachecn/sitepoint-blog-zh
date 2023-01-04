# 跨平台开源更成功？

> 原文：<https://www.sitepoint.com/cross-platform-open-source-more-successful/>

在思考了 PHP 是颠覆性技术(T1)和 T2 为什么 MySQL 发展如此之快(T3)之后，我想到还没有说的是这两种技术都是以跨平台为目标的，平台是操作系统(在 PHP 的情况下是 web 服务器)。

更准确地说，它们都在 Windows 下运行良好。这可能是显而易见的，但在 Windows 上开始使用“dynamic duo”几乎比在*Nix 上更容易，至少对于标准安装来说是这样。通过对 Windows 开放，两家公司都接触到了庞大的用户群。这可能是你在 LAMP 上运行你的 live 站点，但是作为一个开发者，你可以在 Windows 上建立一个足够接近你的代码最终运行的环境。

当你考虑 MySQL 相对于 [PostgreSQL](http://www.postgresql.org/) 时，每个看过的人都知道 PostgreSQL 在功能性方面要成熟得多。那么，为什么 PostgreSQL 不是 MySQL 的大热门呢？也许第一个原因是 PostgreSQL 从来没有让 Windows 用户轻松过。

Python 是另一种可以在许多平台上很好运行的语言，它让 Windows 用户的生活变得更加轻松。尽管它(还)没有 Perl 或 PHP 那么大，但在过去的两年里，它的受欢迎程度有了显著的增长(现在它所需要的只是 Sitepoint 上的一些文章……)。相比之下，Perl 在吸收方面似乎已经达到了极限。在[之外，ActivePerl](http://www.activestate.com/Products/ActivePerl/) 认为 Perl 把*Nix 放在第一位是公平的——也许这就是问题所在。

另一个主要的例子是 Firefox T1，你可能会认为它在 Windows 上运行得最好(我在 Linux 上运行 Firefox 的一些扩展时遇到过错误，但在 Windows 上不会发生)。[的标志](https://www.sitepoint.com/blog/)似乎表明它在 Windows 上做得非常好，能够转化 IE 用户。Firefox 是否能对 IE 造成严重冲击还有待观察，但[的统计数据](http://www.w3schools.com/browsers/browsers_stats.asp)似乎表明，自 IE 彻底击败 Netscape 以来，基于 Mozilla 的浏览器首次显示出用户群的稳定增长。

在开源办公套件中， [OpenOffice](http://www.openoffice.org/) 无疑是领先者，获得了最多的播放时间和认真的考虑。它面临着与微软 Office 的激烈竞争；尽管可以免费使用，用户无疑很高兴地被困在一个车辙里。尽管如此，相对于开源软件，谷歌认为它拥有最大的用户群是相当安全的。

一个有争议的比较是 Java 的 Swing GUI 库与 SWT(由 IBM 开发的标准小部件工具包，用于构建 [Eclipse 的](http://www.eclipse.org)用户界面)。Swing 的目标是成为*独立*平台，而 SWT 的目标是成为*交叉*平台。从理论上看，Swing 是一个更好的选择，因为它将您与运行您的应用程序的平台隔离开来，但在实践中，性能开销的成本很高，用户往往会对看起来不“本机”的应用程序持批评态度。同时，SWT 利用其运行平台的本机 GUI 库(即 Windows 上的 MFC ),这意味着它速度很快，无论在哪里运行，看起来都“像在家里一样”。

与使用与 SWT 相同方法的 C++库 [wxWidgets](http://wxwidgets.org/) 的思路相同。有人说使用 wxWidgets 比微软自己的基础类更容易，在 Windows 上的最终结果是一样的。经常看到 Python 用户说[wxPython](http://wxpython.sourceforge.net/)(wxWidgets 上的一个 Python 包装器)是 Python 最好的 GUI 库。

无论如何——当谈到平台时，在开源开发者中看到某种精英主义是很常见的。有些人甚至对 Windows 说“T0”永远不要“T1”。如果你想让你的项目成功，这可能不是最好的方法…

## 分享这篇文章