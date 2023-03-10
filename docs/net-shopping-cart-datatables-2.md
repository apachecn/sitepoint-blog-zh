# 使用数据表构建 ASP.NET 购物车

> 原文：<https://www.sitepoint.com/net-shopping-cart-datatables-2/>

##### 步骤 3:构建添加到购物车功能

既然已经完全构建了 DataTable 的结构，我们将开始向购物车添加用户从下拉菜单中选择的特定商品。同样，要做到这一点，我们只需构造新的行，并将它们添加到`DataTable` 购物车中的适当位置。要构建添加到购物车功能，请添加以下代码:

```
Sub AddToCart(s As Object, e As EventArgs)  

     objDT = Session("Cart")  

     Dim Product = ddlProducts.SelectedItem.Text  

     objDR = objDT.NewRow  

     objDR("Quantity") = txtQuantity.Text  

     objDR("Product") = ddlProducts.SelectedItem.Text  

     objDR("Cost") = Decimal.Parse(ddlProducts.SelectedItem.Value)  

     objDT.Rows.Add(objDR)  

     Session("Cart") = objDT  

     dg.DataSource = objDT  

     dg.DataBind()  

End Sub
```

您会记得按钮控件有一个调用`AddToCart()`子例程的`onClick`事件。该子例程包含从 `Session`对象中检索现有购物车(如果存在)所需的所有代码，向购物车添加新商品，将购物车放回会话中，最后绑定到数据网格。

正如我提到的，前两行代码从会话中检索购物车(如果存在),并检索从下拉菜单中选择的产品。因为已经创建了一个新的`DataRow`类实例，所以可以为数据表中的特定列创建新行。

您会注意到我们将`Quantity`列设置为 quantity 文本框控件的值，并将 `Product`列设置为下拉菜单选项的文本值。然后，我们将下拉菜单的值(成本)转换为成本列的十进制值。最后，我们将新行添加到 DataTable，将 DataTable 添加到会话，或者覆盖它(如果它已经存在)，并将 DataTable 绑定到 DataGrid。

在浏览器中测试您目前拥有的内容。它应该工作正常，除了一个问题。您将看到，如果您选择一个商品，将其添加到购物车，然后再次选择同一商品，而不是将旧数量与新数量相加，它只是创建一个新行。您可以通过修改`AddToCart()` 子程序来解决这个问题。就在从下拉菜单中检索项目的代码下方，添加以下循环以在数据表中检查现有产品:

```
Dim blnMatch As Boolean = False  

For Each objDR In objDT.Rows  

     If objDR("Product") = Product Then  

          objDR("Quantity") += txtQuantity.Text  

          blnMatch = True  

          Exit For  

     End If  

Next
```

现在包装在条件语句中添加新行的新代码。

```
If Not blnMatch Then  

     objDR = objDT.NewRow  

     objDR("Quantity") = Int32.Parse(txtQuantity.Text)  

     objDR("Product") = ddlProducts.SelectedItem.Text  

     objDR("Cost") = Decimal.Parse(ddlProducts.SelectedItem.Value)  

     objDT.Rows.Add(objDR)  

End If
```

基本上，只有在购物车中找不到产品时，才会创建一个新行。但是，如果找到了，它将简单地通过用户在 quantity textbox 控件中放置的内容来调整数量。

##### 第四步:保持订单总额

下一步将是构建保存购物车中商品的总成本的函数。这将用于在用户添加和删除购物车中的商品时向用户提供工作总数。您可以使用下面的代码添加此功能:

```
Function GetItemTotal() As Decimal  

     Dim intCounter As Integer  

     Dim decRunningTotal As Decimal  

     For intCounter = 0 To objDT.Rows.Count -- 1  

          objDR = objDT.Rows(intCounter)  

          decRunningTotal += (objDR("Cost") * objDR("Quantity"))  

     Next  

     Return decRunningTotal   

End Function
```

您可以看到，该函数简单地遍历数据表中的行，将数量列乘以成本列，然后返回结果。第一步包括定义函数。当您想要返回一个十进制值时，请确保将函数定义为十进制。接下来，我们将创建两个新变量:一个是整数，一个是小数。接下来，遍历数据表中的行，将特定行的 cost 列乘以该行的数量，并将其存储在`decRunningTotal`变量中。最后，我们将返回值。

最后一步是将函数的值写入标签控件。将下面一行添加到`btnAddToCart`子例程的末尾——这有效地将成本写入了标签控件:

```
lblTotal.Text = "$" & GetItemTotal()
```

保存您的工作并在浏览器中运行。这一次，当您向购物车中添加商品时，标签控件中会显示一个总数。太棒了。现在，我们将讨论的最后一部分涉及从购物车中移除商品。

##### 步骤 5:从购物车中移除商品

既然已经构建了购物车的工作模型，我们将添加下一个功能:从购物车中移除商品。显然，您可以看到这一点的重要性:您希望用户能够根据需要在购物车中添加和删除任何和所有商品。使用以下子例程添加从购物车中删除商品的功能:

```
Sub Delete_Item(s As Object, e As DataGridCommandEventArgs)  

     objDT = Session("Cart")  

     objDT.Rows(e.Item.ItemIndex).Delete()  

     Session("Cart") = objDT  

     dg.DataSource = objDT  

     dg.DataBind()  

     lblTotal.Text = "$" & GetItemTotal()  

End Sub
```

这个子例程(我们稍后将与 DataGrid 关联)只是将会话的内容转储到一个新的`DataTable`对象中，删除用户单击的特定行，然后将修改后的数据表放回`Session`变量中进行存储。最后，我们重新绑定到数据网格，并通过调用`GetItemTotal()` 函数来更新总数。

您可能会注意到传递给子例程的参数之一是 `DataGridCommandEventArgs`的参数。如果没有这个参数，我们将无法确定用户选择了 DataGrid 中的哪一项。

最后一步是修改数据网格。您将需要添加一个新的`button`列，以及一个新的事件，以调用`Delete_Item` 子例程。新的数据网格应类似于以下代码:

```
<asp:DataGrid id=dg runat="server"  ondeletecommand="Delete_Item">  

<columns>  

<asp:buttoncolumn buttontype="LinkButton"   

commandname="Delete" text="Remove Item" />  

</columns>  

</asp:DataGrid>
```

保存您的工作并在浏览器中运行。这次你可以自由添加和删除产品了！

##### 摘要

如您所见，构建自己的购物车并不困难。构建自己的购物车的好处在于它是完全可定制的——而且您不必花费数百美元购买一个扩展套件，而您最终无论如何都必须学习该套件。

**Go to page:** [1](/net-shopping-cart-datatables) | [2](/net-shopping-cart-datatables-2/)

## 分享这篇文章