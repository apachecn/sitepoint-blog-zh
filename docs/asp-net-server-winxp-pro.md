# 使用 ASP 发送电子邮件。Net Server 或 WinXP Pro

> 原文：<https://www.sitepoint.com/asp-net-server-winxp-pro/>

***这篇文章的目的是希望它能帮助 ASP 开发者从现有的 NT 和 Windows 2000 服务器过渡到微软新的 Web 服务器平台，同时支持遗留的 ASP 代码。所有代码都经过注释，并已在 Windows XP Pro SP1 和 Windows Dot.Net 服务器 2003 SR1 和 SR2 上测试。***

可以说，一个网站可以完成的最有用的任务之一就是提供收集访问者反馈的能力，并将这些信息发送给负责的各方。大多数在 NT 或 Windows 2000 上使用 ASP 的开发人员都享受到了开发收发电子邮件脚本的便利。提供方便的 CDO 邮件对象的 dll 叫做 cdonts.dll。长名字是 Windows NTÂ Server 的协作数据对象，或者我喜欢称之为 NT 的 CDO。

##### 微软和 cdonts.dll

微软也以自己的方式称 cdonts.dll·CDO 为 NT。

自从 XP 发布以来，我多次被告知 XP 不能运行某个邮件脚本或新的。Net 服务器已经锁定了电子邮件，等等…之所以 XP 和。Net 服务器吐出错误，而不是像 NT 或 Win2k 那样运行 ASP 电子邮件脚本，这是因为 CDONTS 是老技术了。

当 Windows 2000 发布时，微软表示 cdonts.dll 已被弃用，而要使用的新邮件对象是 cdosys.dll。Windows 2000 附带了两个 CDO dll，但这是不祥之兆。CDONTS 就要过时了，ASP 开发人员应该学习新的邮件对象 CDOSYS。不幸的是，还没有很多关于如何移植现有的 ASP 脚本来与 CDOSYS 一起工作的信息。

Windows XP Pro 和。Net Server 2003 不再附带 CDONTS，所以如果你运行 CDONTS 电子邮件脚本，你会得到一个错误。有经验的 ASP 开发人员可能会想，“嗯，我总是可以从我的 Win2k 发行版中取出一个 cdonts.dll，并在我的 XP 或 Dot.Net 机器上注册它”。你可以这样做，但这与微软的最终用户许可协议(EULA)相冲突，而且，由于使用新的邮件对象相对容易，为什么不利用它呢？新对象需要更少的开销，如果你深入研究，你会发现它有更多的功能。

##### 经典的 ASP 脚本

这是一个使用经典 ASP/CDONTS 的反馈电子邮件脚本的例子。

```
<% 

'EMAIL FORM USING CLASSIC ASP AND CDONTS  

Dim varName, varEmail,  

varCompany, varTelephone, varComments, varSend, thisPage 

varName   = Request.Form("txtName") 

varEmail   = Request.Form("txtEmail") 

varCompany   = Request.Form("txtCompany") 

varTelephone   = Request.Form("txtPhone") 

varComments   = Request.Form("txtComments") 

varSend   = Request.Form("send") 

thisPage   = Request.ServerVariables("SCRIPT_NAME") 

If not varSend = 1 Then  

'OUTPUT THE CONTACT FORM 

%> 

<form action="<%= thisPage %>" method="post"> 

  <input type="hidden" name="send" value="1">  

    <p>Feel free to contact us using this web form</p> 

    <p>&nbsp;</p> 

      <table width="100%" border="0" cellspacing="0" cellpadding="0"> 

        <tr valign="top">  

          <td align="right">Name:</td> 

          <td width="3%">&nbsp;</td> 

          <td nowrap><input type="text" name="txtName" size="35"></td> 

        </tr> 

        <tr valign="top">  

          <td align="right">Company:</td> 

          <td width="3%">&nbsp;</td> 

          <td><input type="text" name="txtCompany" size="35"></td> 

        </tr> 

        <tr valign="top">  

          <td align="right">email:</td> 

          <td width="3%">&nbsp;</td> 

          <td nowrap><input type="text" name="txtEmail" size="35"></td> 

        </tr> 

        <tr valign="top">  

          <td align="right">Telephone:</td> 

          <td width="3%">&nbsp;</td> 

          <td><input type="text" name="txtPhone" size="25"></td> 

        </tr> 

        <tr valign="top">  

          <td align="right">Comments:</td> 

          <td width="3%">&nbsp;</td> 

          <td><textarea name="txtComments" rows="5"  

cols="35"></textarea></td> 

        </tr> 

        <tr valign="top">  

          <td align="right">&nbsp;</td> 

          <td width="3%">&nbsp;</td> 

          <td>&nbsp;</td> 

        </tr> 

        <tr valign="top">  

          <td align="right"><input type="submit"  

name="Submit" value="Submit"></td> 

          <td width="3%">&nbsp;</td> 

          <td><input type="reset" name="Reset"  

value="Reset" class="frm"></td> 

        </tr> 

      </table> 

    </form> 

<% 

Else 

  'SEND MAIL  

    Dim ObjMail 

    Set ObjMail = Server.CreateObject("CDONTS.NewMail") 

    objMail.From = varName & " <" & varEmail & ">" 

    objMail.To = "your-name@your-website.com" ' Add your email address 

    objMail.Subject = "Contact from Your Website" 

    objMail.Body = varName & ""_  

        & VBCrLf & varCompany & ""_  

        & VBCrLf & varTelephone & ""_  

        & VBCrLf & VBCrLf & varComments & VBCrLf  

    objMail.Send 

    Set objMail = Nothing 

  'AND OUTPUT THANK YOU 

  Response.Write "Hey "& varName & ",<br />"_  

      & VBCrLf & "Thanks for your feedback.<br />"_ 

      & VBCrLf & "We'll contact you as soon as possible!"  

End If 

%>
```

