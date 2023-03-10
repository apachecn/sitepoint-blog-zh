# 创造和消费。NET Web 服务的 5 个简单步骤

> 原文：<https://www.sitepoint.com/net-web-services-5-steps-3/>

## 步骤 3–构建我们的对象

好了，现在让我们调用。NET C#编译器(csc.exe)或 VB 编译器(vbc.exe)将源文件转换成程序集或 DLL。创建新的。名为 makelib.bat 的 bat 文件，并键入:

**【c#】**

```
csc /t:library /out:binGetSuppliers.dll binGetSuppliers.cs      

  /reference:System.dll,System.Data.dll,System.Web.dll,   

  System.Web.Services.dll,System.XML.dll /optimize 
```

**【VB】**

```
vbc /t:library /out:binGetSuppliers.dll binGetSuppliers.vb      

  /reference:System.dll,System.Data.dll,System.Web.dll,   

  System.Web.Services.dll,System.XML.dll /optimize    

pause
```

所有这些都是将 bin 文件夹中的源文件编译成一个具有 set name 的 DLL，再编译到 bin 文件夹中。

`/t:library`指示编译器创建一个 dll(动态链接库),而不是 exe(可执行文件),同时`/reference:`导入将在我们的 Web 服务中使用的必要的 dll 库。最后，我们`/optimize`我们的 dll 来产生更小、更快、更有效的输出。

## 第四步——把所有东西放在一起

现在将下面的代码复制并粘贴到。aspx。NET 页面并给它命名。此代码用于访问 bin 文件夹中的程序集/DLL 的页面，并使用所有 Web 服务器控件，将适当的参数传递给 Web 服务。继续运行它(http://localhost/Websrvce.aspx)。

```
<%@ Page Language="C#" Explicit="true" Strict="true" Buffer="true"%>   

<%@ Import Namespace="System.Data" %>   

<%@ Import Namespace="System.Data.SqlClient" %>   

<%@ Import Namespace="WService" %>   

<html>   

<script language="C#" runat="server">   

void Page_Load(Object sender, EventArgs e)    

{    

  Response.Flush();   

}   

void SubmitBtn_Click (object src, EventArgs e)   

{   

  int RcdCount;   

  string Catg = DropDown1.SelectedItem.Text;   

  //Instantiate DLL   

  GetInfo supInfo = new GetInfo();   

  //Pass parameter into DLL function   

  DataSet MyData = supInfo.ShowSuppliers(Catg);   

  MyDataGrid.DataSource = MyData.Tables[0].DefaultView;   

  MyDataGrid.DataBind();   

  RcdCount = MyData.Tables[0].Rows.Count;   

  if (RcdCount <= 0)    

  {   

  Message.InnerHtml = "<b>No results were found for <FONT    

                     Color=Red><i>"+ Catg +"</i></font>";   

  MyDataGrid.Visible = false; //Hide Results until needed   

  }   

  else   

  {   

    Message.InnerHtml = "<b><FONT Color=Red><i>" + Catg +   

     "</i></font> has " + RcdCount + " local suppliers</b>";   

  MyDataGrid.Visible = true;   

  }   

}   

</script>   

<body style="font: 10pt verdana">   

<h4>Accessing Data with Web Services</h4>   

<form runat="server">   

<asp:DropDownList id=DropDown1 runat="server">   

<asp:ListItem>Australia</asp:ListItem>   

<asp:ListItem>Brazil</asp:ListItem>   

<asp:ListItem>Canada</asp:ListItem>   

<asp:ListItem>Denmark</asp:ListItem>   

<asp:ListItem>Finland</asp:ListItem>   

<asp:ListItem>France</asp:ListItem>   

<asp:ListItem>Germany</asp:ListItem>   

<asp:ListItem>Italy</asp:ListItem>   

<asp:ListItem>Japan</asp:ListItem>   

<asp:ListItem>Netherlands</asp:ListItem>   

<asp:ListItem>Norway</asp:ListItem>   

<asp:ListItem>Singapore</asp:ListItem>   

<asp:ListItem>Spain</asp:ListItem>   

<asp:ListItem>Sweden</asp:ListItem>   

<asp:ListItem>UK</asp:ListItem>   

<asp:ListItem>USA</asp:ListItem>   

</asp:DropDownList>   

<asp:button text="Submit" OnClick="SubmitBtn_Click" runat=server/>   

<p>   

<span id="Message" runat="server"/>   

<p>   

<ASP:DataGrid id="MyDataGrid" runat="server"   

AutoGenerateColumns="True"   

Width="100%"   

BackColor="White"   

Border="1"   

BorderWidth="1"    

CellPadding="1"   

CellSpacing="1"   

Font-Size="10pt"   

HeaderStyle-BackColor="White"   

HeaderStyle-ForeColor="Blue"   

AlternatingItemStyle-BackColor="White"   

AlternatingItemStyle-ForeColor="Black"   

ShowFooter="false"   

/>   

</form>   

</body>   

</html>
```

## 它是如何工作的

很酷吧。现在，除了常见的数据访问和 Web 服务器控件的使用，我将概述一些细节。由于在步骤 2 中创建了带有名称空间的代理类，我们需要将它导入到我们在步骤 3 中创建的 dll 中，如下所示:

```
<%@ Import Namespace="WService" %>
```

然后我们实例化它或者引用我们的对象:
**【c#】**

`GetInfo supInfo = new GetInfo();`

**【VB】**

```
Dim supInfo As New WService.GetInfo()
```

这是我们的 Web 服务类。然后，我们检索下拉列表参数，并将其传递给 ShowSuppliers 构造函数方法，如下所示:

**【c#】**
`string Catg = DropDown1.SelectedItem.Text;  

DataSet MyData = supInfo.ShowSuppliers(Catg);`

**【VB】**

```
Dim Catg As String = DropDown1.SelectedItem.Text   

Dim MyData As DataSet = supInfo.ShowSuppliers (Catg)
```

其余的呢？很普通。网络的东西，我想你已经掌握了一些。尽管如此，这应该不难理解或理解。的。NET 文档和快速入门教程将有助于了解更多信息。嗯，你猜怎么着？我们完了！

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，比如使用 ASP.NET 的[入门网站开发。](https://learnable.com/courses/introductory-web-development-using-asp-net-125)

**Go to page:** [1](/net-web-services-5-steps) | [2](/net-web-services-5-steps-2/) | [3](/net-web-services-5-steps-3/) | [4](/net-web-services-5-steps-4/)

## 分享这篇文章