# 用 ASP 发送电子邮件

> 原文：<https://www.sitepoint.com/sending-email-asp/>

在动态服务器页面中发送电子邮件并不像看起来那么复杂。您所需要的只是一个包含您的代码的 ASP 页面，一个支持 ASP 和 CDONTS(用于 Windows NT Server 的协作数据对象)的主机，以及 Active Server Pages 的基本知识。

CDONTS 为您提供了对象和类，使您能够从 ASP 页面发送电子邮件。CDONTS 只在 Windows NT/2000 操作系统上工作，但它有强大的功能，允许您:

*   发送电子邮件时附加文件，
*   以 HTML 格式发送电子邮件，
*   像在任何电子邮件客户端一样使用抄送和密件抄送功能，
*   设置电子邮件优先级

…而这仅仅是开始！还是那句话，过程并没有看起来那么复杂。

##### 创造 CDONTS

首先，我们必须使用以下代码在服务器上创建一个 CDONTS 实例:

```
<% 

Set Mail=Server.CreateObject("CDONTS.NewMail") 

%>
```

在上面的代码中，您可以用任何其他变量替换`Mail`，但是您必须确保不断地使用那个变量。既然我们已经在服务器上创建了 CDONTS 的实例，我们需要输入所需的值。

##### 输入您的值

第一个输入应该是我们将向其发送电子邮件的收件人。要添加收件人地址，请使用:

```
<% 

Mail.to=" me@mydomain.com" 

%>
```

```
Mail.to consists of the same variable that we used to name the instance of our CDONTS.NewMail The to after the **.** tells the object the address to which the email should be sent. So now our code looks like this:

```
<%  

Set Mail=Server.CreateObject("CDONTS.NewMail") 

Mail.to=" me@mydomain.com" 

%>
```

现在我们需要告诉对象发件人的电子邮件地址，使用:

```
<% 

Mail.From=" testing-my@SP-Script.com" 

%>
```

`Mail.`后的`From`字段表示发件人的电子邮件地址。电子邮件地址应该始终用引号括起来。添加后，我们的代码变成了:

```
<% 

Set Mail=Server.CreateObject("CDONTS.NewMail") 

Mail.To="me@mydomain.com" 

Mail.From="testing-my@SP-Script.com" 

%>
```

到现在为止，您已经清楚了使用主类的概念，所以让我们看看如何将主题行添加到电子邮件中。如果你还没有猜到，这是如何做到这一点:

```
<% 

Mail.Subject="Just testing my script" 

%>
```

现在代码是:

```
<% 

Set Mail=Server.CreateObject("CDONTS.NewMail") 

Mail.To="me@mydomain.com" 

Mail.From="testing-my@SP-Script.com" 

Mail.Subject="Just testing my script" 

%>
```

现在剩下的就是邮件的内容，我们是这样添加的:

```
<% 

Mail.Body="Hey! I am sending this email through an ASP Page, and  

guess what? I haven't learnt much yet, but know that ASP is very  

powerful." 

%>
```

`Body`类表示我们正在发送的电子邮件的内容。它可以包含任何类型的文本，但是它不识别 HTML 标签——它只是将这些作为普通文本发送。

总而言之，我们的代码是这样的:

```
<%  

Set Mail=Server.CreateObject("CDONTS.NewMail")  

Mail.To="me@mydomain.com"  

Mail.From="testing-my@SP-Script.com"  

Mail.Subject="Just testing my script"  

Mail.Body="Hey! I am sending this email through an ASP Page and   

guess what? I haven't learnt much yet, but know that ASP is very   

powerful."  

%>
```

好的。现在是发送电子邮件的时候了，使用:

```
<%  

Mail.Send  

%>
```

这应该可以了！我们的代码很简洁，应该可以成功发送电子邮件。让我们最后看一下完整的代码:

```
<%  

Set Mail=Server.CreateObject("CDONTS.NewMail")  

Mail.To="me@mydomain.com"  

Mail.From="testing-my@SP-Script.com"  

Mail.Subject="Just testing my script"  

Mail.Body="Hey! I am sending this email through an ASP Page and   

guess what? I haven't learnt much yet, but know that ASP is very   

powerful."  

Mail.Send  

Set Mail=nothing  

%>
```

最后一行:`Set Mail=nothing` 从服务器释放对象的实例——以及一些资源。这会让你的服务器管理员高兴的！

你现在可以复制并粘贴上述代码到你最喜欢的编辑器，保存为 SendingEmail.asp(记住文件扩展名)，并上传到你的网站。请记住用您自己的电子邮件地址替换收件人和发件人的电子邮件地址。现在，您可以访问您上传的页面，并阅读您通过 ASP 页面发送的电子邮件。

##### 获取地址

您还可以使用通过表单或 querystring 请求的字段来获取收件人的电子邮件地址。如果对收件人字段使用 form 或 querystring，那么`.To`的代码将如下所示:

```
<%  

' In case of requesting recipient's address from a form  

Mail.to=request.form("txteMail")   

'txteMail is just a variable, you can change it as per your form  

%>  

<%  

'In case of Querystring  

Mail.To=Request.QueryString("email")  

' eMail is just a variable, again you can change it as per your page  

%>
```

##### 额外帮助？

现在，我们已经探索了如何通过 ASP 页面发送电子邮件，并发现这非常简单。如果您有任何问题或疑问，或者您发现 ASP 电子邮件令人困惑，请尝试在 [SitePoint 论坛](http://www.SitePointForums.com )上发布您的问题。我们忠诚的会员将很乐意帮助您！

 ***临别项目:*** 
为了练习你所学的，创建一个页面，要求用户提供电子邮件地址，并向他们发送一封测试邮件。看懂了这个教程就不难了！

## 分享这篇文章

```