# 保护数据库中的密码

> 原文：<https://www.sitepoint.com/securing-passwords-database/>

当 ASP.NET 开发人员想到 Web 安全和身份验证时，通常会想到三个选项:Windows 身份验证、表单身份验证和 passport 身份验证。

例如，如果您正在组织内部构建一个内部网应用程序，您可能会选择使用 Windows 身份验证。在 IIS 中配置 Windows 身份验证相当简单，只需要两步。

首先，我们按照下面概述的步骤，确保选中集成 Windows 身份验证，并且未选中 Web 应用程序的匿名访问:

1.  右键单击 IIS 中的 Web 应用程序目录，然后选择属性。

3.  从应用程序的属性对话框中选择目录安全性选项卡。

5.  选择匿名访问和验证控制面板中的编辑…按钮。

7.  取消选中匿名访问，并确保选中集成 Windows 身份验证，如下图所示。

![1370_01](img/21736fec13fc1c1ae30364556992f6ab.png)

其次，我们只需要修改应用程序的 Web.config 文件中身份验证标记内的 mode 属性，使其设置为 Windows(默认设置)，如下所示:

```
<authentication mode="Windows" />
```

这就是全部了。每当用户请求一个页面，他们将被要求提供他们的 Windows 用户名和密码。登录后，可以通过访问页面的`user.identity`属性来跟踪用户。

然而，在许多情况下，开发都集中在表单身份验证上。为什么？因为您不希望为访问您站点的成千上万的潜在用户手动创建用户名和密码。假设您正在构建一个可供许多用户通过 Internet 访问的大规模论坛应用程序，那么 forms authentication 将是您的最佳选择，因为它允许您将用户名和密码存储在您选择的数据存储区(通常是一个数据库表)中，然后根据类似于以下内容的 Web 可访问登录页面来验证您的用户:

![1370_02](img/39625c2712fd567df39a2fa2c57d8509.png)

在这个场景中，用户输入他们的用户名和密码，然后单击登录按钮。登录按钮引发一个`Click`事件，调用某种验证方法，执行代码将用户输入文本框的值与数据库中包含的值进行比较。通常，数据库由一个包含 UserID、Username 和 Password 字段的 Users 表构成，类似于这里所示的设计。

![1370_03](img/36b5f059c635fbe01cf169b996d28831.png)

现在，我已经阅读了几十篇文章和书中的几十个章节，这些文章和章节讨论了如何使用表单身份验证，大多数文章和章节似乎都是围绕上面显示的简单设计来构建数据库表的。所以你可能会问自己，“有什么问题？”密码信息以明文形式存储，因此任何能够访问数据库的开发人员都可以访问订阅该应用程序的所有用户的用户名和密码信息。虽然这对于论坛应用程序来说没什么大不了的，但当您开始处理在线银行应用程序，甚至是 E*Trade、TD Waterhouse 等日内交易应用程序时，这就成了一个问题。

这个问题的一个解决方案是使用简单的散列法，并将我们的密码存储在消息摘要中。

##### 消息摘要和简单散列

消息摘要是以单个数字字符串的形式表示的文本，它是使用一种称为单向哈希函数的公式创建的。一个单向散列函数接受一个输入字符串(比如一个密码)，并对其执行一些数学运算，以产生一个天书字符串(一个摘要字符串)。让我们以密码“zak”为例。我可以“散列”该密码，得到的摘要将是:d 52987198 ea 2730 FD 22 a 38 e 7976344d 843 a 7 FFA 0。一点也不像扎克，对吧？

一个好的散列函数使得猜测一个将产生给定摘要字符串的输入字符串变得极其困难。如果您将用户的密码转换成摘要字符串，并将这些摘要字符串存储在数据库中，而不是实际的密码，会怎么样？每当用户登录时，您的身份验证脚本可以对他或她输入的密码执行相同的单向散列函数，并将其与数据库中存储的摘要字符串进行比较。匹配意味着用户输入了正确的密码，应该被授予访问该站点的权限。

这里最大的优势是，可以读取您的用户数据库的人(比如说，数据库管理员)只能看到摘要字符串，而不能看到原始密码！而且，因为几乎不可能找到产生特定消息摘要的密码，所以他们不能使用该信息来访问您的应用程序！

为了真正理解哈希的工作原理，让我们创建一个使用表单身份验证的简单应用程序。

