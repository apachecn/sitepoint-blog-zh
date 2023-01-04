# ADO.NET 简介

> 原文：<https://www.sitepoint.com/introduction-ado-net/>

***感兴趣。网？不要错过[网站。NET 功能指南](https://www.sitepoint.com/using-special-characters-online/) —这是一个极好的资源！***

早在 1996 年末，当 ADO 1.0 发布时，ASP 的出现代表了一种从数据库中动态检索数据的新方法。虽然 ADO 在当时还处于萌芽状态，是道和的分支，但它代表了一个新的大胆的方向。该技术的每一个后续版本都让我们离 ADO 2.6 更近一步，也就是开发接近尾声的时候。此时，迄今为止最具革命性的框架出现了。NET，随之而来的是非常强大和成熟的 ADO.NET。

这个新的数据组件是由。NET 提供了一种激动人心的数据访问新方法。尽管 ADO.NET 用来连接数据库的技术和逻辑与它的前身没有什么惊人的不同，但 ADO.NET 还是有很多可以提供的。这项技术的独特之处在于其背后的架构、其强大的数据管理方法以及在下一级数据呈现设备中的灵活性。

ADO 在当时当然是成功的。尽管该技术的重新发布并没有预示着 ADO 的巨大变化，但它在自己的体系结构中实现了它必须实现的目标。然而，它不断地被一些问题所困扰，这些问题围绕着它对断开连接的数据存储的处理，以及如何正确而简洁地使用 XML。这就是 ADO.NET 介入的地方——这是新技术容易管理的两件事，因为 XML 是整个。NET 框架！创建 ADO.NET 只是为了弥补 ADO 中的所有缺陷，并为开发人员提供事半功倍的能力。

在本文中，我们将深入探讨如何在？NET，并演示了 ADO.NET 开发人员可以使用的各种数据访问和表示选项。本文旨在简要介绍中的数据访问。NET，它会让你很快跟上技术的发展。

##### 名称空间

对于经典的 ASP(或其他数据库访问方法)，在开始考虑任何数据检索之前，您需要首先连接到您的数据存储。然而，在 ADO.NET 中，有两个核心对象允许开发人员最初处理数据:DataReader 和 DataSet。我们很快就会了解他们的一切。

无论如何？NET 数据访问页中，在连接到数据库之前，首先必须导入所有必需的命名空间，以便使用所需的对象。由于我们将使用 SQL Server，我们将首先导入我们需要的名称空间。中的命名空间。NET 仅仅是一种整洁有序的组织对象的方式，所以没有什么是不明确的。

```
<%@ Import Namespace="System" %> 

<%@ Import Namespace="System.Data" %> 

<%@ Import Namespace="System.Data.SqlClient" %>
```

注意:如果我们使用的是 SQL 以外的数据库，例如 MS Access，那么我们将用`OleDb`替换`SQLClient`。如果我们使用 Oracle。NET v 1.1 提供了`System.Data.OracleClient`名称空间，对于任何 ODBC 数据源，它都提供了`System.Data.Odbc` 名称空间。您将找到我们将在中讨论的所有可用方法和对象的详细信息。NET SDK 框架文档。

##### 这种联系

导入所有必需的名称空间后，我们就可以连接数据库了。现在，无论您实现 DataReader 还是 Dataset，您的初始数据库连接仍将如下所示:

```
SqlConnection objConnect = new SqlConnection (Your Connection String); 

objConnect.Open();
```

上面，我们用数据库连接信息设置了 SQLConnection 连接对象，然后打开它。下面列出了我们可以使用的常见连接对象方法:

*   **`Open`**–打开与我们数据库的连接
*   **`Close`**–关闭数据库连接
*   **`Dispose`**–释放连接对象上的资源。用于强制垃圾收集，确保连接使用后没有资源被占用。顺便提一下，通过使用 Dispose 方法，您也可以自动调用 Close 方法。
*   **`State`**–告诉你你的对象处于什么类型的连接状态，常用来检查你的连接是否还在使用任何资源。`Ex. if (ConnectionObject.State == ConnectionState.Open)`

至于打开数据库连接，这确实是它的极限。现在，我们必须决定使用哪个对象来实现您希望呈现的最终结果。我们现在必须选择是使用 Datareader 还是数据集。让我们从查看 DataReader 开始。

##### DataReader

在经典 ASP 中，当我们需要一种数据检索方法时，我们会使用适当的数据对象，并将其光标设置为手边的任务。如果我们需要快速的只进数据读取，我们可以将记录集的 CursorType 设置为 adOpenForwardOnly，将其 LockType 设置为 adLockReadOnly(通常称为“消防软管”游标)。嗯，用。NET 中，您所需要的就是 DataReader，它提供了许多特性，通过这些特性您可以进一步调整其输出效率。

***命令对象方法***

现在我们知道了 DataReader 的功能，有许多方法可以用来实现您的特定目标。以下是 DataReader 通过其命令对象使用的一些方法。这四个执行方法都与命令对象执行的操作有关，而其余的方法用于增强命令对象自身的功能。

*   **`ExecuteReader`**–简单地对数据库执行 SQL 查询，使用`Read()` 方法遍历数据，如下所示
*   **`ExecuteNonQuery`**–每当使用带参数的 SQL 存储过程时使用，如下文的存储过程部分所示
*   **`ExecuteScalar`**–从您的数据库中以对象形式返回一个快如闪电的单值`Ex. object val = Command.ExecuteScalar(); Then check if != null.`
*   **`ExecuteXmlReader`**–仅针对 SQL Server 执行 SQL 查询，同时返回一个`XmlReader`对象。看吧。NET 文档以了解更多信息
*   **`Prepare`**——相当于 ADO 的`Command.Prepared = True`属性。在缓存 SQL 命令时很有用，这样调用多次时运行速度会更快。`Ex. Command.Prepare();`
*   **`Dispose`**–释放命令对象上的资源。用于强制垃圾收集，确保连接使用后没有资源被占用。顺便提一下，通过使用`Dispose`方法，您也可以自动调用连接对象的`Close`方法。

因此，在我们建立了初始数据库连接之后，我们使用 DataReader 检索数据所需要做的就是使用 Command 对象查询打开的数据库。命令。通过命令对象使用的 ExecuteReader 方法创建我们的 DataReader。

```
SqlCommand objCommand = new SqlCommand(Sql String, objConnect);  

SqlDataReader objDataReader = objCommand.ExecuteReader();
```

如果您没有注意到，上面的连接对象显然是为 SQL Server 设置的。对于像 Access 这样的其他对象，您可以使用 OleDbConnection 对象，通过用 OleDB 替换`SqlCommand`中的 SQL 部分，即`OleDbCommand objCommand`，可以很容易地适应这种情况。这也适用于`SqlDataReader`(必须改为`OleDbDataReader`)。这种可互换性适用于。NET 框架，所以要牢记在心。

一旦我们有了 DataReader 对象，我们就在 while 循环中设置 DataReader 的`Read()` 方法，通过索引位置而不是`objDataReader["Name"].ToString()`遍历并显示我们的数据结果。我更喜欢这种方法，因为它稍微快一点，只要你能记住你的列名！

```
while (objDataReader.Read() == true) {  

Response.Write (objDataReader[0].ToString() + "<BR>");  

}
```

***DataReader 方法***

以下是 Datareader 可用的一些常用方法:

*   **`Read`**–将记录指针移动到第一行，这样可以通过列名或索引位置读取数据。可以用条件来检查数据是否存在，如果 `(DataReader.Read() = true)`
*   **`HasRows`**——新的只有用。HasRows 检查是否存在任何数据，并用于代替 Read 方法。`Ex. if (DataReader.HasRows`)。
*   **`IsClosed`**–可以判断 DataReader 是否关闭的方法。`Ex. if (DataReader.IsClosed == false)`
*   **`NextResult`**–相当于 ADO 的 NextRecordset 方法，在前进到下一组数据结果之前，用该方法执行一批 SQL 语句。与刚才列出的循环一样，您可以在多个 SQL 语句中的第一个循环之后添加`DataReader.NextResult()`，然后开始下一组的新循环。
*   **`Close`**–关闭 DataReader

