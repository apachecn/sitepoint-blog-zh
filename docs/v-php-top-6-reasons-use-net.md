# 辩论-。NET V. PHP:使用的六大理由。网络文章

> 原文：<https://www.sitepoint.com/v-php-top-6-reasons-use-net/>

***欢迎来到另一场 SitePoint 辩论。今天，开源和微软这两大技术巨头之间的长期争斗剑拔弩张。***

*具体来说，我们问**“Does。NET 是否意味着 PHP 的终结？”**自己判断……*

首先，我想澄清一下，我是一个 PHP 的家伙。在过去的两年里，我一直用 PHP 编写代码，我目前所有的网站都是用 PHP 和 MySQL 构建的。在我做 Web 开发人员期间，我从来没有喜欢过 ASP，现在仍然不喜欢它。然而，ASP.NET 是一个完全不同的问题，我现在正在以极大的热情学习。这篇文章是针对 PHP 开发人员的，旨在展示 ASP.NET 提供的一些很酷的东西。毕竟，没有理由你不能同时了解 ASP.NET 和 PHP。

##### 是什么？NET 框架？

的。NET 框架由两个主要部分组成:

1.  **CLR** (运行代码的东西)，以及
2.  一组分层的**类库**(想想 PHP 函数 PEAR 库，对它们进行一点扩展，并把它们组织在一个非常好的分层结构中)。这些类库中包括 ASP.NET、ADO.NET(一个数据访问系统)和 windows 窗体(用于构建 Windows 应用程序的类)。

CLR 可以运行用任何适合的语言编写的代码。NET，并且可以在任何有 CLR 版本的操作系统上运行它。换句话说，有点像不一定要用 Java 写的 Java。

***一个例子:Web 表单框架***

ASP.NET 有一种叫做网络表单的模板系统。我首先提到这一点，是因为正是这一体系首先让我对 ASP.NET 产生了兴趣，在我看来，这也是 ASP.NET 最大的特色。据我所知，PHP 中还没有类似的东西。事情是这样的:

`<select id="ColorSelect" runat="server">
<option>SkyBlue</option>
<option>LightGreen</option>
<option>Gainsboro</option>
<option>LemonChiffon</option>
</select>
<span id="Span1" runat="server">Some text.</span>`

注意，上面只是普通的 HTML，在`<span>` 和`<select>` 标签中添加了 runat="server "属性。现在，要向这个选择框添加一个选项，您需要在您的 ASP.NET 代码中包含以下内容(顺便说一下，它可以完全与 HTML 分开):

`ColorSelect.Items.Add('AzureBlue');`

要操作标签，您应该这样做:

`Span1.Style["background-color"] = "red";
Span1.InnerHTML = "Changed text!";`

…系统向浏览器输出 100%有效的 XHTML:

`<select id="ColorSelect">
<option>SkyBlue</option>
<option>LightGreen</option>
<option>Gainsboro</option>
<option>LemonChiffon</option>
<option>AzureBlue</option>
</select>
<span style="background-color: red;">Changed text!</span>`

除了这个*非常酷*的事实之外，它也使得与无知的 HTML/设计人员的合作更加容易。这只是一个非常简单的关于 Web 表单框架能力的例子，如果你感兴趣，你可以在 GotDotNet 的 ASP.NET 快速入门教程中查看更多的例子。

##### 6 大使用理由。网

***1。速度***

就像所有人一样。NET 应用程序，ASP.NET 应用程序编译。这使得它们比 PHP 快得多，PHP 的应用程序是被解释的。要想用 PHP 达到同样的效果，必须在服务器上安装 Zend 和 PHP 加速器，而这在大多数虚拟主机公司很少出现。同样，在 ASP.NET 中，OO 比在 PHP 中要快得多。

***2。更多语言支持***

ASP.NET 是用你选择的“真正的”面向对象编程语言编写的。PHP 只是一种简单的脚本语言。NET 语言，如 C++、VB.NET 或 C#，这些语言给你更多的控制和更多的可重用性。也就是说，这些语言也更难学习和掌握，如果你没有编程很长时间，可能会感到害怕。例如，ASP.NET 就不像 PHP 那么容易上手，尽管如果你已经了解 PHP，C#也不是很难学。

