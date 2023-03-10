# 撰写您的第一个 ASP.NET 页面

> 原文：<https://www.sitepoint.com/writing-your-first-asp-net-page/>

*这是摘自我们的新书《用 C# & VB 构建你自己的 ASP.NET 4 网站》，作者是克里斯蒂安·戴瑞，怀亚特·巴尼特&蒂姆·波西。*

对于您的第一个 ASP.NET 练习，我们将创建如图 1.7 所示的简单示例。我们将一步一步地完成创建这个页面的过程。

![fig 1.7](img/f7c57fdfcba8df939ad10d853c8c6aa8.png)

Figure 1.7\. An exciting preview of your first ASP.NET page!

若要在 Visual Web Developer 中创建此页面，您需要遵循几个简单的步骤:

1.  启动 Visual Web Developer，并选择“文件”>“新建网站”(或点击默认键盘快捷键， **Shift+Alt+N** )。
2.  选择“ASP.NET 网站”作为模板，选择“文件系统”作为位置类型。此位置类型告诉 Visual Web Developer 在磁盘上的物理文件夹中创建项目，并使用集成的 Web 服务器执行该项目。
3.  选择您喜欢用来编写页面代码的语言。尽管 ASP.NET 允许你在一个项目中用不同的语言编写不同的页面，为了简单起见，我们通常假设你使用一种语言。
4.  如果您选择 C#作为语言，请键入 **C:LearningASPCS** 作为您要存储本练习文件的文件夹位置。如果你更喜欢 VB.NET，选择 **C:LearningASPVB** 。你可以选择任何你喜欢的地方。图 1.8 显示了选择的 C#版本。![Fig 1.8](img/d0152fbe1ecf8964ca519534e4958627.png)图 1.8。使用 Visual Web Developer 启动一个新的 ASP.NET 网站项目
5.  单击 OK 后，VisualWeb Developer 将创建该项目以及几个文件，以便于您过渡到 ASP.NET 开发环境。您的项目还将附带一个 Site.master 文件，该文件代表自动应用于整个站点的模板。

您的项目包含一个空的 App_Data 文件夹、一个包含 jQuery 文件的 Scripts 文件夹、包含基本 Site.css 样式表的 Styles、一个基本 Default.aspx 文件和一个基本配置文件 web . config——参见图 1.9。

我们将在第 5 章讨论所有这些文件，以及帐户目录的详细用途。现在，让我们开始创建我们的第一个 ASP.NET 网页。

![Fig 1.9](img/dc793a182e510f0cd20162ef1cfbf4db.png)

Figure 1.9\. Your new project in Visual Web Developer

您可能会注意到 HTML 源代码不同于标准 HTML。这很正常。您还应该注意到有两个内容区域，一个是“标题内容”，另一个是“主要内容”。同样，我们稍后将讨论模板和母版页，但是让我们立即开始吧。为此，我们可以覆盖提供给我们的示例文件。

Visual Web Developer 界面中的主面板是页面编辑器，您可以在其中看到 Default.aspx 网页的 HTML 源代码。编辑页面标题，使其比主页更具体，例如**欢迎建立你自己的 ASP.NET 4 网站！**:

```
<html >

    <head runat="server">

        <title>Welcome to Build Your Own ASP.NET 4 Website!

        </title>

    </head>
```

在 web 浏览器中加载 Default.aspx 页现在可以打开 VisualWeb Developer 创建项目时创建的示例页；这是有意义的，因为我们没有向这个页面添加任何内容！因为我们不关心任何示例页面，所以我们将简单地修改 default.aspx 页面的整个源代码，如下所示:

```
    <body>

        <form id="form1" runat="server">

        <div>

            <p>Hello there!</p>

            <p>

              The time is now:

              <asp:Label ID="myTimeLabel" runat="server" />

            </p>

        </div>

        </form>

    </body>

</html>
```

尽管我们的小页面还没有完成(标签控件的工作还没有结束)，让我们执行这个页面以确保我们在正确的轨道上。点击 **F5** 或进入调试菜单。

**How a Web Server Control Works**

通过向页面添加一个`<asp:Label/>`元素，您已经向页面添加了一个 Web 服务器控件。你将在第 2 章学习所有关于 Web 服务器控件的知识，但是现在你需要学习这个简单的控件是如何工作的，这样你才能理解这个练习。

