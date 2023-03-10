# Editize 让内容管理变得简单！

> 原文：<https://www.sitepoint.com/management-made-editize/>

**内容管理是当今组织面临的最常见的 Web 相关问题之一。每个人都认为保持网站内容的时效性和相关性很重要。过时的信息反映了一个组织的不良形象。内容管理系统允许指定的用户维护和更新他们各自的站点区域。**

许多专有内容管理系统(CMS)的价格在 5 万到 10 万美元之间，甚至更多。因此，许多开发人员选择开发自己的解决方案来满足他们的特定需求。我推荐[使用 PHP & MySQL](https://www.sitepoint.com/books) 建立你自己的数据库驱动网站，这是一本非常实用的书，可以帮助你了解这项任务中涉及的不同问题。

无论您使用哪种 CMS 解决方案，都必须有某种方式让用户格式化他们提交的内容以更新网站。有几个选项，其中之一是教每个用户基本的 HTML 标签。这种方法很麻烦，不能很好地扩展，并且会导致代码错误和其他问题。另一个选择是以编程的方式格式化提交的内容(在上面提到的那本书的第 7 章中讨论)。

但是，最好的解决方案是使用 TEXTAREA 标记的替换。理想情况下，该解决方案将为最终用户提供一个类似于文字处理器的熟悉环境，并允许用户使用点击式方法格式化文本(或其他内容)。

***日程上有什么？***

这篇教程记录了我最近参与的一个项目的进度，我是当地社区大学的网站管理员。首先，我会解释学院的需求，以及我们用来评估每个解决方案的标准。接下来，我将向您展示如何将我们选择的解决方案集成到您自己的站点中。最后，我将解释如何将解决方案嵌入到您站点的 CMS 中。

##### 案例研究

该学院需要一种方法，允许团队成员在短时间内每天更新网站的某些页面。与其让用户不断地向网站管理员提交这些微小的、详细的更改，不如为负责网站特定区域的每个人提供一种能够自己进行更改的方式，这样更有意义。

由于许多团队成员没有经过技术培训，我知道需要替换 TEXTAREA 标签，以便为这些用户提供一个有效且用户友好的界面，他们可以通过该界面提交他们的内容更改。

当时市场上的一些解决方案只适用于 ASP，因此不是一个选项，因为学院使用的服务器只支持 PHP。其他解决方案涉及许可结构，随着越来越多的团队成员开始使用该系统，这将变得相当昂贵——这一事实否定了通过分布式创作和发布获得的好处。

通常，声称易于使用的产品需要通过厚重的说明书来解释。社区大学不想为员工提供大量培训的麻烦。他们想要一个便宜、简单的解决方案。

我的研究最终引导我编辑了 T1。这个产品是一个明显的赢家，因为它很便宜，并且支持包括 PHP 在内的各种脚本语言。许可也非常灵活(事实上，149 美元的初始成本包括无限数量的最终用户)。令我惊讶的是，Editize 真的很容易使用。

***编辑用户界面***

正如您在下面看到的，Editize 直接加载到网页中(作为一个 applet)，并为最终用户提供一个舒适的文字处理器类型的环境。作为网站的站长，Editize 也给了我完全控制编辑功能的权利，我可以放心地给 CMS 的用户提供这些功能。如果我愿意，可以禁用每个功能。

![1069_editize1](img/918282361e9e0dd1e4bfceb4cb1d5ff4.png)

Editize 提供了完整的编辑功能:

*   段落样式
*   段落对齐
*   项目符号和编号列表
*   粗体、斜体和带下划线的文本
*   关键字和短语的可配置突出显示文本颜色
*   技术文章中以单倍行距字体显示的内联代码
*   完全支持超链接(带有可预配置的 URL)
*   完全支持图像
*   插入和修改图像
*   插入和编辑表格
*   面向高级用户的选项卡式 WYSIWYG/代码视图界面

将 Editize 整合到您的内容管理系统中的一个关键优势是，您的站点的“外观和感觉”得到了保护，同时用户可以在给定的网页中编辑和格式化预定义的区域。例如，Editize 的“高亮文本”选项可以由一个 CSS 设置来控制，该设置强制将高亮文本设置为预定义的颜色。

到目前为止，一切顺利。我喜欢我所看到的，所以我决定给 Editize 一个尝试。

##### 履行

***第一步——下载软件***

该软件的免费试用版可在 www.editize.com/download.php 获得，下载该软件当然不成问题。

这是值得一提的，因为一些软件供应商只允许单次下载，如果由于某种原因下载不成功，你最终不得不打电话给公司等。Editize 的情况并非如此。如果出于某种原因，你需要重复下载，或者你想下载最新版本的软件，这是一个没有麻烦的过程。只需登录会员区，下载软件。

***第二步——将文件复制到您的服务器***

在服务器上为编辑文件创建一个文件夹，例如 http://www.yoursite.com/editize/,，并将文件上传到该文件夹。以下是下载中包含的文件的描述:

*   **Editize . jar**–Editize Java 库文件。这包含 Editize 小程序。
*   **editize.php**–PHP 的 Editize API。这个 PHP 库将生成在几乎任何浏览器或操作系统上显示 Editize applet 所需的 HTML 和 JavaScript 代码。
*   Kunststoff . jar–Kunststoff 外观&感觉。极大地改进了 Editize 用户界面的外观，但是增加了 43KB 的 Java 文件，供用户浏览器在第一次访问您的站点时下载。如果删除该文件，Editize 将在没有任何视觉增强的情况下运行，并且在慢速调制解调器连接上加载速度会稍快。
*   osx.html——一个包含 JavaScript 的小网页，允许 Editize 像 Mac OS X 浏览器上的表单字段一样工作。
*   index.php:一个简单的 PHP 脚本，演示了如何创建与大多数浏览器兼容的编辑表单域。*
*   **index-mac.php**–一个 PHP 示例脚本，演示了如何创建与大多数浏览器兼容的 Editize 表单域，包括 Mac OS 10.1 上的 Internet Explorer 和 Mozilla。*
*   **imglist.php**——一个简单的 PHP 脚本，它将列出你的 Web 服务器的给定目录中的图像，供编辑用作图像列表。*

***第三步——将许可文件复制到服务器***

这个文件将被命名为 editize.hostname.lic 将这个文件放在与其他文件相同的文件夹中。

***第四步——将 Editize 整合到你的更新页面中***

任何使用 Editize 的页面都必须知道在哪里可以找到它。提供这些指示非常简单，只需在任何使用 Editize 的页面上添加这一行:

```
<?php require('path/to/editize.php'); ?>
```

这一行将指向您之前创建的文件夹中的 editize.php 文件。然后，将使用下面的代码，而不是典型的 HTML 表单。

```
<form action="..." method="post">  

<?php  

$ed = new Editize;  

$ed->name = 'example';  

$ed->display('<p align="center">This is the document that   

Editize will edit.</p>');  

?>  

<input type="submit" value="Submit" />  

</form>
```

Editize 下载中提供的说明包括关于设置配置属性的更多信息，以及一些其他调整。当然，如果您使用的是 ASP 或其他脚本语言，说明会稍有不同。所有的说明都写得很好，直截了当，易于理解，所以你不会有任何问题，按照他们定制系统，以满足你的需求。

##### 结果

*   最终的结果是一个非常光滑的界面，最终用户使用起来非常舒服。Editize 为他们提供了一个非常熟悉的点击式环境，类似于大多数文字处理软件，因此用户更新网站内容没有任何问题。
*   从开发的角度来看，所涉及的工作要少得多，因为 Editize 是一个开箱即用的解决方案。因此，我可以避免处理 php 代码中的各种格式问题。
*   将设计从用户提交的内容中分离出来，确保了网站的一致性和专业性。
*   最终用户几乎不需要培训。
*   最初的投资很少。

试验成功了！网站经理很高兴，我要求试用该软件的团队成员也非常喜欢使用它。社区大学决定继续将该产品与其 CMS 整合。

##### 在内容管理系统中利用编辑

使用 Editize 背后的基本思想是允许最终用户格式化他们的内容，并提交该内容以存储在数据库中。Editize 不是更新静态网页，而是更新数据库，从而自动更新使用该特定数据的每个页面。

每当用户请求给定的页面时，Web 服务器从数据库中检索适当的数据，并在将页面返回给用户之前将其插入到网页中。生成的网页将显示格式良好的内容。下图简要总结了这种互动:

![1069_editize2](img/37c879eb73c94353d7bcca1024442eff.png)

***数据***

有许多类型的数据可以存储在内容管理系统中，内容管理系统有许多共同的功能。一般来说，数据变化越频繁，就越适合包含在系统中。

出于本教程的目的，我们将研究一个非常简单的系统，该系统可用于更新 Web 页面的主要内容区域，并显示页面上次更新的日期。

为了将这个过程限制在授权用户，我们将包括一个数据库驱动的用户认证系统。

我们的用户认证表包含 4 个字段:

![1069_table1](img/47bccd94440089f8afc9511f27f87116.png)

内容将存储在包含以下字段的内容表中:

![1069_table2](img/c23288ead6c916365ad97bb16518619a.png)

***用户登录页面***

用户登录页面如下所示:

![1069_editize3](img/7f2460baf2b3357c8eaea905f601ac50.png)

首先，我们需要创建一个允许用户登录并访问管理页面的页面。下面的 PHP 代码就是这样一个页面的例子。请注意，这段代码将数据提交给一个名为 authuser.php 的页面:

```
<HTML>   

<HEAD>   

<TITLE>Login</TITLE>   

</HEAD>   

<BODY>   

<H1>Login to Secret Area</H1>   

<FORM METHOD="post" ACTION="authuser.php">   

<P><STRONG>Username:</STRONG><BR>   

<INPUT TYPE="text" NAME="username"    

SIZE=25 MAXLENGTH=25></p>   

<P><STRONG>Password:</STRONG><BR>   

<INPUT TYPE="text" NAME="password"    

SIZE=25 MAXLENGTH=25></p>   

<P><INPUT TYPE="SUBMIT" NAME="submit"    

VALUE="Login"></P>   

</FORM>   

</BODY>   

</HTML>
```

***验证页面***

一旦用户提交数据，authuser.php 将根据存储在 authuser(用户身份验证)数据库表中的值来验证信息。authuser.php 列在下面:

```
<?   

if ((!$username) || (!$password)) {   

header("Location: http://localhost/login.html");   

exit;   

}    

//The code above will check to make sure the username    

and password were not left blank, if either are blank    

the user will be sent back to the login page.   

$db_name = "testDB";   

$table_name = "authuser";   

$connection = @mysql_connect("localhost", "baggins",    

"adventure")    

or die("Couldn't connect.");   

$db = mysql_select_db($db_name, $connection)   

or die("Couldn't select database.");   

$sql = "SELECT * FROM $table_name   

WHERE username = "$username" AND password =    

password("$password")   

";    

$result = mysql_query($sql)    

or die ("Can't execute query.");    

$num = mysql_numrows($result);    

if ($num != 0) {    

// Since $num holds the number of returned rows, we    

know that a match was found if it is not = 0 and we    

then set a cookie for the authorized user.   

$cookie_name = "authorize";   

$cookie_value = "ok";   

$cookie_expire = "";   

$cookie_domain = "";   

setcookie($cookie_name, $cookie_value, $cookie_expire,    

"/" , $cookie_domain, 0);   

// This cookie will be valid for the browser session   

$display_area = "   

<p><strong>Secret Menu:</strong></p>   

<ul>   

<li><a href="secret_page1.php">secret page 1</a>   

<li><a href="secret_page2.php">secret page 2</a>   

</ul>   

";   

// * Note that you could also display a different    

menu depending on which user signed in.   

} else {    

header("Location: http://localhost/login.html");   

exit;   

}    

?>    

<HTML>   

<HEAD>   

<TITLE>Secret Area</TITLE>   

</HEAD>   

<BODY>   

<? echo "$display_area"; ?>   

</BODY>   

</HTML>
```

***菜单选择页面***

在上面代码生成的页面上，用户将能够选择他们想要编辑的页面:

![1069_editize4](img/33cb328a7632a567d3353766b9d9a737.png)

***编辑页面***

做出选择后，用户将看到 Editize 页面，在这里他们可以使用 Editize applet 提供的内置功能轻松地编辑和格式化他们的内容。

![1069_editize5](img/b4c42066ac9de3dcd3b99ffd042cac25.png)

该内容编辑页面的代码如下:

```
<?php require('editize.php'); ?>    

<?    

if ($auth == "authorize") {    

$msg = "<p>You may now perform updates</p>";    

} else {    

header("Location: http://localhost/login.htm");    

exit;    

}    

mysql_connect('localhost','baggins','adventure');    

mysql_select_db('content');    

$sql_result = mysql_query("SELECT pagebody FROM     

content WHERE id = 20");    

$content = mysql_fetch_array($sql_result);    

$pagecontent = $content["pagebody"];    

?>    

<HTML>    

<HEAD>    

<TITLE>Your Update Page</TITLE>    

</HEAD>    

<BODY>    

<form name="form1" method="post" action="update.php">     

// Note that the form submits the data to update.php    

The following code loads the Editize applet, and     

is used to replace the textarea tag. The applet     

is also set here to initially display the old content     

(stored in $pagecontent) which is to be updated.    

<?php    

$ed = new Editize;    

$ed->name = 'pagecontent';    

$ed->display("<p align="center">$pagecontent</p>");    

?>    

<input type="submit" name="Submit" value="Update Record">    

</form>    

</BODY>    

</HTML>
```

***数据库更新页面***

当用户编辑完内容后，他们将提交数据。表单被提交到 update.phpm 文件，该文件更新内容数据库。让我们看看如何:

```
<?    

if (!$pagecontent) {    

header( "Location: http://localhost/folder/update.php");    

exit;    

}    

$db_name = "content";    

$table_name = "content";    

$cnx = @mysql_connect("localhost", "bilbo", "adventure")     

or die("Couldn't connect.");    

$db = @mysql_select_db($db_name, $cnx) or die("Couldn't     

select database.");    

$dateupdated = date("l dS M. Y h:i a");     

mysql_query("UPDATE content SET pagebody = '$pagecontent'     

lastupdated = '$dateupdated' WHERE id = 20");    

// The code above will update the database with     

the newly submitted content and also capture the     

current date and time and store it in the variable     

$dateupdated which will also be stored in the     

database and eventually displayed on the final page.    

?>    

<HTML>    

<HEAD>    

<TITLE>Update Record</TITLE>    

</HEAD>    

<BODY>    

<h3>Your page has been updated.</h3>    

<P><a href="updated_page.php">View changes here</a></P>    

// The above option lets the user view the page     

they have just updated     

</BODY>    

</HTML>
```

***动态内容页面***

那么，我们的格式化内容是如何出现在网站上的呢？以下是可能使用新格式化数据(现在存储在数据库中)的动态页面示例:

![1069_editize6](img/472f2cf3590344928b893d4e3b392f02.png)

上面页面的代码是:

```
<HTML>    

<HEAD>    

<TITLE>Dynamic Content Page</TITLE>    

</HEAD>    

<h3>Dynamic Content Page</h3>    

<?php    

mysql_connect('localhost','baggins','adventure');    

mysql_select_db('contentdb');    

$sql_result = mysql_query("SELECT pagebody, lastupdated     

FROM content WHERE id = 20");    

$content = mysql_fetch_array($sql_result);    

$pagecontent = $content["pagebody"];    

$displaydate = $content["lastupdated"];    

echo "$pagecontent" ;    

echo "<p>&nbsp; </p>";    

echo "<p>Date Last Updated: $displaydate</p>" ;    

?>    

</BODY>    

</HTML>
```

就这么简单！为你的 CMS 创建一个允许用户轻松格式化他们提交的内容的界面是一件轻而易举的事。祝你网站的 CMS 好运。

## 分享这篇文章