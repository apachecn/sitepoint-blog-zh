# 微软 ASP.NET AJAX 扩展 Beta 1 发布

> 原文：<https://www.sitepoint.com/atlas-err-uhm-microsoft-aspnet-ajax-extensions-beta-1-released/>

首先，如果你错过了，Atlas 现在是微软 ASP.NET AJAX 扩展。有时候，人们真的不得不怀疑微软产品命名团队的优先级。姜戈是个很酷的名字。Script.aculo.us 很酷。阿特拉斯很酷。ASP.NET AJAX 扩展不是。至少他们没有在那里开“窗户”。但是我跑题了。

不管你想叫它什么，ASP.NET AJAX Beta 1 已经发布了。与此相关的是 ASP.NET·阿贾克斯“增值”CTP Beta 1 的发布[。关于哪个包里有什么的精彩解释，请看](http://www.microsoft.com/downloads/details.aspx?FamilyId=8A3FD0DD-D75E-4249-86DA-3D4AAC649652&displaylang=en)[斯科特·格思里对新土地布局的精彩解释](http://weblogs.asp.net/scottgu/archive/2006/10/20/ASP.NET-AJAX-Beta-1-Released.aspx)。

对于懒惰和/或匆忙的人，这里有一个快速总结:

*   ASP.NET AJAX Beta 1 基本上包含了 Atlas 的“核心”功能，包括 UpdatePanel。
*   增值包包含了很多 Atlas 核心中的东西，比如 AutoCompleteExtender 或 UpdateProgress 控件。
*   [一定要看看迁移指南](http://ajax.asp.net/files/Migration%20Guide.doc)；这有助于让很多改变变得有意义。但是，如果您大量使用以前版本的 Atlas，您将会对您的应用程序进行一些非常重要的更改。

不幸的是，微软在这个版本中做了一件非常愚蠢的事情:他们将它安装到 GAC 中，并鼓励开发人员从那里引用它。如果您没有意识到这一点，这将是一件非常糟糕的事情，因为如果您不小心的话，它很容易破坏您的应用程序的部署。对于共享主机上的应用程序，它需要主机公司来安装应用程序。我怀疑会有太多的共享主机想要安装任何不在最终发布状态的东西。

幸运的是，这个问题可以解决。如何做取决于你使用什么样的 web 项目。无论是哪种情况，都强烈建议将 AJAX 框架的二进制文件复制到您的项目中，就像任何其他静态包含的库一样。现在，如果您使用 Web 项目，就像在 Visual Studio 2005 默认 Web 项目中一样，您只需要将 dll 复制到应用程序的 bin 文件夹中。如果您正在使用 Web 应用程序项目(无论如何，您确实应该使用它)，这就需要一点技巧。首先，像引用任何其他想要引用的 DLL 一样引用文件 DLL。然后在 Microsoft 上编辑“复制本地”属性。Web.Extensions 为 true。这将迫使您的应用程序处理程序集的本地副本，而不是依赖 GAC 副本。

享受和快乐 web 2.0

## 分享这篇文章