标签控件是中最简单的控件之一。NET，它允许您在页面中插入动态内容。标记名的 asp:部分将其标识为内置的 ASP.NET 标记。ASP.NET 有许多内置标签，而`<asp:Label/>`可能是使用最频繁的标签之一。

`runat="server"`属性值将标签标识为需要在服务器上处理的东西。换句话说，web 浏览器将永远看不到`<asp:Label/>`标签；当客户端请求页面时，ASP.NET 会看到它，并在页面被发送到浏览器之前将其转换为常规的 HTML 标签*。由我们来编写代码，告诉 ASP.NET 用对加载页面的用户有意义的内容替换这个特殊的标签。*

第一次这样做时，VisualWeb Developer 会让您知道您的项目没有进行调试配置，它会为您对配置( **Web.config** )文件进行必要的更改—参见图 1.10。点击**确定**确认更改。

![Fig 1.10](img/de8bf0a3a178baa7399ebee640ead615.png)

Figure 1.10\. Enabling project debugging in Visual Web Developer

如果在 Internet Explorer 中没有启用脚本调试，您将看到如图 1.11 所示的对话框。勾选**不再显示该对话框**复选框，点击**是**。

![Fig 1.11](img/87f1ad01a3a38ab927bc507934c31338.png)

Figure 1.11\. Enabling script debugging in Internet Explorer

当所有的通知都被删除后，你应该有一个如图 1.12 所示的页面:

![Fig 1.12](img/0f53b229911ba215668627f10a242261.png)

Figure 1.12\. Executing your first ASP.NET web page

您现在可以关闭 Internet Explorer 窗口。Visual Web Developer 将自动检测此操作，并将取消调试模式，允许您再次开始编辑项目。现在让我们对标签控件做一些事情。

**Set Your Default Browser to Internet Explorer**When executing the project, the website is loaded in your system’s default web browser. For the purposes of developing ASP.NET applications, we recommend configuring VisualWeb Developer to use Internet Explorer, even if this is not your preferred web browser. We recommend Internet Explorer because it integrates better with Visual Web Developer’s .NET and JavaScript debugging features. For example, VisualWeb Developer knows to automatically stop debugging the project when the Internet Explorer window is closed. To change the default browser to be used by Visual Web Developer, right-click the root node in **Solution Explorer**, choose **Browse With**, select a browser from the **Browsers** tab, and click **Set as Default**.

对于我们第一个使用 ASP.NET 的动态网页，让我们编写一些代码，在标签控件中显示当前时间。这听起来可能不太令人兴奋，但这只是为了这个简单的演示；别担心，我们很快就会找到好东西的。要以编程方式操作 Label 控件，您必须编写一些 C#或 VB.NET 代码，这取决于您在创建项目时选择的语言。正如本章前面提到的，ASP.NET 允许 **web 表单**(。aspx 页面)来包含 C#或 VB.NET 代码，或者他们可以使用单独的文件——名为**代码隐藏文件**—来存储这些代码。创建项目时为您生成的 **Default.aspx** 文件是用代码隐藏文件生成的，我们现在想要编辑该文件。有许多方法可以打开那个文件。您可以点击**解决方案浏览器**窗口顶部的`View Code`图标，右键点击**解决方案浏览器**中的 **Default.aspx** 文件，选择**查看代码**，或者点击+号展开 **Default.aspx** 条目。无论您如何打开这个文件，如果您使用 C#，它应该如图 1.13 所示，如果您使用 VB.NET，它应该如图 1.14 所示。

![Fig 1.13](img/d1ddb3e3af4b30ce465e2533921cbf1e.png)

Figure 1.13\. Default.aspx.cs in Visual Web Developer**C#, VB.NET, and Visual Web Developer**You may be slightly alarmed, at first, by the fact that the code-behind file template that VisualWeb Developer generates for the Default.aspx file in a new project when you’re using C# is completely different from the one it generates when you’re using VB.NET. They’re based on the same platform and use the same data types and features, so C# and VB.NET are fundamentally very similar. However, there are still large differences between the languages’ syntax. VB.NET is frequently preferred by beginners because its syntax is perceived to be easier to read and understand than C#. While the differences can be intimidating initially, after we discuss their details in Chapter 3, you’ll see that it can be relatively easy to understand both.

![Fig 1.14](img/09923e680e094ab9515b88be66696505.png)

