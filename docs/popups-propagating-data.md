# 灾难恢复设计—关闭弹出窗口并传播数据

> 原文：<https://www.sitepoint.com/popups-propagating-data/>

本周，医生状态良好，处理各种疾病。在这里，他对待 SSI，弹出窗口，等等…但如果你有一个问题没有涵盖，[给他发电子邮件，让他知道](mailto:drdesign@sitepoint.com)。他会很乐意帮忙的！

##### 关闭 Onunload 上的弹出窗口

**医生，我的问题是** [类似于安娜的](http://www.WebmasterBase.com/article/780 ) **，但我想要加载页面的内容框架中的链接卸载一个弹出窗口。这个页面上有打开其他窗口的链接，当一个新窗口打开时，我想卸载一个带有链接的弹出窗口。**

**舒安**

肖恩:这不是问题。您肯定知道，弹出窗口需要一点 javascript 才能运行。这里有一个框架集文档中的 javascript 示例，它会在加载框架集时打开弹出窗口。

在下面的例子中，我们有框架集(frameset.html)及其子框架之一 top.html 的代码

frameset.html

```
<script language="javascript"> 

// var settings holds the settings for the popup window 

var settings = "toolbar=1, scrollbars=1, location=1, status=1, menubar=1,  

resizable=1, width=640, height=400"; 

// windowUrl holds the url for the popup window 

windowUrl = "url/to/popup.html"; 

// now let's open the popup window. 

outerName = window.open(windowUrl, 'innerName', settings); 

// function closepopup will be called to close the popup 

function closepopup() { 

   outerName.close(); 

} 

</script> 

<frameset rows="200,*"> 

<frame src="top.html"> 

<frame src="bottom.html"> 

</frameset>
```

正如你所看到的，frameset.html 将弹出一个新窗口。函数 closepopup 将在被调用时关闭弹出窗口。现在，棘手的部分来了。我们将从子框架中调用该函数。

top.html

```
<HTML> 

<HEAD> 

<TITLE>TITLE> 

</HEAD> 

<BODY> 

<a href="#" onclick="parent.closepoup()">close pop</a> 

</BODY> 

</HTML>
```

在我的例子(top.html)中，我使用了一个锚点和 onclick 事件来触发父文档(frameset.html)中的 javascript 函数 closepopup。

但是，您可以在这里看到原理，并通过为文档的 onunload 事件调用 parent.closepopup()将其应用于您的情况。你可能也想看看这个关于弹出窗口 javascript 的指南。希望这有所帮助！

##### 更多的 SSI 魔法

**嘿设计博士，**

**我有两个问题。第一，你上一篇文章里说要用 SSIs，你用`<!--#include file="menu.txt"-->`。这和`<!--#include virtual="menu.txt"-->`有区别吗？**

第二，我为一个客户建立了一个模板，并试图让更新对我自己来说尽可能的简单——或者是任何一个网站管理员。所以我想我应该在页眉和页脚使用服务器端包含。

**然而，带有页面标题的图像在表格中，比内容“高”得多。所以，我想在 SSI 中使用变量。下面是我想出来的:**

```
 **<!--#set var="which" value="blah.blah" --**  

 **<!--#echo $which-->** 
```

**然而，当我尝试运行这段代码时，屏幕上什么也没有出现。我哪里做错了？还有，我可以做同样的事情来输出属性到其他标签(即`<IMG SRC="[variable here]">` )吗？**

谢谢医生！…顺便问一下，谁是真正的设计博士专栏的作者？马特。凯文吗？

**Corbb**

你站在哪一边，考伯？你真的想让我暴露自己的真实身份，暴露在敌人面前吗？此外，我在你的邮件中记下了三个问题——不过没关系，我只收你两个问题的费用；-)

SSI include 命令中的虚拟和文件属性是不同的。

virtual 告诉服务器，文档的路径是一个虚拟路径，当我们想要插入 CGI 脚本的结果时，通常使用这个路径。什么是虚拟路径？虚拟路径由您的 Web 主机在 apache 配置文件中指定。

