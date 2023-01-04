# 创造和消费。NET Web 服务的 5 个简单步骤

> 原文：<https://www.sitepoint.com/net-web-services-5-steps-2/>

除了典型的数据名称空间导入之外，您还可以添加 Web 服务名称空间:

```
using System.Web.Services
```

在 VB 中，这将是:

```
Imports System.Web.Services

Here I've added the [WebService(Description="My Suppliers List Web Service")], which gives a custom description. Next we create our class, which, in order that it be exposed, and able to inherit the Webservice base class, must be a public class. 

```

```
public class GetInfo : WebService   

{
```

现在我们标记要通过`WebMethod`属性公开的方法。包含在`WebMethod`中的每个类都将作为服务公开。此外，我通过将`BufferResponse`设置为`true`来提升性能。

```
[WebMethod (BufferResponse=true)]
```

如果您喜欢将描述放在暴露的`WebMethod`链接的正下方，您可以在 C#中这样实现:

```
[WebMethod (Description="My Suppliers List Web   

Service",BufferResponse=true)]
```

VB 版本将描述和`BufferResponse`合并在一个语句中，即`WebMethod`，使用尖括号。它位于类创建和 Web 服务继承行之后，与在同一行，但在函数之前，如下所示:

```
<WebMethod(Description:="My Suppliers List Web   

Service",BufferResponse:=True)>  

Public Function   

ShowSuppliers (ByVal str As String) As DataSet
```

接下来，我们设置我们的方法或 VB 函数，它将从我们的下拉列表中接受一个字符串参数，并将其传递给我们的 SQL 查询(您将在步骤 4 中看到所有这些细节)。如果你真的很聪明，你可以在 C#结构(缩小的类)的帮助下传递这个和其他参数来枚举你的数据值类型，并为你提供更好的内存分配。但是现在，我们将坚持最基本的:

```
public DataSet ShowSuppliers (string str)
```

在这里，我们设置了 dataset 方法来返回这个值。最后，我们执行典型的数据连接和访问，并返回我们的结果，我们完成了！到目前为止还好吗？在您的浏览器中查看后，上面的代码应该开始有意义了。让我们进入第二步。

## 步骤 2–使用 Web 服务源文件

接下来，追加？WSDL 对 Web 服务 URI(统一资源标识符)这样评价:

http://localhost/suppliers . asmx？WSDL

“这是什么？”你问。这是客户端将用来访问该服务的 WSDL 文档。

即便如此，您也不必对这些不可读的代码了解太多就能产生结果，这正是 WSDL.exe 命令行工具发挥作用的地方。由于 Web 服务的开放协议特性，该工具使您能够使用非。NET Web 服务也是如此。

您可以通过在浏览器中键入以下内容，绕过 WSDL，通过 HTTP GET 协议立即测试 XML 结果:

http://localhost/suppliers . asmx/show suppliers？str =美国

这将 USA 作为参数传递给`ShowSuppliers`类方法。注意如果你使用。NET SDK Beta 1.1 (v.1.1.4322)，它似乎更倾向于 HTTP POST 协议，所以通过其暴露的方法链接来调用 Web 服务。XML 结果？有点疯狂，是吧？

尽管如此，向下滚动一点，您会看到我们的类调用的查询结果。在第 4 步的后面，所有这些官样文章将更有可读性。

因此，要创建我们的代理类源文件，需要创建一个名为 makeWS.bat 的批处理文件，并输入:

**【c#】**
`wsdl.exe /l:CS /n:WService /out:bin/GetSuppliers.cs  
 http://localhost/suppliers.asmx?WSDL`

**【VB】**

```
wsdl.exe /l:VB /n:WService /out:bin/GetSuppliers.vb    

  http://localhost/suppliers.asmx?WSDL  

pause
```

这告诉我们的 WSDL 工具`/l`是你正在使用的语言。

```
/n: creates the Wservice namespace so you can reference it from your .aspx page, and finally send the C#/VB source file to the bin folder from which we'll create the dll assembly that we'll use in Step 3\. Also add pause, so after this is all said and done, you'll be prompted to press any key to continue, and more importantly, you'll know for sure that it all went according to plan. 

现在找到新的批处理文件，双击它来运行它。一旦完成了这些，您就已经创建了代理类或源文件 GetSuppliers.cs，而不是直接从。asmx 文件。看看你的文件夹。

注意，您可以通过命令行提示符运行这些命令，但是为了方便起见，我更喜欢传统的批处理文件。你可能想知道“消费”这个词是从哪里来的？就这一点而言，它只是指将 Web 服务呈现给“消费者”。

```

**Go to page:** [1](/net-web-services-5-steps) | [2](/net-web-services-5-steps-2/) | [3](/net-web-services-5-steps-3/) | [4](/net-web-services-5-steps-4/)

## 分享这篇文章