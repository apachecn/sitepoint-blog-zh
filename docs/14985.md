# 用 ASP.NET 缓存动态页面

> 原文：<https://www.sitepoint.com/dynamic-pages-asp-net/>

***感兴趣。网？不要错过[网站。NET 功能指南](https://www.sitepoint.com/using-special-characters-online/) —这是一个极好的资源！***

缓存是将数据保存到某种形式的内存(通常是计算机，而不是您的)中的方法，目的是加快访问该数据的时间。当您访问一个页面时，您的浏览器会将该页面缓存在您的计算机上，这样，如果您按下后退按钮，它就不必再次下载页面文件。

这并不是一个新概念——CPU 制造商已经做了很多年了——但是缓存动态网页对于开发人员来说一直是一项困难且难以管理的任务。许多人使用 ASP 中的应用程序状态或使用第三方软件来实现缓存。

***为什么要缓存？***

缓存的吸引力在于它可以提高应用程序的性能。假设一个页面一天向数据库发出 30，000 次请求。有了缓存，你可以把请求减少到一天一次，甚至一周一次！

这就是缓存的吸引力！

ASP.NET 提供了许多不同的方法来缓存信息，并控制何时发生和缓存什么。使用支持 HTTP 1.1 的机制，ASP.NET 可以在浏览器(实际上是在客户的计算机上)、代理服务器和应用程序所在的 Web 服务器上缓存页面。您可以缓存整个页面、页面的各个部分，甚至是您在代码中创建的对象(数据集、Web 服务)！它只需要一行代码！这可能是我最喜欢的 ASP.NET 特色之一！

由于 ASP 的庞大规模。NET 的缓存能力，我在这里只讨论缓存页面。

***先决条件***

我假设您对 ASP.NET 开发有基本的了解，包括对数据处理、表单验证和 SQL Server 或 MSDE 的掌握。

##### 入门指南

正如我在介绍中所说的，几乎所有的缓存都可以用一行代码来完成，那行代码是:

```
<%@ OutputCache Duration="60" VaryByParam="None" %>
```

标签中定义的两个属性是唯一需要的属性。属性`Duration`以秒为单位定义了页面将被缓存多长时间，另一个属性`VaryByParam`定义了一个查询字符串(POST 和 GET)键，该键将自动从缓存中删除页面。

就是这样！页面被缓存。虽然上面那一行是最基本的缓存页面的方法，但是即使这样也能给你的站点带来更好的性能。

`OutputCache`指令有另外 3 个属性可以控制页面的缓存:

*   `Location`
*   `VaryByCustom`
*   `VaryByHeader`

每一种都提供了不同的方法来控制缓存页面，这意味着 ASP.NET 的缓存非常灵活！

现在，让我们更详细地检查一下每个属性。

***`Duration`***

正如我已经说过的，这控制了页面被缓存的时间，前提是它没有被其他东西删除。该属性以秒为单位定义，并且是必需的。如果省略了`Duration`，它将返回一个解析错误(bad)。

***地点***

此属性定义页面缓存的位置。有 5 个选项，都可以在系统的一部分 `OutPutCacheLocation` 枚举中找到。Web.UI 命名空间。

1.  **任意**
    这是默认值。它基本上会试图将它缓存在任何它能缓存的地方(我的意思是在下面列出的某个位置，而不是在你的橱柜里！).

*   **客户端**
    该选项在客户端的浏览器中缓存页面。*   **下游**
    这将在除原始服务器之外的任何启用 HTTP 1.1 的设备中缓存页面。通常包括代理服务器或客户端。*   **服务器**
    在服务器内存中缓存页面。*   **None**
    禁用被请求页面的输出缓存，等同于去掉标签。

***VaryByParam***

此属性允许您定义通过 POST 或 GET 发送的哪些参数将从缓存中移除页面。这将覆盖`Duration`属性并从缓存中移除页面。可以通过用逗号分隔来定义多个值，并且可以使用通配符(`*`)来说明每个变化。带有一个参数的标签的例子是:

```
<%@ OutputCache Duration="30" VaryByParam="username" %> 

<html> 

   <body> 

      Page was cache at: <strong><%=dateTime.Now.toString("G")%></strong> 

   </body> 

</html>
```

每次发送不同的用户名时，页面都会从缓存中删除。尝试转到没有查询字符串的页面，记下时间。现在补充？用户名=鲍勃在网址的末尾，时间会改变。删除查询字符串并返回页面，它将显示与第一次相同的时间。而如果你等 30 秒，刷新页面，时间就不一样了！

***VaryByCustom***

为了使缓存对象更加灵活，Microsoft 内置了基于字符串缓存页面的功能，然后从应用程序的实际代码中控制该功能。

它有一个“开箱即用”的属性，浏览器。当`VaryByCustom`设置为 Browser 时，每次不同的浏览器代理和主版本号请求页面时，页面都会被缓存。

所以，如果你有一页上面有这个:

```
<%@ OutputCache Duration="60" VaryByCustom="Browser" VaryByParam="none" %> 

<html> 

   <body> 

      Page was cache at: <strong><%=dateTime.Now.toString("G")%></strong> 

   </body> 

</html>
```

只有当不同的浏览器访问该页面，或者已经过了 60 秒时，时间才会改变。

还可以通过重写 HTTPApplication 来使用自定义键和值。Global.asax 文件中的`GetVaryByCustom`方法。

好的。该是我们思考的时候了…

假设您想要缓存一个页面，该页面传递谁登录了您的站点。让我们从查看我们想要缓存的页面开始。

```
<%@ Page Language="VB" Debug="true" %>  

<%@ OutputCache Duration="300" Location="Server"   

VaryByCustom="userLogged" VaryByParam="None" %>  

<%@ import Namespace="System.Data" %>  

<%@ import Namespace="System.Data.SQLClient" %>  

<script runat="server">  

Dim pageTimeDate As DateTime  

Sub Page_Load(byVal obj As Object, byVal e As EventArgs)  

   userName.Text = Context.User.Identity.Name  

End Sub  

Sub logOut(ByVal obj As Object, ByVal e As EventArgs)  

   FormsAuthentication.SignOut()  

End Sub  

</script>  

<html>  

   <head>  

   </head>  

   <body>  

   <form runat="server">  

      Page cached: <%=DateTime.Now.toString("G")%>  

      <br />  

      Logged in as:   

      <asp:Label id="userName" runat="server">Label</asp:Label>  

      <br />  

      <asp:Button id="Button1" onclick="logOut" runat="server"   

Text="Log Out"></asp:Button>  

</form>  

</body>  

</html>
```

在第一行，我们将`OutputCache`指令的`VaryByCustom`设置为`userLogged`。使用`Page_Load`子例程，我们显示当前登录的用户(使用名为 username 的标签)并显示时间。我们还有一个注销按钮，所以我们可以轻松地以其他人的身份登录来测试脚本。

现在我们来看看设置`userLogged`字符串的脚本。

如前所述，我们必须覆盖`HTTPApplication.GetVaryByCutom`属性，我们可以在 global.asax 文件中这样做。任何广泛使用 ASP 的人都会熟悉 global.asa 文件。这个文件驻留在站点的根目录中，允许您处理应用程序级别的函数，这些函数需要在每次应用程序启动或创建新会话时触发。

对于 ASP.NET，保留了 global.asa 文件，但将其重命名为 global.asax(也称为 ASP.NET 应用程序文件)，但它本质上做的是同样的事情。这个文件是完全可选的，你可以在 MSDN 网站找到更多关于它的信息。

现在，让我们继续我们的代码…

在文件(global.asax 文件)的开头，就在`<script>`标记之后，添加以下内容:

```
Public Overrides Function GetVaryByCustomString(ByVal   

currentContext As HTTPContext, ByVal customArgs As String)   

As String  

   Select CustomArgs  

      Case "userLogged"  

         Return "userLogged=" & Context.User.Identity.Name  

      Case Else  

         Return MyBase.GetVaryByCustomString(currentContext, customArgs)  

   End Select  

End Function
```

首先我们覆盖了`GetVaryCustomString`方法(因此有了`Overrides`关键字)。它有两个参数，第一个是`HTTPContext` (包含所有关于页面请求的特定于 HTTP 的信息)，另一个是一个表示`VaryByCustom`属性值的字符串。由于我们的`VaryByCustom`属性是用户登录的，当页面被请求时，它将作为参数传递给我们的函数。我们使用 Select 语句来检查 customtArgs 参数是什么，因此，我们可以很容易地添加其他值，这意味着我们可以以多种不同的方式缓存页面。

如果字符串已经被缓存，这被认为是“命中”，并且页面从缓存中被提供。如果字符串没有被缓存，那么这被认为是“未命中”，并被缓存。

这就是全部了。

***VaryByHeader***

每次你请求一个网页，你的浏览器都会发送一些 HTTP 头。你可以根据这些标题来定制缓存，例如，你可以缓存像 Referer 或 Accept-Language 这样的页面。你可以在这里找到 HTTP 头列表。你会这样使用它:

```
<%@ OutputCache Duration="30" VaryByParam="none" VaryByHeader="Referer" %>
```

与我们到目前为止所了解的大多数信息一样，这是在 OutputCache 标记中定义的一个简单属性！这个简单的例子将基于 Referer 缓存页面 30 秒。

##### 收尾工作

正如我们刚刚看到的，用 ASP.NET 缓存网页非常容易，只用一行简单的代码就完成了。有了 ASP 的这一瞥。NET 的缓存功能，您可以提高您的 Web 应用程序的性能。但是请记住:我们在本文中讨论的只是整个缓存名称空间的一小部分！尝试其他一些对象，不断尝试，很快您的应用程序就会工作得更快、更有效，并且服务器负载更轻！

*本文是 [SitePoint 的一部分。NET 功能指南](https://www.sitepoint.com/using-special-characters-online/) —对于有抱负和有经验的人来说是一个极好的资源。NET 开发人员。不要错过！*

## 分享这篇文章