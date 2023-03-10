# 构建可重用的数据网格，让生活更轻松

> 原文：<https://www.sitepoint.com/datagrid-life-easier/>

您还在为客户的网站使用传统的 ASP 吗？你正在为那些只想编辑他们的新闻和常见问题页面的客户编写 20 或 30 页的管理部分吗？好吧，放下你的棍子，因为我要给你一个金色的 Zippo！

如果你是自由职业者或者经营一个小的设计公司，那么你就没有理由继续使用经典的 ASP。就在大约 8 个月前，我还记得写了 8 或 10 页来完成对数据库表的添加/编辑/删除。现在我可以在一个完全可重复使用的页面中完成。我听过很多很多关于。但是我已经用 ASP 很长时间了，每当我看到它时，这种变化似乎太令人生畏了。我不记得是什么让我崩溃并陷入。但是当我看到可以实现的东西时，我知道我做出了正确的决定。在浏览了几本书之后，我已经为 6 个客户端站点建立了管理部分，每一个都比前一个功能更强大，更容易使用。

我将要教你的是 ASP.NET 数据网格。我假设你对 ASP.NET 和 VB.NET 有一个基本的了解。

##### 数据网格

通过一些初步的工作，您可以创建一个添加/编辑/删除数据网格，这将使您的生活更加轻松。想象一下，每当您的客户想要编辑一个简单的数据库表，如日历、“新功能”部分或常见问题时，您所要做的就是复制您上次创建的 datagrid，然后花 20 分钟进行修改。这是我最喜欢的 ASP.NET 部分，因为它是如此节省时间。现在你可以看到它会为你做什么。

我认为展示这一点的最佳方式是让您看到我目前使用的数据网格的演变。它开始只是编辑/删除，现在它负责我所有简单而耗时的任务。

开箱即用，datagrid 几乎可以满足您的所有需求。这里是你开始需要的。

```
Sub Page_Load(sender as object, e as eventargs) 

    If Not Page.IsPostBack Then 

        BindDataGrid() 

    End If 

End Sub 

Sub BindDataGrid() 

    Dim objConn as New OleDbConnection("Provider=Microsoft 

    .Jet.OLEDB.4.0; Data Source=c:inetpubsitessite.com 

    wwwdatabasesite.mdb") 

    objConn.Open() 

    Dim ds as Dataset = New DataSet() 

    Dim objAdapter as New OleDbDataAdapter("SELECT * FROM  

    News ORDER BY NewsDate DESC;", objConn) 

    objAdapter.Fill(ds,"News") 

    EditNews.DataSource = ds.Tables("News").DefaultView 

    EditNews.DataBind() 

    objConn.Close() 

End Sub
```

在这个例子中，我编写了一个`BindDataGrid()`函数，它将从数据库中提取数据，并将其写入 datagrid。您可能还注意到我正在使用 OleDb。您可以替换任何类型的连接，只要确保导入正确的名称空间。我还编写了在页面第一次加载时简单运行`BindDataGrid()`函数的`Page_Load`函数。

##### 定义数据网格

接下来，我将向您展示数据网格的开端。在这一部分中，您定义哪些信息将出现在哪个列中，并决定界面的外观和感觉。

