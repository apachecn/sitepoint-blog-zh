# 为什么用。网？

> 原文：<https://www.sitepoint.com/why-dot-net/>

**。NET 传统上并不是 SitePoint 社区进行 Web 开发的首选框架。PHP 和。NET 论坛强调了这一事实。但是随着 SitePoint 的第一本 ASP.NET 书《T2》的发行，我认为是时候了。NETers 骄傲地站着，从屋顶上大喊到底是什么让这项技术如此之好。**

然而，本文的目的不是嘲笑其他技术；这不是[“PHP vs。净部 2"](https://www.sitepoint.com/article/php-top-10-net-myths-exposed) 。每个平台、框架和架构都有自己的优点和缺点。NET 也不例外。因此，本文中强调的特性并没有被当作是独一无二的或者在中更好的特性来讨论。网(虽然，当然大部分都是！)，但它应该给那些仍在涉足。找一些好的理由开始吧。

##### 框架

无需编写一行代码。NET 为您提供了一个可伸缩的、强大的编码框架。在我解释它的好处之前，让我们先讨论一下它到底是如何工作的。

编译. NET 应用程序时，它不会被编译成机器码。相反，。NET 应用程序被编译成 IL(中间语言)，类似于 Java 字节码。执行应用程序时，它会被编译成本机可执行代码，这些代码由公共语言运行库(CLR)管理。作为开发人员，这种管理对我们来说意味着 CLR 可以保证您的应用程序功能的某些方面，例如，垃圾收集和异常处理，赋予它内在的健壮性和安全性。这种架构被称为托管代码，为您的应用程序提供了很大的开箱即用的容错能力。

在 ASP.NET，编译是动态的。。NET 在第一次请求页面或资源时编译其输出。随后的请求使用这个编译后的输出来产生资源，从而产生速度极快的编译后的应用程序。这就是为什么当您第一次运行 ASP.NET 应用程序时，在请求被返回之前会有短暂的延迟。所以，不要担心:事情只会变得更快…快得多！

该框架还包括 Microsoft Framework 类，这是 Microsoft 有史以来发布的最大、功能最丰富的即用类集。众所周知，系统类可以被任何。NET 应用程序，这意味着你为你的网站编写的代码可以很容易地在桌面应用程序或移动设备中使用(假设你已经正确地设计了它们！).这反过来使您的开发更有成效，因为为 Web 编写可以像为桌面编写一样简单。

系统类还为执行某些任务提供了不同的方法。举个例子，假设我们需要发送一封电子邮件。我们可以使用`System.Web.Mail.SmtpMail`类来实现这一点:

```
SmtpMail.Send("FROM","TO","SUBJECT","MESSAGE BODY");
```

因为我们有一个定义的邮件服务访问点(在本例中是`SmtpMail`类)，所以对发送邮件的代码的任何未来更改或改进都是协调的，我们的应用程序将自动受益。与 ASP 开发形成对比，例如，发送电子邮件有许多不同的实现，通常使用 COM。此外，。NET 的协调增加了代码的可读性。如果我们作为一个新的开发者来看待一个系统，SmtpMail 类很容易被识别出来，它的功能也归于它。同样，与 ASP 相比，我们需要识别用于理解代码的特定 COM 对象，这提供了相当大的好处。

该框架不仅提供了编写和执行应用程序的可靠、可管理的方法，还帮助协调您的代码。对于你免费得到的东西来说还不错！

##### 面向对象的架构

一切都在。NET 是一个对象。这意味着你使用和书写的一切也是一个对象。例如，在 ASP.NET，一个网页被当作一个对象——甚至一个标签也可以被当作一个对象。当您处理属性来设置和检索信息，以及响应应用程序中发生的事件时，这为访问和控制您的 Web 应用程序提供了一种强大的方法。

让我们来看看实际情况。下面的代码在第一次加载网页时设置网页的标题，然后在用户单击按钮时更改标题:

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

注意，为了在页面第一次加载时执行代码，我们将它放在`Page_OnLoad`事件中，该事件在页面第一次被请求时被触发。在 ASP 这样的过程语言中，这段代码应该放在页面的最开始，以达到类似的效果。然而，通过使用事件，我们可以非常有效地组织我们的代码并控制它的执行。因此，当用户点击页面上的按钮控件时，按钮的`OnClick`事件被触发，我们的代码被执行。

代码还将页面的 title 标签公开为一个对象，我们可以在代码中使用它，方法是给标签添加一个`runat="server"`属性，并给它一个 ID。然后我们可以访问这个标签的属性，在本例中是`InnerHTML`来设置它的值。这使得访问和设置页面输出变得容易控制。

这种面向对象的方式也意味着我们可以以服务器控件的形式访问大量的封装代码和功能。就像在桌面开发中一样，您可以将控件拖放到页面上，设置它们的外观、位置，并编写代码以在特定事件时触发。上面定义的按钮是服务器控件的一个例子。。NET 附带了一整套强大的服务器控件，如 DataGrid，它允许对各种数据进行强大而精确的显示 Panel 控件，它允许您对页面进行分段，以根据需要隐藏或显示不同的元素组。

面向对象的所有规则也适用于控件。以同样的方式，你可以创建类的专门化，你可以继承控件的能力并添加你自己的功能。最近的 [RSS 数据列表控件](https://www.sitepoint.com/article/rss-datalist-control-asp-net)教程强调了这是多么容易实现。

##### 贮藏

对于整体性能，ASP.NET 包括一个非常强大和易于使用的缓存系统。任何对象都可以发送到缓存供以后检索，并完全控制其生命周期。例如，下面的代码从数据库中获取结果(更多信息请参见后面的数据访问一节):

```
SqlConnection conn = new SqlConnection(connectionString);  

SqlDataAdapter a = new SqlDataAdapter("select * from mytable;", conn);  

DataSet s = new DataSet();  

a.Fill(s); 我们可以缓存查询的结果(在本例中，用一个`DataSet`表示),因此后续请求不需要连接到数据库来完成请求。ASP.NET 缓存有多容易？看一看:

```
Cache["databasequery"] = s;
```

一旦一个对象存储在缓存中，我们可以用同样简单的方式再次访问它:

```
s = (DataSet)Cache["databasequery"];
```

然而，在我们访问缓存之前，重要的是我们首先检查缓存中是否有可用的东西。因此，在我们之前的代码中引入缓存只意味着增加 3 行代码:

```
DataSet s = new DataSet(); 

if (Cache["databasequery"] == null) { 

  SqlConnection conn = new SqlConnection(connectionString); 

  SqlDataAdapter a = new SqlDataAdapter("select * from mytable;", conn); 

  a.Fill(s); 

  Cache["databasequery"] = s; 

} 

else 

{ 

  s = (DataSet)Cache["databasequery"]; 

}
```

ASP.NET 也可以缓存完整的页面。通过向页面添加指令，可以通过参数(例如`http://mydomain.com/myresource.aspx?clearcache=true`)或请求者的 IP 地址来控制缓存，并且可以设置缓存的超时。
XML 配置
ASP.NET 应用程序的配置是通过使用一个`Web.config`文件来控制的。采用 XML 格式，这个文件是完全可扩展的，所以您可以有效地从代码中抽象出配置设置(比如数据库连接字符串)。这通过提供一个中央位置来存储许多资源可能需要的信息，改进了应用程序的管理和维护。
让我们来看看实际情况。下面的 XML 是完整的`Web.config`的一个片段，它指定了一个字符串，该字符串包含我们将在应用程序中使用的数据库连接字符串:

```
<?xml version="1.0" encoding="utf-8" ?> 

<configuration> 

  <appSettings> 

    <add key="ConnectionString" 

      value="server=localhost;database=test;uid=sa;pwd=password;" 

    /> 

  </appSettings> 

  <system.web> 

    <customErrors mode="Off"/> 

  </system.web> 

</configuration>
```

应该注意的是，ASP.NET 使得`Web.config`文件不能通过网络访问，这提供了一些安全性。但是，以纯文本形式在服务器上的任何地方存储连接字符串等敏感信息可能会带来安全风险。在这种情况下，您可以在将连接字符串添加到您的`Web.config`文件之前首先加密它，并在需要时解密它。
为了通过我们的代码访问我们放在`Web.config`文件中的设置，我们使用下面的调用:

```
connString = ConfigurationSettings.AppSettings("ConnectionString");
```

现在可以改变连接字符串的值，而无需更改或重新编译我们的代码。
代码分离
任何花时间编写内联脚本的人都知道，页面设计的改变会改变应用程序的整体效果。结合 ASP。NET 的网页对象模型，代码隐藏将代码从网页设计中分离出来，让你的网页设计者在不破坏代码的情况下自由地修改你的设计。
代码隐藏本身只是一个包含页面源代码的文件，它通过声明链接到网页(HTML):

```
<%@ Page language="c#" Codebehind="WebForm1.aspx.cs" Inherits="SitePoint.Webform1" %> 

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN"> 

<html> 

  <head> 

    <title>WebForm1</title> 

  </head> 

  <body> 

    <form id="Form1" method="post" runat="server"> 

    </form> 

  </body> 

</html>
```

代码隐藏文件(此处称为 WebForm1.aspx.cs)必须包含此页面继承的对象的实现，在此处定义为 SitePoint。Webform1:

```
using System; 

namespace SitePoint { 

  public class WebForm1 : System.Web.UI.Page { 

    private void Page_Load(object sender, System.EventArgs e) { 

      //code to run at page load 

    } 

  } 

}

##### 移动支持

移动设备正成为在网上获取信息的一种非常流行的方法。。NET 为您提供了从单一来源支持 200 多种不同移动设备的工具。移动互联网工具包(MIT)在分析了任何请求您的页面的设备后，会自动以不同的标记(WML、HTML 3.0)和适合于相关设备的不同形式(屏幕尺寸、卡片尺寸)打印出您的页面。

这意味着，如果我们通过 WAP 浏览器访问我们的页面，我们将得到一个 WML 输出，分页到我们手机的显示大小和网络连接。然而，如果我们通过 PDA 访问同一个页面，我们将得到一个 HTML 输出，每个页面上有更多的信息，考虑到了我们的 PDA 在手机上的额外功能和更大的屏幕尺寸。

##### 数据存取

其中一个领域。NET 的真正亮点是它的可扩展数据访问组件，即 ADO.NET。ADO.NET 为几乎所有现存的数据库提供了一个高级接口，通过 ODBC 和 OLE DB 提供通用连接，并为 SQL Server、Oracle 和 MySQL(通过第三方组件)提供专门和优化的连接。

使用 ADO.NET，所有数据都以 XML 格式传输，允许数据提供者和应用程序之间有很好的互操作性。这也允许使用与传统数据库相同的结构和访问技术将 XML 文件用于数据存储。

ADO.NET 使用断开模型，这意味着应用程序在本地处理数据，只联系服务器进行数据传输或更新。这意味着您的应用程序不再依赖于数据库的性能，通过消除瓶颈来提高应用程序的性能和可伸缩性。

这些改进带来了生产率的提高。虽然数据访问仍然不是“即插即用”的——也就是说，您仍然需要处理 SQL 语句、数据结构，并且在一定程度上处理您的数据库——但它确实引入了易于使用的数据访问方法。例如，下面的代码从正在运行的 SQL Server 上的表中提取一组行，并显示每一行中的第一个字段:

```
SqlConnection conn = new SqlConnection(connectionString);  

SqlDataAdapter a = new SqlDataAdapter("select * from mytable;", conn);  

DataSet s = new DataSet();  

a.Fill(s);  

foreach (DataRow dr in s.Tables[0].Rows)  

{  

  Console.WriteLine(dr[0].ToString());  

}
```

任何处理过传统 ADO 或其他数据访问对象的人都会注意到访问是多么干净和有条不紊。

我们还可以在任何时候使用`ReadXML`和`WriteXML`方法从我们的数据集中读取和写入 XML。因此，为了扩展前面的例子，我们现在可以将结构化 XML 格式的查询结果输出到一个文件`fileName`:

```
SqlConnection conn = new SqlConnection(connectionString);  

SqlDataAdapter a = new SqlDataAdapter("select * from mytable;", conn);  

DataSet s = new DataSet();  

a.Fill(s);  

s.WriteXml(fileName);
```

##### 语言偏好

。NET 是语言中立的。正如本文开头提到的，所有。NET 编译器解释并编译你的代码到相同的基础语言，IL(中间语言，类似于 Java 字节码)上运行。这意味着您可以愉快地使用可视化 Basic.NET，并获得与 C++等传统上更强大的语言相同的性能和功能。本质上，你可以选择你使用的语言。

和口语一样，不同的语言有不同的例子形式的信息。虽然你可以用世界语阅读和表达任何你想表达的东西，但是要阅读网上的大部分内容，你仍然需要掌握相当好的英语。这与. NET 类似，你可以选择 Eiffel.NET 作为开发应用程序的语言，但是你会发现大多数例子和参考资料都是用 C#或 NET 编码的。要将这些应用于 Eiffel，您还需要理解这些语言。

那么，有哪些选择呢？C#是微软专门为. NET 开发的一种语言。它采用了与 Java 相似的语法风格，被认为是一种干净的现代语言，并且由于它与 Java 的相似性，它是从 Java 到. NET. Visual Basic 的一座巨大桥梁。NET(VB.NET)扩展了 Visual Basic 6，在. NET 中提供了面向对象编程的能力。简而言之，任何已经熟悉 VB6 的人迁移到 VB.NET 都没有问题。

让我们来看一些示例代码:一个用 C#编写的类，以及它在 VB 中的等效代码。网络:

```
namespace sitepoint.csharp  

{  

  public class HelloWorld  

  {  

    public string sayHello(string name)  

    {  

      return "Hello "+name+"!";  

    }  

  }  

}  

Namespace sitepoint.visualbasic  

  Public Class HelloWorld  

    Function sayHello(ByVal name As String) As String  

      Return "Hello " + name + "!"  

    End Function  

  End Class  

End Namespace
```

一个单独的项目甚至可以为不同的文件使用不同的类！这种语言容忍度也意味着我们可以重用用不同语言开发的代码，而不会像过去那样令人头疼。没有编组，没有 COM，只是由. NET 提供的透明的语言互操作性。

##### 网络服务

Web 服务对于. NET 来说并不新鲜，也不独特。事实上，这就是 Web 服务的全部意义——它使用标准来实现跨平台、跨开发的代码执行。然而，这种方式。NET 简化了 Web 服务的创建，这无疑是它的一大优势。

Web 服务本质上是可以通过 HTTP 和 SOAP 访问的方法的集合。与任何方法一样，您可以拥有自定义对象返回类型，或者将自定义对象传递给该方法，这意味着您可以非常简单地分发应用程序的各个部分。

例如:下面是本文前面使用的 Hello World 示例类:

```
namespace sitepoint.csharp  

{  

  public class HelloWorld  

  {  

    public string sayHello(string name)  

    {  

      return "Hello "+name+"!";  

    }  

  }  

}
```

目前，要使用`sayHello`方法，我们必须在内部使用它。并让编译后的程序集在我们的机器上可用。

然而，通过将方法`sayHello`公开为 Web 服务，我们可以将代码托管在网络上，远离我们的机器，并且还允许其他语言的开发人员(不一定。网络语言！)来使用我们的功能。

将方法公开为 Web 方法本身就很简单。我们只需向该方法添加一个 WebMethod 属性，并从`WebService`类继承行为:

```
using System.Web.Services;  

namespace sitepoint.csharp  

{  

  public class HelloWorld: WebService  

  {  

    [WebMethod]  

    public string sayHello(string name)  

    {  

      return "Hello "+name+"!";  

    }  

  }  

}
```

在将它链接到服务页面(包含在. asmx 文件中的一行指令)之后，我们在 Web 上公开了这个方法及其功能，供所有人使用(或消费)。

当您使用 Web 服务时，您创建一个包含服务公开的方法的代理类，并为方法中使用的类型定义类。这个代理类意味着您可以将 Web 服务视为本地应用程序:它自动处理对服务的远程调用。

如果您已经在代码中确定了您认为可以从分布式中受益的功能(例如，繁重的计算任务)，这些功能可以作为服务出售给第三方，或者可能需要高级别的维护，Web 服务将有助于缓解部署难题，同时开辟新的销售途径。这是双赢的局面。

##### 这是微软的

这可能是反对的一点。如果你希望编写跨平台的软件，它肯定是。但是不管你个人对这家公司的看法如何，有一个事实是不变的:微软拥有业界独一无二的资金来创新和营销他们的产品。这意味着。NET 不会在短时间内消失，就像每一个微软开发框架一样，有大量免费的指南、电子书和参考图书馆来帮助我们开发人员。

微软下一代操作系统 Longhorn 的预览展示了如何从开发中学习技能。NET 今天将直接转移到 Longhorn development 明天。这几乎保证了。NET 技能，这在快速发展的计算机行业中通常是很难说的。

当然，有利也有弊，最主要的是. NET 缺乏全面的跨平台支持。NET 从技术上发展到 Linux(像 Mono 这样的项目证明了这一点)，没有微软的支持。因为与微软赚钱的市场 Windows 存在明显的利益冲突。虽然这可以排除。实际上，对于某些产品来说，像 Windows Server 2003 这样的产品现在消除了采用 Windows Server 的许多障碍，例如过去存在的对安全性、可靠性和性能的担忧。

##### 很便宜！

这不是一个通常可以针对微软的声明，尤其是与它的开源竞争对手相比，而是一英镑换一英镑，一美元换一美元。NET 提供了巨大的经济价值。

开始吧。NET，你所需要的只是一个装有现代版 Windows 的盒子(ASP.NET 要求 IIS 5.0 或更高版本，这意味着需要 Windows 2000 或 XP Pro 来服务 Web 应用程序)。框架本身是免费的，微软对。NET 意味着以 Web Matrix 形式出现的像样的 IDE 是可以免费获得的；然而，与大多数开发框架一样，要编写代码，您需要的只是记事本。

当然，还有强大的 Visual Studio .NET。通过要求高额的许可费，它不能被称为廉价，但它带来的生产率提高和项目组织意味着 Visual Studio。NET 可以在一个开发过程中多次支付许可费用。

正如本文的数据访问部分所提到的。NET 支持当今几乎所有可用的数据库，包括开源、免费的数据库，如 MySQL。然而，微软也有一个 MSDE 形式的免费数据库，这是他们的 SQL Server 数据库的开发版。虽然微软仍然不允许数据库的商业用途，但 MSDE 提供了一个强大的、易于管理的数据库，可以直接扩展到 SQL Server。

有关从哪里获得所需组件的更多信息，以及如何安装它们的全面指导，请参见第一章[“使用 C# & VB 构建您自己的 ASP.NET 网站”。NET"](https://www.sitepoint.com/books/aspnet1/) 。

自那以后，虚拟主机的价格也在稳步下降。NET 的发布。像 InterLand 和 1&1 这样的大型主机现在提供全功能。NET 包，价格与传统 LAMP (Linux-Apache-MySQL-PHP)配置类似。

##### 未来

微软还没有结束。网还没有。将于明年发布的 Whidbey 包含了无数的增强和新增功能，包括对象关系映射、简化的用户管理、主题化等等。请阅读[为 Whidbey](https://www.sitepoint.com/article/microsoft-whidbey-dot-net) 做准备文章，了解更多信息。

##### 尽力尝试；努力去做

我吊起你的胃口了吗？那你自己去试试吧！用 c#&VB.NET 建立你自己的 ASP.NET 网站，作为你前进道路上的友好向导，你将会产生强大的。NET Web 应用程序。

## 分享这篇文章

```

```