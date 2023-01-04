# 创造和消费。NET Web 服务的 5 个简单步骤

> 原文：<https://www.sitepoint.com/net-web-services-5-steps/>

这篇文章写于 2002 年，至今仍是我们最受欢迎的帖子之一。如果你想了解更多。NET，你可能会对这篇[最近关于少佐与 ASP.NET](https://www.sitepoint.com/combining-less-with-asp-net/)的文章很感兴趣。

互联网出现了一个新玩家。它被大量的炒作所包围，甚至还有一些电视广告！显然，这种新的“下一代技术”将改变网上交易的方式。似乎很快，公司、其应用程序或软件以及任何支持互联网的设备将能够轻松地相互通信并提供服务，而不管平台或语言如何。听起来是革命性的！

那么，到底是什么打开了这些无限的交流渠道呢？Web 服务，就是这样！

Web 服务使开发人员能够利用四种开放的 Web 标准:

1.  **HTTP**–超文本传输协议
    80 端口上使用的标准协议，它穿越防火墙，负责通过互联网请求和传输数据。
2.  简单对象访问协议(Simple Object Access Protocol)
    一种 XML 固有的协议，包含一组数据描述和处理规则。作为一个标准，这是补充这里提到的其他三个标准的核心部分。
3.  XML(可扩展标记语言)
    ,这是编写所有信息的最常用的标记语言。

5.  **WSDL**–Web 服务描述语言
    一种基于 XML 的方法，用于在运行时识别 Web 服务及其访问。。NET 提供了一个叫做 WSDL.exe 的工具，这使得以 XML 文件的形式生成 XML Web 服务变得非常容易。这包含了 Web 服务拥有的所有方法和指令，通常使用 SOAP 作为缺省值。

本文将介绍如何创建和使用数据驱动的。NET XML Web 服务只需 5 个简单快捷的步骤！

我会假设你对共同语言有相当的理解。NET 数据访问、Web 服务器控件(如 datagrid)和一些面向对象的编程概念。如果没有，也不用太担心。如果您完成了这些示例，并查看了结果，那么您应该不难跟上并观察到本教程所要求的因果关系。

之前。NET 之外，还有其他方法可以用来访问 Web 服务，比如微软的 MSXML 组件，它使您能够通过 HTTP POST 与给定的 Web 服务进行通信。然而，尽管这个过程是可以接受的，但它并不是. NET。

好，我们开始吧！

## 步骤 1–创建 Web 服务

首先，我们将创建 Web 服务函数或方法，您可以像调用任何面向对象的类一样通过互联网调用(或“公开”)。不同之处在于，在这种情况下，我们必须合并和导入所有必要的 Web 服务名称空间、语法和属性，以及我们的数据名称空间。由于本文使用的是 C#，所以关于 VB 的任何重要区别也会显示出来。

因此，请将下面的代码复制到一个名为 suppliers.asmx 的文件中。将其保存到 Inetpub/wwwroot 文件夹中，然后使用 http://localhost/suppliers . asmx 在浏览器中运行它。您将看到一个 Web 服务描述列表，包括 Web 服务名称和公开的方法。

通过单击 exposed method 链接，您将看到可供您使用的三个主要协议。如你所知那个带有。asmx 扩展是实际的 Web 服务文件，它使 ASP.NET 运行时能够返回所有相关的公开的 Web 服务方法和信息。

```
<%@ WebService Language="C#" Class="GetInfo" %> 

using System; 

using System.Data; 

using System.Data.SqlClient; 

using System.Web.Services; 

[WebService(Description="My Suppliers List Web Service")] 

public class GetInfo : WebService  

{ 

  [WebMethod(BufferResponse=true)] 

  public DataSet ShowSuppliers (string str)  

  { 

    SqlConnection dbConnection = new SqlConnection("server=(local); 

                                 uid=sa;pwd=;database=Northwind;"); 

    SqlDataAdapter objCommand = new SqlDataAdapter("select  

               ContactName, CompanyName, City, Phone from Suppliers  

               where Country = '" + str + "' order by ContactName  

               asc", dbConnection); 

    DataSet DS = new DataSet(); 

    objCommand.Fill(DS); 

    return DS; 

    dbConnection.Close(); 

    dbConnection = null; 

  } 

}
```

指令将文件设置为一个 Web 服务，给它一个名字并指定它使用的语言。

**Go to page:** [1](/net-web-services-5-steps) | [2](/net-web-services-5-steps-2/) | [3](/net-web-services-5-steps-3/) | [4](/net-web-services-5-steps-4/)

## 分享这篇文章