虚拟路径的示例有:

document root/home/web users/your domain . com/public _ html

script alias/CGI-bin/usr/local/httpd/CGI-bin

因此，在 include 命令中使用虚拟路径的一个示例是:

```
<!--#include virtual="cgi-bin/some_script.cgi"-->
```

file 告诉服务器文件的路径表示为相对路径。所以，

```
<!--#include file="menu.txt"-->
```

告诉服务器文档 menu.txt 与当前文档在同一个目录中。

现在，继续你的第二个问题。SSI 指令由一个命令和其后的属性/值对组成。

```
<!--#command attribute1=value1 attribute2=value2 -->
```

因此，您的示例的正确代码应该是

```
<!--#set var="which" value="blah.blah" -->  

<!--#echo var="which" -->
```

最后，Corbb，你可以按照你的建议，在 HTML 标签中插入一个变量。

例如:

```
<!--#set var="myimage" value="imagename.gif" -->  

<img src="<!--#echo var="myimage" -->">
```

更多信息请参考[Apache SSI 教程](http://httpd.apache.org/docs/howto/ssi.html)以及 [NCSA HTTPd 服务器端包含教程。](http://hoohoo.ncsa.uiuc.edu/docs/tutorials/includes.html)

##### 传播表单数据和访问 ASP 请求对象

你好，医生，我在拆分长输入表单时遇到了问题。 [我读了你对杰夫](http://www.WebmasterBase.com/article/822) **说的话，起初听起来很简单，但当我开始做的时候，我意识到我错过了一些东西……我做错了什么？**

1)表单 1 开启了表单验证(检查数据的有效性)。对吗？

**2)我得到了提交按钮(如你笔记的步骤 1 所示)，form 1 的动作是对 form 2(页面名为 reg2)。**

```
**<FORM ACTION = "reg2.asp" METHOD = "post"**   

**onsubmit = "return Validator(this)" name="Form1" LANGUAGE=javascript**   

**onsubmit="return Form1_onsubmit()">**
```

**3)我的问题来自于从 pg1 收集关于 pg2 的信息…我已经用 global.asa 中的会话变量试过了…**

```
**Sub Session_OnStart**   

**'**Put your code here ****   

 **Session( "LCode" ) = Request( "LCode" )**   

**Session( "LName" ) = Request( "LName" )**   

**etc etc...**   

**End Sub**
```

**然后我在数据库中输入这些会话变量(在表格 2 提交后)…**

```
**insert = "INSERT INTO Learner VALUES ( '" & _   **   

 **Session( "LCode" ) & "','" & _**   

 **Session( "LSurname" ) & "','" & _**   

 **Session( "LName" ) & "','" & _**   

 **etc etc...**
```

**请帮帮忙！我做错了什么？**

贾尼斯

珍妮丝，看看你的代码，可能是因为漏掉了点运算符。当试图访问请求对象的属性时。

例如:

```
Request("Lcode")
```

应该是

```
Request.("Lcode")
```

以上仍然是一个快捷方式，MSDN 文档推荐通过集合访问一个请求变量。在您的例子中，集合是表单数据，因此您可以按如下方式访问它:

```
Request.Form("Lcode")
```

为什么建议这样做？

首先，请求对象由几个“集合”组成，这些集合本身由客户端发送的各种变量组成，或者是预定义的服务器环境变量。这些集合是:

***收藏***

*   **ClientCertificate:** 在 HTTP 请求中发送的客户端证书中存储的字段值。
*   **cookie:**HTTP 请求中发送的 cookie 的值。
*   **表单:**HTTP 请求体中表单元素的值。
*   **query string:**HTTP 查询字符串中变量的值。
*   **ServerVariables:** 预先确定的环境变量的值。

当你没有说明你想要访问的变量在哪个集合中时，ASP 必须在所有的集合中寻找你想要的。

这也减少了不确定性，因为您指定您想要的是表单数据，而不是 URL 查询字符串数据，等等。

***使用隐藏字段***