毕竟，关闭、释放和清除所有连接和资源始终是良好的编程实践:

```
objDataReader.Close();  

objDataReader = null;  

objCommand.Dispose();  

objCommand = null;   

objConnect.Close();  

objConnect= null;
```

***DataReader 对象命令行为***

但这还不是全部！DataReader 对象还有一些很酷的可选命令行为，您应该尽可能地使用它们来增加应用程序的可伸缩性和提高性能。这些可以在 DataReader 对象中实现。例如:

```
objCommand.ExecuteReader(CommandBehavior.CloseConnection);   

or objCommand.ExecuteReader(CommandBehavior.SingleResult);
```

行为是:

*   **`SingleResult`**–类似于`ExecuteScalar,`返回单个值
*   **`SingleRow`**–顾名思义，返回一行
*   **`SequentialAccess`**–是一种非常高效的有序数据访问方法，有两种用途。首先，它与 DataReader 的 Get 方法一起使用，您可以使用列的序号位置从数据库中按顺序返回结果，这比使用`objDataReader["Column1"].ToString()`或`objDataReader[0].ToString()`方法更好(就速度而言)。其次，数据读取器有许多可用的 Get 方法，即`objDataReader.GetString(0)`或`objDataReader.GetInt32(0)`，允许我们绕过任何可能发生的初始数据转换。但是，非空数据必须存在才能工作，并且所有数据检索必须按顺序进行，这取决于您的 SQL 查询。
*   **`CloseConnection`** –强制 DataReader 及其连接对象在读取完数据后关闭