请注意，本文中包含的所有代码都包含在[这个可下载的代码档案](https://www.sitepoint.com/examples/securepword/securepword.zip)中。

设置网站使用窗体身份验证就像 Windows 身份验证一样简单。首先，我们只需要在应用程序的 Web.config 文件中修改身份验证标记内的 mode 属性，使其设置为`Forms`,如下所示:

```
<authentication mode="Forms" />
```

其次，我们可以将授权部分处理程序中 deny 标记的`users`属性设置为`"?"`,如下所示:

```
<authorization> 

  <deny users="?" /> 

</authorization>
```

本质上，我们会拒绝所有匿名用户。接下来，我们创建一个注册页面(registration.aspx ),其中包含三个`TextBox`控件和一个`Button`控件来处理用户注册。代码应类似于以下内容:

```
<form runat="server"> 

<strong>User Registration</strong><br /><br /> 

Username:<br />  

<asp:TextBox id="txtUsername" runat="server" /><br /> 

Password:<br />  

<asp:TextBox id="txtPassword" runat="server" TextMode="Password" /><br /> 

Email:<br />  

<asp:TextBox id="txtEmail" runat="server" /><br /><br /> 

<asp:Button id="btnAddCredentials" runat="server" Text="Add Credentials" /> 

</form>
```

在浏览器中查看时，您的页面应该类似于下图。

![1370_04](img/5c6778b7ef7203a84bbe3c28c21b6f7c.png)

现在您已经看到了我们的标记的 UI 输出，让我们添加将调用我们的`AddCredentials_Click()`方法的`OnClick`属性，我们将在一分钟内构建它:

```
<asp:Button id="btnAddCredentials" runat="server" Text="Add Credentials" OnClick="AddCredentials_Click" />
```

既然我们已经设计好了用户界面，让我们创建一个新的数据库和数据库表来存储用户名、密码和电子邮件(如果用户碰巧忘记了密码，必须重置密码，我们可以将指令发送到用户的电子邮件中)。对于本例，我创建了一个名为 Customers.mdb 的简单 Access 数据库，其中只有一个名为 Users 的表。用户表的设计概述如下:

![1370_table1](img/0f2f5dd64408a7660639a1adb794118e.png)

完成后，数据表将如下所示。

![1370_05](img/ef6dab30683d6147a16c7a05eeb26797.png)

现在，我们可以编写代码，将用户的用户名、散列密码和电子邮件存储在数据库中。我们首先在页面顶部添加必要的指令，如下所示:

```
<%@ Import Namespace="System.Data.OleDb" %> 

<%@ Import Namespace="System.Web.Security" %>
```

因为我们将与 Access 数据库进行交互，所以我们导入了`System.Data.OleDb`名称空间。类似地，因为我们需要使用`FormsAuthentication`类，所以我们导入了`System.Web.Security`名称空间。接下来，我们可以在文档的`<head>`标签中添加一个代码声明块，用变量声明和我们的`AddCredentials_Click()`方法完成，如下所示:

```
<script runat="server"> 

Dim objConn As New OleDbConnection("Provider=Microsoft.Jet.OleDb.4.0;Data  

  Source=C:InetpubwwwrootPasswordHashingDatabaseCustomers.mdb") 

Dim objCmd As OleDbCommand 

Dim sqlCmd As String 

Sub AddCredentials_Click(s As Object, e As EventArgs) 

  sqlCmd = "INSERT INTO Users (Username, Pass, Email)  

        VALUES (@Username, @Pass, @Email)" 

  objCmd = New OleDbCommand(sqlCmd, objConn) 

  objCmd.Parameters.Add("@Username", txtUsername.Text) 

  objCmd.Parameters.Add("@Pass",  

               FormsAuthentication.HashPasswordForStoringInConfigFile(txtPassword.Text,  

    "SHA1")) 

  objCmd.Parameters.Add("@Email", txtEmail.Text) 

  objConn.Open() 

  objCmd.ExecuteNonQuery() 

  objConn.Close() 

End Sub 

</script>
```

为了简洁起见，我假设您有使用 ASP.NET 与数据库交互的经验。乍一看，上面代码中的一切似乎相当简单，除了使用了`FormsAuthentication`类的`HashPasswordForStoringInConfigFile()`方法。此方法是一种在 Web.config 文件中哈希和存储密码的方法。我们可以根据自己的目的使用这种方法，即将散列密码存储到数据库中。

如您所见，该方法接受两个参数。第一个显然是要使用的文本值。因为我们从密码文本框中检索这个值，所以我们使用 txtPassword.Text。在这个例子中，我使用了 SHA1，它产生一个 160 位摘要。您也可以使用 MD5，在这种情况下，结果将是一个 128 位的摘要。一般来说，摘要字符串中的位数越多，就越难猜测出将产生它的输入字符串。

现在，当我添加一个新用户时，密码被散列，摘要被存储在 Users 表的 Pass 字段中，如下所示。

![1370_06](img/3b4df49732dbd99dacf5100a6a1b132e.png)

看到多简单了吗？为了验证用户，我们只需创建一个登录表单(login.aspx ),用两个`TextBox`控件和一个`Button`控件来处理验证。标记如下所示:

```
<form runat="server">  

<strong>Authenticate:</strong><br /><br />  

Username:<br />   

<asp:TextBox id="txtUsername" runat="server" /><br />  

Password:<br />   

<asp:TextBox id="txtPassword" runat="server" TextMode="Password" /><br /><br />  

<asp:Button id="btnCheck" runat="server" Text="Check Credentials" /><br /><br />  

<asp:Label id="lblCheck" runat="server" />  

</form>
```

由于这些标记，我们的登录页面应该如下所示:

![1370_07](img/2d3ba353d765559de334e10c30a602ee.png)

一旦你得到了你想要的界面，添加`OnClick="CheckCredentials_Click"`属性到`Button`控件，如下所示:

```
<asp:Button id="btnCheck" runat="server" Text="Check Credentials" OnClick="CheckCredentials_Click" />
```

最后，我们在文档的`<head>`标记中创建一个代码声明块，并添加代码来验证我们的用户，如下所示:

```
<script runat="server">  

Dim objConn As New OleDbConnection("Provider=Microsoft.Jet.OleDb.4.0;Data   

  Source=C:InetpubwwwrootPasswordHashingDatabaseCustomers.mdb")  

Dim objCmd As OleDbCommand  

Dim objDR As OleDbDataReader  

Function CheckCredentials(Username As String, Password As String) As Boolean  

  objConn.Open()  

  objCmd = New OleDbCommand("SELECT Username, Pass FROM Users WHERE   

    Username=@givenUsername", objConn)  

  objCmd.Parameters.Add("@givenUsername", Username)  

  objDR = objCmd.ExecuteReader()  

  If Not objDR.Read() Then  

    Return False  

  Else  

    Dim storedHashedPassword As String = objDR("Pass")  

    Dim givenHashedPassword As String =   

      FormsAuthentication.HashPasswordForStoringInConfigFile(Password,   

      "SHA1")  

    Return storedHashedPassword = givenHashedPassword  

  End If  

End Function  

Sub CheckCredentials_Click(s As Object, e As EventArgs)  

  If (CheckCredentials(txtUsername.Text, txtPassword.Text) = True) Then  

    lblCheck.Text = "You entered the right credentials!"  

  Else  

    lblCheck.Text = "You entered the wrong credentials!"  

  End If  

End Sub  

</script>
```

同样，不要忘记在页面顶部添加以下指令:

```
<%@ Import Namespace="System.Data.OleDb" %>  

<%@ Import Namespace="System.Web.Security" %>
```

现在，在浏览器中启动`login.aspx`，输入用户名和密码。如下图所示，当我使用正确的凭证登录时，`Label`控件显示消息“您输入了正确的凭证”。

![1370_08](img/51473b351d50b4efe6869a5589ae11df.png)

你马上就能看到简单散列的好处。这比存储明文安全得多，因为有人很难猜出产生散列消息摘要的原始密码。注意我说的是“极其困难”。因为我们只执行了简单的散列，所以黑客不可能执行所谓的字典攻击和利用漏洞。同样，这个问题也有一个解决方案。

##### 腌杂烩

到目前为止，我们已经设法以相对安全的方式解决了在数据库中存储明文密码的问题。不幸的是，这个解决方案足够安全，只能防止数据库管理员的眼睛看到我们数据库中的用户名和密码。黑客仍然可以进行所谓的字典攻击。恶意方可以通过获取例如 100，000 个他们知道人们经常使用的密码(例如，城市名称、运动队等)来进行字典攻击。)，散列它们，然后将字典中的每个条目与数据库表中的每一行进行比较。如果黑客们找到了匹配，就对了！他们有你的密码。然而，要解决这个问题，我们只需要在杂凑中加盐。

