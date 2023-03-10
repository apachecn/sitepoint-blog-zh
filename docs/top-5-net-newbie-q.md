# 前 5 名。网络新手问答

> 原文：<https://www.sitepoint.com/top-5-net-newbie-q/>

***感兴趣。网？不要错过* [SitePoint 的。NET 功能指南](https://www.sitepoint.com/using-special-characters-online/) *—这是一个极好的资源！***

不熟悉。网？还在决定是否冒险吗？你并不孤单。在这里，SitePoint 专家回答了关于这项技术的 5 个最常见的问题…

##### 1.的优点是什么。NET 平台？

“它比我用过的任何东西都更快、更容易、更时尚！”

这是许多. NET 拥护者的呼声——但是它实际上意味着什么呢？

***语言***

你可以编码。NET，这很好。如果你已经有了自己喜欢的语言语法，就不必为了适应. NET 而做太大的改动。

还有，。NET 包括 C#。C#是由安德斯·海尔斯伯格设计的，他是 Turbo Pascal 和 Borland Delphi 的创始人。它是 C、C++和 Java 的结合体，可以说是最好的编程语言之一。

***速度***

在我见过的所有基准测试中。与 Java 等其他解决方案相比，NET 已经非常快了。我还没有看到两者之间的比较。NET 和 PHP，但最有可能的是。除非你使用某种形式的第三方 PHP 加速软件，否则. NET 将会胜出(因为它是编译过的，并且具有极其强大的缓存能力)。

***类库***

但伟大之处在于。NET 在于一流的类库。说“类库很棒”并不能表达它有多好:

**ASP.NET**

ASP.NET 是类库的一部分，它是一个非常强大的东西。它将为您节省大量时间，并提供极其优雅的代码和内容分离。

看看这个。

```
WebForm1.aspx: 

<%@ Page language="c#" Codebehind="WebForm1.aspx.cs"  

Inherits="GuitarSite.GuitarSelectionPage" %> 

<html> 

<body> 

<head> 

</head> 

<form runat=server> 

  <select id="GuitarSelectBox" runat="server" />  

</form> 

</body> 

</html>
```

WebForm1.aspx.cs:

```
using System; 

using System.Collections; 

using System.ComponentModel; 

using System.Data; 

using System.Drawing; 

using System.Web; 

using System.Web.SessionState; 

using System.Web.UI; 

using System.Web.UI.WebControls; 

using System.Web.UI.HtmlControls; 

namespace GuitarSite 

{ 

  public class GuitarSelectionPage : System.Web.UI.Page 

  { 

    Protected HtmlSelect GuitarSelectBox; 

    void Page_Load(Object sender, EventArgs e)  

    {   

      if (!Page.IsPostBack) 

      { 

        String[] guitarMakers = {"Gibson", "Fender",  

"Paul Reed Smith", "Hamer"}; 

        GuitarSelectBox.DataSource = guitarMakers; 

        GuitarSelectBox.DataBind(); 

      } 

    } 

  }  

}
```

这将会产生这样的结果:

```
<select name="GuitarSelectBox" id="GuitarSelectBox"> 

  <option value="Gibson">Gibson</option> 

  <option value="Fender">Fender</option> 

  <option value="Paul Reed Smith">Paul Reed Smith</option> 

  <option value="Hamer">Hamer</option> 

</select>
```

我来解释代码。重要的行是这些:

```
String[] guitarMakers = {"Gibson", "Fender", "Paul Reed  

Smith", "Hamer"}; 

GuitarSelectBox.DataSource = guitarMakers; 

GuitarSelectBox.DataBind();
```

第一行创建了一个吉他数组。第二行告诉选择框从数组中提取条目。第三行使它发生。

很酷吧。更酷的是，如果您希望选择框从数据库中获取结果，您只需这样做:

```
String myConnectionString = "server=(local) 

MyMSSQLServer;database=myDataBase;User ID=Admin" 

SqlConnection myConnection =  

new SqlConnection(myConnectionString); 

SqlDataAdapter myCommand =  

new SqlDataAdapter("SELECT * FROM Guitars", myConnection); 

DataSet ds = new DataSet(); 

myCommand.Fill(ds, "Guitars"); 

GuitarSelectBox.DataSource = ds.Tables["Guitars"].DefaultView; 

GuitarSelectBox.DataBind();
```

请注意，您根本不需要更改包含 HTML 的文件。这种方法的另一个妙处是，一旦使用了 DataBind，数据就可以保持绑定状态。因此，例如，如果用户提交的表单不正确，表单重新加载(不正确的表单项旁边有红色文本和星号)，您不必重新查询数据库或再次设置数据源。代码的“`if (PageIsPostBack)`”部分确保数据不会被不必要的重载。

这是因为 ASP.NET 在页面加载之间维护所有 Web 控件(如表单项)的状态。这也意味着 ASP.NET 会自动记住在 e 出错之前用户在表单中输入了什么——你不必担心这个逻辑。ASP.NET 到处都是这样的东西，这可以节省你的时间。

**ADO.NET**

ADO.NET 是的数据层。NET，而且很不错。ADO.NET(与其前身 ADO 技术不同)是围绕一个不相连的数据架构构建的。就性能而言，数据库连接是“昂贵的”,所以 ADO.NET 只短暂地使用数据连接。它连接到数据库以获取数据和更新数据。当它没有完成这些任务时，连接就会被释放，供其他页面使用。

大多数 Web 应用程序花费大部分时间只是简单地通读数据并显示它，而 ADO.NET 在阅读和显示的同时为应用程序提供了数据的一个不相连的子集(称为数据集)。然后，只有当数据集发生变化时，ADO.NET 才会更新数据库。

考虑到所有数据库连接的打开和关闭，这种方法当然存在可伸缩性问题。ADO.NET 通过连接池来解决这个问题，也就是说，虽然你的应用程序看起来像是在创建和销毁连接，但你实际上是从 ADO.NET 为你管理的连接池中借用和返回连接。

此外，ADO.NET 提供了极其强大的数据库抽象。还记得前面 ASP.NET 部分的例子吗，有数据库和选择框？

```
String myConnectionString = "server=(local)  

MyMSSQLServer;database=myDataBase;User ID=Admin"  

SqlConnection myConnection =   

new SqlConnection(myConnectionString);  

SqlDataAdapter myCommand =   

new SqlDataAdapter("SELECT * FROM Guitars", myConnection);  

DataSet ds = new DataSet();  

myCommand.Fill(ds, "Guitars");  

GuitarSelectBox.DataSource = ds.Tables["Guitars"].DefaultView;  

DataBind();
```

现在，把数据集想象成一个桶。在这个桶中，您已经缓存了应用程序需要的所有 DB 表的版本。可以把`SqlDataAdapter`想象成一个瓶子，在里面混合了 DB 连接字符串和 SQL SELECT 命令。然后将`SqlDataAdapter` -bottle 的内容倒入 DataSet-bucket，您的应用程序就可以访问所有的表。

现在，为什么数据集和 DataAdapter 是分开的？为什么不能将 SQL 连接字符串和 SELECT 命令直接混合到 DataSet-bucket 中？因为您可以使用几种不同类型的数据适配器，如 XML 数据适配器、Oracle 数据适配器等。，以便一个数据集可以由几个不同的数据源组成。因此，即使您可能使用 4 个不同的 RDBMS 和 3 个 XML 数据源，您也可以让这个数据集成为您的应用程序进行数据访问所需的唯一对象。很酷吧。

***缓存***

。NET 会在编译类时自动缓存它们，这样在调用它们时就能以极快的速度交付它们。但是这个框架也提供了一些其他很酷的缓存类型，比如输出缓存。在这里，动态生成的网页的某些部分可以以静态形式存储在硬盘上，这样就可以直接交付，而不用每次都生成。这很容易实现，我的猫也能做到:

```
<%@ Page language="C#">  

<%@ OutputCache Duration="120" VaryByParam="*" %>  

<html>  

<!-- rest of page goes here -->
```

这将在最初运行页面后缓存页面输出 120 秒，并为表单变量查询字符串值的每个唯一组合保存一个单独的页面版本。您还可以使用 VaryByParam 属性让页面仅在您定义的某些变量发生变化时改变缓存。— [约翰逊](http://www.sitepointforums.com/member.php?s=&action=getinfo&userid=7809)

*T1。NET 还提供了跨平台的兼容性。*

仅在 Windows 上跨平台？

从术语上来说，这听起来有点矛盾，但是我参与 Visual Basic 客户机/服务器应用程序已经有几年了。我们已经设置好了服务器，开发 PC 也运行良好。当我们在大楼里四处走动，在用户的电脑上安装新应用程序时，我们遇到了各种各样的问题，其中大多数都是个人电脑特有的。

与 Java 类似。NET 平台作为基本操作系统和程序之间的一层运行。你的程序不是运行在 Windows 98 或 2000 或 XP 上，而是运行在一个。NET 平台——这使得多语言成为可能。

在理想的情况下，微软会向其他非微软操作系统开放这个平台。想想看:你可以用任何语言编写一个程序，与用任何语言编写的其他程序一起在任何计算机上运行！–[韦斯特米什](http://www.sitepointforums.com/member.php?s=&action=getinfo&userid=6394)

##### 2.那么，如果它适用于这么多种语言，那么最好学习哪一种呢？

语言选择是个人喜好的问题。下面是一篇名为“ [VB”的优秀文章。NET vs c#](http://www.aspnetpro.com/features/2002/04/asp200204cu_f/asp200204cu_f.asp via aspnetpro.com)”。

如果你觉得你的 PHP 技能相当强，你可能会想从 C#开始。它很像 C++，但没有它复杂。此外，请记住，C#与 Java 几乎完全相同。您也将认识到许多语法，因为它是基于 C 的。杰里米

如果你有 Visual Basic、VBScript 或 VBA 的基础经验(比如在 Office 中创建宏)，你可能想试试 VB.NET。— [韦斯特米什](http://www.sitepointforums.com/member.php?s=&action=getinfo&userid=6394)

或者，如果你从长远考虑你的就业能力，看看你所在地区的工作情况。了解雇主在本地和更远的求职网站上寻找什么，如果可以的话，加入一个. NET 用户组，四处打听。— [杰里米](http://www.sitepointforums.com/member.php?s=&action=getinfo&userid=6071)

不要害怕联系招聘机构，询问他们雇主最普遍需要的技能是什么，并询问更有经验的程序员他们认为市场的发展方向是什么。

##### 3.好的。所以我想进入. NET。学习它的最好方法是什么？

丰富的资源包括:

*   [。NET Home](http://www.microsoft.com/net/ )
*   [MSDN 图书馆](http://msdn.microsoft.com/library/default.asp?url=/nhp/default.asp?contentid=28000519 )
*   [Gotdotnet](http://www.gotdotnet.com/)
*   [网络瘾君子](http://www.dotnetjunkies.com)

要开始学习，请查看:

*   [The。NET 框架](http://download.microsoft.com/download/.netframesdk/Redist/1.0/W98NT42KMeXP/EN-US/dotnetredist.exe )
*   [MSDE](http://www.asp.net/msde/default.aspx)——微软 SQL server 的免费版本。除了不能在生产环境中使用之外，它的工作原理与真实的东西完全一样。
*   下载[。NET SDK](http://www.asp.net/ )——在最近的一次民意调查中，84.62%的 SitePoint。NET 开发者说离不开它！
*   不要忘记凯文·杨克的[ASP.NET](http://www.webmasterbase.com/article/781)入门，就在 SitePoint 的 [ASP 和。网络类别](http://www.webmasterbase.com/subcats/52)。

如果你喜欢印刷，看看这些书:

*   [使用 C# /VB 开始 ASP.NET。网](http://www.amazon.com/exec/obidos/tg/detail/-/1861007345/webmasterresou05 )由 Wrox 出版社。
*   如果你以前做过一些面向对象的编程，试试“[专业 ASP.NET](http://www.amazon.com/exec/obidos/ASIN/1861007035/webmasterresou05 )”
*   VB.NET 程序员，试试卡尔·摩尔的第一本书，“ [Visual Basic。NET:教程](http://www.amazon.com/exec/obidos/tg/detail/-/159059021X/webmasterresou05  His second book )”。
*   杰西·利伯蒂的《编程 ASP.NET》也不错。

##### 4.有没有我可以用的 ASP.NET 编辑器？

有！微软发布了他们自己的免费所见即所得编辑器，叫做网络矩阵项目，你可以在这里下载。

如果你想对你的代码格式有更多的控制，你可以重新配置另一个编辑器——[mtffafl](http://www.sitepointforums.com/member.php?s=&action=getinfo&userid=7735)重新配置他的 [Editpad Lite](http://www.editpadpro.com/editpadlite.html) 来自动缩进。aspx 和. aspx.cs 文件。

Macromedia 的 Dreamweaver MX 在识别和突出显示 ASP.NET 代码方面做得很好，同时还提供了实时预览。

##### 5.我能在哪里得到。网络托管？

第一站应该是你现有的网络主机——打电话或发电子邮件问他们是否支持. NET。

如果你没有主人，或者你在逛商店，[www.asp.net](http://www.asp.net)有一个广泛的清单。net 主机的资源页面上。

更多的 ASP 主机成为。全网启用，包括[www.adehost.com](http://www.adehost.com)，杰瑞米亲自推荐

也可以免费。网络托管，来自[http://www.dotnetplayground.com](http://www.dotnetplayground.com)和[http://www.brinkster.com](http://www.brinkster.com)等公司。

*本文是* [网站的一部分。NET Feature Guide](https://www.sitepoint.com/using-special-characters-online/)*——有抱负、有经验者的绝佳资源。NET 开发人员。不要错过！*

## 分享这篇文章