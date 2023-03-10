# ASP.NET 2.0:入门指南

> 原文：<https://www.sitepoint.com/asp-net-getting-started/>

ASP.NET 是当今最令人兴奋的 web 开发技术之一。当微软几年前发布第一个版本时，许多 web 开发人员认为他们所有的梦想都实现了。这是一个强大的平台，有许多内置功能，惊人的性能水平，以及最好的 ide(集成开发环境)之一:Visual Studio。还有人想要什么？事实上，ASP.NET 展示了更快、更容易、更有规律地开发动态网站的方法，结果令人印象深刻。

时光流逝，ASP.NET 成长了。ASP.NET 2.0 带来了非凡的新功能，以及扩展和更强大的底层框架。不仅如此，包括 Visual Web Developer 2005 速成版和 SQL Server 2005 速成版在内的所有开发工具的基础版都是免费的！

这本书——SitePoint 的 [*使用 VB 和 C#构建你自己的 ASP.NET 网站，第二版*](https://www.sitepoint.com/books/aspnet2/)——向你展示如何将所有这些技术结合起来，以产生奇妙的效果。它将带您一步一步地完成每项任务，向您展示如何充分利用每项技术和工具。已经使用过 ASP.NET 早期版本的开发人员会发现，最新版本的变化如此之大，以至于本书的整个章节都在讨论 ASP.NET 2.0 特有的特性。

在这一节选中，包含了本书的前四章，我们将从设置您的系统开始。网络开发。在第 2 章中，我们将逐步了解 ASP.NET 的基础知识，并了解页面的各个部分、视图状态和指令，以及 ASP.NET 语言。[第 3 章](https://www.sitepoint.com/asp-net-getting-started/)介绍 VB 和 C#编程基础，从控件和页面事件到命名空间和代码隐藏文件。本节选的最后一章，[第 4 章](https://www.sitepoint.com/asp-net-getting-started/)，汇集了我们在构建 web 表单时讨论过的所有实用信息，并将 web 和 HTML 服务器控件、Web 用户控件、母版页和级联样式表付诸实践！

在接下来的几页中，我们将会涉及*很多*的内容，所以如果你愿意，可以自由地[下载并离线阅读这些章节](https://www.sitepoint.com/show-modal-popup-after-time-delay/)。

我们开始吧！

##### 什么是 ASP.NET？

多年来，Active Server Pages (ASP)技术可以说是 web 开发人员在 Windows web 服务器上构建动态网站的首选，因为它提供了灵活而强大的脚本功能。2002 年初，微软发布了一项名为 ASP.NET 的互联网开发新技术。ASP.NET 代表了 ASP 的一个飞跃，无论是它的复杂性还是它为开发人员带来的生产力的提高。它继续在语言支持方面提供灵活性，但现在 ASP.NET 开发人员已经可以使用几种成熟的编程语言，而不是一系列简单的脚本语言。在 ASP.NET 的开发不仅需要理解 HTML 和网页设计，还需要牢固掌握面向对象编程和开发的概念。

在接下来的几节中，我们将向您介绍 ASP.NET 的基本知识。我们将逐步完成在您的 web 服务器上安装它的过程，并通过一个简单的示例演示 ASP.NET 页面是如何构建的。但是首先，让我们定义一下 ASP.NET 到底是什么。

*ASP.NET 是一种基于微软开发 web 应用的服务器端技术。NET 框架。让我们来分解这个充满术语的句子。*

ASP.NET 是服务器端技术；也就是说，它运行在 web 服务器上。大多数网页设计师的职业生涯都是从学习客户端技术开始的，比如 HTML、JavaScript 和层叠样式表(CSS)。当 web 浏览器请求仅使用客户端技术创建的网页时，web 服务器只需抓取浏览器(或客户端)请求的文件，然后将它们发送出去。客户端完全负责读取这些文件中的标记，并解释这些标记以在屏幕上显示页面。

像 ASP.NET 这样的服务器端技术是不同的。服务器端代码(例如，ASP.NET 页面中的代码)不是由客户端解释，而是由 web 服务器解释。在 ASP.NET 的例子中，页面中的代码由服务器读取，并用于生成 HTML、JavaScript 和 CSS，然后发送到浏览器。因为 ASP.NET 代码的处理发生在服务器上，所以它被称为服务器端技术。如图 1.1 所示，客户端只能看到 HTML、JavaScript 和 CSS。服务器完全负责处理服务器端代码。

![1556_fig-userinteractwithwebapp](img/4a6ce174be22e9a55446576a5973c84f.png)
*图 1.1。与网络应用交互的用户*

请注意此类交易中涉及的三个角色:

*   **用户**–永远不要忘记在队伍的末端(或起点)有一个真实的人。
*   **网络客户端**–这是人们用来与网络应用程序交互的软件程序。客户端通常是网络浏览器，如 Internet Explorer 或 Firefox。
*   **网络服务器**–这是位于服务器上的软件程序。它处理 web 客户端发出的请求。

ASP.NET 是一种开发 web 应用程序的技术。web 应用程序只是动态网站的一个花哨名称。Web 应用程序通常(但不总是)将信息存储在数据库中，并允许网站的访问者访问和更改这些信息。已经开发了许多不同的编程技术和支持的语言来创建 web 应用程序；PHP、JSP、Ruby on Rails、CGI 和 ColdFusion 只是其中比较流行的几种。然而，ASP.NET 并没有把你束缚在一种特定的技术和语言上，而是让你使用各种熟悉的编程语言来编写 web 应用程序。

ASP.NET 使用微软。NET 框架。的。NET Framework 将构建 Windows 桌面应用程序、web 应用程序、web 服务等所需的所有技术收集到一个包中，并使它们可用于 40 多种编程语言。

即使解释了所有的行话，你可能仍然想知道是什么让 ASP.NET 这么好。事实是，现在有很多服务器端技术，每种技术都有自己的优点和缺点。然而，ASP.NET 有几个真正独特的特征:

*   ASP.NET 允许你使用你最喜欢的编程语言，或者至少是一种接近它的语言。的。NET 框架目前支持 40 多种语言，其中许多可用于构建 ASP.NET 网站。最受欢迎的选择是 C#(读作“C sharp”)和 Visual Basic(或 VB)，这是我们将在本书中涉及的。
*   ASP.NET 网页是编译的，不是解释的。在 ASP。NET 的前身 ASP 对页面进行了解释:每当用户请求一个页面时，服务器会将该页面的代码读入内存，找出如何执行代码(即解释代码)，然后执行它。在 ASP.NET，服务器只需要知道如何执行代码一次。代码被编译成高效的二进制文件，可以非常快速地反复运行，而不需要每次都重新读取页面。这代表着与 ASP 旧时代相比，性能上的一大飞跃。
*   ASP.NET 可以完全使用的功能。NET 框架。内置了对 XML、web 服务、数据库交互、电子邮件、正则表达式和许多其他技术的支持。NET，这样你就不用重新发明轮子了。
*   ASP.NET 允许您将页面中的服务器端代码从 HTML 布局中分离出来。当你和一个由程序员和设计专家组成的团队一起工作时，这种分离是一个很大的帮助，因为它允许程序员修改服务器端代码，而不需要踩在设计师精心制作的 HTML 上？反之亦然。
*   ASP.NET 使得在许多 web 表单中重用公共用户界面元素变得容易，因为它允许我们将这些组件保存为独立的 web 用户控件。在这本书的过程中，你将学习如何给你的网站添加强大的功能，并在许多地方用最少的努力重用它们。
*   您可以获得帮助开发 ASP.NET web 应用程序的优秀工具。Visual Web Developer 2005 是一个免费的、功能强大的可视化编辑器，包括代码自动完成、代码格式化、数据库集成功能、可视化 HTML 编辑器、调试等功能。在本书的过程中，你将学习如何使用这个工具来构建我们讨论的例子。
*   的。NET Framework 最初只适用于 Windows 平台，但由于有了像 [Mono](https://www.mono-project.com/) 这样的项目，它已经被移植到其他操作系统上了。

还和我在一起吗？太好了！是时候收拾我们的工具开始建造了！

##### 安装所需的软件

如果你打算学习 ASP.NET，你首先需要确保你已经安装了所有必要的软件组件，并且可以在你的系统上运行。在我们继续前进之前让我们处理这个。

*   **互联网信息服务(IIS)或 Cassini**–IIS 是运行 ASP.NET 网络应用程序的首选网络服务器。您需要 Windows 光盘来安装和配置它。遗憾的是，Windows 的某些版本(如 Windows XP 家庭版)不支持 IIS。如果你是这些用户中的一员，那就是卡西尼号。Cassini 是一个小型网络服务器，专为希望建立 ASP.NET 网站的爱好者设计。它不如 IIS 健壮、强大或用户友好，但对于我们的目的来说已经足够了。当我们在《第 5 章，构建 Web 应用程序》中使用 Visual Web Developer 时，我们将利用该产品的内置开发 Web 服务器，因此无法访问您系统上的 IIS 不会成为问题。
*   **现代网络浏览器**——在本书中，我们将使用 Internet Explorer 6，但如果您愿意，也可以在开发过程中使用其他浏览器。任何现代浏览器都可以。
*   **。NET Framework 2.0**–正如我们已经讨论过的。NET 框架驱动 ASP.NET。当您安装。NET 框架，您将自动安装运行 ASP.NET 所需的文件。你很可能会。因为它是通过 Windows Update 服务自动安装的。
*   **。NET 框架软件开发工具包(SDK)**—NET Framework 2.0 软件开发工具包(SDK)是免费下载的，其中包含必要的 Web 应用程序开发工具、用于纠错的调试器以及一套示例和文档。

我们还需要一个数据库。在本书中，我们将使用以下内容:

*   Microsoft SQL Server 2005 Express Edition–这是 SQL Server 2005 的免费版本，但功能仍然齐全。如果您使用过这些技术的早期版本，您应该知道 SQL Server 2005 Express 是早期 Microsoft SQL 数据引擎(MSDE)的替代产品。你可以在微软网站的[阅读更多关于 SQL Server 2005 各版本之间的差异。](https://www.microsoft.com/sql/2005/productinfo/sql2005features.asp)
*   **SQL Server Management Studio Express**–因为 SQL Server Express 版没有附带任何可视化管理工具，所以您可以使用这个由微软开发的免费工具来访问您的 SQL Server 2005 数据库。

##### 安装 Web 服务器

***安装互联网信息服务(IIS)***

 *IIS 带有大多数版本的支持服务器的 Windows 操作系统，包括 Windows 2000 Professional、server 和 Advanced ServerWindows XP 专业版；Windows XP 媒体中心版；和 Windows Server 2003——但它不会在所有版本中自动安装，这就是它可能不会出现在您的计算机上的原因。IIS 不适用于这些操作系统的家庭版，如 Windows XP 家庭版。如果你运行这个，你将需要依靠卡西尼，我们将在下面讨论。

要查看您是否安装并运行了 IIS，只需找到您的管理工具文件夹(有时它是一个菜单选项；有时它是控制面板中的一个文件夹(要查看此文件夹，您需要在“经典视图”中查看控制面板))并检查它是否包含 Internet 信息服务的快捷方式。如果快捷方式不可见，则说明它没有安装。要安装 IIS，只需按照下列步骤操作:

1.  在控制面板中，选择添加或删除程序。

3.  选择添加/删除 Windows 组件。组件列表将在几秒钟内可见。

5.  在组件列表中，选中 Internet 信息服务(IIS)，如图 1.2 所示。默认的安装选项对于 ASP.NET 开发来说已经足够了，但是您可能希望单击“详细信息”来查看您可以添加的额外选项。

7.  单击下一步。Windows 可能会提示您插入 Windows 光盘。

![1556_0107_iis_install](img/f6cbdb29fd2b8eb0625120a15ab7f08c.png)
*图 1.2。安装 IIS*

***向开始菜单添加管理工具***

以下是将管理工具添加到 Windows XP“开始”菜单的方法:

1.  右键单击开始按钮，选择属性，打开任务栏和开始菜单属性对话框。

3.  单击自定义…按钮，调出自定义开始菜单对话框。

5.  如果您使用的是经典的“开始”菜单，请选中“显示管理工具”，然后单击“确定”。

7.  如果您使用的是 Windows XP 风格的“开始”菜单，请单击“高级”选项卡，滚动“开始”菜单项列表，直到找到“系统管理工具”，然后选择“所有程序”菜单上的“显示”或“所有程序”菜单和“开始”菜单中的“显示”。

一旦安装了 IIS，请关闭“添加或删除程序”对话框。若要检查 IIS 是否已正确安装，请查看能否在“管理工具”中找到 Internet 信息服务快捷方式。如果可以，安装 IIS。打开链接，与 IIS 管理控制台进行第一次联系，如图 1.3 所示。在左窗格中，您最初会看到您的计算机的名称，您可以展开其节点。

![1556_0114_iis_tool](img/cf25d8e93e81eccd4cfb3227acc017ae.png)
*图 1.3。IIS 管理工具*

您现在可以关闭此工具；以后还会遇到的。

您现在已经准备好托管 web 应用程序了。虽然我们不会讨论外部使用的 IIS 的配置，但是我们将向您展示如何配置 IIS 来支持 ASP.NET 应用程序的本地开发，以便以后可以将它们上传到您的外部 web 托管提供商。

***安装卡西尼***

如果你不能安装 IIS，你需要下载并安装 Cassini:

1.  转到卡西尼号下载页面。

3.  下载 Cassini 安装程序可执行文件(cassini.exe)。

5.  运行 cassini.exe，按照安装程序显示的步骤操作，接受默认选项。

如果过程按计划进行，运行 Cassini 所需的一切都可以在文件夹`C:Cassini`中找到。双击该文件夹中的`CassiniWebServer.exe`，启动其管理控制台，如图 1.4 所示。

![1556_fig-cassini](img/bbc24c05c2796189420d8f13408d37c4.png)
*图 1.4。卡西尼管理控制台*

我们需要做更多的工作来让卡西尼号正常运行，但是我们需要安装。NET 框架和软件开发工具包。

##### 安装。NET 框架和 SDK

要开始创建 ASP.NET 应用程序，您需要安装。NET 框架和软件开发工具包(SDK)。的。NET Framework 包括运行和查看 ASP.NET 页面所需的文件，而 SDK 包括示例、文档和各种免费工具。

***安装。NET 框架***

获取。NET 框架是直接从网上下载并安装的。请注意，它也是通过 Windows Update 提供的，因此您的系统上可能已经安装了它。要进行检查，请打开文件夹`C:WINDOWSMicrosoft.NETFramework`(如果您的 Windows 安装在`C:WINDOWS`以外的地方，请相应地更改此路径)。如果这个文件夹不存在，你肯定没有。已安装. NET Framework。如果它确实存在，你应该在里面找到至少一个名为`v1.1.4322`的文件夹。每种类型的文件夹都包含不同版本的。您已经安装了. NET Framework。如果这些文件夹名称中至少有一个不是以 v2 或更高版本开头，您需要安装最新版本的。NET 框架。

安装最新版本的。NET Framework，只需遵循下面概述的步骤:

1.  转到[ASP.NET 支持网站](https://www.asp.net/)并点击下载。NET 框架链接。

3.  在下面。NET Framework 版可再发行软件包标题下，单击适合您的硬件的下载链接。记住，我们将首先安装可再发行软件包，然后安装 SDK。该链接将带您进入下载页面。

5.  选择您想要的安装语言和版本，然后点按“下载”。

7.  将文件保存到本地目录。下载完成后，双击可执行文件开始安装。

9.  按照向导显示的步骤操作，直到安装完成。

***安装 SDK***

现在您已经安装了可再发行软件包，您需要安装软件开发工具包(SDK):

1.  回到 ASP.NET 支持网站，按照下载。NET 框架链接。

3.  这一次，单击。NET Framework 版软件开发工具包标题。该链接将带您进入下载页面。

5.  选择要使用的安装语言版本，然后单击下载，就像下载可再发行软件包一样。

7.  出现提示时，将文件保存到本地目录。

9.  下载完成后，双击可执行文件开始安装。在此之前，我强烈建议您关闭所有其他程序，以确保安装顺利进行。

11.  按照。直到安装完成。当询问设置选项时，使用默认值是安全的。

SDK 的安装时间比框架稍长。

*下载量很大！*
*这个。NET Framework SDK 重约 350MB，所以下载可能需要一段时间。*

##### 配置 Web 服务器

***配置 IIS***

安装后。NET 框架和 SDK，您将需要配置 IIS，使它知道 ASP.NET。为此，您需要遵循几个简单的步骤:

通过选择开始>所有程序> Microsoft 打开命令提示符。NET 框架 SDK v2.0 > SDK 命令提示符。

键入以下命令来安装 ASP。网络:

```
C:Program Files...SDKv2.0>**aspnet_regiis.exe -i**  

Start installing ASP.NET (2.0.50727).  

.........  

Finished installing ASP.NET (2.0.50727).
```

一旦 ASP.NET 安装，关闭命令提示符，并再次检查，以确认 ASP.NET 安装正确。

*运行`aspnet_regiis.exe`*
*视情况而定，aspnet 可能已经给你装上了，但是运行 aspnet_regiis.exe 也无妨。此外，请记住，如果重新安装 IIS，您需要再次运行该实用工具。*

***配置卡西尼***

如果你已经安装了卡西尼号，你需要进入。NET 框架来强迫 Cassini 正常工作。

通过选择开始>所有程序> Microsoft 打开命令提示符。NET 框架 SDK v2.0 > SDK 命令提示符。

在提示符下输入以下命令:

```
C:Program Files...SDKv2.0>**gacutil /i C:CassiniCassini.dll**  

Microsoft (R) .NET Global Assembly Cache Utility. Version 2.0...  

Copyright (c) Microsoft Corporation.  All rights reserved.  

Assembly successfully added to the cache
```

卡西尼号现在准备出发。

我的文件放在哪里？

*IIS 推荐*
*从现在开始，我们提供的说明将围绕 IIS，因为 Cassini 不适合生产环境。我们将讨论的许多概念不适用于 Cassini，因为它简单得多，并且缺少 IIS 的许多功能。在需要的地方，卡西尼号会给出指令，但是 IIS 将会得到大部分的讨论。*

既然您已经安装并运行了 ASP.NET，让我们来看看您的 web 应用程序文件保存在计算机的什么地方。您可以设置 IIS 在任何文件夹中查找 web 应用程序，包括`My Documents`文件夹，甚至是网络共享。默认情况下，IIS 将磁盘上的`C:Inetpubwwwroot`文件夹映射到网站的根目录，这通常被认为是存储和管理 web 应用程序的好地方。

如果您在 Windows 资源管理器中打开这个`wwwroot`文件夹，并将它的内容与出现在 IIS 管理工具的默认网站中的文件进行比较，如图 1.5 所示，您会注意到这些文件和文件夹是相同的(IIS 中会列出一些额外的项目；我们很快就会看到这些)。您需要使用 IIS 管理工具来设置 IIS 下这些文件和文件夹的行为。我们很快会看到更多。

![1556_fig-foldersiis](img/2b48d51a3d785682bb3b40e6e381c228.png)
*图 1.5。wwwroot 中的文件夹也会出现在 IIS 中*

***使用本地主机***

通过将您的文件放在`C:Inetpubwwwroot`中，您可以让您的 web 服务器访问它们。如果你已经开发网页很长时间了，习惯可能会驱使你通过双击 HTML 文件直接在浏览器中打开文件。然而，因为 ASP.NET 是一种服务器端语言，所以在文件被发送到浏览器显示之前，你的 web 服务器需要对其进行破解。如果服务器没有得到这个机会，ASP.NET 代码就不会被转换成你的浏览器可以理解的 HTML。因此，不能使用 Windows 资源管理器直接从磁盘打开 ASP.NET 文件。

您的本地 web 服务器可以通过一个指示当前计算机的特殊网址来访问:`https://localhost/`。如果您现在尝试这样做，IIS 将打开一个默认的帮助页面(尽管此行为会根据您的 Windows 安装的设置而有所不同；例如，如果您得到一个错误而不是默认的帮助页面，请不要担心)。

但是，您需要记住的是，您将用来访问本地 web 应用程序的地址总是以`https://localhost/`开头，并且默认情况下，这个根地址指向您磁盘上的文件夹。

为了在实践中看到这一点，在`C:Inetpubwwwroot`中创建一个名为`index.htm`的新文件，包含以下内容(注意，本书中使用的所有代码和图像都可以从 sitepoint.com 下载。更多信息请参见前言):

```
Example 1.1\. index.htm  

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  

<html>  

  <head>  

    <title>Simple HTML Page</title>  

  </head>  

  <body>  

    <P>This is a simple HTML page.  

  </body>  

</html>
```

现在，通过`https://localhost/index.htm`加载这个页面，如图 1.6 所示。

![1556_0125_first](img/35980ef7b054231a7fe8ccdbfe76b000.png)
*图 1.6。测试 IIS*

*遇到错误？*
*如果页面没有如图 1.6 所示加载，您的 IIS 安装有问题。您可能需要仔细检查安装步骤是否正确，并重新检查 IIS 配置过程。*

这个 localhost 名称相当于所谓的环回 IP 地址 127.0.0.1，所以在浏览器中输入`https://127.0.0.1/index.htm`可以得到相同的结果。如果您知道它们，您也可以使用您的机器的名称或 IP 地址来达到相同的目的。

请注意，如果您尝试这些等效操作，在页面打开之前会出现一个对话框，要求您提供网络凭据。发生这种情况是因为您不再使用本地身份验证，这在 localhost 中是隐含的。

*停止和启动 IIS*
*现在我们已经启动并运行了 IIS，并且安装了 ASP.NET，让我们来看看如何在需要时启动、停止和重启 IIS。在大多数情况下，您总是希望运行 IIS 但是，如果您出于某种原因(比如安全考虑)想要暂时关闭它，您可以这样做。此外，由于潜在的安全漏洞，一些外部程序可能会在启动时停止 IIS，因此您需要自己重新启动它。如果您想在不使用 IIS 时停止它，只需打开 Internet 信息服务管理控制台，右键单击默认网站并选择停止。或者，在选择默认网站后，您可以使用工具栏中的停止、暂停和播放图标。*

***虚拟目录***

正如我们在“我把文件放在哪里？”一节中看到的那样，物理子文件夹`C:Inetpubwwwroot`也成为网站的子目录。例如，假设您的公司有一个 web 服务器，提供来自`C:Inetpubwwwroot`的文档。您的用户可以通过`https://www.example.com/`访问这些文档。如果您创建了一个名为`about`的`wwwroot`子文件夹，则可以通过`https://www.example.com/about/`访问该目录下的文件。

您也可以在您的网站中设置另一个子目录，但提供磁盘上不同位置的文件。例如，如果您正在开发另一个 web 应用程序，您可以将它的文件存储在`C:devOrderSystem`中。然后，您可以在 IIS 中创建一个名为`order`的新虚拟目录，它映射到这个位置。这个新网站将可以通过网址`https://www.example.com/order/`访问。由于该应用程序正在开发中，您可能希望将 IIS 设置为在项目完成之前对公众隐藏该虚拟目录；您现有的网站仍然可见。

默认情况下，在 IIS 中预配置了一个名为`IISHelp`的虚拟目录；它映射到`c:windowshelpiishelp`。你可以在图 1.7 中看到，IISHelp 包含名为`common`和`iis`的子目录——这些是`c:windowshelpiishelp`中的物理文件夹。

![1556_fig-iisvirtualdirectory](img/7700aaaa3ac632ef768ff44bc47a5721.png)
*图 1.7。IISHelp 虚拟目录*

让我们在您的服务器上创建一个虚拟目录，并用一个简单的页面测试它:

首先，您需要在磁盘上创建一个虚拟目录将映射到的文件夹。在磁盘上容易访问的位置创建一个名为 WebDocs 的文件夹，比如`C:`，并在该文件夹中创建一个名为`Learning`的文件夹。我们将使用这个文件夹`C:WebDocsLearning`，来做书中的各种练习。

将之前创建的`index.htm`文件复制到新创建的`Learning`文件夹中。

在 Internet 信息服务管理控制台中，右键单击默认网站，然后选择新建>虚拟目录。将出现虚拟目录创建向导。单击下一步。

您需要为您的虚拟目录选择一个别名:输入`Learning`，然后单击 Next。

浏览并选择您在步骤 1 中创建的学习文件夹，或输入其完整路径(`C:WebDocsLearning`)。单击下一步。

在下一个屏幕中，您可以选择目录的权限设置。通常，您会希望保持默认选项(读取和运行脚本)处于选中状态。单击下一步。

单击完成。您将看到新的虚拟目录是默认网站的子目录，如图 1.8 所示。

![1556_0130_NewVirtualDirectory](img/3d9f50c6a0229846447bdd62ab947a1c.png)
*图 1.8。创建新的虚拟目录*

通过在浏览器的地址栏中输入`https://localhost/Learning/index.htm`来加载该链接。如果一切顺利，您应该看到您的小 HTML 页面加载，如图 1.9 所示。

![1556_0135_TestingVirtualDirectory](img/da573fb481dd9be52ddb42fda0e19391.png)
*图 1.9。测试您的新虚拟目录*

请注意，通过 HTTP 协议加载页面，您的请求会通过 IIS。由于`index.htm`是一个简单的 HTML 页面，不需要任何服务器端处理，所以也可以直接从磁盘加载。然而，这将不会是 ASP.NET 脚本的情况，你会看到通过这本书的其余部分。

一旦创建了新的虚拟目录，您就可以通过 Internet 信息服务管理控制台查看和配置它，如图 1.8 所示。您可以在右侧面板中看到该文件夹的内容。

由于 index.htm 是默认的文档名称之一，您只需在浏览器的地址栏中输入`https://localhost/Learning/`即可访问该页面。要查看和编辑虚拟目录(或任何目录)的默认文档名，您可以在 IIS 管理控制台中右键单击目录名，单击属性，然后选择文档选项卡。您将看到如图 1.10 所示的对话框。

![1556_0140_DefaultDocuments](img/d52a119024b36d17046c9867f77196cc.png)
*图 1.10。学习虚拟目录的默认文档类型*

默认情况下，当我们在没有指定文件名的情况下请求一个目录时，IIS 会查找一个带有默认文档名称的页面，比如`index.htm`或`default.htm`。如果没有索引页，IIS 会认为我们希望看到所请求位置的内容。仅当为相关目录选择了目录浏览选项时，才允许此操作。您可以在“属性”窗口的“目录”选项卡中找到该选项。

*目录浏览*
*启用目录浏览不是你通常想做的事情。允许访问者自由地查看和访问组成你的网页的所有文件和目录，不仅有点凌乱和不专业，还会增加潜在的安全问题(你不希望任何黑客把鼻子伸进你的代码，对吧？).因此，默认情况下，当请求目录时，IIS 不允许目录浏览:如果一个默认文件如`index.htm`不存在，准备提供给访问者，一条消息“拒绝目录列表”将被提供。*

要更改虚拟目录的选项，您必须在 IIS 控制台中右键单击虚拟目录(在我们的例子中是 Learning ),然后选择 Properties。我们刚刚使用的属性对话框允许我们配置各种有用的属性，包括:

*   **虚拟目录**–该选项允许您配置目录级属性，包括路径信息、虚拟目录名称、访问权限等。通过向导设置的所有内容都可以通过该选项卡进行修改。
*   **Documents**–该选项允许您配置当用户键入完整 URL 时显示的默认页面。例如，因为`default.aspx`被列为默认页面，用户只需要在浏览器的地址栏中输入`https://www.mysite.com/`，而不是`https://www.mysite.com/default.aspx`。通过选择菜单右侧的相应按钮，您可以轻松地更改和删除这些默认页面。
*   **目录安全**–该选项为您提供虚拟目录的安全配置设置。
*   **HTTP Headers**–该选项使您能够强制控制服务器上的页面缓存，添加自定义 HTTP 头，编辑评级(这有助于识别您的网站向用户提供的内容)，以及创建 MIME 类型。暂时不要担心这个。
*   **自定义错误**–该选项允许您定义自己的自定义错误页面。您可以使用您公司的徽标和您选择的消息来自定义错误消息，而不是显示出现在 Internet Explorer 中的标准错误消息。
*   **ASP.NET**–该选项卡允许您为存储在该文件夹中的 ASP.NET 应用程序配置选项。

此时需要注意的一点是，我们可以为默认的 Web 站点节点设置属性，并选择让它们“传播”到我们创建的所有虚拟目录。

***利用卡西尼***

如果您坚持使用不支持 IIS 的 Windows 版本，您将需要利用 Cassini 来启动和运行简单的 ASP.NET web 应用程序。Cassini 不支持虚拟目录、安全设置或 IIS 的任何其他花哨功能；它只是一个非常简单的 web 服务器，为您提供启动和运行所需的基础知识。

要开始使用 Cassini:

1.  创建一个名为`C:WebDocsLearning`的目录，就像我们在“虚拟目录”一节中创建的目录一样。

3.  把 index.htm 复制到这个文件夹里。我们首先在“使用本地主机”一节中看到了 index.htm。

5.  通过打开`C:Cassini`启动 Cassini(或者，如果您选择在其他地方安装 Cassini，请打开该文件夹)，然后双击文件`CassiniWebServer.exe`。

7.  Cassini has just three configuration options:
    1.  应用程序目录——应用程序文件存储在这里。在这个字段中输入 C:WebDocsLearning。

    3.  服务器端口——Web 服务器几乎总是运行在端口 80 上，所以我们不会触及这个设置。

    5.  虚拟根目录——这类似于 IIS 的虚拟目录功能，尽管它远没有那么灵活。默认情况下，它被设置为`/`，这意味着您可以通过输入地址`https://localhost/index.htm`来访问文件`C:WebDocsLearningindex.htm`。然而，为了匹配我们的 IIS 虚拟目录配置，我们希望将这个文件的地址设为`https://localhost/Learning/index.htm`。为了创造这种效果，在这个字段中输入`/Learning/`。
8.  填写完应用程序目录和虚拟根目录字段后，单击 Start 按钮启动 web 服务器。

10.  在点击 Start 之后，一个链接将会出现在窗口的底部，如图 1.11 所示。

![1556_fig-cassingserving](img/3d01258575a1f8da276457becf25a881.png)
*图 1.11。卡西尼服务网站*

当你在 Cassini 中浏览打开这个网站时，你不会马上看到`index.htm`——相反，你会看到一个文件列表。Cassini 只将名为`default.htm`或`default.aspx`的文件识别为默认文档，它不允许你像 IIS 一样配置这个特性。

##### 安装 SQL Server 2005 Express Edition

确保 IIS 之后。NET Framework 和 SDK 都已正确安装，现在是时候继续安装下一个您将在本书中使用的软件了:SQL Server 2005 Express Edition。

SQL Server 2005 是微软为大中型公司和企业提供的数据库解决方案。SQL Server 2005 可能非常昂贵，它通常需要自己的专用数据库服务器，有时还需要雇用经过认证的数据库管理员(DBA)来确保其维护；然而，它确实为大型 web 应用程序提供了一个健壮的、可伸缩的解决方案。

对于本书中的示例，我们将使用 SQL Server 2005 Express Edition，它是免费的，功能足够强大，可以满足我们的需求。与昂贵的版本不同，SQL Server 2005 Express Edition 不附带可视化管理实用程序，但您可以使用 Microsoft 的另一个免费工具—SQL Server Management Studio Express，我们接下来将安装它—来实现这些目的。

您可以按如下方式安装 SQL Server 2005 Express Edition:

1.  导航到[https://msdn.microsoft.com/vstudio/express/sql/](https://msdn.microsoft.com/vstudio/express/sql/)，点击立即下载链接。

3.  在下一页中，您可以在 SQL Server 2005 Express Edition 和 SQL Server 2005 Express Edition with Advanced Services 之间进行选择。前者将满足我们的需求。您的系统应该满足必要的要求，所以请点击下载。

5.  下载完成后，双击下载的可执行文件，并按照步骤安装产品。全程使用默认选项是安全的，尽管这是一个相当长的过程。

如果一切顺利，SQL Server 2005 Express Edition 将在过程结束时启动并运行。与 IIS 一样，SQL Server 作为服务在后台运行，接受到数据库而不是网页的连接。可以在地址`(local)SqlExpress`访问 SQL 服务器。

##### 正在安装 SQL Server Management Studio Express

为了有效地使用您的 SQL Server 2005 安装，您将需要某种管理工具来允许您使用您的数据库。SQL Server Management Studio Express 是由 Microsoft 提供的免费工具，允许您管理 SQL Server 2005 的安装。

若要安装 SQL Server Management Studio Express，请按照下列步骤操作:

1.  再次导航到[https://msdn.microsoft.com/vstudio/express/sql/](https://msdn.microsoft.com/vstudio/express/sql/)，并点击立即下载链接。

3.  这一次，下载与您以前安装的 SQL Server 2005 版本相对应的 SQL Server Management Studio Express edition。

5.  下载完成后，执行文件并按照步骤安装产品。

安装后，可以从“开始”>“所有程序”>“Microsoft SQL Server 2005”>“SQL Server Management Studio Express”访问 SQL Server Manager Express。当执行时，它将首先要求您的凭证，如图 1.12 所示。

![1556_0145_Connect](img/20c1161c6670d36ee8bd767f80f0df06.png)
*图 1.12。连接到 SQL 服务器*

默认情况下，安装后，SQL Server 2005 Express Edition 将只接受使用 Windows 身份验证的连接，这意味着您将使用 Windows 用户帐户登录到 SQL Server。因为您是安装 SQL Server 2005 的用户，所以您已经拥有 SQL Server 的完全权限。单击“连接”连接到您的 SQL Server 2005 实例。

![1556_0150_Managing](img/0622941335ef71e704abca74cf9ea8e9.png)
*图 1.13。管理您的数据库服务器*

通过身份验证后，您将看到图 1.13 所示的界面，该界面为您提供了许多与 SQL Server 2005 实例进行交互和管理的方法。

SQL Server Management Studio 允许您浏览 SQL Server 内部的对象，甚至修改它们的设置。例如，您可以更改服务器的安全设置，方法是右键单击 COMPUTERSQLEXPRESS(其中 COMPUTER 是您的计算机的名称)，选择“属性”，然后从面板中选择“安全”，如图 1.14 所示。

![1556_0155_Security](img/9cf2e191905b29ba8a1a0c4b4e02b2c0.png)
图 1.14。使用 SQL Server Management Studio 更改服务器设置

*SQL Server 和实例*
*你可以在一台电脑上同时运行多台 SQL Server？每个 SQL Server 称为一个 SQL Server 实例。这有什么用？假设您有一台生产服务器，在同一个 SQL Server 实例上运行两个应用程序和两个独立的数据库。如果出于某种原因，我们需要为第一个应用程序重启 SQL Server，那么在重启过程中，第二个应用程序的数据库将不可用。如果第二个应用程序的数据库运行在 SQL Server 的第二个实例上，我们就不会有这样的问题——第二个应用程序会继续工作，不会错过任何一个节拍。*

*SQL Server 的每个实例都需要一个名称。SQL Server 2005 Express Edition 的默认实例名是`SQLEXPRESS`。连接到数据库服务器时，必须以计算机名/实例名的形式指定计算机名和 SQL Server 实例名。你可以在图 1.12 和图 1.13 中看到这个规范，这里我们连接到一台名为 VM2 的计算机上的一个名为`SQLEXPRESS`的实例。*

##### 安装 Visual Web Developer 2005

Visual Web Developer 自动化了许多您在其他环境中需要自己完成的任务，并且包括许多强大的功能。对于本书中的第一个练习，我们将推荐您使用简单的文本编辑器，如记事本，但是您将逐渐学习如何使用 Visual Web Developer 来简化我们将要处理的一些任务。

因此，让我们安装这个工具，以确保我们在需要时可以随时使用它。

1.  前往[https://msdn.microsoft.com/vstudio/express/vwd/](https://msdn.microsoft.com/vstudio/express/vwd/)，点击下载链接。

3.  执行下载的文件。

5.  接受默认选项。在某一点上，你会被要求安装微软 MSDN 2005 速成版，这是该产品的文件。安装它不会有什么坏处，但是你需要有耐心，因为它相当大。(请注意，您已经安装了。NET Framework 2.0 文档，以及 SDK。)

*奖金！*
*如果你已经安装了。你已经安装了微软 MSDN 2005 速成版。*

在本书中，我们将在第 5 章“构建 web 应用程序”中开始使用 Visual Web Developer 构建真正的 Web 应用程序。在此之前，我们将使用记事本(或另一个简单的文本编辑器)创建示例，以便您准备好在需要使用 Visual Web Developer 时充分利用它所提供的功能。

##### 撰写您的第一个 ASP.NET 页面

对于您的第一个 ASP.NET 练习，我们将创建如图 1.15 所示的简单示例。

![1556_1060_FirstPage](img/9629dba30b935843ebb316c788510995.png)
图 1.15。你的第一个 ASP.NET 页面

我们开始吧！打开你的文本编辑器(记事本也可以)。如果您有自动创建 ASP.NET 页面的软件，如 Visual Studio。NET 或 Visual Web Developer 2005 速成版，请先不要用？虽然这些都是很好的工具，可以让你快速启动并运行，但它们确实假设你已经了解 ASP.NET 是如何工作的。

因此，打开文本编辑器，在前面创建的学习文件夹中创建一个名为 FirstPage.aspx 的新文件。通过输入页面的 HTML 开始编辑 FirstPage.aspx，如下所示:

```
Example 1.2\. FirstPage.aspx (excerpt)    

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"    

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">    

<html>    

  <head>    

    <title>My First ASP.NET 2.0 Page</title>    

  </head>    

  <body>    

    <p>Hello there!</p>    

    <p>The time is now: </p>    

  </body>    

</html>
```

到目前为止，一切顺利，对吧？现在，让我们添加一些 ASP.NET 代码来创建页面的动态元素，从时间开始。

```
Example 1.3\. FirstPage.aspx (excerpt)    

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"    

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">    

<html>    

  <head>    

    <title>My First ASP.NET 2.0 Page</title>    

  </head>    

  <body>    

    <p>Hello there!</p>    

    <p>The time is now:     

        <asp:Label runat="server" id="timeLabel" /></p>    

  </body>    

</html>
```

我们在文档中添加了一个`<asp:Label/>`标签。这是一个特殊的标记，让我们可以将动态内容插入到页面中。标记名的 asp:部分将其标识为内置的 ASP.NET 标记。ASP.NET 有许多内置标签；`<asp:Label/>`是最简单的一种。

属性将标签标识为需要在服务器上处理的东西。换句话说，web 浏览器将永远看不到`<asp:Label/>`标签；当客户端请求页面时，ASP.NET 会看到它，并在页面发送到浏览器之前将其转换为常规的 HTML 标记。由我们来编写代码，告诉 ASP.NET 用当前时间替换这个特殊的标签。

为此，我们必须向页面添加一些脚本。ASP.NET 让你选择在你的脚本中使用许多不同的语言。最常见的两种语言是 VB 和 C#。让我们来看看使用两者的例子。这是一个 VB 版本的页面:

```
Example 1.4\. FirstPage.aspx (excerpt)    

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"    

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">     

<html>    

  <head>    

    <title>My First ASP.NET Page</title>    

    <script runat="server" language="VB">    

      Sub Page_Load(sender As Object, e As EventArgs)    

        timeLabel.Text = DateTime.Now.ToString()    

      End Sub    

    </script>    

  </head>    

  <body>    

    <p>Hello there!</p>    

    <p>The time is now:     

       <asp:Label runat="server" id="timeLabel" /></p>    

  </body>    

</html>
```

这是用 C#写的同一个页面:

```
Example 1.5\. FirstPage.aspx (excerpt)    

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"    

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">     

<html>    

  <head>    

    <title>My First ASP.NET Page</title>    

      <script runat="server" language="C#">    

        protected void Page_Load(object sender, EventArgs e)    

        {    

          timeLabel.Text = DateTime.Now.ToString();    

        }     

    </script>    

  </head>    

  <body>    

    <p>Hello there!</p>    

    <p>The time is now:     

       <asp:Label runat="server" id="timeLabel" /></p>    

  </body>    

</html>
```

*区分大小写的线索*
*注意，C#和 VB 不一样，是区分大小写的。如果您键入的字母大小写不正确，页面将无法加载。如果这些语言看起来很复杂，不要担心:你会在第 3 章《VB 和 C#编程基础》中了解到更多。*

页面的两个版本实现了完全相同的东西。您甚至可以保存它们，给它们一个不同的文件名，然后分别测试它们。如果你以前从未做过任何服务器端编程，代码可能看起来有点吓人。但是在我们详细分析它之前，让我们加载页面并测试它的工作情况。使用您的网络浏览器，加载`https://localhost/Learning/FirstPage.aspx`。无论你加载的是 C#版本还是 VB 版本，输出应该如图 1.15 所示。

*没时间？*
*如果页面上没有显示时间，很可能是你直接在浏览器中打开了文件，而不是通过你的网络服务器加载。因为 ASP.NET 是一种服务器端语言，所以在将文件发送到浏览器进行显示之前，您的 web 服务器需要对其进行处理。如果它不能访问文件，ASP.NET 代码就永远不会被转换成你的浏览器可以理解的 HTML，所以要确保你通过输入一个实际的 HTTP URL(比如`https://localhost/Learning/FirstPage.aspx`)来加载页面，而不是一个本地路径和文件名(比如`C:WebDocsLearningFirstPage.aspx`)。*

那里发生了什么？让我们来分解一下这一页的新元素。

```
Example 1.6\. FirstPage.aspx (excerpt)    

<script runat="server">
```

此标记标记服务器端代码或代码声明块的开始。像`<asp:Label/>`标签一样，这个`<script>`标签使用`runat="server"`属性让 ASP.NET 知道在将页面发送到浏览器之前应该处理这个标签。

```
Example 1.7\. FirstPage.aspx (excerpt)    

Sub Page_Load(sender As Object, e As EventArgs)
```

```
Example 1.8\. FirstPage.aspx (excerpt)    

protected void Page_Load(object sender, EventArgs e)    

{
```

这里就不赘述了。现在，您需要知道的是，您可以编写脚本片段来响应不同的事件，如单击按钮或从下拉列表中选择项目。第一行代码的基本意思是，“每当页面被加载时，执行下面的脚本。”注意，C#用花括号(`{`和})将代码分组到块中，而 Visual Basic 使用诸如`End Sub`之类的语句来标记特定序列的结束。所以，上面 C#代码中的花括号(`{`)标志着页面第一次加载时将要执行的脚本的开始。

最后，这是页面上实际显示时间的一行:

```
Example 1.9\. FirstPage.aspx (excerpt)    

  timeLabel.Text = DateTime.Now.ToString()
```

```
Example 1.10\. FirstPage.aspx (excerpt)    

  timeLabel.Text = DateTime.Now.ToString();
```

如你所见这些。NET 语言有很多共同点，因为它们都是建立在。NET 框架。事实上，这两种语言处理上述代码行的唯一区别是 C#用分号(`;`)结束代码行。简单地说，这一行是这样说的:

*将`timeLabel`的文本设置为当前日期和时间，用文本表示。*

注意，`timeLabel`是我们给`<asp:Label/>`标签的`id`属性的值，我们想在这里显示时间。所以，`timeLabel.Text`，或者说`timeLabel`的`Tex`属性，指的是标签将要显示的文本。`DateTime`是一个内置于。NET 框架；它可以让你执行各种有用的日期和时间功能。的。NET Framework 有数千个这样的类，它们做了无数方便的事情。这些类统称为。NET 框架类库。

`DateTime`类有一个名为`Now`的属性，它返回当前的日期和时间。这个 Now 属性有一个名为`ToString`的方法，它将日期和时间表示为文本(一段文本在编程界被称为字符串)。类、属性和方法:这些都是任何程序员的词汇表中的重要词汇，我们将在本书的稍后部分更详细地讨论它们。现在，你需要从这个讨论中得到的是，`DateTime.Now.ToString()`会给你一个文本字符串形式的当前日期和时间，然后你可以告诉你的`<asp:Label/>`标签显示它。脚本块的其余部分只是简单地将松散的部分连接起来:

```
Example 1.11\. FirstPage.aspx (excerpt)    

End Sub    

</script>
```

```
Example 1.12\. FirstPage.aspx (excerpt)    

}    

</script>
```

VB 代码中的`End Sub`和 C#代码中的`}`标记了加载页面时要运行的脚本的结尾，而`</script>`标记标记了脚本块的结尾。

最后一件值得研究的事情是 ASP.NET 为您生成的代码。现在很明显，您的 web 浏览器只接收 HTML(没有服务器端代码！)，那么为那个标签生成了什么样的 HTML 呢？答案很好找！当网页显示在浏览器中时，您可以使用浏览器的查看源代码功能来查看网页的 HTML 代码。以下是您将看到的内容:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"    

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">     

<html>    

  <head>    

    <title>My First ASP.NET Page</title>    

  </head>    

  <body>    

    <p>Hello there!</p>    

    <p>The time is now:     

       <span id="timeLabel">6/20/2006 8:00:49 PM</span></p>    

  </body>    

</html>
```

注意，所有的 ASP.NET 代码都不见了！甚至脚本块也被完全移除了，`<asp:Label/>`标记被包含日期和时间的`<span>`标记(它与我们使用的`<asp:Label/>`标记具有相同的 id 属性)所取代。

这就是 ASP.NET 的工作方式。从网络浏览器的角度来看，ASP.NET 页面没有什么特别的；它只是一个普通的 HTML。所有的 ASP.NET 代码都由你的网络服务器运行，并转换成普通的 HTML 格式发送给浏览器。到目前为止，一切顺利:上面的例子相当简单。下一章当我们研究一些有价值的编程概念时，会变得更有挑战性。

##### 获得帮助

当您开发 ASP.NET web 应用程序时，您无疑会有需要回答的问题，以及需要解决的问题。ASP.NET 支持网站由微软开发，作为 ASP.NET 社区的门户网站，解答开发者在使用 ASP.NET 时遇到的问题。支持网站提供有用的信息，如新闻、下载、文章和论坛。您也可以在 [SitePoint 论坛](https://www.sitepoint.com/forums/)向有经验的社区成员提问。

##### 摘要

在本章中，您了解了. NET。您还了解了 ASP.NET 的好处，以及它是。NET 框架。

首先，您了解了 ASP.NET 的组件以及如何定位和安装。NET 框架。然后，我们探索了不仅使用这本书所需的软件，而且为了你或你的公司与 ASP.NET 开发的进展。

你已经在 ASP.NET 的世界里打下了坚实的基础！下一章将以这些知识为基础，开始更详细地向您介绍 ASP.NET，包括页面结构、您可以使用的语言、各种编程概念以及表单处理的细节。

##### 第二章。ASP.NET 基础知识

到目前为止，您已经了解了什么是 ASP.NET，以及它能做什么。您已经安装了开始工作所需的软件，并且已经了解了一些非常简单的表单处理技术，您甚至知道如何创建一个简单的 ASP.NET 页面。如果这一切现在看起来有点令人困惑，不要担心，因为随着本书的进展，你将学习如何在更高级的水平上使用 ASP.NET。

随着接下来几章的展开，我们将探索一些更高级的主题，包括控件的使用和各种编程技术。但是在你开始用 ASP.NET 开发应用程序之前，你需要理解一个典型的 ASP.NET 页面的内部工作原理——有了这些知识，你就能识别出我们将在本书中讨论的例子中引用的 ASP.NET 页面的部分。因此，在这一章中，我们将讨论 ASP.NET 页面的一些关键机制，具体来说:

*   页面结构
*   视图状态
*   名称空间
*   指令

我们还将介绍。NET 框架:VB 和 C#。随着本节的进行，我们将探索这两种语言之间的差异和相似之处，并清楚地了解它们为创建 ASP.NET 应用程序的人提供的能力。

那么，ASP.NET 页面到底是由什么组成的呢？接下来的几节将让你深入了解典型的 ASP.NET 页面的结构。

##### ASP.NET 页面结构

ASP.NET 网页只是文件扩展名为`.aspx`的文本文件，可以放在任何装有 ASP.NET 的网络服务器上。

当客户端请求 ASP.NET 页面时，web 服务器将页面传递给 ASP.NET 运行时，一个运行在 web 服务器上的程序负责读取页面并将其编译成. NET 类。然后，这个类被用来生成 HTML，并将它发送回用户。对此页的每个后续请求都避免了编译过程。NET 类可以直接响应请求，生成页面的 HTML 并将其发送给客户端，直到。aspx 文件更改。这个过程如图 2.1 所示。

ASP.NET 页面由以下元素组成:

*   指令
*   代码声明块
*   代码呈现块
*   ASP.NET 服务器控件
*   服务器端注释
*   文字文本和 HTML 标签

图 2.2 展示了一个简单的 ASP.NET 页面的各个部分。

![1556_fig-aspnetpagelifecycle](img/12e7e63829a1c1c9a55dd405bd1af8df.png)
*图 2.1。ASP.NET 页面的生命周期*

![1556_fig-partsofaspnetpage](img/662b1b0dd832e2025269dc154b017f57.png)
*图 2.2。ASP.NET 页面的部分*

为了确保我们在同一页上并且代码有效，将这段代码保存在你在第一章中创建的学习虚拟目录下的一个名为`Hello.aspx`的文件中。通过`https://localhost/Learning/Hello.aspx`加载文件应该会呈现如图 2.3 所示的结果。

## 分享这篇文章*