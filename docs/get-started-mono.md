# 开始使用单声道

> 原文：<https://www.sitepoint.com/get-started-mono/>

这里有一个免责声明:我避免使用 Linux，而且无论如何我也不是 Linux 专家。一想到黑屏和闪烁的光标，我就不寒而栗。我发现自己在移动鼠标，试图找到一个可以点击的图标或一个可以选择的菜单。

正是从这样一个角度，这篇文章将展示任何人(甚至我！)可以让 Mono 在一个新的、干净的 Linux 机器上运行。我将讲述我安装这个包的经历，并讨论运行 Mono 所需的所有组件。

##### 什么是单声道？

Mono 是一个开源项目，用于编写许多。NET 框架的跨平台可执行代码。Mono 由 Novell 赞助，它让开发人员有机会不仅为 Windows 开发 ASP.NET 和 Windows 窗体应用程序，而且现在主要为 Linux 开发。

虽然它们还没有完成，但为标准微软编写的应用程序。NET 框架现在可以通过 Mono 移植并运行在 Linux 上。然而，Mono 上框架实现中的漏洞意味着您仍然需要检查您的应用程序没有使用任何尚未实现的服务。

##### 单声道有什么好用的？

目前，Mono 正在开发中，但该项目已经达到了一些重要的里程碑，这使得它对于今天的部署来说足够合适和稳定。C#编译器是 Mono 唯一的全功能编译器。是的，没错:VB.NET 和 J#开发者需要切换到 C#才能完全使用 Mono。[在这里观看 VB.NET 编译器的进展](http://www.go-mono.com/mbas.html)。

编译器本身是用 C#编写的。您可以下载全部 170 万行 C#代码并自己编译，或者，正如我们很快会看到的，您可以使用众多发行版中的一个来简化安装过程。

ASP.NET 功能齐全，在 Mono 中得到支持。其实是目前 Mono 的一大强项。您可以使用 Mono (XSP)附带的内置 Web 服务器或者通过 Apache 的修改来构建和部署 Web 表单和 Web 服务。对于那些不喜欢使用 Windows 和 IIS 来托管应用程序的人来说，Mono 提供了一个可行的替代方案。

Windows 窗体目前正在开发中，但功能正在发展。虽然复杂的 WinForm 应用程序，如 Web Matrix，目前还不可用，但 Mono 中有 WinForms 的替代方案，可以构建 GUI 应用程序。Gtk# 和#WT 是 Linux 上流行的 GUI 工具的包装器。WinForms 本身是作为 Wine(Linux 上的 Win32 实现)的库扩展而构建的。项目的[进度记录在这里](http://www.go-mono.com/winforms.html)。

ADO.NET 和这个系统。数据类也相当成熟；然而，在撰写本文时，它们还没有达到生产水平。这是 Mono 中最大的项目之一，目前您可以连接到各种各样的数据库。为许多通常与 Linux 相关的数据库提供了本机支持，比如 PostgreSQL。

Mono 已成功用于商业和大量使用的应用。Novell 用这个工具开发了两个产品，iFolder 和 ZenWorks。此外，SourceGear 在其 Vault 应用程序中使用了部署在 Mono 中的 Web 服务。

##### 入门:下载单声道

Mono 有很多包。你可以下载最新的源代码版本和每日快照源代码版本，通过 CVS，一个 RPM 包，或者为那些使用 Xiamian Desktop 的用户提供红地毯安装。到目前为止，最容易使用的是红地毯系统，虽然类似于 RPM，但它提供了良好的版本控制，允许您非常容易地在您的机器上升级 Mono。

Mono 下载页面详细介绍了各种包和如何下载它们，以及针对特定种类的 Linux 的特定包。

