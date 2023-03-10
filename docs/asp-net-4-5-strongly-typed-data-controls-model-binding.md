# ASP.NET 4.5 强类型数据控件和模型绑定

> 原文：<https://www.sitepoint.com/asp-net-4-5-strongly-typed-data-controls-model-binding/>

我将继续撰写关于 ASP.NET 4.5 新特性的系列文章。

一段时间以来困扰 WebForm 开发人员的一个痛点是没有任何强类型数据控件。我所说的一些数据控件包括 Repeater、FormView 和 GridView 控件。它们都使用模板，这可以让你为不同的操作指定一个视图，比如当你编辑数据和添加新数据时。

当您今天使用这些模板时，它们使用后期绑定表达式来绑定数据。如果您正在使用 GridView 控件或任何其他数据控件，您将熟悉 Bind 或 Eval 语法:

```
<asp:GridView ID="GridView1" runat="server" AutoGenerateColumns="false">
      <Columns>
           <asp:TemplateField HeaderText="Name">
                    <ItemTemplate>
                        <asp:Label ID="lblName" runat="server" Text='<%# Bind("Name") %>'></asp:Label>
                    </ItemTemplate>
                </asp:TemplateField>
                <asp:TemplateField HeaderText="City">
                     <ItemTemplate>
                        <asp:Label ID="lblCity" runat="server" Text='<%# Bind("Address.City") %>'></asp:Label>
                     </ItemTemplate>
                </asp:TemplateField>
      </Columns>
 </asp:GridView>
```

后期绑定数据控件的一个问题是使用字符串来表示属性名。如果你输入了一个错误的名字，直到运行时你才会看到这个异常。在编译时捕捉这些错误要好得多。谢天谢地，微软在 ASP.NET 4.5 中通过实现强类型数据控制解决了这个问题。

### 装置

在开始任何开发之前，你需要安装 ASP.NET 4.5。最简单的方法是通过 Web 平台安装程序。我创作的所有 ASP.NET 4.5 文章都是在 Visual Studio 2011 开发者预览版中开发的。下面是开始使用的链接。

*   [ASP.NET MVC 4 for Visual Studio 2011 开发者预览版](http://www.microsoft.com/web/gallery/install.aspx?appid=MVC4VS11&prerelease=true)

### 强类型数据控件

ASP.NET 4.5 在模板中引入了强类型数据控件。数据控件中添加了一个新的 ModelType 属性，这允许您指定绑定到控件的对象的类型。

设置该属性会将该类型添加到数据控件 Intellisense(一种自动完成功能)中，这意味着不会再出现键入错误！这样就不需要运行网站来查看你在开发过程中是否犯了任何打字错误。

在这个例子中，我连接到了一个 Northwind web 服务。使用 ASP.NET 4.5，我可以设置模型类型为北风。如果需求是单向数据绑定，可以使用*项*表达式。`Bind("Name")` 变成了`Item.Name`。城市房地产也是如此。将`Bind("Address.City")`替换为`Item.Address.City`。

```
<asp:GridView ID="GridView1" runat="server" AutoGenerateColumns="false"
            ModelType="WebApplication2.NorthwindService.Supplier">
        <Columns>
            <asp:TemplateField HeaderText="Name">
                <ItemTemplate>
                    <asp:Label ID="lblName" runat="server" Text='<%# Item.Name %>'></asp:Label>
                </ItemTemplate>
            </asp:TemplateField>
            <asp:TemplateField HeaderText="City">
                <ItemTemplate>
                    <asp:Label ID="lblCity" runat="server" Text='<%# Item.Address.City %>'></asp:Label>
                </ItemTemplate>
            </asp:TemplateField>
        </Columns>
    </asp:GridView>
```

对于双向数据绑定，使用 *Binditem* 。使用上面的例子，绑定到文本框的数据将如下所示:

```
<asp:GridView ID="GridView1" runat="server" AutoGenerateColumns="false"
            ModelType="WebApplication2.NorthwindService.Supplier">
        <Columns>
            <asp:TemplateField HeaderText="Name">
                <ItemTemplate>
                    <asp:TextBox ID="txtName" runat="server" Text='<%# Binditem.Name %>'></asp:TextBox>
                </ItemTemplate>
            </asp:TemplateField>
            <asp:TemplateField HeaderText="City">
                <ItemTemplate>
                    <asp:TextBox ID="txtCity" runat="server" Text='<%# Binditem.Address.City %>'></asp:TextBox>
                </ItemTemplate>
            </asp:TemplateField>
        </Columns>
    </asp:GridView>
```

Intellisense 是可用的，所以不会再有只在运行时才发现的错误类型的属性。

![A typographical error in code](img/21132147e217f836b5a7ee603d6028cc.png "Sheridan_001")

### 模型绑定

模型绑定关注编码的数据访问逻辑。以前，如果想在 GridView 控件中显示数据，要么必须显式设置 DataSource 属性，然后从代码中调用它的 DataBind 方法。比如这个例子:

```
protected void Page_Load(object sender, EventArgs e)
{
     var products = GetProducts();
     GridView1.DataSource = products;
     GridView1.DataBind();
}
```

或者，您可以使用众多数据源控件之一将数据绑定到 GridView。既然模型绑定是 ASP.NET 的一部分，您可以通过使用 SelectMethod 属性显式地告诉 GridView 调用哪个方法来检索其数据。这是更新后的 GridView。

```
<asp:GridView ID="GridView1" runat="server" AutoGenerateColumns="false"
            ModelType="WebApplication2.NorthwindService.Supplier"
            SelectMethod="GetProducts">
        <Columns>
            <asp:TemplateField HeaderText="Name">
                <ItemTemplate>
                    <asp:Label ID="lblName" runat="server" Text='<%# Item.Name %>'></asp:Label>
                </ItemTemplate>
            </asp:TemplateField>
            <asp:TemplateField HeaderText="City">
                <ItemTemplate>
                    <asp:Label ID="lblCity" runat="server" Text='<%# Item.Address.City %>'></asp:Label>
                </ItemTemplate>
            </asp:TemplateField>
        </Columns>
    </asp:GridView>
```

在后面的代码中，有一个 GetProducts 方法:

```
public IQueryable<NorthwindService.Supplier> GetProducts()
{
    var service = new NorthwindService.DemoService(new Uri(@"http://services.odata.org/OData/OData.svc/"));
    var suppliers = (from p in service.Suppliers
                             select p);
    return suppliers;
}
```

这个方法不需要在代码后面。它可以存在于另一个类或程序集中。返回`IQueryable`的好处是它支持查询的延迟执行，并允许数据绑定控件在执行查询之前进一步修改查询。当您需要实现排序和分页方法时，这很有用。

我对 ASP.NET 4.5 中的模型绑定和强绑定数据控件感到兴奋。它肯定从 MVC 中借鉴了这些想法和概念，所以希望在即将到来的版本中实现更多的想法和概念。

这篇文章的代码可以从[这里](http://bit.ly/zlHsPg "Github")下载。

## 分享这篇文章