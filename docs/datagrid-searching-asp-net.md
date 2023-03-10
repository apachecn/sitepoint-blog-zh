# 使用 ASP.NET 进行追溯数据网格搜索

> 原文：<https://www.sitepoint.com/datagrid-searching-asp-net/>

**利用。NET 的 Datagrid 来检索和显示数据是一个快速而简单的过程。NET 提供了许多特性来定制它的外观和性能。唯一不为人知的是。NET 使开发人员能够在数据网格结果中进行搜索，或者在数据中进行钻取和过滤，直到找到他们想要的东西。**

有了所有可用的搜索引擎，一次搜索往往会返回比需要的更多的结果。这就是为什么许多领先的网站，如微软，为用户提供搜索结果的能力。这使得用户不必为了在一大类结果中找到特定的东西而重复使用完全不同的标准进行搜索。

在本文中，我将向您展示如何使用 Datagrid 执行这种钻取数据搜索。。NET 提供了两种过滤数据的方法。一个是我们将在这里使用的数据集的 Dataview Rowfilter 属性。是 Datatable 的 Select 命令，但是，由于它不支持分页等功能。对于数据网格，我们不会在这里重点讨论这个解决方案。

无论这两种方法在过滤数据方面有多有效，你很快就会意识到，你无法像我们希望的那样深入搜索，因为。网络过滤器只有一次！有鉴于此，我要。NET 的标准过滤更进了一步，并演示了如何多次钻取和过滤数据。使用一些简单的逻辑和字符串连接，我们将能够每次在给定的结果集中不断地向下钻取并构建一个新的过滤器字符串，直到您找到您正在搜索的确切内容。

##### 基础知识