另一个好处是。NET 的最大优势在于它支持多语言。你现在可以用 C++、C#、Visual Basic.NET、Jscript.NET、Python、Perl、Java (J#)、COBOL、Eiffel 和 Delphi 编写(或者在不久的将来能够)ASP.NET 应用程序。将来你甚至会发现自己在用 PHP 编写 ASP.NET 应用程序——这不是不可能的！

这样做的好处是你可以混合代码。可以用 C#从用 C++或 VB.NET 写的类中实例化一个对象。这增加了公司招聘程序员的数量，也增加了你在网上为你的项目找到合适的预写课程的机会。

***3。您选择的开发环境***

这是一个 ASP.NET 大放异彩的领域！

微软为 ASP.NET 发布了一个名为 Web Matrix 的免费开发环境，它将 PHP 的其他免费开发环境打得落花流水。它有一个内置的 Web 服务器，数据库管理界面 FTP 集成，等等。不仅如此，微软还发布了 MSDE——MS SQL server 的免费开发版。它完全具备 MS SQL server 2000 的所有功能，你为 MSDE 编写的任何应用程序都可以在 MS SQL server 上正常运行。

如果你买得起 [Visual Studio。NET](http://msdn.microsoft.com/vstudio/productinfo/overview.asp) ，它也提供了一些惊人的品质。它允许您:

*   从数据库中自动创建报告和图表，
*   逐行调试代码，同时查看应用程序中发生了什么，
*   在执行过程中给变量赋一个临时值，以便测试不同的场景，
*   调试时将光标悬停在代码中的变量上，以查看它们“现在”有什么值，

…还有更多。

***4。这是……**网*

ASP.NET 是。这种好处太大了，不容忽视。如果你知道如何编写 ASP.NET 应用程序，你也知道如何编写普通的应用程序。即使是 windows 应用程序，如果你对 Windows 窗体类(相对于 Web 窗体)有所了解的话。PHP 有 PHP-GTK，但与. NET 相比，它目前还很不成熟

***5。为**开发更便宜*

没想到会这样，是吧？甚至让我大吃一惊！由于 ASP.NET 是一个如此强大的应用程序，而且它是免费提供的(包括代码编辑器、Web 服务器和 FTP 客户端)，我实际上最终支付的费用比我购买由 UltraEdit(35 美元)、防弹 FTP(30 美元)和 MySQL front(0 美元)组成的 PHP 开发环境要少(0 美元)。也就是说，托管 ASP.NET 仍然比 PHP 昂贵。

***6。真是跨平台***

。NET 目前非常依赖于 Windows 平台。这是一件坏事，但我很有信心。NET 将在几年内变得非常跨平台。为什么？不久前，微软发布了 Rotor，这是 CLR (CLR =运行代码的东西)和 windows 和 BSD Unix 的大多数非 Windows 特定类库的共享源代码实现，并提供了两者的源代码。当我第一次听到这个消息时，我不相信——这听起来真的不像是微软会做的事情。当我意识到这事实上是真的时，我认为 Rotor 是微软可能做到的最小最弱的实现。

我大错特错了。Rotor 并不是廉价制造的——在大多数重要方面，它实际上与商业上的同类产品相同。。NET 还有一个非常强大的平台抽象层，使得移植到其他操作系统变得非常容易。不仅如此，CLI 和 C#现在已经被 ECMA 标准化了。在 Ximian 的支持下，Mono 项目正在开发。NET 框架。所有这些因素使我相信。NET 成为跨平台的可能性非常高。

##### 试试看！

但不要把我(或哈利)的话当作真理！亲自去 ASP.NET 试试。首先，[下载。NET framework](http://download.microsoft.com/download/.netframesdk/Redist/1.0/W98NT42KMeXP/EN-US/dotnetredist.exe) (21MB)，然后[下载 MSDE](http://www.asp.net/msde/default.aspx) (我前面讲的 MS SQL 2000-light—33MB)最后，[ASP.NET Web Matrix](http://www.asp.net/webmatrix/download1.aspx)(1.2 MB)。

一旦你安装了这些，你就准备好开发 ASP.NET 应用程序了——去看看[官方的 ASP.NET 快速入门教程](http://www.gotdotnet.com/quickstart/)，或者更好的是，[凯文·杨克关于开始 ASP.NET 的优秀文章系列](http://webmasterbase.com/article/781)。尽情享受吧！

听听双方的观点！找出为什么[反对派认为 PHP 优越](http://www.webmasterbase.com/article/870)，自己拿主意！

## 分享这篇文章