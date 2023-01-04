# 在 ASP.NET 建立 WHOIS 查询

> 原文：<https://www.sitepoint.com/build-whois-lookup-asp-net/>

这个问题我已经听过几百次了:“我如何编写一个脚本来检查域的可用性？”

今天我将向你展示如何用 C#和 ASP.NET 实现所谓的 WHOIS 脚本。

##### 关于谁

WHOIS 脚本用于检查域名的可用性，或找出谁拥有特定的域名。

我打赌你已经使用过基于网络的 WHOIS 查询，比如[http://checkdomain.com](http://checkdomain.com)

你所需要做的就是输入你想要检查的域名，然后点击提交按钮。如果该域名已经注册，结果将显示该域名注册人的信息，否则您将收到消息说该域名仍然可用。

很明显，每当你进行 WHOIS 查询时，你都要查询某种保存注册信息的数据库。然而，这个 WHOIS 数据库不是集中的，所以实际的域名记录通常不能从单个 WHOIS 服务器获得。域名注册记录的 WHOIS 服务器由授权注册域名的组织维护。

一般来说，WHOIS 服务器接受通过 TCP 在端口 43 上的连接，所以如果我们使用这个端口，我们能够与服务器通信。有关 WHOIS 服务器的完整列表，请访问[http://www . MIT . edu/AFS/Athena/contrib/pot luck/Net-Services/WHOIS-servers . list](http://www.mit.edu/afs/athena/contrib/potluck/Net-Services/whois-servers.list)

##### 导入命名空间

的。Net framework 为我们提供了`TcpClient`类(`System.Net.Sockets`名称空间)，在它的帮助下，我们将连接到 WHOIS 服务器并直接请求域名注册数据。

首先，我们需要导入`System.Net.Sockets`、`System.Text`、`System.IO`和`System.Text.RegularExpressions`名称空间，因为我们将在我们的 WHOIS ASP.NET 页面中使用它们:

```
<% @Import Namespace="System.Net.Sockets" %> 

<% @Import Namespace="System.Text" %> 

<% @Import Namespace="System.IO" %> 

<% @Import Namespace="System.Text.RegularExpressions" %>
```

##### 连接到主机

`TcpClient`类提供了通过网络连接、发送和接收数据的方法。有两种方法可以连接到远程主机:

1.  Create an instance of the `TcpClient` class using no parameters, and then call one of the three `TcpClient` connect methods:

    ```
    TcpClient objTCPC = new TcpClient(); 

    objTCPC.Connect("whois.networksolutions.com", 43);
    ```

2.  Create an instance of the `TcpClient` class using the host name and port number of the server to which you want to connect. This constructor will automatically establish your connection:

    ```
    TcpClient objTCPC = new TcpClient 

    ("whois.networksolutions.com", 43);

    ```

##### 发送您的查询

在我们成功连接到 WHOIS 服务器后，下一步是发送我们的域名查询。

首先我们声明 strDomain string，它将保存我们要查找的域名。我们将“`rn`”连接到我们的域名末尾，因为这是 WHOIS 服务器期望的格式。最后，我们使用`Encoding`(`System.Text`名称空间)的`GetBytes`方法将`strDomain`编码成一个字节数组(`arrDomain`)。

```
string strDomain = "domain-for-check.com" + "rn"; 

byte[] arrDomain = Encoding.ASCII.GetBytes(strDomain);
```

我们将使用`GetStream`方法获得一个`Stream` ( `System.IO namespace`)对象，该对象可以在底层连接的套接字上发送和接收数据:

```
Stream objStream = objTCPC.GetStream();
```

一旦我们获得了对`Stream`对象的访问权，我们就可以使用`Write`方法向当前流写入(发送)一个字节序列:

```
objStream.Write(arrDomain, 0, strDomain.Length);
```

##### 得到回应

获得 WHOIS 服务器响应的方法之一是创建 StreamReader 类(`System.IO`名称空间)的实例，该实例以特定编码从字节流中读取字符:

```
StreamReader objSr = new StreamReader  

(objTCPC.GetStream(), Encoding.ASCII);
```

下一步是使用我们的`objSr`对象的`ReadToEnd`方法，获得 WHOIS 服务器响应:

```
string strServerResponse = objSr.ReadToEnd();
```

因为我们已经立即读取了服务器响应，所以我们需要将文本响应中的新行字符“`n`”替换为“`<br>`”，以便在浏览器中正确显示。我们可以借助 Regex 类(`System.Text.RegularExpressions`)尤其是它的`Replace`方法来实现这一点:

```
strServerResponse  = Regex.Replace  

(strServerResponse, "n", "<br>");
```

在我们收到服务器响应后，剩下要做的唯一一件事就是将`strServerResponse`值赋给`lblResponse.Text`属性(`lblResponse`是我们的标签服务器控件，结果将在这里显示)，并关闭与 WHOIS 服务器的 TCP 连接:

```
lblResponse.Text = strServerResponse;  

objTCPC.Close();
```

##### 代码

总之，要创建 WHOIS 查找 ASP.NET 页面，您需要:

1.  在您的 ASP.NET 页面中导入`System.Net.Sockets`、`System.Text`、`System.IO`和`System.Text.RegularExpressions`名称空间
2.  使用`TcpClient`类连接到 WHOIS 服务器
3.  向 WHOIS 服务器发送域查询
4.  通过`StreamReader`类读取服务器响应
5.  使用 Regex 类将服务器响应中的新行字符"`n`"替换为"`<br>`"
6.  显示响应字符串

这是 WHOIS ASP.NET 页面的完整功能代码。请记住，示例中使用的 WHOIS 服务器可以更改为任何有效的 WHOIS 服务器以供参考，请查看[http://www . MIT . edu/AFS/Athena/contrib/pot luck/Net-Services/WHOIS-servers . list](http://www.mit.edu/afs/athena/contrib/potluck/Net-Services/whois-servers.list)

看看这个 ASP.NET WHOIS 页面的[全功能示例。](http://www.aspdev.org/aspnet_whois/whois.aspx)

以下是该页面的代码:

```
<% @Page Language="C#" Debug="false"%>  

<% @Import Namespace="System.Net.Sockets" %>  

<% @Import Namespace="System.Text" %>  

<% @Import Namespace="System.IO" %>  

<% @Import Namespace="System.Text.RegularExpressions" %>  

<script language="C#" runat=server>   

public void btn_Click(object sender, EventArgs eArgs)  

{  

  try  

  {  

    TcpClient objTCPC = new TcpClient(Request.Form["WhoisServer"], 43);  

    string strDomain = Request.Form["DomainName"] + "rn";  

    byte[] arrDomain = Encoding.ASCII.GetBytes(strDomain);  

    Stream objStream = objTCPC.GetStream();  

    objStream.Write(arrDomain, 0, strDomain.Length);  

    StreamReader objSR = new StreamReader(objTCPC.GetStream(),   

Encoding.ASCII);  

    lblResponse.Text = "<b>" + Request.Form["DomainName"] +   

"</b><br><br>" + Regex.Replace(objSR.ReadToEnd(),"n","<br>");  

    objTCPC.Close();  

  }  

  catch(Exception ex)  

  {  

    lblResponse.Text = ex.ToString();  

  }  

}  

</script>   

<html>  

<head>  

<style>  

.main {font-family:Verdana; font-size:12px;}  

.title {font-family:Verdana; font-size:18px; font-weight:bold;}  

</style>  

</head>  

<body>  

<span class="title" align="center">WHOIS ASP.NET page</span>  

<form method="POST" name="MainForm" runat="server">  

<table>  

  <tr>  

    <td class="main" align="right">Whois Server</td>  

    <td class="main">  

      <asp:DropDownList class="main" id="WhoisServer" runat="server">  

        <asp:ListItem value="whois.networksolutions.com">  

whois.networksolutions.com (.COM, .NET, .EDU)</asp:ListItem>  

        <asp:ListItem value="whois.ripe.net">whois.ripe.net  

 (Europe)</asp:ListItem>  

        <asp:ListItem value="whois.cira.ca">whois.cira.ca (.CA)  

</asp:ListItem>  

        <asp:ListItem value="whois.nic.uk">whois.nic.uk  

 (.CO.UK)</asp:ListItem>  

        <asp:ListItem value="whois.domain-registry.nl">  

whois.domain-registry.nl (.NL)</asp:ListItem>  

      </asp:DropDownList>  

    </td>  

  </tr>  

  <tr>  

    <td class="main" align="right">Domain Name:</td>  

    <td class="main"><input type="text" class="main"   

name="DomainName" value=""></td>  

  </tr>  

  <tr>  

    <td class="main">&nbsp;</td>  

    <td class="main"><input type="Submit" id="btnSubmit"   

OnServerClick="btn_Click" value="Send" runat="server" /></td>  

  </tr>  

</table>  

<br><br>  

<asp:Label class="main" id="lblResponse" runat="server"/>  

</form>  

</body>  

</html>
```

## 分享这篇文章