在我们的示例中，我将使用 SQL server 附带的 Pubs 数据库，以及一个包含两个表单的 html 页面:一个用于初始查询，另一个用于深入搜索。我们的标准。aspx。NET 页面总是包含必要的名称空间导入，这将允许我们连接我们的数据。此外，由于我们将使用数据网格，如果您需要更多信息，请尝试阅读标题为[Custom ASP.NET 数据网格分页与 4guysfromrolla.com 的精确计数](http://www.4guysfromrolla.com/webtech/082901-1.shtml)的文章。

我们代码的其余部分使用标准。NET Web 表单控件来显示我们的数据结果统计等。除了在任何页面上都可以找到的常见元素之外，我们任务的真正大部分依赖于两个关键概念:会话和字符串连接。我们将充分利用 ASP。NET 的会话状态来存储我们所有的数据结果和查询，而不是通过[隐藏字段、查询字符串、cookies 甚至。NET 的 Viewstate](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/vbcon/html/vbconChoosingServerStateOption.asp) ，这可能会带来安全问题。

所以，记住所有这些，让我们开始吧。

##### 最初的搜索

我们的。如下所示，NET 页面显示有两个搜索字段:主搜索框`<input type="text" name="search">`，它初始化我们的主搜索，以及我们的向下钻取搜索框`<input type="text" name="sub">`，用于我们所有的子搜索。

一旦用户在我们的主搜索栏中输入搜索词并提交，我们的`Page_Load`子例程将被提示确认提交。下一个。它将确定在我们使用新的结果集之前，是否有任何需要清除的现有会话。在这之后，它确保我们的数据网格被重置到第 1 页，然后最终调用我们的数据访问子例程— `BindGrid()` —来检索和显示我们的数据，并将我们的 subsearch 文本框带入视图:

```
Sub Page_Load(Source As Object, E As EventArgs) 

If Len(Request ("search")) > 0 Or Len(Request ("sub")) > 0 Then 

If Len(Request ("search")) > 0 Then Session.Clear() 

MyDataGrid.CurrentPageIndex = 0 'resets the Datagrid to page 1 

BindGrid() 

subsrch.visible = "true" 

Else 

subsrch.visible = "false" 

End if 

End Sub
```

既然我们已经讨论了该代码背后的逻辑，我们将通过一个简单的、包罗万象的“select * from all”查询来启动我们的数据网格钻取搜索，该查询将向我们返回所有可用的记录—确切地说是 43 个结果—并启用我们的子搜索文本字段，如下所示。

![1118_image1](img/f6679ad27e732e9aa3c16840183e6d2c.png)

##### 完美的数据网格

上面显示的数据网格结果是通过访问数据库获得的。然而，如果你看一下代码，你会发现这只会发生一次。每当您开始编写任何处理大量数据的应用程序时，尽量减少长时间的数据库访问总是一个好习惯。

。无论是检索数据还是页面，NET 的直接开箱即用、未增强的 Datagrid 都受到这种类型的限制，原因很简单，因为它每次都需要连续的数据库工作。正如您所想象的，这些永无止境的数据库点击并不是我们要走的路。为了显著地改进我们的应用程序，并确保它可以很好地伸缩，我们将在我们的示例中探索专门补救这一点的方法，并且我们还将讨论另一个替代方案。

我已经说过我们会充分利用。NET 的会话状态来存储我们的任何和所有数据和字符串信息，这些都将用于钻取搜索..NET 提供了两个可靠的选项来避免重复的数据库访问:前面提到的会话和[。NET 数据缓存](http://aspnet.4guysfromrolla.com/articles/100902-1.aspx)，我们一会儿将讨论它。

##### 我们的会议开始了

下面列出的`BindGrid()` 子例程负责让我们的应用程序变得可伸缩。我们首先将数据网格的数据源值`Source`设置为 Dataview 变量，因为这是数据网格的数据结果集。

接下来，我们做两件事。首先，我们检查用户是否提交了新的搜索，如果是，我们将变量 Source 分配给我们的 Session `("CachedGrid")`键。接下来，我们确定是否有一个现有的会话——如果有，我们将使用它来绑定我们的数据网格。如果没有，我们继续访问我们的数据库，然后将我们的数据结果插入到一个新的会话密钥中，并用它绑定我们的数据网格。

从这一点开始，我们将执行的任何过滤将只来自我们现有的结果集— `Session("CachedGrid")`，避免任何不必要的数据库访问，如下所示:

```
Sub BindGrid()  

Dim Source As DataView  

If Len(Request("search")) = 0 Then Source =   

CType(Session("CachedGrid"), DataView)  

If IsNothing(Source) Then  

'... Data access here   

Source = DS.Tables(0).DefaultView  

' Insert Dataset into Session  

Session ("CachedGrid") = Source  

End If  

'... other page code  

MyDataGrid.DataSource = Source 'Either from a database   

hit or unique cached Dataview  

MyDataGrid.DataBind()  

'... more page code  

End Sub
```

##### 会议和替代技术

使用会话来存储我们的数据结果非常有效，并且是安全的。我们的数据存储在服务器内存中，永远不会冗余(因为我们在一开始就清除了现有会话密钥的任何值)，并且它不会与任何其他人的数据冲突，因为创建的每个新会话都是唯一的。一旦浏览器关闭或会话超时，搜索中存储的任何信息也将随之消失。

尽管这个解决方案看起来不错，但有一点需要注意。会话可能不是存储大量信息的最佳方法，因为此过程会降低 Web 服务器的性能和资源。因此，如果你觉得这不适合你的特殊需要，你可以试试我们的第二种方法。NET 数据缓存。

正在实施。NET 的缓存对象在我们的例子中工作得非常好，并提供了明显的性能提升。然而，请记住，NET 的缓存对象是基于应用程序的，这意味着数据是在应用程序级缓存的，而不是在用户级。那么，有什么问题呢？那么，以这种方式使用缓存对象将会影响所有用户——我指的是所有的用户！

想象一下，如果您正在运行应用程序并执行您的工作，而几秒钟后另一个人正在使用相同的应用程序，会发生什么情况。第二个人将最终使用你的结果，反之亦然，因为你已经创建了一个缓存，每个人都将最终唱！您可以想象这种情况下会出现的问题，这揭示了有关此解决方案的安全性和功能性的许多问题。如前所述，会话没有这个缺点。

这就是我们在这里使用会话值的原因——每个用户都有自己唯一的会话 ID，当他们关闭浏览器或超时时，该 ID 会自动过期。另一方面，。NET 的缓存对象的明显缺点可以很容易地通过为每个用户创建一个唯一的缓存对象来解决，类似于一个唯一的会话 ID。这是如何实现的？[精确的文章。NET 服务器内容缓存](http://www.aspnextgen.com/tutorials.aspx?tutorialid=576 )展示了一种用对象创建独特区别的聪明方法，避免了任何可能出现的关于数据排他性的问题。

有了这些信息，您就可以像我们在这里用会话完成的那样，对缓存做同样的事情。此外，您可以通过创建依赖于时间的缓存来模拟会话超时。现在，让我们继续下一步研究。

##### 让我们往下钻

既然我们已经准备好了主要的数据网格结果，并且已经创建了 Dataview Session `("CachedGrid"`键值，我们就可以开始我们的钻取搜索了。在本例中，我们将首先在子搜索文本框中输入“an”作为我们的标准。当我们提交它并得到回发时，我们的`Page_Load`子程序，现在知道我们的会话密钥存在，只调用我们的`BindGrid()` sub。一旦发生这种情况，我们的数据网格将从我们存储的会话密钥中绑定自己，如上面的代码片段所示。

这就是钻取魔法发生的地方！

下面，`If Len(Request.Form("sub")) > 0 Then`“有条件”负责两件事。首先，它在第二个会话密钥`("Sub").`中分配并设置我们的 subsearch filter 命令。它还将使用会话内存中保存的字符串过滤我们缓存的 Datagrid。因此，随着每次回发的发生，我们将逐步创建一个新的 rowfilter 字符串，通过`&=`操作符将新的过滤器字符串连接到前一个。

```
If Len(Request.Form("sub")) > 0 Then  

Session ("sub") &= " and (fname LIKE '%" & Request.Form("sub") &   

"%' or lname LIKE '%" & Request.Form("sub") & "%') "  

Source.RowFilter = Session ("sub").SubString(5)  

End If
```

因此，从我们对“an”的第一个子搜索中，有 11 个结果(如下所示)从我们“缓存的”会话保存的数据网格中返回。我们的第一个 rowfilter 命令将如下所示:"`and (fname LIKE '%an%' or lname LIKE '%an%').`"

![1118_image2](img/0f182724fe4cb557965d207d42c3627c.png)

例如，在进一步的子搜索中，对于术语“man ”,我们最终得到 1 个结果，如下所示。这一次，我们的 Dataview 的 rowfilter 被分配了这个“`and (fname LIKE '%an%' or lname LIKE '%an%') and (fname LIKE '%man%' or lname LIKE '%man%'),`”，同样都是从我们的 Session `("sub")`键中获取的，这个键是通过我们的重复回发连接创建的。`Response.Write`Session`("sub")`键，当你提交你的子搜索时，你会看到连接的发生，你会明白我的意思。

![1118_image3](img/4858e582a196241c6b513490559c542e.png)

这太酷了！但是等等，不要这么快…上面列出的 filter 命令并不完全包含这样做所需的字符串语法，如果您按照所示运行它，它会产生一个错误。为什么？因为来自初始 Select 子句的 SQL 查询如果写成:"`Select * from employee where and (fname LIKE '%an%' or lname LIKE '%an%'),`"就永远不会执行，对吗？没错。这就是为什么我使用 Substring 函数来解决这个问题。

为了使 Rowfilter 正确生效，我们显然需要"`and`"所有的 filter 命令，因此它们作为一个长的 SQL 字符串附加在一起，但是没有开头的"`and.`"为了完成这个，我们需要读取前 5 个字符之后的所有内容，这就是为什么使用会话`("sub").SubString(5)`。它为我们提供了 rowfilter 命令"`(fname LIKE '%an%' or lname LIKE '%an%') and (fname LIKE '%man%' or lname LIKE '%man%')"`,该命令维护我们的钻取数据网格搜索，不会出错。

我们代码的其余部分非常简单明了，包括记录计数、隐藏或显示页面元素。

##### 代码

这是完整的 ASP.NET 页面代码。

剪切并粘贴到你最喜欢的编辑器，让它撕裂！

```
<%@ Page Language="VB" Debug="False" Explicit="True" Buffer="True"%>   

<%@ Import Namespace="System.Data" %>   

<%@ Import Namespace="System.Data.SqlClient" %>   

<html><head><title>Drilldown Datagrid Searching Within    

Results</title></head>   

<script runat="server" language="VB">   

Sub Page_Load(Source As Object, E As EventArgs)   

Response.BufferOutput = "True"   

If Len(Request("search")) > 0 Or Len(Request("sub")) > 0 Then   

If Len(Request("search")) > 0 Then Session.Clear()   

MyDataGrid.CurrentPageIndex = 0 ' resets the Datagrid to page 1   

BindGrid()   

subsrch.visible = "true"   

Else   

subsrch.visible = "false"   

End If   

End Sub   

Sub BindGrid()   

Dim Source As DataView   

If Len(Request("search")) = 0 Then Source =    

CType(Session("CachedGrid"), DataView)   

If IsNothing(Source) Then   

Dim MyConnection As SqlConnection   

Dim MyCommand As SqlDataAdapter   

Dim RcdCount As integer   

Dim DS As DataSet   

Dim sqlQuery As String = "SELECT emp_id, fname, lname FROM    

employee where (fname like '%" & Request("search") & "%'    

or lname like '%" & Request("search") & "%') Order by emp_id asc"   

Dim strConn As String = "server=(local);uid=sa;pwd=;database=pubs;"   

MyConnection = New SqlConnection(strConn)   

MyCommand = New SqlDataAdapter(sqlQuery, MyConnection)   

DS = new DataSet()   

MyCommand.Fill(DS, "MyDataGrid")   

MyDataGrid.CurrentPageIndex = 0   

Source = DS.Tables(0).DefaultView   

' Insert Dataset into Session   

Session("CachedGrid") = Source   

End If   

If Len(Request.Form("sub")) > 0 Then   

Session ("Sub") &= " and (fname LIKE '%" & Request.Form("sub")    

& "%' or lname LIKE '%" & Request.Form("sub") & "%') "   

Source.RowFilter = Session ("Sub").SubString(5)   

End If   

If (Source.Count.ToString) >= 1 Then   

MyDataGrid.visible = "true"   

Else   

msg.text = "<br><b>No records found</b>"   

subsrch.visible = "false"   

MyDataGrid.visible = "false"   

End If   

Try   

'Get count from Dataview row count, same as Datagrid row    

count   

count.text = "<b>" & Source.Count.ToString & "    

</b>results found<br>"   

MyDataGrid.DataSource = Source   

MyDataGrid.DataBind()   

Catch e As Exception   

MyDataGrid.CurrentPageIndex = 0 'reset the Datagrid back    

to page 1 on any errors   

End Try   

End Sub   

Sub MyDataGrid_Page(sender As Object, e As    

DataGridPageChangedEventArgs)   

MyDataGrid.CurrentPageIndex = e.NewPageIndex   

BindGrid()   

End Sub   

</script>   

<body bgcolor="#FFFFFF" topmargin="0" marginheight="0"    

onLoad="document.forms[0].search.focus();">   

   

<h3>Datagrid Drilldown Search Example</h3>   

<form method="post">   

<input type="text" name="search">   

<input type="submit" Value="Go">   

</form>   

<form runat="server">   

<span id="subsrch" runat="server">   

Search Within results   

<input type="text" name="sub">   

<input type="submit" Value="Go">   

</span>   

<br><asp:label id="msg" runat="server" />   

<br><asp:label id="count" runat="server" />   

<ASP:DataGrid id="MyDataGrid" runat="server"   

AllowPaging="True" PageSize="10" PagerStyle-Mode="NumericPages"    

PagerStyle-HorizontalAlign="Right"   

PagerStyle-NextPageText="Next" PagerStyle-PrevPageText="Prev"    

OnPageIndexChanged="MyDataGrid_Page"   

BorderColor="black" BorderWidth="1" GridLines="Both" CellPadding="3"    

CellSpacing="0" Font-Name="Verdana"   

Font-Size="8pt" HeaderStyle-BackColor="#FF8040"    

AlternatingItemStyle-BackColor="#eeeeee" />   

</form></body></html>
```

##### 结论

好了，这是一种很酷的过滤数据的方法，而不是使用新的标准再次运行查询，尤其是当实际的查询可能不会返回预期的结果时。这是一个简单的方法，让您的应用程序有更多的功能，并增强您的用户的最终结果。

## 分享这篇文章