```
<form runat="server"> 

        <asp:datagrid id="EditNews" runat="server" GridLines="Horizontal"  

Cellspacing="0" CellPadding="5" EditItemStyle-BackColor="#333366" 

EditItemStyle-ForeColor="white" DataKeyField="NewsID"  

OnEditCommand="DoItemEdit" OnUpdateCommand="DoItemUpdate"  

OnCancelCommand="DoItemCancel" OnDeleteCommand="DoItemDelete"  

AutoGenerateColumns="false" AlternatingItemStyle-BackColor="#CCCCCC"  

BorderColor="black"> 

            <columns> 

                <asp:boundcolumn HeaderText="Date"  

                DataField="NewsDate" ReadOnly="true"  

                HeaderStyle-BackColor="#333366"  

                HeaderStyle-ForeColor="#FFFFFF" /> 

                <asp:templatecolumn HeaderText="News Title"  

                ItemStyle-Width="200"    HeaderStyle-BackColor="#333366"  

                HeaderStyle-ForeColor="#FFFFFF"> 

                    <ItemTemplate> 

                        <asp:label Text='<%# Container.DataItem 

                        ("NewsTitle")%>' runat="server"></asp:label> 

                    </ItemTemplate> 

                    <EditItemTemplate> 

                        <asp:textbox ID="NewsTitle"  

                        TextMode="MultiLine" Rows="3" size="60"  

                        runat="server" Text='<%#  

                        Container.DataItem("NewsTitle")%>' /> 

                    </EditItemTemplate> 

                </asp:templatecolumn> 

                <asp:templatecolumn HeaderText="NewsBody"  

                ItemStyle-Width="400"  

                HeaderStyle-BackColor="#333366"  

                HeaderStyle-ForeColor="#FFFFFF"> 

                    <ItemTemplate> 

                        <asp:label Text='<%# Container.DataItem 

                        ("NewsBody")%>' 

                         runat="server"></asp:label> 

                    </ItemTemplate> 

                    <EditItemTemplate> 

                        <asp:textbox ID="NewsBody"  

                        Columns="50" TextMode="MultiLine"  

                        Rows="8" runat="server" Text='<%#  

                        Container.DataItem("NewsBody")%>' /> 

                    </EditItemTemplate> 

                </asp:templatecolumn> 

                <asp:templatecolumn HeaderText="News Author"  

                ItemStyle-Width="100" HeaderStyle-BackColor="#333366" 

                HeaderStyle-ForeColor="#FFFFFF"> 

                    <ItemTemplate> 

                        <asp:label Text='<%# Container.DataItem 

                        ("NewsWrittenBy")%>' runat="server"></asp:label> 

                    </ItemTemplate> 

                    <EditItemTemplate> 

                        <asp:textbox ID="NewsAuthor" width="100"  

                        runat="server" Text='<%# Container.DataItem 

                        ("NewsWrittenBy")%>' /> 

                    </EditItemTemplate> 

                </asp:templatecolumn> 

                <asp:templatecolumn HeaderStyle-BackColor="#333366" 

                 HeaderStyle-ForeColor="#FFFFFF"  

                 ItemStyle-Width="75"> 

                    <ItemTemplate> 

                        <asp:button CommandName="Edit"  

                        Text="Edit" runat="server" /> 

                    </ItemTemplate> 

                    <EditItemTemplate> 

                        <asp:button CommandName="Update"  

                        Text="Update" runat="server" Width="75" /> 

                        <asp:button CommandName="Delete"  

                        Text="Delete" runat="server" Width="75" /> 

                        <asp:button CommandName="Cancel" 

                        Text="Cancel" runat="server" Width="75" /> 

                    </EditItemTemplate> 

                </asp:templatecolumn> 

            </columns> 

        </asp:datagrid> 

    </form>
```

##### 代码的属性

现在这看起来像很多 HTML，但要记住的是，其中很大一部分永远不会改变。改变的部分实际上很容易操作。让我们从`<asp:datagrid>`标签开始，看看它的一些属性。首先，您必须用一个`runat="server"`将标签包含在一个`<form>`标签中，否则您的任何更新和删除功能都将不起作用。接下来是颜色、填充和边框。你可以弄清楚这些。我们主要想探讨的是功能属性。

对于您使用的每个表来说,`DataKeyField`是必须更改的。它带有你的每条记录的唯一标识符，并允许你更新和删除这些记录。下一件大事是“On Commands”。如果你看这个例子，你可以看到一个`OnUpdateCommand`、`OnCancelCommand`、`OnEditCommand`和一个`OnDeleteCommand`。它们告诉 datagrid 当有人按下按钮时运行哪个函数。这些函数将是下一个例子。然而，在此之前，我确实想提到列格式，并让您了解它是如何工作的。

每一列都有一个模板，甚至包含按钮的那一列也是如此。每个模板包含`<ItemTemplate>`和`<EditItemTemplate>`。`<ItemTemplate>`将以只读形式包含来自数据库的动态内容。在`<EditItemTemplate>`中，你必须编写所有要在`<asp:textbox>`中显示的内容。这将是人们在某一行按下编辑按钮时看到的内容。您将使用`Container.DataItem("FieldName")`在两侧显示您的动态内容:只读和编辑。现在让我们看看这些函数是什么样子的。