如果会话让您毛骨悚然，另一种方法是通过使用隐藏的输入标记将表单数据从第一个表单嵌入到第二个表单来传播数据。根据您的代码，示例如下:

```
<FORM ACTION = "reg2.asp" METHOD = "post" onsubmit = "return Validator   

(this)" name="Form1" LANGUAGE=javascript onsubmit="return    

Form1_onsubmit()">    

<%   

Response.Write "<input type='hidden' name='Lcode' value='"    

& Request.Form("Lcode")  & "'></input>"   

%>
```

如果你在调试你的代码时遇到了问题，你可以在 SitePoint Forums ASP forum 的友好人群中寻求帮助。

***调试你的代码***

无论您选择哪种服务器端脚本语言，目前都有大量功能强大的集成开发环境(ide)可以帮助您编写和调试代码。然而，让我们面对现实吧，我们中的许多人喜欢在自己选择的文本编辑器中不停地工作。这让我想到了一个简单的调试技术，您可以用它来找到是什么在绊倒您的代码。技术很简单:将变量和消息打印到浏览器，以查看脚本执行时幕后发生了什么。换个环境，让我们用 PHP 来做下面的例子。

我的 SQL 中讨厌的错误总是让我出错。我认为我的脚本工作正常，并对其进行了测试，但是遗憾的是，无论什么应该插入数据库的东西都消失得无影无踪了。SQL 语句通常很难写，尤其是当您在其中嵌入变量时(尤其是当使用 VBScript 连接 SQL 字符串并被引号混淆时)。当我在调试时，我总是先查看我的 SQL，因为这是我最容易出错的地方。

**第一步。**始终将您的 SQL 字符串赋给一个变量，以便您可以轻松地添加一行来将其打印到浏览器。

例如，不要写:

```
$result = mysql_query("SELECT * FROM TableName WHERE foo=$myFoo");
```

而是写:

```
$sql = " SELECT * FROM TableName WHERE foo=$myFoo";    

$result = mysql_query($sql);
```

**第二步。**出于调试目的，您可以临时将以下代码行添加到代码中:

```
// hmm, why isn't this working?     

// let's see what that SQL string ended up looking like    

echo $sql;
```

当您运行脚本时，您甚至可以将脚本输出的 SQL 语句剪切并粘贴到浏览器窗口中，并在命令行启动 MySQL(假设在本例中，您使用的是 MySQL)。

将查询粘贴到 MySQL 中，查看查询结果是否符合预期，或者数据库中的数据状态是否有问题。如果是这种情况，我们知道我们必须在脚本或数据模型中的其他地方寻找，找出为什么我们期望在数据库中看到的数据不在那里。

或者，当我们将 SQL 输出到浏览器时，我们可能会发现一个简单的语法错误；或者，我们可能会发现我们嵌入到 SQL 字符串中的变量的值与预期的不同，或者根本没有值！这通常是因为我们无意中对想要嵌入 SQL 的变量使用了错误的名称。

也许这只是一个打字错误，或者也许我们认为要发送到脚本的一些数据(在表单的 POST 数据中或在 URL 查询字符串中)没有到达我们期望的带有 name 属性的脚本。那么，我们如何知道我们期望从客户端浏览器发送到脚本的数据实际存在呢？您猜对了:我们打印出调试脚本中的值！

例如，假设我们有一个关于 page1.php 的表单，它有一个输入字段:

```
<form action="page2.php" method="POST">    

<input type="text" name="firstName"></input>    

</form>
```

现在，在 page2.php 上，我们要检查这个值是否确实存在。所以我们在脚本的开头添加了一些调试代码。

```
// start debugging code -- don't forget to remove!    

echo '<br />firstName :  ';    

echo $_POST['firstName'];    

echo '<br />';    

// end debugging code
```

完成调试后，不要忘记从脚本中删除所有调试代码！

这就是本周的全部内容，下次再见，不要忘记发送您的问题，以便我及时处理！

传播爱！

设计博士

## 分享这篇文章