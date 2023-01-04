# 在 ASP.NET 发送网络电子邮件

> 原文：<https://www.sitepoint.com/sending-web-email-asp-net/>

Web 开发中最常用的功能之一是从网页发送电子邮件。在你问之前，我会给你几个你可能想从你的 Web 应用程序发送电子邮件的理由:

*   create a "give us your feedback" Web page*   implement a "forgotten password" script that sends a password to the user’s email account*   send an automatic welcome email to your new newsletter subscriber*   send automatic email update notifications*   send automatic email notifications whenever an error occurs in your Web application

    当然，在很多情况下，从 Web 应用程序发送电子邮件是合适的，这取决于您来决定如何以及在哪里实现这一功能。

    ##### 输入:。网

    的。NET framework 使从网页发送电子邮件的任务变得难以置信的简单，我将在几分钟内向您展示如何做到这一点。本文假设您对如何实现有一个基本的了解。NET 和 ASP.NET 作品。

    ***1。导入名称空间，并创建所需类的实例***

    我们将使用`SmtpMail`和`MailMessage` 类，它们属于`System.Web.Mail`名称空间。为了使用它们，我们需要像这样导入`System.Web.Mail`名称空间:

    ```
    <%@ Import Namespace="System.Web.Mail" %>
    ```

    `MailMessage`类提供了构造电子邮件消息的属性和方法。为了构建我们的电子邮件，我们需要创建这个类的一个实例:

    ```
    Dim objMail As New MailMessage()
    ```

    ***2。设置对象的属性***

    接下来，我们必须设置`objMail` 对象的所有属性:

    ```
    ' The email address of the sender 

    objMail.From = "yourname@yourdomain.com" 

    ' The email address of the recipient 

    objMail.To = "recipientname@somedomain.com" 

    ' The email address of the Cc recipient 

    objMail.Cc = "name1@anotherdomain.com" 

    ' The email address of the Bcc recipient 

    objMail.Bcc = "name2@anotherdomain.com" 

    ' The format of the message - it can be MailFormat.Text 

    or MailFormat.Html 

    objMail.BodyFormat = MailFormat.Text 

    ' The priority of the message  - it can be MailPriority.High, 

    MailPriority,Normal or MailPriority.Low 

    objMail.Priority = MailPriority.High 

    'The subject of the message 

    objMail.Subject = "My first ASP.NET email" 

    'The message text 

    objMail.Body = "This is my first email sent via 

    ASP.NET. It was easier than I thought :)"
    ```

    在我们的新电子邮件的所有属性(其中一些是可选的)都设置好之后，剩下唯一要做的事情就是发送邮件。

    ***3。发送消息***

    如果你有使用`CDONTS`从经典 ASP 脚本发送电子邮件的经验，你可能会认为`MailMessage`类有方法`Send`或类似的东西。嗯，ASP.NET 发送电子邮件的方式和 ASP 有点不同。

    我们需要使用`SmtpMail`类的静态方法`Send`，传入我们的`objMail`实例。你可能会问“什么是‘静态方法’？”静态方法是与类型的实例不相关联的方法。类型实例的一个例子是我们的`objMail`对象。通过实例引用类的静态成员是非法的。正确的做法是:

    ```
    SmtpMail.Send(objMail)
    ```

    如果您想指定一个不同于默认的 SMTP 服务器，您需要设置`SmtpMail`类的`SmtpServer`属性:

    ```
    SmtpMail.SmtpServer = "smtp.your-server.com"
    ```

    ##### 摘要

    总之，要从您的 ASP.NET 页面发送电子邮件，您需要:

    *   import the `System.Web.Mail` namespace in your ASP.NET page*   create an instance of the `MailMessage` class*   set all the properties of the `MailMessage` instance*   send the message with `SmtpMail.Send` method

    最后，作为开始，下面是 ASP.NET 页面的完整功能代码，它通过电子邮件将用户的反馈发送给网站管理员:

    ```
    <%@ Page Language="VB" EnableSessionState="False"  

    EnableViewState="False" Trace="False" Debug="False"%> 

    <%@ Import Namespace="System.Web.Mail" %>  

    <script language="VB" runat=server>  

    Sub Page_Load(Sender as Object, E as EventArgs)  

        If Page.IsPostBack Then 

            lblResponse.Text = "Your email has been sent." 

        End If 

    End Sub  

    Sub btn_Click(sender as Object, e as System.EventArgs) 

      If  Request.Form("Email") <> "" Then 

        Dim objMail As New MailMessage() 

        objMail.From = "your_name@yourdomain.com" 

        objMail.To = Request.Form("Email") 

        objMail.Subject = Request.Form("Subject") 

        objMail.Body = Request.Form("Message") 

        objMail.BodyFormat = MailFormat.Text 

        SmtpMail.SmtpServer = " smtp.your-server.com" 

        SmtpMail.Send(objMail) 

      Else 

        lblResponse.Text = "Please enter an email address." 

      End If 

    End Sub 

    </script>  

    <html> 

    <head> 

    <style> 

    .main {font-family:Verdana; font-size:12px;} 

    .title {font-family:Verdana; font-size:18px; font-weight:bold;} 

    </style> 

    </head> 

    <body> 

    <span class="title" align="center">Send email from  

    an ASP.NET page</span> 

    <br><br><asp:Label class="main" id="lblResponse"  

    runat="server"/> 

    <form method="POST" name="MainForm" runat="server"> 

    <table> 

      <tr> 

        <td class="main" align="right">Email:</td> 

        <td class="main"><input type="text"  

        class="main" name="Email" value=""></td> 

      </tr> 

      <tr> 

        <td class="main" align="right"> 

        Subject:</td> 

        <td class="main"><input type="text"  

        class="main" name="Subject" value=""></td> 

      </tr> 

      <tr> 

        <td class="main" align="right"  

        valign="top">Message:</td> 

        <td class="main"><textarea name="Message"  

        cols="50" rows="8"></textarea></td> 

      </tr> 

      <tr> 

        <td class="main">&nbsp;</td> 

        <td class="main"><input type="Submit"  

        id="btnSubmit" OnServerClick="btn_Click" value="Send"  

        runat="server" /></td> 

      </tr> 

    </table> 

    </form> 

    </body> 

    </html>
    ```

## 分享这篇文章