Figure 1.14\. Default.aspx.vb in Visual Web Developer

查看图 1.13 和图 1.14，你可以看到 C#版本包含了一个名为`Page_Load`的方法的定义，而 VB.NET 版本没有。这是项目执行时自动执行的方法，我们想用它来编写将在`Label`控件内显示当前时间的代码。

如果您正在使用 VB.NET，您将需要首先生成`Page_Load`方法。让 Visual Web Developer 为您生成`Page_Load`的最简单方法是打开 **Default.aspx** — *而不是*它的代码隐藏文件—并切换到**设计**视图(如图 1.15 所示)。如果您双击表单上的空白位置，将在代码隐藏文件中为 **Default.aspx** 创建一个空的`Page_Load`方法。

![Fig 1.15](img/7e4a5cc07d564f1afda32cd3e65659a4.png)

Figure 1.15\. Default.aspx in Design view in Visual Web Developer

现在编辑`Page_Load`方法，使其看起来像这样，选择适用于您选择的语言的版本:

Visual Basic       LearningASPVBDefault.aspx.vb *(excerpt)*Partial Class _Default
Inherits System.Web.UI.Page
Protected Sub Page_Load(ByVal sender As Object,
➥ ByVal e As System.EventArgs)
Handles Me.Load
myTimeLabel.Text = DateTime.Now.ToString()
End Sub
End ClassC#       LearningASPCSDefault.aspx.cs *(excerpt)*public partial class _Default : System.Web.UI.Page
{
protected void Page_Load(object sender, EventArgs e)
{
myTimeLabel.Text = DateTime.Now.ToString();
}
}**C# is Case Sensitive**C#, unlike VB, is case sensitive. If you type the case of a letter incorrectly, the page won’t load. If these languages look complicated, don’t worry: you’ll learn more about them in Chapter 3.

如果你以前从未做过任何服务器端编程，代码可能看起来有点吓人。但是在我们详细分析它之前，让我们加载页面并测试它是否真的工作。要查看动态生成的网页内容，点击 **F5** 再次执行项目，并查看当前日期和时间，如图 1.16 所示。

![Fig 1.16](img/5a2e08cb6faba537f5851996372d67f3.png)

Figure 1.16\. Loading Default.aspx with a Label element with dynamically generated content

页面的两个版本实现了完全相同的东西。您甚至可以保存它们，给它们一个不同的文件名，然后分别测试它们。或者，您可以创建两个 Visual Web Developer 项目——一个用于 C#代码，在 **C:LearningASPCS** 中，另一个用于 VB.NET 代码，在 **C:LearningASPVB** 中。

**No Time?**If the time isn’t displayed in the page, chances are that you opened the file directly in your browser instead of loading it through your web server. Because ASP.NET is a server-side language, your web server needs to process the file before it’s sent to your browser for display. If it doesn’t gain access to the file, the ASP.NET code is never converted into HTML that your browser can understand, so make sure you load the page by executing it in Visual Web Developer. Loading the page in your browser directly from Windows Explorer will not execute the code, and consequently the time won’t display.

那么代码是如何工作的呢？让我们来分解一下构成页面的一些元素。我们在两种语言中都定义了一个名为`Page_Load`的方法:

