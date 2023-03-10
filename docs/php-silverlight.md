# 带 PHP 的 Silverlight 初学者指南文章

> 原文：<https://www.sitepoint.com/php-silverlight/>

近年来，web 开发人员越来越倾向于开发所谓的 RIA，即富互联网应用程序。这些可以使用许多不同的技术来构建，但是它们的共同点是它们的目标是在 web 浏览器中重建桌面应用程序体验。微软在 2007 年末凭借 Silverlight 进入 RIA 领域。对于外行来说，Silverlight 是一种客户端技术。NET 框架。这意味着。NET 开发人员可以利用他们现有的技能来创建基于 Silverlight 的应用程序，学习起来相对容易。Silverlight 可以在大多数现代浏览器的 Windows 和 Mac 上运行。值得注意的是，Silverlight 是一种客户端技术，这意味着它不依赖于任何特定的服务器端平台。不管你的网站是用普通的 HTML、ASP.NET 还是 PHP 开发的，它都可以是托管的。你可能认为在 aPHP 应用中使用 Silverlight 会比较困难，但实际上相当简单；您可以利用 JSON、XML 或任何数量的通信技术将数据从 Silverlight 传递到 PHP 后端。然而，在本文中，我们选择使用 SOAPWeb 服务。我将首先向您展示如何开始使用 Silverlight 进行开发，然后介绍如何在 PHP 中设置 web 服务，最后演示如何从您的 Silverlight 应用程序访问该服务。要跟进，您需要 Visual Studio、Silverlight 工具和 PHP5。我将向您展示如何用 SilverlightTools 安装 Visual Studio，但我将假设您已经用 PHP 安装了一个 web 服务器。否则，使用微软的 Web PlatformInstaller 进行设置是非常简单的，你可以在[以前的 SitePoint 文章](https://www.sitepoint.com/article/php-windows-web-platform-installer)中找到这样做的说明。您应该熟悉面向对象的编程概念，尽管没有必要了解任何 C#或 VB.NET，因为我将向您展示所有需要的代码。为了跟随本文中的示例，您可以下载所有源文件的 [anarchive。在该下载中，`SampleApplication`目录包含 Visual Studio 项目的所有源代码，`silverlight`目录包含完成的 PHP 站点，包括编译的 Silverlight 应用程序。当你完成教程后，一定要去看看我们由微软赞助的](http://sitepoint-examples.s3.amazonaws.com/php-silverlight/php-silverlight.zip)[文章测试](https://www.sitepoint.com/quiz/microsoft/silverlight-php)来测试你的学习成果。

## 从 Silverlight 开始

Silverlight 应用程序使用 Visual Studio 构建代码，使用 Expression Studio 工具套件构建用户界面。ShaneMorris 在这里有一篇[很棒的文章](https://www.sitepoint.com/article/microsoft-expression-blend)详细介绍了使用 Expression Studio 来设计一个 Silverlight UI。在本文中，我们将把重点放在开发方面。准备构建 Silverlight 应用程序非常简单。首先，你需要 Visual Studio。微软有这个软件的免费速成版，所以去[Silverlight 网站](http://www.silverlight.net/getstarted/)下载网络平台安装程序。它将引导您下载 Visual Web Developer 和 Silverlight 工具。如果你已经有了 Visual Studio，你可以直接下载 Silverlight 工具。

**warning:** Versions

出于本教程的目的，示例应用程序是使用 Visual Studio 2008 和 Silverlight 3 工具构建和测试的。但是，如果你使用的是最新最好的，它应该可以很好地与 Visual Studio 2010 和 Silverlight 4 兼容。

一旦安装了 Silverlight 工具，Visual Studio 中将会提供新的选项来创建 Silverlight 应用程序。从“文件”菜单中选择“新建项目”开始。浏览到 Silverlight ，选择 Silverlight 应用。输入保存新项目的名称和位置，点击确定。

**note:** C# or VB.NET?

Silverlight 选项在新项目屏幕的 Visual C#和 VB.NET 类别中都可用。您选择哪一种将取决于您更喜欢用哪种语言编程。对于本教程中的例子，我将使用 C#代码，所以如果你想继续下去，那就是你应该选择的。

**图一。创建新的 Silverlight 应用程序**

![Creating a new Silverlight application](img/1c5daf2acb7af3504992e544b5aca0e5.png)

下一个屏幕允许您选择希望如何托管您的应用程序。出于本教程的目的，只需保留默认设置并点击确定。您将在 Visual StudioSolution Explorer 中看到两个项目——一个是 Silverlight 项目，另一个是 and one 站点。尽管我们计划将 PHP 用于服务器端代码，但我们将能够从 ASP.NET 站点获取一些生成的代码，以简化我们的工作。

**图二。解决方案资源管理器中显示的两个新项目**

![The two new projects shown in the Solution Explorer](img/0e93e81673dceb229c697a9859b56050.png)

## 创建一个简单的 Silverlight 应用程序

在我们开始将 Silverlight 与 PHP 集成之前，让我们创建一个基本的 Silverlight 应用程序来熟悉它的工作方式。从在 Silverlight 应用程序中打开`MainPage.xaml`开始。这是一个 XAML 文件，代表可扩展应用程序标记语言，也是 Silverlight(和 WPF)用户界面的构建方式。它和 HTML 很相似，所以你可以很快学会。让我们从简单的开始，在屏幕上添加一个文本框和一个按钮。将光标放在`<Grid>`元素中，输入下面的 XAML: `<StackPanel></StackPanel>`。`StackPanel`是一种灯光布局控件。它会强制你输入的控件垂直或水平堆叠。接下来，我们将在`StackPanel`中添加一个`TextBox`和一个`Button`，就像这样:

**例 1。`MainPage.xaml`(节选)**

```
﻿<Grid x:Name="LayoutRoot">  <StackPanel>    *<TextBox Text="Joe Citizen" x:Name="txtName"/>    <Button Content="Click Me!"/>*  </StackPanel></Grid>
```

按下 **F5** 将运行应用程序。您应该会看到屏幕上出现一个按钮和文本框。

**图 3。在浏览器中查看我们的示例应用程序**

![Our sample application viewed in a browser](img/6ad94f6bd12f20715332f4419cc4bab8.png)

在此阶段，单击按钮将不会有任何效果。让我们解决这个问题。通过关闭浏览器来停止应用程序。回到 Visual Studio，在 XAML 文件中找到按钮，并添加一个名为`Click`的属性。键入`Click=""`将导致 Visual Studio 用`<New EventHandler>`提示您。通过按 **回车** 选择该选项。然后，您可以右键单击添加的文本，并选择导航到事件处理程序。这将带你进入“代码背后”后面的代码是用来输入 C#(或 VB.NET)代码的区域——应用程序的核心是用代码写在后面的。让我们让应用程序在消息对话框中显示文本框中的文本。首先，将`using System.Windows.Browser;`添加到页面顶部的区域，您会看到一个`using`陈述列表:

**例二。`MainPage.xaml.cs`(节选)**

```
using System.Windows.Shapes;*using System.Windows.Browser;*namespace PHPTutorialFirst{
```

这段代码导入一个名称空间。命名空间允许非常大的。NETframework 类库被拆分成更小的、可管理的部分。这个特定的名称空间包含了我们所追求的警报功能。接下来，在`Button_Click`函数中输入以下代码以显示弹出窗口:

**例 3。`MainPage.xaml.cs`(节选)**

```
private void Button_Click(object sender, RoutedEventArgs e){  *HtmlPage.Window.Alert(txtName.Text);*}
```

这将在屏幕上显示文本框的内容。您可能还记得，在 XAML 文件中，您给`TextBlock`添加了一个名为`x:Name`的属性。这个名字是我们在代码中引用元素的方式:`txtName`。现在，您已经编写了一个基本的应用程序，您可以进一步开始使用 web 服务了。但在此之前，我们将看看如何将 Silverlight 应用程序插入到 HTML 文件或 PHPtemplates 中。

## 在页面中包含 Silverlight 应用程序

将 Silverlight 与 PHP 应用程序集成的第一步是将应用程序本身包含在 PHP 生成的页面中。有几种方法可以实例化 Silverlight。第一个选项是使用 JavaScript 动态添加新的 Silverlight 应用程序。这里我就不赘述了，不过你可以在[MSDN Silverlight . js 参考文档](http://code.msdn.microsoft.com/silverlightjs)中找到更多信息。第二种选择是使用`<object>` HTML 标签。标签是一种告诉浏览器加载外部对象的方式，比如图像或插件。像 Flash 和 Silverlight 这样的大多数插件现在都使用这个标签。为了帮助您，当您创建新的 Silverlight 项目时，Visual Studio 会为您制作一个示例页。您可以在之前创建的名为`[applicationname]TestPage.html`的 web 项目中找到它。因此，对于我们的示例应用程序，文件查询是`SampleApplicationTestPage.html`。在你的服务器的 web 根目录下为你的新应用程序创建一个新目录(在我们的例子中，我们称它为`silverlight`，并在里面创建一个新的`index.php`页面。将 Visual Studio 创建的测试页面的内容复制到该文件中，并将 web 项目中的`Silverlight.js`复制到您的新目录中。现在您有了一个 PHP 文件，其中包含一个指向 Silverlight 应用程序的`<object>`标签，以及一个附带的 JavaScript 文件，其中包含一些必要的实用程序。但是，如果您检查`object`元素，您会看到它的`src`属性是`ClientBin/SampleApplication.xap`。回头看看 Visual Studio 为您创建的 web 项目，您会发现它确实包含一个带有`SampleApplication.xap`文件的`ClientBin`目录。Silverlight 应用程序被编译成所谓的 XAP(读作“zap”)包，这个包在`<object>`标签的帮助下包含在页面中。所以你只需要将`ClientBin`目录复制到你的应用程序的目录中，你的 index.php 页面现在应该可以正确显示你的 Silverlight 应用程序了。您可以重命名或移动 XAP 文件；你只需要记得相应地调整对象的`src`。请记住，如果您修改 Silverlight 应用程序，您将需要再次复制`ClientBin`目录文件。在开发过程中，从 VisualStudio 运行 Silverlight 应用程序可能更容易，完成后将它们复制到 PHP 目录中。您的`silverlight`目录现在应该如图 4 中的[所示，“silverlightdirectory 的内容”](#fig_directory "Figure 4. The contents of the silverlight directory")，其中的`index.php`文件包含了从您的 Visual Studio web 项目中生成的`SampleApplicationTestPage.html`的内容。

**图 4。`silverlight`目录的内容**

![The contents of the silverlight directory](img/5a1d7033c8d4475267374ed01dcaf4a2.png)

## 分享这篇文章