要对散列加盐，我们只需拿出一个看起来随机的文本字符串，将其与用户提供的密码连接起来，然后将随机生成的字符串和密码作为一个值一起散列。然后，我们将 hash 和 salt 作为单独的字段保存在 Users 表中。

在这种情况下，黑客不仅需要猜测密码，还必须猜测盐。在明文中添加 salt 可以提高安全性:现在，如果黑客试图进行字典攻击，他必须用每个用户行的 salt 散列他的 10 万个条目。尽管仍有可能，但黑客攻击成功的几率会大幅降低。

为了实现这个功能，我们首先向我们的用户数据库表添加一个名为`Salt`的新字段。修改后的设计如下图所示。

![1370_table2](img/c96f59fad9c6fcf48a73d2e95d20bd69.png)

完成后，数据表将类似于下图所示。

![1370_09](img/bd0add8b30c03f29a24215688754a9a3.png)

现在，我们可以稍微修改一下`registration.aspx`中的代码来容纳盐。我将添加的代码加粗，以便于区分:

```
<%@ Import Namespace="System.Data.OleDb" %>  

<%@ Import Namespace="System.Web.Security" %>  

<%@ Import Namespace="System.Security.Cryptography" %>  

<html>  

<head>  

<title>Password Hashing</title>  

<script runat="server">  

Dim objConn As New OleDbConnection("Provider=Microsoft.Jet.OleDb.4.0;Data   

  Source=C:InetpubwwwrootPasswordHashingDatabaseCustomers.mdb")  

Dim objCmd As OleDbCommand  

Dim sqlCmd As String  

Dim objRng As New RNGCryptoServiceProvider  

Dim intSaltSize As Integer = 16  

Sub AddCredentials_Click(s As Object, e As EventArgs)  

  sqlCmd = "INSERT INTO Users (Username, Salt, Pass, Email) VALUES                

                           (@Username, @Salt, @Pass, @Email)"  

  objCmd = New OleDbCommand(sqlCmd, objConn)  

  objCmd.Parameters.Add("@Username", txtUsername.Text)  

  Dim objByte() As Byte = New Byte(intSaltSize) {}  

  objRng.GetBytes(objByte)  

  Dim strSalt As String = Convert.ToBase64String(objByte)  

  objCmd.Parameters.Add("@Salt", strSalt)  

  objCmd.Parameters.Add("@Pass",   

                          FormsAuthentication.HashPasswordForStoringInConfigFile(strSalt +   

                          txtPassword.Text, "SHA1"))  

  objCmd.Parameters.Add("@Email", txtEmail.Text)  

  objConn.Open()  

  objCmd.ExecuteNonQuery()  

  objConn.Close()  

End Sub  

</script>  

</head>  

<body>  

<form runat="server">  

<strong>User Registration</strong><br /><br />  

Username:<br />   

<asp:TextBox id="txtUsername" runat="server" /><br />  

Password:<br />   

<asp:TextBox id="txtPassword" runat="server" TextMode="Password" /><br />  

Email:<br />   

<asp:TextBox id="txtEmail" runat="server" /><br /><br />  

<asp:Button id="btnAddCredentials" runat="server" Text="Add Credentials"   

onClick="AddCredentials_Click " />   

</form>  

</body>  

</html>
```