Visual Basic      LearningASPVBDefault.aspx.vb *(excerpt)*Protected Sub Page_Load(ByVal sender As Object,
➥ ByVal e As System.EventArgs)
Handles Me.LoadC#      LearningASPCSDefault.aspx.cs *(excerpt)*protected void Page_Load(object sender, EventArgs e)
{

这里就不赘述了。现在，您需要知道的是，您可以编写脚本片段来响应不同的事件，例如单击按钮或从下拉列表中选择项目。第一行代码的基本意思是，“每当页面被加载时，执行下面的脚本。”

请注意，C#用花括号({和})将代码分组到块中，而 Visual Basic 使用 End Sub 等语句来标记特定代码序列的结束。因此，上面 C#代码中的花括号({)标记了第一次加载页面时将执行的脚本的开始。

这是页面上实际显示时间的一行:

Visual Basic      LearningASPVBDefault.aspx.vb *(excerpt)*myTimeLabel.Text = DateTime.Now.ToString()C#      LearningASPCSDefault.aspx.cs *(excerpt)*myTimeLabel.Text = DateTime.Now.ToString();

如你所见这些。NET 语言有很多共同点，因为它们都是建立在。NET 框架。事实上，这两种语言处理上面一行代码的唯一区别是 C#用分号(；).

简单地说，这一行是这样说的:

Set the Text of myTimeLabel to the current date and time, expressed as text

请注意，`myTimeLabel`是我们给`<asp:Label/>`标签的 id 属性的值，我们希望在这里显示时间。所以，`myTimeLabel.Text`，或者说`myTimeLabel`的`Text`属性，指的是标签将要显示的文本。`DateTime`是一个内置于。NET 框架；它可以让你执行各种有用的日期和时间功能。的。NET Framework 有数千个这样的类，它们做了无数方便的事情。这些类统称为**。NET 框架类库**。

`DateTime`类有一个名为`Now`的属性，它返回当前的日期和时间。这个`Now`属性有一个名为`ToString`的方法，它将日期和时间表示为文本(在编程圈子中，一段文本被称为**字符串**)。类、属性和方法:这些都是任何程序员的词汇表中的重要词汇，我们将在本书的稍后部分更详细地讨论它们。现在，你需要从这个讨论中得到的是，`DateTime.Now.To-String()`会给你一个文本字符串形式的当前日期和时间，然后你可以告诉你的`<asp:Label/>`标签显示它。

脚本块的其余部分只是简单地连接松散的部分。VB 代码中的 End Sub 和 C#代码中的}标记了加载页面时要运行的脚本的结尾:

Visual Basic LearningASPVBDefault.aspx.vb *(excerpt)*End SubC# LearningASPCSDefault.aspx.cs *(excerpt)*}

最后一件值得研究的事情是 ASP.NET 为您生成的代码。现在很明显，您的 web 浏览器只接收 HTML(没有服务器端代码！)，那么为那个标签生成了什么样的 HTML 呢？答案很好找！当网页显示在浏览器中时，您可以使用浏览器的查看源代码功能来查看网页的 HTML 代码。在源代码的中间，您会看到类似这样的内容:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
   "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html >

    <head>

        <title>

          Welcome to Build Your Own ASP.NET 4 Web Site!

        </title>

    </head>

    <body>

        <form name="form1" method="post" action="Default.aspx" id="form1">

        <div>

          <input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="*…*" />

        </div>

        <div>

          <p>Hello there!</p>

          <p>

            The time is now:

            <span id="myTimeLabel">5/13/2008 3:10:38 PM</span>

          </p>

        </div>

        </form>

    </body>

</html>
```

注意到所有的 ASP.NET 代码都不见了吗？连`<asp:Label/&gt`；标签已经被一个包含日期和时间的`<span>`标签(它与我们使用的`<asp:Label/>`标签具有相同的 id 属性)所取代。有一个名为`__VIEWSTATE`的神秘隐藏输入元素，它被 ASP.NET 用于某些目的，但我们现在忽略它。(不要担心，我们将在书中稍后讨论它！)

这就是 ASP.NET 的工作方式。从网络浏览器的角度来看，ASP.NET 页面没有什么特别的；它只是一个普通的 HTML。所有的 ASP.NET 代码都由你的网络服务器运行，并转换成普通的 HTML 格式发送给浏览器。到目前为止，一切顺利，但是上面的例子相当简单。下一章将会更有挑战性，因为我们将研究一些有价值的编程概念。

### 获得帮助

当您开发 ASP.NET web 应用程序时，您无疑会有需要回答的问题和需要解决的难题。帮助就在眼前——微软已经开发了[ASP.NET 支持网站](http://www.asp.net/)。该门户为 ASP.NET 社区提供有用的信息，如新闻、下载、文章和论坛。你也可以在[论坛](http://forums.asp.net/)向有经验的社区成员提问。

### 摘要

在本章中，您了解了。NET，包括 ASP.NET 的好处，它的一部分。NET 框架。首先，我们介绍了 ASP.NET 的组成部分。然后，我们探索了不仅需要使用这本书，而且为了与 ASP.NET 开发的进展所需的软件。

你已经打下了坚实的 ASP.NET 基础。下一章将以这些知识为基础，开始更详细地向您介绍 ASP.NET，包括页面结构、您可以使用的语言、各种编程概念以及表单处理的细节。

*像这样？看看这本书:[用 C# & VB](../books/aspnet4/) 搭建自己的 ASP.NET 4 网站。*

## 分享这篇文章