[http://www.mono-project.com/downloads/](http://www.mono-project.com/downloads/)

我下载了 [Mono Runtime tarball](http://mono2.ximian.com/archive/1.0/mono-1.0.tar.gz) ，并使用下面的命令解包发行版:

```
tar -zxvf mono-1.0.tar.gz
```

解压缩 tarball 后，我可以使用以下命令开始安装过程:

`./configure
make
make install`

就在这时，我意识到我需要升级系统上的 pkg-config，因为安装时出现了一些错误。我为此找到了[RPM 分布](http://www.rpmfind.net//linux/RPM/cooker/cooker/i586/Mandrake/RPMS/pkgconfig-0.15.0-4mdk.i586.html)，并使用以下命令安装它:

```
rpm -Uvh pkgconfig-0.15.0-4mdk.i586.rpm
```

制作过程现在顺利进行了。

##### 运行在 Linux 上的 Hello World

使用陈词滥调的“Hello World”例子来测试一个安装总是一件好事！

打开您最喜欢的 Linux 文本编辑器(我用的是 vi ),输入以下简单的 C#应用程序代码:

```
public class HelloWorld  

{ 

  static void Main()  

  { 

    System.Console.WriteLine("Hello World!"); 

  } 

}
```

将该文件另存为 HelloWorld.cs，并用 Mono C#编译器编译该类:

`mcs HelloWorld.cs`

在保存 HelloWorld.cs 的目录中，您现在应该会看到一个 HelloWorld.cs 文件。这是标准的 MSIL 代码，可以在安装了。安装了. NET 框架，包括 Windows。

有两种方法运行单声道应用程序。一种方法是使用“mint”，它是 MSIL 字节码的解释器。Mint 不会将您的应用程序编译并运行成本机代码，因此它不是 JIT 运行时。然而“Mono”是一个 JIT 运行时，当第一次被请求时，它把字节码编译成它被设计的平台和处理器的本机代码。

这意味着，就性能而言，Mono 应用程序要比 mint 快得多，尽管它不像绑定到特定操作系统那样可移植。另一方面，Mint 的可移植性要好得多，因为它是用 ANSI C 编写的，所以可以在多种部署平台上使用。

要运行我们的 Hello World 应用程序，我们可以使用以下命令来调用 Mono:

```
mono hello.exe
```

我们使用以下命令来调用 mint:

```
mint hello.exe
```

##### 品尝 ASP.NET

Mono 自带网络服务器，随时可以发布你的 ASP.NET 应用。单声道。XSP 非常易于使用，是一个简单的 ASP.NET 网络服务器，几乎类似于 Windows 上 Web Matrix 附带的 Cassini 服务器。为了获得更大的优势，你可以下载一个名为 [mod_mono]( /) 的模块，它允许 Apache 支持 ASP.NET。然而，在本文中，我们将研究一个简单 Web 应用程序的形成，并使用 XSP 托管它。

我必须承认，我在创建 Web 应用程序的代码时作弊了:我使用 Visual Studio 来创建 Web 应用程序及其相关文件。然后，我将代码复制到准备托管的 Linux 机器上。

对于我们的例子，我们将创建一个带有简单按钮和标签的网页。单击按钮时，标签将显示用户单击了该按钮。

```
<%@ Page Language="C#" %>  

<script runat="server">  

 void Button1_Click(object sender, EventArgs e)   

 {  

   titleTag.InnerText = "You clicked the button!";  

 }  

 void Page_Load(object sender, EventArgs e)   

 {  

   if (!IsPostBack)  

     titleTag.InnerText = "Page has loaded";  

 }  

</script>  

<html>  

<head>  

<title runat="server" id="titleTag"/>  

</head>  

<body>  

 <form runat="server">  

   <asp:Button id="Button1" OnClick="Button1_Click" runat="server" Text="Button"/>  

 </form>  

</body>  

</html>
```

您可以将该文件保存到一个目录中，该目录将作为 Web 应用程序的根目录。

如果您的应用程序使用 codebehinds，您还需要使用 Mono 编译器编译源文件，以便获得应用程序的编译后的程序集。就像 Windows 上的 ASP.NET 一样，您需要将它放到根目录下的/bin 目录中。

默认情况下，XSP 监听 8080 端口，以免干扰 Apache 但是，您可以通过命令行设置服务器监听不同的端口。您还可以指定希望托管的应用程序的根目录:

`xsp.exe  [--root rootdir]  
               [--port N]`

随着服务器的运行，您可以通过任何标准的 Web 浏览器访问您的页面。现在，我们知道了:ASP.NET 服务于 Linux！

##### 第三方工具

有许多工具可以帮助你开发 Mono，所以，不像我，你不需要求助于 Visual Studio。

*   MonoDevelop:
    MonoDevelop 的 GUI 功能在 Mono 中仍然缺乏，MonoDevelop 正在推动当前的实现来展示 Mono 可以实现的功能。与#develop for Windows 类似，MonoDevelop 支持语法高亮显示和从易于使用的界面编译项目。然而，这个工具仍然处于开发的早期阶段，目前缺乏使它成为真正有用的工具所需的特性。
*   Eclipse:
    被宣传为“一种通用的工具平台——一个开放的可扩展的 IDE，可以做任何事情，不需要特别说明”，Eclipse 是开发 Mono 应用程序的一个很好的解决方案。通过下载并安装 Eclipse 的[改进 C#插件，你可以为你的 Mono 开发拥有一个全功能的免费的基于 Java 的 IDE。](http://www.improve-technologies.com/alpha/esharp/)
*   [**MonoDoc:**](http://mono2.ximian.com/archive/1.0/monodoc-1.0.tar.gz) ******这是一个用于 Mono 文档的浏览器。默认情况下，它不是通过标准的 Mono 包安装的，但对于那些需要检查某些 API 和框架部分是否在 Mono 中可用的人来说，它是一个救命稻草。******

 ******##### 摘要

从我对 Mono 的研究中，我逐渐明白这是一个对. NET 非常重要的项目。NET 从微软操作系统的束缚中解放出来，将允许它在通常禁止微软技术进入的社区中扩展。

Mono 只会越来越强大，也许在不久的将来。NET 开发人员将能够像我们今天为 Windows 开发一样容易地为 Linux 开发。****** 

## **分享这篇文章**