***已完成的 DataReader***

这是我们的 Datareader 完整代码。您可以在任何函数中运行它，或者在`Page_Load`上运行它。此外，包含以下 ADO 代码的标记对于本文中列出的所有代码示例都是通用的:

```
<%@ Page Language="C#" Debug="False" Explicit="True"%>  

<%@ Import Namespace="System.Data" %>  

<%@ Import Namespace="System.Data.SqlClient" %>  

<html>  

<head>  

<title>ADO.NET</title>  

</head>  

<body>  

<script runat="server" language="C#">  

  void Page_Load (Object Source, EventArgs E) {  

    SqlConnection objConnect = new SqlConnection   

("server=(local);uid=sa;pwd=;database=Northwind;");  

        objConnect.Open();  

    SqlCommand objCommand = new SqlCommand("Select * from   

Suppliers Order by ContactName Asc", objConnect);  

    objCommand.Prepare(); SqlDataReader objDataReader =   

objCommand.ExecuteReader CommandBehavior.CloseConnection);  

    while (objDataReader.Read() == true) {  

        Response.Write (objDataReader  

[2].ToString() + "<BR>");  

    }  

    objDataReader.Close();  

        objDataReader = null;   

        objCommand.Dispose();  

        objCommand = null;  

        objConnect.Close();  

        objConnect= null;  

  }  

</script>  

</body>  

</html>
```