这个方便的反馈页面在 NT 和 Windows 2000 服务器上运行良好，但是要小心！如果您在没有 CDONTS 支持的服务器上运行上面的脚本，您将会看到一个讨厌的 VB 脚本错误，表明您调用了某种无效的类。为了使这个脚本与新的邮件对象 CDOSYS 兼容，您需要做一些小的修改。

##### 针对 CDOSYS 合规性的调整

真的没那么难。ASP 代码的第一块和主 HTML 表单保持不变。只有 ASP 代码的输出块需要更改。

另外，我们将使用 mail 对象的 HTMLBody 属性将我们的电子邮件输出为 HTML。默认情况下，这也将创建一个纯文本版本来支持旧的电子邮件客户端。

对 ASP 代码的最后一块进行以下更改(注意，更改后的代码以粗体显示):

```
<%  

Else  

'SEND MAIL AND OUTPUT THANK YOU RESPONSE  

  'REPLACE LINE RETURNS WITH HTML BREAKS   

  **varComments = Replace(varComments, chr(10), "<br />")**  

  'THE MAIL OBJECT  

  Dim ObjMail  

  **Set ObjMail = Server.CreateObject("CDO.Message")**  

  objMail.From = varName & " <" & varEmail & ">"  

  objMail.To = "your-name@your-website.com" ' add your email address  

  objMail.Subject = "Contact from Your Website"  

  **objMail.HTMLBody = varName & "<br />"_   

      & VBCrLf & varCompany & "<br />"_   

      & VBCrLf & varTelephone & "<br />"_   

      & CBCrLf & varComments & "<br />"**    

  objMail.Send  

  Set objMail = Nothing  

'AND OUTPUT THANK YOU  

Response.Write "Hey "& varName & ",<br />"_   

    & VBCrLf & "Thanks for your feedback.<br />"_  

    & VBCrLf & "We'll contact you as soon as possible!"   

End If  

%>
```

正如您所看到的，从 CDONTS 切换到 CDOSYS 只需要调整 3 行代码，第一个变化是用 html 换行符标记替换 ascii 换行。如果我们使用纯文本版本`**objMail.TextBody**`，而不是`**objMail.HTMLBody**`，我们只需要对代码做 2 处修改。

然而，在测试过程中，我遇到了一个小故障…

##### 小故障

在…的测试版上。Net 服务器 SR1 和 SR2，上面的代码应该运行正常，但我花了很长时间说服它在 WinXP Pro 上运行。我无法避免这些错误:

```
**Error Type:  

CDO.Message.1 (0x80040220)  

The "SendUsing" configuration value is invalid.**
```

msdn.Microsoft.com 的知识库中有一些关于 Exchange server 的这些错误的信息，但是我找到的关于 ASP 和 Windows 2000/XP 的最佳参考资料都在本文的末尾。此后，我修改了最初的 CDOSYS 代码，添加了一些配置脚本来防止“SendUsing”错误。如果在 Windows XP Pro 或 Windows 2000 计算机上运行代码时遇到问题，请在代码中添加以下内容:

在任何其他代码出现之前，将此代码放在页面顶部(www.asp101.com 和 www.markforum.tk 的文章激发了以下更改):

```
<!--METADATA TYPE="typelib"   

UUID="CD000000-8B95-11D1-82DB-00C04FB1625D"   

NAME="CDO for Windows 2000 Library" -->  

<!--METADATA TYPE="typelib"   

UUID="00000205-0000-0010-8000-00AA006D2EA4"   

NAME="ADODB Type Library" -->
```

最后一个代码块应该如下所示(注意，更改后的代码以粗体显示):

```
<%  

Else  

'SEND MAIL AND OUTPUT THANK YOU RESPONSE  

  'REPLACE LINE RETURNS WITH HTML BREAKS   

  varComments = Replace(varComments, chr(10), "<br />")  

  'THE MAIL OBJECT  

  Dim ObjMail  

  Set ObjMail = Server.CreateObject("CDO.Message")  

  **Set objConfig = CreateObject("CDO.Configuration")  

  'Configuration:  

  objConfig.Fields(cdoSendUsingMethod) = cdoSendUsingPort   

  objConfig.Fields(cdoSMTPServer) = "localhost"  

  objConfig.Fields(cdoSMTPServerPort) = 25   

  objConfig.Fields(cdoSMTPAuthenticate) = cdoBasic    

  'Update configuration  

  objConfig.Fields.Update  

  Set objMail.Configuration = objConfig**  

  objMail.From = varName & " <" & varEmail & ">"  

  objMail.To = "your-name@your-website.com" 'Add your email address  

  objMail.Subject = "Contact from Your Website"  

  objMail.HTMLBody = varName & "<br />"_   

      & VBCrLf & varCompany & "<br />"_   

      & VBCrLf & varTelephone & "<br />"_   

      & CBCrLf & varComments & "<br />"    

  objMail.Send  

  Set objMail = Nothing  

  Set objConfig = Nothing  

'AND OUTPUT THANK YOU  

Response.Write "Hey "& varName & ",<br />"_   

    & VBCrLf & "Thanks for your feedback.<br />"_  

    & VBCrLf & "We'll contact you as soon as possible!"   

End If  

%>
```

##### Windows XP 和 Windows 2000 CDO 配置资源

微软开发者网络
[http://msdn.microsoft.com](http://msdn.microsoft.com)

在 ASP101.com
[使用 CDO 通过外部 SMTP 服务器发送电子邮件](http://www.asp101.com/articles/john/cdosmtprelay/default.asp)

## 分享这篇文章