# ASP.NET 入门

> 原文：<https://www.sitepoint.com/getting-started-asp-net/>

多年来，ASP 一直是 Web 开发人员在 Windows Web 服务器上构建动态网站的首选。提供脚本的简单性和多种语言选择的灵活性，再加上 ASP 内置于每一个基于 Microsoft Windows 的 Web 服务器中的事实，使得 ASP 很难效仿。

2002 年初，微软发布了新的互联网开发平台。配音。这个平台包含了期待已久的 ASP。最初命名为 ASP+，最终在微软互联网开发产品的新命名方案下以 ASP.NET 的名字发布。

与 ASP 相比，对于开发人员来说，ASP.NET 在复杂性和生产力上都代表了一个飞跃。它保持了语言选择的灵活性，但是开发人员必须在几种成熟的编程语言之间进行选择，而不是一系列简单的脚本语言。在 ASP.NET 进行开发不仅需要理解 HTML 和 Web 设计，还需要牢固掌握面向对象编程和开发的概念。

在这篇文章中，我将向你介绍 ASP.NET 的基本知识。我将带您在 Web 服务器上安装它，并带您浏览几个简单的动态页面，展示 ASP.NET 和其他服务器端语言之间的差异。在这篇文章的结尾，我希望向你展示 ASP.NET 可以更复杂一点，但是比其他选择要强大得多。

##### 什么是 ASP.NET？

ASP.NET 是一个服务器端框架，用于开发基于微软。NET 框架。对于今天想用微软技术构建动态网页的任何开发人员来说，掌握这种充满术语的描述是面临的第一个挑战。在这一节中，我将试图打破行话，给你一个清晰的画面，到底什么是 ASP.NET，它能为你做什么。

**ASP.NET 是服务器端**；也就是说，它运行在 Web 服务器上。大多数网页设计师都是从学习客户端技术开始的，比如 HTML、JavaScript 和层叠样式表(CSS)。当 Web 浏览器请求使用客户端技术创建的网页时，Web 服务器只需获取浏览器(客户端)请求的文件，然后将它们发送出去。客户端完全负责读取文件中的代码，并解释它以在屏幕上显示页面。

像 ASP.NET 这样的服务器端技术是不同的。服务器端代码(例如 ASP.NET 页面中的代码)不是由客户端解释，而是由 Web 服务器解释。在 ASP.NET 的情况下，页面中的代码由服务器读取，并用于动态生成标准的 HTML/JavaScript/CSS，然后发送到浏览器。因为 ASP.NET 代码的所有处理都发生在服务器上，所以它被称为服务器端技术。

ASP.NET 是一个开发网络应用的框架。Web 应用程序只是动态网站的一个别出心裁的名字。Web 应用程序通常(但不总是)将信息存储在 Web 服务器上的数据库中，并允许网站的访问者访问和更改这些信息。有许多不同的编程语言可以用来创建 Web 应用程序:PHP、Java、Perl 和 Cold Fusion 只是其中比较流行的几种。然而，ASP.NET 不是把你束缚在一种特定的语言上，而是一种框架——一种让你用你最喜欢的(受支持的)编程语言编写网络应用的技术。

最后，**ASP.NET 基于微软。NET 框架。**这个。NET Framework 将构建 Windows 应用程序、Web 应用程序和 Web 服务所需的所有技术收集到一个包含 20 多种编程语言的包中。要使用 ASP.NET 开发网站，您需要下载。NET 框架软件开发工具包，我将在下一节中指导您完成。

好了，所有的行话都已不再神秘，你可能还在想:是什么让 ASP.NET 如此出色？与构建 Web 应用程序的其他选择相比，ASP.NET 具有以下优势:

*   ASP.NET 允许你使用你最喜欢的编程语言，或者至少是和它非常接近的语言。的。NET 框架目前支持 20 多种语言，所有这些语言都可以用来构建 ASP.NET 网站。
*   ASP.NET 网页是编译的，不是解释的。ASP.NET 不是在每次请求动态页面时都读取并解释代码，而是将动态页面编译成高效的二进制文件，服务器可以非常快速地执行这些文件。与 PHP、Perl 和 ASP 等解释型语言相比，这代表了性能上的巨大飞跃。
*   ASP.NET 可以完全使用的功能。NET 框架。内置了对 XML、Web 服务、数据库交互、电子邮件、正则表达式和许多其他技术的支持。NET，这样你就不用重新发明轮子了。
*   ASP.NET 允许你将页面中的服务器端代码与 HTML 布局分开。当在一个由程序员和设计专家组成的团队中工作时，这种分离非常有帮助，因为它允许程序员修改服务器端代码，而无需踩在设计人员精心制作的 HTML 上，反之亦然。

有了这些有利条件，ASP.NET 相对来说没有什么不利因素。事实上，我想到的只有两个:

*   ASP.NET 是微软的技术。虽然这本身不是问题，但它确实意味着(至少目前)您需要使用 Windows 服务器来运行 ASP.NET 网站。如果你的组织使用 Linux 或其他操作系统作为它的网络服务器，你就不走运了。
*   严肃的 ASP.NET 开发需要理解面向对象编程。如果你不认为自己是程序员，JavaScript 让你头疼，ASP.NET 可能不是你的最佳选择。

还和我在一起吗？太好了！是时候收集交易工具了…

##### 我需要什么？

目前，如果你要学习 ASP.NET，你需要一个基于 Windows 的网络服务器。开源倡议正在进行中，以生产将在其他操作系统上运行的 ASP.NET 版本，如 Linux 然而，预计这些药物至少在几个月内不会以稳定的形式出现。

虽然开发人员可以选择在 Windows 95、98 或 ME 上试用 ASP，但 ASP.NET 需要的是真正的交易。在开始之前，您至少需要一台装有 Windows 2000 Professional 的计算机。Windows XP Professional 也能很好地工作，任何 Windows 2000 Server 软件包也一样。

如果你没有一个必需的 Windows 版本，另一个选择是从有这个版本的人那里获得 ASP.NET 托管。你可以在任何操作系统上编写 ASP.NET 页面；但是要运行它们，你需要把它们放在一个装有 IIS 和 ASP.NET 的网络服务器上。

除此之外，你所需要的就是足够的磁盘空间来安装 IIS(如果你还没有的话)，以及一个文本编辑器。记事本对于涉猎来说很好，当然这也是本文所需要的，但是如果你对 ASP.NET 很认真，你可能会想投资一个像 Visual Studio 这样的开发环境。NET 或 Dreamweaver MX。

##### 安装 ASP.NET

如果您将在支持 ASP.NET 的 Web 主机上工作，这一节中的所有内容都已经为您做好了，您可以放心地跳过。如果没有，您需要将您的机器设置为配备 ASP.NET 的本地 Web 服务器，这正是我要向您展示的。

在安装 ASP.NET 之前，您必须安装 Microsoft Internet 信息服务(IIS)。如果没有(或者不确定)，请完成以下步骤进行安装:

1.  打开*控制面板*

3.  点击*添加/删除程序*

5.  点击*添加/删除 Windows 组件*

7.  从组件列表中选择 IIS

9.  按照屏幕提示完成安装

你需要把你的 Windows 光盘放在手边，所以确保你提前把它从壁橱里拿出来。

一旦安装了 IIS，你可以通过打开 Internet Explorer(或任何其他网络浏览器)并在地址栏中键入`http://localhost/`来确保它正常工作。当您按 Enter 键时，浏览器应该会加载一个介绍 IIS 并提供服务器文档的页面。

安装了 IIS 之后，您就可以添加 ASP.NET 支持了。ASP.NET 是美国的一部分。NET Framework，微软为 Windows 互联网应用新推出的多语言开发平台。要开发 ASP.NET 网站，您至少需要。NET Framework Redist，里面包含了运行所需的一切。包括你电脑上的 ASP.NET 网站。您可能还想获取。NET Framework SDK，其中包含示例应用程序和面向程序员的完整参考文档。