注意`RNGCryptoServiceProvider`类的使用。这个类用于创建 salt。`RNGCryptoServiceProvider`类的`GetBytes()`方法创建一个随机字节数组用于加密目的。虽然我们可以使用`Random`类，但是`RNGCryptoServiceProvider`类提供的结果比`Random`类提供的结果更加随机。

这一次，当我添加新的用户名(zak)和密码(zak)时，结果与我们的第一个示例类似。这里的区别在于，当我添加具有相同密码(zak)的第二个用户(fred)时，密码和 salt 的摘要完全不同。这一点如下图所示。

![1370_10](img/c692b05a93e5236a3d554912543015fc.png)

现在要验证用户，我们只需修改`login.aspx`中的`CheckCredentials_Click()`函数，如下所示:

```
Function CheckCredentials(Username As String, Password As String) As Boolean  

  objConn.Open()  

  objCmd = New OleDbCommand("SELECT Salt, Pass FROM Users WHERE   

    Username=@givenUsername", objConn)  

  objCmd.Parameters.Add("@givenUsername", Username)  

  objDR = objCmd.ExecuteReader()  

  If Not objDR.Read() Then  

    Return False  

  Else  

    Dim strSalt As String = objDR("Salt")  

    Dim strStoredPassword As String = objDR("Pass")  

    Dim strGivenPassword As String =   

      FormsAuthentication.HashPasswordForStoringInConfigFile(strSalt   

      & Password, "SHA1")  

    Return strStoredPassword = strGivenPassword    

  End If  

End Function
```

这一次，当用户登录时，散列密码将与 salt 和密码的组合值进行比较。正如你在下图中看到的，我已经输入了正确的用户名和密码。

![1370_11](img/db5ea2626867b3e63936c898cc64b497.png)

##### 结论

现在你知道了。NET Framework 为安全存储密码提供了出色的支持，您再也不必在数据库中存储明文密码了！

## 分享这篇文章