在上面的示例中，我们设置了页面，并导入了允许我们处理数据的 necassary 名称空间(请记住，在 C#中，SqlClient 是区分大小写的)。接下来，我们将 datareader 代码放在连接到 SQL 数据库的服务器端标记之间，并依次显示我们的结果。在这之后，我们关闭所有的对象。

##### 存储过程

Datareader 本身和 DataSet 一样，能够更好地处理存储过程；它们提供了大量的性能优势，并且能够在一个位置整合多个操作。然而，数据集对存储过程的处理方式往往有点冗长，因为它紧密遵循数据集提供的数据操作模型。当涉及到快速和简单的存储过程处理时，DataReader 的命令对象方法已经足够了，正如我们现在将要看到的。

我们首先创建一个普通的存储过程，它接受一个参数—`@txt`,这个参数被传递到我的查询中。

```
CREATE PROCEDURE ShowSuppliers (   

@txt varchar(50)   

)   

AS   

Select CompanyName, City, Country from Suppliers Where    

Country like "%" + @txt + "%" 
```

如果你想简单地执行一个存储过程，给它传递一个值，然后读完它，你会怎么做？轻松点。在调用 ExecuteReader 方法之前，用存储过程的名称替换 SqlCommand 构造函数中的 SQL 字符串，然后向 Command 对象的 CommandType 属性指定您正在使用存储过程。完成此操作后，实现参数集合(通过 Param SqlParameter 变量)来设置希望传递给存储过程的值，如下所示:

```
SqlCommand objCommand = new SqlCommand("ShowSuppliers",   

objConnect);   

objCommand.CommandType = CommandType.StoredProcedure;   

SqlDataReader objDataReader = objCommand.ExecuteReader   

(CommandBehavior.CloseConnection);   

SqlParameter Param = objCommand.Parameters.Add("@txt",    

SqlDbType.VarChar, 50);   

Param.Value = "US";   

// ... Get Data
```

然后使用 DataReader 方法显示数据，比如上一节中列出的 while 循环代码示例，或者通过即将出现的 DataGrid 示例。

***-存储过程和替代方法***

在这个上下文中使用存储过程的另一种方法是`Command.ExecuteNonQuery()`方法。这在处理具有输入、输出和返回值的多面存储过程时非常有用。使用它们并没有那么复杂；只需实现如下所示的参数集合，即`Param.Direction = ParameterDirection.Input` 或。`OutPut or .ReturnValue;` 并敷之。`Value =`，无论值类型如何:

```
CREATE PROCEDURE ShowSuppliers (   

@txt varchar(50),   

@Name varchar (50) output,   

@Company varchar (50) output,   

@Country varchar (50) output   

)   

AS   

Select @Name = ContactName, @Company = CompanyName,    

@Country = Country from Suppliers Where Country like "%" + @txt + "%"   

GO 
```

这里我们创建了一个稍微复杂一点的存储过程来说明 ExecuteQuery 方法。如您所见，这不仅包含我们最初的搜索词变量–`@txt`,而且我们现在可以获得几个输出值。

```
// ... Database Connection / Command here like above   

SqlParameter Param = objCommand.Parameters.Add("@txt",    

SqlDbType.VarChar, 50);   

Param.Direction = ParameterDirection.Input;   

Param.Value = "US";   

Param = objCommand.Parameters.Add("@Name", SqlDbType.VarChar,50);   

Param.Direction = ParameterDirection.Output;   

Param = objCommand.Parameters.Add("@Company", SqlDbType.VarChar,50);   

Param.Direction = ParameterDirection.Output;   

Param = objCommand.Parameters.Add("@Country", SqlDbType.VarChar,50);   

Param.Direction = ParameterDirection.Output;   

objCommand.ExecuteNonQuery();   

Response.Write (objCommand.Parameters["@Name"]   

.Value.ToString() + "<BR>");   

Response.Write (objCommand.Parameters["@Company"]   

.Value.ToString() + "<BR>");   

Response.Write (objCommand.Parameters["@Country"]   

.Value.ToString() + "<BR>");
```

这里唯一不同于我们上一个存储过程例子的是，我们刚刚添加了一个新的参数方向来反映我们希望检索的值。反过来，我们回应。按照这里显示的方式写下它们。有关这一点以及使用具有返回值的存储过程的更多信息，请参见。NET 文档。

但是现在让我们假设您想要使用 datareader 在 datagrid 中显示数据，而不是使用 while 循环并通过 HTML 格式化一个表。但是在这种情况下，您不需要 DataGrid 的任何公共属性，比如分页。这将如何实现？与上一节末尾的 DataReader 示例代码类似。除了在运行 ExecuteReader()方法之后，只需将 DataGrid 的 datasource 绑定到 Datareader，如下所示。

```
MyDataGrid.DataSource = objDataReader;   

MyDataGrid.DataBind();
```

你觉得怎么样？很多很酷的方法，是吧？所有这些在技术和使用顺序上与经典 ASP 并没有太大的不同。但是很明显，你还需要更多的选项来快速显示你的数据。

除了绑定我们的数据网格，绑定其他服务器控件也很容易。例如，要绑定一个 DropDownList 控件，显然需要将 DataGrid 控件更改为 DropDownList，尽管数据源和绑定技术是相同的。唯一的区别取决于您使用的控件，因为它们都有自己独特的属性可以分配。我们如何让 DropDownList 显示我们的查询结果？就在 ExecuteReader 方法之后，指定它的`DataTextField`——要显示的值，以及`DataValueField`——选择一个项目时要传递的值。这是:

```
// ... our Data Access was here. Then we assign    

our new control's properties   

MyDropList.DataSource = objDataReader;   

MyDropList.DataTextField = "Country";   

MyDropList.DataValueField = "Country";   

MyDropList.DataBind();   

//And our control placed in our runat="server"    

form tags on our page   

<ASP:DropDownList id="MyDropList" runat="server" />
```

最终，在访问数据时，您应该始终努力利用最少的开销。在这种情况下，DataReader 显然是最佳选择。尽管如此，在下一节中，我们将探索一些更复杂的数据访问方法，您可以用它们来呈现您的数据。

##### 数据集

数据集可以被视为所有数据的内存存储容器。它允许我们在数据的操作和表示上有更多的能力，甚至扩展到 XML。在这里，DataAdapter 充当负责将我们的数据库与我们的数据集连接起来的中介对象，就像 Command 对象与 DataReader 一样。

与 DataReader 对象不同，DataAdapter/DataSet 不仅使您能够使用断开连接的数据存储区，还可以用于常见的编辑功能，如插入、删除和更新数据，甚至设置事务逻辑。

***处理数据集***

在本例中，我们将演示使用 DataAdapter 和 DataSet 时的一个常见最终结果:用于唯一数据表示的 DataGrid 服务器控件。

我们已经知道如何打开到数据库的连接。然而，当我们要处理数据集时，事件的顺序在这一点上发生了变化。还记得我们使用命令对象查询数据库吗？当处理数据集时，我们将使用 DataAdapter 进行查询，并使用 `Fill()`方法用结果填充数据集:

```
SqlDataAdapter objDataAdapter = new SqlDataAdapter ("Select     

CompanyName, ContactName, City, Country, Region from     

Suppliers", objConnect);    

DataSet objDS = new DataSet();    

objDataAdapter.Fill (objDS);
```

这是怎么回事？嗯，我们使用 SqlDataAdapter 来执行 SQL 语句，并根据此操作返回数据来填充我们的数据集。一旦我们的 DataSet 对象被创建，它就依次公开 Datatable，并在其中公开我们稍后将研究的行和列的集合。顺便提一下，如果您愿意，您可以给新创建的数据表命名，如下所示:`objDataAdapter.Fill (objDS,"MyTable");`注意:对于 OleDB 数据库，请使用`OleDbDataAdapter`。

在创建断开连接的数据存储时公开数据的最常见方法是`Fill()` 方法，我们已经提到过，稍后将演示。此时，您可能想知道为什么如此详细地研究 DataReader。这里，我们将重点放在数据访问和表示上，DataReader 更常用于这些目的。数据集有大致相同数量的方法，更适合于数据管理。我们暂时处理了它的 `Fill()` 方法，因为这使我们能够轻松地检索数据。其他数据集方法将涉及复杂的数据处理方法。我建议你参考。NET 文档，因为我们不会在这里讨论这些信息。

中的 DataGrid 控件。NET 可能是最常用的控件，因为它提供分页、过滤和排序。这就是通用数据集/数据网格关系的原因。Datareader 作为一个转发/只读对象，不能支持这些条件，除非您巧妙地对它进行编程。

***显示数据***

既然我们已经讨论了选择的数据检索方法，我们必须看看我们将如何显示它。我们可以通过将数据集绑定到数据网格来做到这一点。我们将 DataGrid 的 DataSource 属性分配给数据集，然后绑定它以查看我们的结果:

```
MyDataGrid.DataSource = objDS    

MyDataGrid.DataBind(); 
```

我们已经知道数据集本身能够保存多个数据表，每个都是一个数据表。从这一点出发，在将数据与 DataTable 对象绑定之前，您可以进一步操作数据，我们将在后面进一步说明。

此外，通过使用 DataView，我们可以在数据集的 DataTable 上创建不同的视图，这使我们能够对数据进行过滤或排序。为此，您需要将 DataGrid 的 Datasource 属性分配给 DataView，然后绑定它。

```
DataView objView = objDS.Tables[0].DefaultView;    

objView.RowFilter = "Country like '%US%'";
```

或者

```
objView.Sort = "Country asc";    

MyDataGrid.DataSource = objView;    

MyDataGrid.DataBind();
```

然后，结果将反映您已经建立的过滤或排序。例如，您可以通过从下拉列表框中提取一个值来实现这一点，该值指定了您希望过滤或排序的内容。

还要记住在你的。所有的服务器控件都放在`runat="server"`表单标签中。下面是完整的代码:

```
<script runat="server" language="C#">    

void Page_Load(Object Source,EventArgs E) {    

SqlConnection objConnect = new SqlConnection     

("server=(local);uid=sa;pwd=;database=Northwind;");     

objConnect.Open();    

SqlDataAdapter objDataAdapter = new SqlDataAdapter     

("Select CompanyName, ContactName, City, Country, Region from     

Suppliers", objConnect);    

DataSet objDS = new DataSet();    

objDataAdapter.Fill (objDS);    

MyDataGrid.DataSource = objDS;    

//DataView objView = objDS.Tables[0].DefaultView;    

//objView.RowFilter = "Country like '%US%'";    

//MyDataGrid.DataSource = objView;    

MyDataGrid.DataBind();    

objDataAdapter.Dispose();     

objDataAdapter = null;     

objDS.Dispose();    

objDS = null;    

objConnect.Close();    

objConnect = null;    

}    

</script>    

<form runat="server">    

<ASP:DataGrid id="MyDataGrid" runat="server" />    

</form> 
```

还记得在上一节中我是如何快速演示绑定到 DropDownList 的吗？同样的过程也可以应用于数据集，只是在这种情况下，它会发生在 Fill 方法执行之后(与 ExecuteReader 相反)，并且每个方法会被分配一个不同的 datasource。

在我们结束之前，让我们回顾一下我们的数据表。如果您希望对数据有更多的控制，您可以从数据集设置 DataTable，并像使用 DataReader 一样遍历它。因此，在使用了上面解释的`Fill()`方法之后，您可以像这样遍历您的数据表 DataRows:

```
DataTable dsReader = objDS.Tables[0];     

foreach(DataRow row in dsReader.Rows) {    

Response.Write (row[0] + "<BR>");    

}
```

##### 结论

总之，这篇介绍性教程应该已经激起了您的兴趣，并有希望鼓励您立即着手创建一两个应用程序。此外，请务必阅读。NET 文档和快速入门教程，以了解我们所研究的所有内容的更多信息，以及我们顺便提及的更多内容(包括使用 DataReader 读取 XML、实现事务，以及使用 DataSet 插入、编辑和更新数据)。

现在，您应该理解了 ADO.NET 中数据库访问和数据表示的简易性，并对这些对象(尤其是数据集)的未来发展方向有所了解。我希望你现在能更好地理解。NET 提供了足够多的信息来帮助您启动和运行。

下次见，开心。NETing！

## 分享这篇文章