的。NET Framework Redist 和。NET Framework SDK 是微软提供的免费下载。现在，你可以通过这个地址直接进入下载页面:[http://www.asp.net/download-1.1.aspx](http://www.asp.net/download-1.1.aspx)要知道 Redist 和 SDK 加起来有 131MB 的下载量，所以如果你用的是调制解调器，这可能需要一些时间！

下载完 SDK 后，运行。NET Framework Redist 安装文件。该程序可能会警告您，您的 Windows Installer 和/或 Microsoft 数据访问组件(MDAC)已过期。如果程序要求，允许它自动更新您的 Windows Installer。如果您的 MDAC 已经过时，请取消 SDK 安装，并在重新开始 SDK 安装之前，从[http://www.microsoft.com/data/](http://www.microsoft.com/data/)下载最新的 MDAC(撰写本文时为 2.7 版)。

如果您下载了 SDK，请在。NET Framework Redist 安装完毕。安装程序完成后，ASP.NET 将被安装到您的系统上。

##### 测试 ASP.NET

确保 ASP.NET 在您的系统上正常工作的一个好方法是配置。NET Framework SDK 示例(假设您下载了 SDK)。在“开始”菜单上，您会发现一个名为 Microsoft 的新文件夹。NET 框架 SDK。在该文件夹中，单击示例和快速入门教程。

Internet Explorer 将打开一个页面，其中列出了在示例和教程可用之前需要完成的两个步骤:

1.  安装。NET Framework 示例数据库

3.  设置快速入门

继续并单击第一个链接，然后在出现的对话框中单击 Open 运行数据库安装。不要被步骤 1 的描述中提到的 Microsoft SQL Server 所迷惑；该程序将自动在您的系统上安装 MSDE，这是一个 SQL Server 的独立版本，用于托管需要数据库后端的示例。

完成步骤 1 后，单击步骤 2，让它用快速入门教程的示例数据填充新安装的数据库。第 2 步完成后，单击“启动”转到快速入门教程主页(以后，您可以通过单击“开始”菜单上与之前相同的图标来访问此页面)。

由于本文的重点是 ASP.NET 开发，请单击 ASP.NET 快速入门链接。如果你被带到 ASP.NET 快速入门教程，你可以放心，ASP.NET 是正确安装在您的系统上。教程本身是在 ASP.NET 写的，所以如果你能看到教程，你的服务器可以运行 ASP.NET。

快速入门教程给出了大量 ASP.NET 的实际例子，但是除非你是一个有经验的程序员，否则你需要更多的指导才能完全通过例子来学习。观众中的凡人因此被建议关闭他们的浏览器并继续阅读…

##### 你的第一个 ASP.NET 页面

对于您在 ASP.NET 的第一次运行，我们将创建如下所示的简单示例:

![My First ASP.NET Page](img/dd872ad684ed0a4b945edb9eac3aa172.png)我们开始吧！打开你的文本编辑器(同样，记事本也可以)。如果你有自动创建 ASP.NET 页面的软件，比如 Visual Studio。请不要使用它。这些程序为快速构建复杂的 ASP.NET 页面提供了许多强大的工具，但是对于像这样的简单例子，它们往往会成为最大的障碍。此外，在让一个奇特的工具为你做之前，学习如何手工做一些事情是有一定价值的。

所以用你的文本编辑器，创建一个名为`hellothere-cs.aspx`的文件。如果使用的是记事本，保存文件时记得从保存类型下拉菜单中选择*所有文件*；否则，记事本会将其保存为`hellothere-cs.aspx.txt`并且无法正常工作。

让我们从这个页面的普通 HTML 开始:

```
<html>    

<head>    

<title>My First ASP.NET Page</title>    

</head>    

<body>    

<p>Hello there!</p>    

<p>The time is now: </p>    

</body>    

</html>
```

到目前为止一切顺利，对吧？现在，对于这个简单的 HTML 代码，我们将添加一些 ASP.NET 代码来创建页面的动态元素，从时间开始。

```
<html>    

<head>    

<title>My First ASP.NET Page</title>    

</head>    

<body>    

<p>Hello there!</p>    

<p>The time is now: **<asp:label runat="server" id="TimeLabel" />**</p>    

</body>    

</html>
```

我们在文档中添加了一个`<asp:label>`标签。这是一个特殊的标记，让我们可以将动态内容插入到页面中。标签名的`asp:`部分将其标识为内置的 ASP.NET 标签。ASP.NET 有一大堆内置标签，而`<asp:label>`可以说是最简单的。

属性将标签标识为需要在服务器上处理的东西。换句话说，网络浏览器永远看不到`<asp:label>`标签——ASP.NET 看到了它，并在页面发送到浏览器之前将其转换为常规的 HTML 标签。由我们来编写代码，告诉 ASP.NET 用当前时间替换这个特殊的标签。

为此，我们必须向页面添加一些脚本。像之前的 ASP 一样，ASP.NET 让你选择在你的脚本中使用许多不同的语言。最常见的两种语言是 Visual Basic。NET(VB.NET)和 C#(发音为 C 升 C 调)。我个人更喜欢 C#，所以让我们从 C#版本的脚本开始:

```
**<%@ Page Language="C#" %>**    

<html>    

<head>    

<title>My First ASP.NET Page</title>    

**<script runat="server">**    

 **protected void Page_Load(Object Source, EventArgs E)**    

 **{**    

 **TimeLabel.Text = DateTime.Now.ToString();**    

 **}**    

**</script>**    

</head>    

<body>    

<p>Hello there!</p>    

<p>The time is now: <asp:label runat="server" id="TimeLabel" /></p>    

</body>    

</html>
```

好吧，如果你以前从未做过任何服务器端编程，这可能开始看起来有点可怕。让我们来分解本页的新元素:

```
<%@ Page Language="C#" %>
```

这一行叫做*页面指令*。它允许您设置应用于整个页面的选项。在本例中，我们使用它来设置页面上服务器端脚本块将使用的语言。我们将在以后的文章中看到可以用 page 指令设置的其他页面属性。

```
<script runat="server">
```

这个标签标记了服务器端脚本块的开始。像`<asp:label>`标签一样，这个`<script>`标签使用`runat="server"`属性让 ASP.NET 知道在将页面发送到浏览器之前应该处理这个标签。

```
 protected void Page_Load(Object Source, EventArgs E)    

  {
```

如果你曾经用 Java 或 C++写过程序，这几行就不需要太多解释了。如果不是，它们会非常令人困惑。现在，您需要知道的是，您可以编写脚本片段来响应不同的事件，如单击按钮或从下拉列表中选择项目。第一行基本上说的是“每当页面加载时执行下面的脚本”。左大括号(`{`)标记了页面加载时要执行的脚本的开始。

对于注重技术的人来说，我们刚刚看到的代码是页面加载事件处理程序的方法声明。如果这对你来说毫无意义，不要担心，但是如果它对你来说毫无意义，你就领先了十步。

最后，这是页面上实际显示时间的一行:

```
 TimeLabel.Text = DateTime.Now.ToString();
```

简单地说，这一行是这样说的:

<q>*将`TimeLabel`的`Text`属性设置为当前日期/时间，用一串文本表示。*</q>

```
TimeLabel is the id attribute of the <asp:label> tag that we want to show the time. So TimeLabel.Text, the Text property of TimeLabel, refers to the text that will be displayed by the tag.

```
DateTime is a *class* that is built into the .NET Framework that lets you do all sorts of useful things with dates and times. There are thousands of these *classes* in the .NET Framework that do all sorts of useful things. If you're interested in perusing them, you can use the Class Browser sample application that comes with the ASP.NET QuickStart Tutorial, and should be available on your server at this address:[http://localhost/quick start/aspplus/samples/class browser/VB/class browser . aspx](http://localhost/quickstart/aspplus/samples/classbrowser/vb/classbrowser.aspx)

`DateTime`类有一个名为`Now`的*属性*，它总是包含当前日期/时间。这个`Now`属性有一个名为`ToString()`的*方法*，它将日期/时间表示为文本(一段文本在编程界通常称为*字符串*)。

类、属性、方法...在任何面向对象程序员的词汇表中，这些都是重要的词汇，但是现在你需要从这个讨论中得到的是,`DateTime.Now.ToString()`将以文本字符串的形式给出当前日期/时间，然后你可以告诉你的`<asp:label>`标签显示它。

脚本块的其余部分只是收尾工作:

```
 }    

</script>
```

右大括号(`}`)标记加载页面时运行的脚本的结尾，`</script>`标记标记脚本块的结尾。
保存对文件的更改，将其放在 Web 服务器上的目录中，然后在浏览器中加载页面。您应该会看到这样一个页面:
如果没有显示时间，很可能是你直接在浏览器中打开了文件，而不是通过网络服务器加载。
因为 ASP.NET 是一种服务器端语言，所以在文件被发送到浏览器显示之前，您的 Web 服务器需要对其进行破解。否则，ASP.NET 代码就永远不会被转换成你的浏览器能够理解的 HTML。在这个例子中，`<asp:label>`标签没有被当前日期/时间替换。
确保通过您的 Web 服务器加载页面(例如，通过键入以 http://localhost/开头的 URL，或者如果您没有使用自己的机器，则键入您的 Web 服务器的名称)，示例应该显示良好。
当页面显示在浏览器中时，使用查看源代码功能(在 Internet Explorer 中使用*视图- >源代码*)查看页面的 HTML 代码。以下是您将看到的内容:

```
<html>    

<head>    

<title>My First ASP.NET Page</title>    

</head>    

<body>    

<p>Hello there!</p>    

<p>The time is now: <span id="TimeLabel">6/2/2002 1:55:09 PM</span></p>    

</body>    

</html>
```

注意，所有的 ASP.NET 代码都不见了！页面指令和脚本块已经被完全移除，`<asp:label>`标记已经被包含日期/时间字符串的`<span>`标记(具有相同的`id`)所取代。
看到这里，你会对 ASP.NET 的工作方式有一个全面的了解。从 Web 浏览器的角度来看，ASP.NET 页面没有什么特别的；它只是一个普通的 HTML，就像其他网页一样。所有的 ASP.NET 代码都由您的 Web 服务器运行，并转换成发送到浏览器的普通 HTML。

##### VB.NET 版本

正如我前面说过的，ASP.NET 允许您使用自己选择的多种编程语言来编写页面中的脚本块。为了便于比较，下面是我们刚刚看的转换成 VB 的例子。网络:

```
**<%@ Page Language="VB" %>**     

<html>     

<head>     

<title>My First ASP.NET Page</title>     

<script runat="server">     

 **Sub Page_Load(Source As Object, E As EventArgs)**     

 **TimeLabel.Text = DateTime.Now.ToString()**     

 **End Sub**     

</script>     

</head>     

<body>     

<p>Hello there!</p>     

<p>The time is now: <asp:label runat="server" id="TimeLabel" /></p>     

</body>     

</html>
```

如您所见，页面中唯一发生变化的部分是 page 指令和代码块，该指令现在将 VB 指定为页面中使用的语言。

我不会浪费你的时间在脚本块里描述三行 lines 代码；如果你仔细观察，你会发现它本质上和我们最初例子中的 C#代码是一样的。改变的只是语言语法。

为了证明这两个示例是等价的，请在文本编辑器中键入上面的代码，并将其保存为 hellothere-vb.aspx。如果在浏览器中查看，您会发现它与 C#示例完全相同，包括它生成的 HTML 代码。

![My First ASP.NET Page](img/a556ccd92493744724beec8cbfa906df.png)注意:ASP.NET 页面的文件名不必包含`-vb`或`-cs`来标识使用的语言；我这样做只是为了我们自己的利益，让我们的例子有条理。然而，所有 ASP.NET 页面都应该以`.aspx`结尾。

##### 敬请期待...

在我的下一篇文章中，我将向您展示如何使用 ASP.NET 在 Web 上创建和处理表单！在那之前，我欢迎你的评论！

## 分享这篇文章

```

```