```
Sub DoItemEdit(objSource as Object, objArgs As DataGridCommandEventArgs)  

    EditNews.EditItemIndex = objArgs.Item.ItemIndex  

    BindDataGrid()  

End Sub  

Sub DoItemCancel(objSource as Object, objArgs As DataGridCommandEventArgs)  

    EditNews.EditItemIndex = -1  

    BindDataGrid()  

End Sub  

Sub DoItemUpdate(objSource as Object, objArgs As DataGridCommandEventArgs)  

    Dim strTitle, strBody,strWrittenBy as String  

    Dim intID as String  

    strTitle = Ctype(objArgs.Item.Cells(1).Controls(1), Textbox).text  

    strBody = Ctype(objArgs.Item.Cells(2).Controls(1), Textbox).text  

    strWrittenBy = Ctype(objArgs.Item.Cells(3).Controls(1), TextBox).text  

    'strDate =  

    intID = EditNews.DataKeys(objArgs.Item.ItemIndex)  

    Dim strSQL as String  

    strSQL = "UPDATE News SET NewsTitle='" & strTitle & _  

             "', NewsBody='" & strBody & _  

             "', NewsWrittenBy='" & strWrittenBy & _  

             "' WHERE NewsID=" & intID & ";"  

    ExecuteSQLStatement(strSQL)  

    EditNews.EditItemIndex = -1  

    BindDataGrid()  

End Sub  

Sub DoItemDelete(objSource as Object, objArgs as DataGridCommandEventArgs)  

    Dim intID as String  

    Dim strSQL as String  

    intID = EditNews.DataKeys(objArgs.Item.ItemIndex)  

    strSQL = "DELETE FROM News WHERE NewsID=" & intID & ";"  

    ExecuteSQLStatement(strSQL)  

    EditNews.EditItemIndex = -1  

    BindDataGrid()  

End Sub  

Sub ExecuteSQLStatement(strSQL as String)  

    Dim objConn as New OleDbConnection("Provider=Microsoft.Jet.OLEDB.4.0;   

    Data Source=c:inetpubsitessite.comwwwdatabasesite.mdb")  

    objConn.Open()  

    Dim objCommand as New OleDbCommand(strSQL, objConn)  

    objCommand.ExecuteNonQuery()  

    objConn.Close()  

End Sub
```

```
DoItemEdit and DoItemCancel are, as I am sure you can tell by looking at them, the easiest functions.  After all they change where the EditItemIndex is pointed.  The EditItemIndex is a number value that tells you which item is in edit mode.  If you set the EditItemIndex to -1 then none of the items are in edit mode.  As you can see in the example, DoItemEdit takes the ItemIndex and sets the EditItemIndex equal to it, and all that the DoItemCancel ever does is set the EditItemIndex equal to -1\. 现在，对于整个过程中最困难的事情(其实并不难)。当我第一次启动时，`DoItemUpdate`功能让我头疼。我开始明白很难引用数据网格内部的对象。这是通过引用其单元和控制号来实现的。它是这样工作的。`objArgs`指的是选中的行，那么`Cells`指的是从左到右从 0 开始计数的单元格的编号，而`Controls`指的是该单元格中控件的编号，可以是文本框、日历或任何类型的控件。然后，使用`Ctype`函数，这些值被转换成字符串并写入 SQL UPDATE 语句。然后我编写了`ExecuteSQLStatement`函数来获取字符串并在数据库中运行它。
接下来是`DoItemDelete`函数，这是我们的最后一个基本函数。这里需要做的就是从更新函数中使用同一个`Datakey`调用，编写一个 SQL DELETE 语句，并将其发送给`ExecuteSQLStatement`函数。

我留给你的唯一一件事就是如何完成 Add，毕竟如果我把一切都告诉你，你什么也学不到！不过，我会给你指出正确的方向。您必须编写一个向数据库添加一个空行的函数，然后让`EditItemIndex`成为该行。
现在，我知道我们刚刚做的事情看起来工作量很大。这是因为它*比*大得多，但它最大的优点是它是你在表格或视图中进行编辑所需的一切工作的极好基础。我的经验是，你每次为网站写管理部分所节省的时间远远超过了最初写这篇文章所花费的时间。仔细想想，需要更改的只是连接字符串、SQL 语句和模板列。我可以在 20 分钟内制作出一个数据网格，编辑一个简单的表格，并在规定的时间内完成。我认为这是一个成功的 Web 程序员不可或缺的工具！

```

## 分享这篇文章