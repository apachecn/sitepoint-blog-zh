# 用 PHP 构建一个基于 XML 的内容管理系统

> 原文：<https://www.sitepoint.com/management-system-php/>

与大多数开发人员一样，您可能听说过很多关于 XML 和内容管理系统的事情。然而，很可能你只是接触到了没有实践知识基础的理论讨论。

这个循序渐进的教程将让您开始使用一个非常基本的基于 XML 的内容管理系统(或 CMS)。我在这里没有足够的空间来讨论一个非常复杂的例子，但是如果幸运的话，这里介绍的概念和想法将为您提供自己探索的必要跳板。

开始之前，请注意一些事项。我使用 DOMXML 函数和会话来使这个应用程序工作，所以您需要使用 PHP 4.2.1 或更高版本，并打开 DOM 支持。

另外，不要忘记[下载本教程的所有代码](https://www.sitepoint.com/examples/xmlcms/xmltest.zip)。会派上用场的！

##### XML 和 CMSs 简介

让我先给你一些关于 CMSs 和 XML 的背景知识。我假设您已经在其他地方阅读或听说过这两种技术，所以我将简短地讨论一下。

XML 代表可扩展标记语言，是 SGML(标准化通用标记语言)的子集。XML 与 HTML 非常相似，除了在 XML 中你可以定义自己的标签。当您需要非常密切地跟踪某些类型的数据时，这种生成定制文档的能力非常方便，尤其是在出版和电子商务领域。

例如，对于您在在线杂志上发表的任何给定文章，您可以为作者姓名、署名(如果与作者姓名不同)、字数、发表日期、标题或大标题、文章正文、关键字等创建标签。正如您将在本文后面看到的，将您的文章分解成这些 XML 标签或节点允许 CMS 对它保存的所有文章做有用的事情。

本质上，XML 允许您用每个文档制作一个小型数据库，而没有许多数据库给 Web 项目带来的开销和费用。

CMS 用于创建、发布和维护网站上的内容。它通常由以下几部分组成:

*   一个数据后端(XML 或数据库表)，包含您的所有文章、新闻故事、图像和其他内容。
*   一个数据显示组件，通常是模板或其他页面，您的文章、图像等可以放在上面。是由 CMS 为网站访问者“绘制”的。
*   一个数据管理组件，通常是易于使用的 HTML 表单，允许站点管理员在某种安全的工作流中创建、编辑、发布和删除文章。CMS 的数据管理部分通常是最复杂的，也是您可能花费大部分开发时间的地方。

在过去的十年中，不同的脚本语言被用来创建 CMSs，包括 Perl/CGI、ASP、TCL、JSP、Python 和 PHP。每种语言都有自己的优缺点，但是我将重点介绍使用 PHP 和 XML 来构建一个简单的内容管理系统。

##### 要求

构建任何类型的 CMS，无论是数据库支持的还是 XML 支持的，都需要收集定义项目基本需求的信息。尽管许多开发人员一想到这种练习就抱怨，但是一组定义良好的需求可以让您的生活轻松很多。

因为这是一个相当简单的项目，而且因为你要自己去做，一个简单的需求列表就可以了。

我们需要收集什么样的需求？本质上，需求分为三个主要类别:

1.  What kind of content will the CMS handle? Furthermore, how is each type of content broken down? (The more complete your understanding of this issue, the easier it’ll be to create your XML files.)
2.  Who will be visiting the site, and what behaviors do these users expect to find? (For example, will they want to browse a hierarchical list of articles, search for articles by keyword, and see links of related articles?)
3.  网站管理员需要做什么？(例如，安全登录、创建内容、编辑内容、发布内容和删除内容。如果你的内容管理系统有管理用户的角色——比如站点管理员、编辑、作者——那么你的系统会变得更加复杂。

为了避免本文变得复杂，我将为我的简单的基于 XML 的 CMS 选择非常基本的需求:

*   The CMS will handle the management of articles only. Each article will have a:
    *   唯一 ID
    *   头条新闻
    *   作者姓名
    *   作者电子邮件
    *   摘要
    *   可以包含段落和一级副标题的文章正文
    *   状态(“进行中”或“实时”)
    *   关键词列表
*   Site visitors will be able to view “live” articles listed by author name. They will also be able to perform a search on headline and keywords.
*   网站本身将由以下页面组成:
    *   一个主页，列出了网站上发表的五篇文章和一个搜索功能。
    *   一次显示一篇文章的文章详细信息页面。
    *   一个搜索结果页面，将按作者、关键字或输入搜索引擎的字符串列出所有文章。
    *   站点管理员可以安全登录，添加更多管理员，并且可以轻松地添加新的 XML 文件、编辑现有文件、发布文件和删除文件。

##### 定义 XML 文件

每当我构建一个 CMS 时，我都试图首先定义数据后端，因为我发现所有其他元素都是从那里级联的。在这种情况下，我们的数据后端是一个 XML 文件存储库，因此我们需要定义我们的文件应该如何构造。

XML 文件由嵌套的开始和结束标记组成，每个标记定义了一些信息块。XML 文件还必须包含一个包含所有其他标签的“根”开始和结束标签。

因为我们在这个例子中只处理文章，所以我们的“根”开始和结束标签应该是:

```
<article>  

</article>
```

我们在发现阶段识别的所有其他标记必须在这两个标记之间。基于该列表，我们的文章文件可能会如下所示:

```
<?xml version="1.0"?>  

<article id=\"xml-howto-1\">  

<headline>Writing XML Articles</headline>  

<status>in progress</status>  

<author>Joe Author</author>  

<email>jauthor@example.com</email>  

<abstract>A short article about writing XML articles.</abstract>  

<keywords>XML,articles,how to</keywords>  

<para-intro>Intro paragraph here.</para-intro>  

<para-main>Main paragraph.</para-main>  

<para-conclusion>Conclusion paragraph.</para-conclusion>  

</article>
```

关于我们的文章示例，有几点需要注意:

*   通常，您会创建一个 DTD 或模式来定义文章的外观。创建有效的 dtd 或模式本身就是一个完整的教程，所以在这里，我使用了一个简单的方法。这比开发一个模式要快，但是请注意，您可能会遇到问题，因为您的示例可能太简单了。另外，如果您想验证 XML 文档，您需要创建一个 DTD。
*   你注意到文章标签中的“`id=`”部分了吗？这叫做属性。我们将在后面讨论为什么我们在系统中创建的每篇文章都有一个惟一的 id 属性是非常重要的。
*   因为我们想保持这个例子的简单，我将假设我们的文章每篇只有三个段落，我们稍后构建的表单将适应这个结构。在未来的教程中，我们将构建一个更加动态的结构，将段落标签嵌套到一个`<body>`标签中。

##### 构建管理工具

我们基于 XML 的 CMS 的管理工具将只是几个 PHP 页面，允许管理员登录并创建、编辑、发布和删除 XML 文章。管理员还可以创建、编辑和删除其他管理员。

***登录页面***

登录页面非常简单。它包括一个简单的 HTML 表单，允许管理员输入用户名和密码。这个页面上的 PHP 逻辑需要根据管理员列表检查输入的值。如果我们有足够的时间，我会带您构建一个包含这些值的 admin.xml 文件。但是现在，我们将采取在 PHP 中嵌入值的捷径。

以下是 login.php 页面的代码:

```
<?php  

session_start();  

?>  

<html>  

<title>Please Log In</title>  

<body>  

<form name="login" method="post" action="verify.php">  

<table width="290" border="0" align="center" cellpadding="4" cellspacing="1">  

    <tr>   

      <td colspan="2"><div align="center">Please log in</div>   

      </td>  

    </tr>  

    <tr>   

      <td width="99" bgcolor="#CCCCCC"> <div align="right">login</div></td>  

      <td width="181" bgcolor="#CCCCCC"> <div align="left">   

          <input name="username" type="text" id="username">  

        </div></td>  

    </tr>  

    <tr>   

      <td bgcolor="#CCCCCC"> <div align="right">password</div></td>  

      <td bgcolor="#CCCCCC"> <div align="left">   

          <input name="password" type="password" id="password">  

        </div></td>  

    </tr>  

    <tr>   

      <td colspan="2"><div align="center">   

          <input type="submit" name="Submit" value="Submit">  

          &nbsp;   

          <input name="reset" type="reset" id="reset" value="Reset">  

        </div></td>  

    </tr>  

  <tr>  

  <td colspan=2 align=center>  

  <?php echo $_SESSION[\"error\"]; ?>  

  </td>  

  </tr>  

  </table>  

</form>  

</body>  

</html>
```

请注意，表单的动作被设置为一个名为 verify.php 的页面。verify.php 页面非常简单。它所做的只是检查传入的用户名和密码值是否与存储的用户名/密码值相匹配。

如果两者都匹配，PHP 将设置一个会话变量，并将用户重定向到管理页面。如果没有，PHP 将用户送回 login.php 页面，并显示一个包含错误消息的特殊会话变量。以下是 verify.php 页面的代码:

```
<?php  

session_start();  

$user = 'tom';  

$passw = 'test';  

if (($_POST["username"] == $user) and ($_POST["password"] == $passw)){  

  $_SESSION["login"] = "true";  

  header("Location:adminindex.php");  

  exit;  

} else {  

  $_SESSION["error"] = "<font color=red>Wrong username or password. Try again.</font>";  

  header("Location:login.php");  

}  

?>
```

因为任何人都可以输入管理页面的 URL，所以我们必须增加额外的安全性。在每页的顶部，我们需要检查会话变量“login”的值是否设置为“true”如果不是，让人们回到 login.php 页面；如果是，显示他们的管理页面。

***管理员索引页面***

我们管理工具的第一页是 adminindex.php。该页面列出了站点上当前显示的所有 XML 文章，允许您编辑或删除它们，并更改它们的状态(例如，发布它们)。它还允许您创建新的 XML 文章。

这个页面的代码非常简单和紧凑。我们想包括一个链接到 createArticle.php 网页。我们希望打开 xml/目录(我们将在其中存储所有文章)，取出每个文件的名称，并将这些名称传递到 editArticle.php 页面的链接中。我们将为我们的 delArticle.php 页面做同样的事情。

下面是 adminindex.php 的代码:

```
<?php   

session_start();   

if ($_SESSION["login"] != "true"){   

  header("Location:login.php");   

  $_SESSION["error"] = "<font color=red>You don't have privileges to see the admin page.</font>";   

  exit;   

}   

?>   

<h1>Welcome to the Admin Index Page</h1>   

<a href="createArticle.php">Create New XML Article</a><br><br>   

<table border=0 cellspacing=0 cellpadding=3 width="85%">   

<tr valign=top>   

<td width="75%">   

<table border=1 cellspacing=0 cellpadding=2>   

<?php   

$dh = opendir('./xml/');   

while ($file = readdir($dh)){   

  if (eregi("^..?$", $file)) {   

    continue;   

  }   

  echo "<tr valign=top><td width="80%">";   

  echo "<a href="editArticle.php?file=".$file . "">".$file . "</a></td>";   

  echo "<td width="20%">";   

  echo "<a href="delArticle.php?file=" .$file . "">delete</a>";   

  echo "&nbsp;</td></tr>";   

}   

?>   

</table>   

</td></tr></table>
```

***创建文章页面***

createArticle.php 页面非常重要，它允许站点管理员在站点上创建新的 XML 文章。这只是一个简单的表单，允许站点管理员输入相关信息。每个表单字段都映射到我们前面已经弄清楚的 XML 文档结构。

该表单还将检查以确保用户输入了 ID——如果没有文章 ID，网站的大部分功能都将无法运行。

以下是该页面的代码:

```
<?php   

session_start();   

if ($_SESSION["login"] != "true"){   

  header("Location:login.php");   

  $_SESSION["error"] = "<font color=red>You don't have privileges to see the admin page.</font>";   

  exit;   

}   

?>   

<html>   

<head>   

<title>Create an XML Article</title>   

<script>   

function isReady(form){   

  if(form.id.value == "") {   

    alert("Please enter an ID!");   

    return false;   

  }   

}   

</script>   

</head>   

<body>   

<h1>Create an XML Article</h1>   

<a href="adminindex.php">Cancel</a><br><br>   

<form name="createArticle" action="addArticle.php" method="post" onSubmit="return isReady(this)">   

  <table border=1 cellspacing=0 cellpadding=3>   

    <tr valign=top>    

      <td width="135">Article ID</td>   

      <td width="634"> <input name="id" type="text" id="id"> <br> <font size="-1">(no    

        spaces, must be unique)</font></td>   

    </tr>   

    <tr valign=top>   

      <td>Status</td>   

      <td>In Progress <input type="hidden" name="status" value="in progress"></td>   

    </tr>   

    <tr valign=top>    

      <td>Headline</td>   

      <td> <input name="headline" type="text" id="headline" size="60"></td>   

    </tr>   

    <tr valign=top>    

      <td>Author Name</td>   

      <td> <input name="name" type="text" id="name" size="30"></td>   

    </tr>   

    <tr valign=top>    

      <td>Author Email</td>   

      <td> <input name="email" type="text" id="email" size="30"></td>   

    </tr>   

    <tr valign=top>    

      <td>Keywords</td>   

      <td> <p>    

          <input name="keywords" type="text" id="keywords">   

          <font size="-1"><br>   

          </font><font size="-1">(separate keywords with commas)</font> </p></td>   

    </tr>   

    <tr valign=top>    

      <td>Abstract</td>   

      <td><textarea name="abstract" cols="50" rows="5" id="abstract"></textarea></td>   

    </tr>   

    <tr valign=top>    

      <td> <p>Article Body<br>   

        </p></td>   

      <td> <p>Intro paragraph:</p>   

        <p>    

          <textarea name="body[intro]" cols="70" rows="10" wrap="soft" id="body[intro]"></textarea>   

        </p>   

        <p>Main paragraph:</p>   

        <p>    

          <textarea name="body[main]" cols="70" rows="10" wrap="soft" id="body[main]" ></textarea>   

        </p>   

        <p>&nbsp;</p>   

        <p>Conclusion paragraph:</p>   

        <p>    

          <textarea name="body[conclusion]" cols="70" rows="10" wrap="soft"></textarea>   

        </p></td>   

    </tr>   

    <tr valign=top>    

      <td colspan=2> <div align="center">    

          <input type="submit" name="Add Article" value="Add Article">   

          &nbsp;    

          <input name="reset" type="reset" id="reset" value="Reset">   

        </div></td>   

    </tr>   

  </table>   

</form>   

</body></html>
```

该表单的动作被设置为 addArticle.php 页面，该页面使用 DOMXML 函数根据表单中的信息创建一篇 XML 文章。因为这有点复杂，所以我将一节一节地检查代码。

代码的第一部分初始化我们的新 XML 文件，设置版本并创建根节点，即`<article>`。

```
<?php   

//create document root   

$doc = domxml_new_doc("1.0");   

$root = $doc->create_element("article");   

$root = $doc->append_child($root);
```

接下来，我们向`<article>`节点添加一个 id 属性。但是，首先，我们需要确保用户选择了一个惟一的值，因为 id 将被用作文件名。我们通过查看 xml 目录中的所有文章来执行这项检查。如果我们从表单中找到一个包含 id 的文件名(存储在传入的$id 变量中)，那么我们在我们的 id 中添加一个“`â€“`”和从 UNIX 纪元开始以来的秒数。虽然没有警告就更改用户输入被认为是不好的行为，但是现在这样做就可以了。最后，我们将 id 属性添加到`<article>`节点。

```
//add ID attribute   

//FIRST, let's make sure that the id they chose isn't going to overwrite a file!   

$dh = opendir('./xml/');   

while ($file = readdir($dh)){   

  $string = $id . \\".xml\\";   

  if (eregi("^\.\.?$", $file)) {   

    continue;   

  }   

  if (eregi($string, $file)){   

    $time = date("U"); //num of seconds since unix epoch   

    $id = $id . "-" . $time;   

  }   

}   

$root->set_attribute('id', $id);
```

既然我们已经创建了根节点，那么是时候按顺序创建该节点的每个子节点了。首先是`<headline>`。注意，`<headline>`节点是`<article>`的子节点，标题文本是`<headline>`的子节点。

```
//create headline   

$head = $doc->create_element("headline");   

$head = $root->append_child($head);   

$htext = $doc->create_text_node($headline);   

$htext = $head->append_child($htext);
```

对于`<author>`、`<email>`、`<abstract>`和`<status>`节点也是如此:

```
//create author name   

$aname = $doc->create_element("author");   

$aname = $root->append_child($aname);   

$atext = $doc->create_text_node($name);   

$atext = $aname->append_child($atext);   

//create author email   

$mail = $doc->create_element("email");   

$mail = $root->append_child($mail);   

$mtext = $doc->create_text_node($email);   

$mtext = $mail->append_child($mtext);   

//create abstract   

$abs = $doc->create_element("abstract");   

$abs = $root->append_child($abs);   

$abstext = $doc->create_text_node($abstract);   

$abstext = $abs->append_child($abstext);   

//create status, always in progress when first created   

$stat = $doc->create_element("status");   

$stat = $root->append_child($stat);   

$stat_text = $doc->create_text_node($status);   

$stat_text = $stat->append_child($stat_text);
```

接下来是关键词:

```
//create keyword listing   

$keylisting = $doc->create_element("keywords");   

$keylisting = $root->append_child($keylisting);   

$ktext = $doc->create_text_node($keywords);   

$ktext = $keylisting->append_child($ktext);
```

这些段落可以作为一个数组来处理，因为它们是作为`body[lead]`、`body[second]`等来传递的。我们的 PHP 代码设置为使用这些传入的键创建 para 标签；我们将以名为`<para-lead>`、`<para-second>`等等的标签结束。

```
//create paras   

if (is_array($body)){   

  foreach ($body as $K => $V){   

    if ($V != ""){   

      $para = $doc->create_element("para-$K");   

      $para = $root->append_child($para);   

      $ptext = $doc->create_text_node($V);   

      $ptext = $para->append_child($ptext);   

      //$para->set_attribute('order', $K);   

    }   

  }   

}
```

最后，我们可以将整个 XML 树写入一个文件，使用 id 作为文件名，并将用户发送回 adminindex.php，在那里他们应该会看到刚刚创建的文件被添加到 XML 文章列表中。

```
//write to the file   

$filename = "./xml/".$id . ".xml";   

$doc->dump_file($filename, false, true);   

//send user back to adminindex   

header("Location:adminindex.php");
```

***编辑 XML 文章***

一般来说，编辑 XML 文章与创建文章非常相似，只是您必须将现有文章的节点加载到编辑表单中，然后将您的更改写出到文件中。

在本例中，我们不允许对文章的 id 属性进行任何更改，因为这将使应用程序的其余部分很难正常工作。限制对 id 属性的更改还允许我们在更新 XML 文件时执行一个简单的捷径——我们所要做的就是删除旧文件并用我们的新信息创建一个新文件。这很粗糙，但是快速有效。

这是 editArticle.php 页面的代码。注意，表单的动作被设置为 updateArticle.php。还要注意使用`extractText()`函数从 XML 对象中提取内容，并将其放入一个变量中，然后该变量可以被分配给一个表单元素。

```
<?php    

session_start();    

function extractText($array){    

  if(count($array) <= 1){    

    //we only have one tag to process!    

    for ($i = 0; $i<count($array); $i++){    

      $node = $array[$i];    

      $value = $node->get_content();    

    }    

    return $value;    

  }     

}      

if ($_SESSION["login"] != "true"){    

  header("Location:login.php");    

  $_SESSION["error"] = "<font color=red>You don't have privileges to see the admin page.</font>";    

  exit;    

}    

//pull in the XML file    

if ($file == ""){    

  echo "<h2>You didn't choose a file to edit!</h2>";    

  echo "<a href="adminindex.php">Go back to index and choose a file</a>";    

  exit;    

} else {    

  $filename = "./xml/".$file;    

  $xml = domxml_open_file($filename);    

  $root = $xml->root();    

  $id = $root->get_attribute("id");    

  $h_array = $root->get_elements_by_tagname("headline");    

  $headline = extractText($h_array);    

  $stat_array = $root->get_elements_by_tagname("status");    

  $status = extractText($stat_array);    

  $a_array = $root->get_elements_by_tagname("author");    

  $author = extractText($a_array);    

  $e_array = $root->get_elements_by_tagname("email");    

  $email = extractText($e_array);    

  $ab_array = $root->get_elements_by_tagname("abstract");    

  $abstract = extractText($ab_array);    

  $kl_array = $root->get_elements_by_tagname("keywords");    

  $keywords = extractText($kl_array);    

  $lead_array = $root->get_elements_by_tagname("para-intro");    

  $plead = extractText($lead_array);    

  $second_array = $root->get_elements_by_tagname("para-main");    

  $pmain = extractText($second_array);    

  $con_array = $root->get_elements_by_tagname("para-conclusion");    

  $pcon = extractText($con_array);    

  $statusList = array("live","in progress");    

?>    

<html>    

<title>Edit an XML Article</title>    

<body>    

<h1>Edit an XML Article</h1>    

<a href="adminindex.php">Cancel</a><br><br>    

<form name="createArticle" action="updateArticle.php" method="post">    

  <table border=1 cellspacing=0 cellpadding=3>    

    <tr valign=top>     

      <td width="135">Article ID</td>    

      <td width="634"> <?php echo htmlspecialchars($id); ?> <input type="hidden" name="id" value="<?php echo $id; ?>">     

      </td>    

    </tr>    

    <tr valign=top>    

      <td>Status</td>    

      <td>    

    <select name="status">    

    <?php    

      foreach ($statusList as $stat){    

      if($stat == $status){    

        echo "<option value="".$stat."" selected>$stat";    

      } else {    

        echo "<option value="".$stat."">$stat";    

      }    

    }    

    ?>    

    </select>    

    </td>    

    </tr>    

    <tr valign=top>     

      <td>Headline</td>    

      <td> <input name="headline" type="text" id="headline" value="<?php echo htmlspecialchars($headline); ?>" size="60"></td>    

    </tr>    

    <tr valign=top>     

      <td>Author Name</td>    

      <td> <input name="name" type="text" id="name" value="<?php echo htmlspecialchars($author); ?>"size="30"></td>    

    </tr>    

    <tr valign=top>     

      <td>Author Email</td>    

      <td> <input name="email" type="text" id="email" value="<?php echo htmlspecialchars($email); ?>"size="30"></td>    

    </tr>    

    <tr valign=top>     

      <td>Keywords</td>    

      <td> <input name="keywords" type="text" value="<?php echo htmlspecialchars($keywords); ?>">     

        <br> <font size="-1">(separate keywords with commas)</font> </td>    

    </tr>    

    <tr valign=top>     

      <td>Abstract</td>    

      <td><textarea name="abstract" cols="50" rows="5" id="abstract"><?php echo htmlspecialchars($abstract); ?></textarea></td>    

    </tr>    

    <tr valign=top>     

      <td> <p>Article Body<br>    

        </p></td>    

      <td> <p>Intro paragraph:</p>    

        <p>     

          <textarea name="body[intro]" cols="70" rows="10" wrap="soft" id="body[intro]"><?php echo htmlspecialchars($plead); ?></textarea>    

        </p>    

        <p>Main paragraph:</p>    

        <p>     

          <textarea name="body[main]" cols="70" rows="10" wrap="soft" id="body[main]" ><?php echo htmlspecialchars($pmain); ?></textarea>    

        </p>    

        <p>Conclusion paragraph:</p>    

        <p>     

          <textarea name="body[conclusion]" cols="70" rows="10" wrap="soft"><?php echo htmlspecialchars($pcon); ?></textarea>    

        </p></td>    

    </tr>    

    <tr valign=top>     

      <td colspan=2> <div align="center">     

          <input type="submit" name="Add Article" value="Add Article">    

          &nbsp;     

          <input name="reset" type="reset" id="reset" value="Reset">    

        </div></td>    

    </tr>    

  </table>    

</form>    

</body>    

</html>    

<?php    

}//end if-else    

?>
```

updateArticle.php 页面与 addArticle.php 页面非常相似，但是不需要在前面检查唯一的 id 属性。同样，在最后，PHP 代码将删除现有文件，然后将新的 XML 信息结构转储到一个新创建的同名文件中。这节省了我们尝试将编辑插入适当节点的大量时间。

```
<?php    

//create document root    

$doc = domxml_new_doc("1.0");    

$root = $doc->create_element("article");    

$root = $doc->append_child($root);    

//add ID attribute    

$root->set_attribute('id', $id);    

//create headline    

$head = $doc->create_element("headline");    

$head = $root->append_child($head);    

$htext = $doc->create_text_node($headline);    

$htext = $head->append_child($htext);    

//create author name    

$aname = $doc->create_element("author");    

$aname = $root->append_child($aname);    

$atext = $doc->create_text_node($name);    

$atext = $aname->append_child($atext);    

//create author email    

$mail = $doc->create_element("email");    

$mail = $root->append_child($mail);    

$mtext = $doc->create_text_node($email);    

$mtext = $mail->append_child($mtext);    

//create abstract    

$abs = $doc->create_element("abstract");    

$abs = $root->append_child($abs);    

$abstext = $doc->create_text_node($abstract);    

$abstext = $abs->append_child($abstext);    

//create keyword listing    

$keylisting = $doc->create_element("keywords");    

$keylisting = $root->append_child($keylisting);    

$ktext = $doc->create_text_node($keywords);    

$ktext = $keylisting->append_child($ktext);    

//create status, always in progress when first created    

$stat = $doc->create_element("status");    

$stat = $root->append_child($stat);    

$stat_text = $doc->create_text_node($status);    

$stat_text = $stat->append_child($stat_text);    

//create paras    

if (is_array($body)){    

  foreach ($body as $K => $V){    

    if ($V != ""){    

      $V = stripslashes($V);    

      $para = $doc->create_element("para-$K");    

      $para = $root->append_child($para);    

      $ptext = $doc->create_text_node($V);    

      $ptext = $para->append_child($ptext);    

    }    

  }    

}    

//write to the file (first delete existing one!)    

$filename = "./xml/".$id . ".xml";    

unlink($filename);    

$doc->dump_file($filename, false, true);    

//send user back to adminindex    

header("Location:adminindex.php");    

?>
```

***删除 XML 文章***

删除 XML 文件非常简单。您所要做的就是向 delArticle.php 页面传递一个文件名，取消该文件的链接，然后让用户返回到 adminindex.php 页面:

```
<?php     

session_start();     

if ($_SESSION["login"] != "true"){     

  header("Location:login.php");     

  $_SESSION["error"] = "<font color=red>You don't have privileges to see the admin page.</font>";     

  exit;     

}     

$dir = "./xml/";     

$filetoburn = $dir . $file;     

unlink($filetoburn);     

header("Location: adminindex.php");     

?>
```

至此，我们已经完成了 XML 支持的 CMS 的文章管理部分。我们已经构建了一个登录页面、一个管理索引以及用于添加、编辑和删除文章的页面。

在本文的下一部分，我们将构建网站的显示面，以便访问者可以阅读和搜索文章。

##### 构建显示面

既然我们已经定义了 XML 文章结构，并构建了一个非常简单、安全的管理工具来帮助我们创建、编辑、删除和发布文件，那么是时候构建为站点访问者显示文章的站点部分了。

让我们回顾一下我们的显示端要求:

*   网站访问者将能够查看按标题列出的“实时”文章。他们还可以对标题和关键词进行搜索。
*   网站本身将由以下页面组成:
    *   一个主页，列出了网站上发表的五篇文章和一个搜索功能。此外，网站访问者可以单击一个链接来显示所有文章。
    *   一次显示一篇文章的文章详细信息页面。
    *   一个搜索结果页面，将按作者、关键字或输入搜索引擎的字符串列出所有文章。

***首页***

主页，index.php，包含的代码基本上重复了我们在管理端创建的内容。它基本上遍历 xml/ directory 中的所有文章，打开每个文件并查找状态、标题和摘要。如果状态设置为“live”之外的值，那么循环会找到下一篇文章。

结果是显示在主页上的文章标题和摘要列表。每个链接都将网站访问者导向 showArticle.php 页面。

下面是代码(注意，我还包含了一个搜索小部件):

```
<?php     

function extractText($array){     

  if(count($array) <= 1){     

    //we only have one tag to process!     

    for ($i = 0; $i<count($array); $i++){     

      $node = $array[$i];     

      $value = $node->get_content();     

    }     

    return $value;     

  }      

}       

?>     

<html>     

<head>     

<title>Welcome to XMLTEST</title>     

<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1">     

</head>     

<body>     

<h1>Welcome to the XMLTEST site</h1>     

<p><br>     

  <a href="adminindex.php">Admin Login</a> </p>     

<form name="search" method="post" action="searchArticles.php">     

  Search articles:      

  <input name="search" type="text" id="search">     

  <input name="Search" type="submit" id="Search" value="Search">     

</form>     

<p>The following articles are available: </p>     

<table border=1 cellspacing=0 cellpadding=2 width=500>     

<?php     

$dh = opendir('./xml/');     

$fileCount = 0;     

while ($file = readdir($dh) and $fileCount <= 5){     

  if (eregi("^\.\.?$", $file)) {     

    continue;     

  }     

  $open = "xml/".$file;     

  $xml = domxml_open_file($open);     

  //we need to pull out all the things from this file that we will need to      

  //build our links     

  $root = $xml->root();     

  $stat_array = $root->get_elements_by_tagname("status");     

  $status = extractText($stat_array);     

  $ab_array = $root->get_elements_by_tagname("abstract");     

  $abstract = extractText($ab_array);     

  $h_array = $root->get_elements_by_tagname("headline");     

  $headline = extractText($h_array);     

  if ($status != "live"){     

    continue;     

  }     

  echo "<tr valign=top><td>";     

  echo "<a href="showArticle.php?file=".$file . "">".$headline . "</a><br>";     

  echo $abstract;     

  echo "</td></tr>";     

  $fileCount++;     

}     

?>     

</table>     

<br><a href="adminindex.php">Admin Login</a>     

</body>     

</html>
```

***显示文章页面***

showArticle.php 页面是展示个人文章的地方。

您学到的关于从 XML 结构中提取文本的所有知识也适用于此。基本上，您根据传递给页面的文件名打开 XML 文章。然后你取出你需要的每个适当的节点，并把它们作为变量存储起来。对于我们的段落，我们将它们存储在一个数组中，这样我们就可以用一个简单的 foreach 循环将它们打印出来。然后我们将变量打印到屏幕上。

代码如下:

```
<?php     

session_start();     

function extractText($array){     

  if(count($array) <= 1){     

    //we only have one tag to process!     

    for ($i = 0; $i<count($array); $i++){     

      $node = $array[$i];     

      $value = $node->get_content();     

    }     

    return $value;     

  }      

}       

//pull in the XML file     

if ($file == ""){     

  echo "<h2>You didn't choose a file to edit!</h2>";     

  echo "<a href="index.php">Go back to index and choose a file</a>";     

  exit;     

} else {     

  $open = "./xml/" . $file;     

  $xml = domxml_open_file($open);     

  $root = $xml->root();     

  $id = $root->get_attribute("id");     

  $h_array = $root->get_elements_by_tagname("headline");     

  $headline = extractText($h_array);     

  $stat_array = $root->get_elements_by_tagname("status");     

  $status = extractText($stat_array);     

  $a_array = $root->get_elements_by_tagname("author");     

  $author = extractText($a_array);     

  $e_array = $root->get_elements_by_tagname("email");     

  $email = extractText($e_array);     

  $ab_array = $root->get_elements_by_tagname("abstract");     

  $abstract = extractText($ab_array);     

  $kl_array = $root->get_elements_by_tagname("keywords");     

  $keywords = extractText($kl_array);     

  $lead_array = $root->get_elements_by_tagname("para-intro");     

  $para["intro"] = extractText($lead_array);     

  $second_array = $root->get_elements_by_tagname("para-main");     

  $para["main"] = extractText($second_array);     

  $con_array = $root->get_elements_by_tagname("para-conclusion");     

  $para["con"] = extractText($con_array);     

?>     

<html>     

<head>     

<title><?php echo $headline; ?></title>     

<meta name="keywords" content="<?php echo $keywords; ?>">     

<meta name="description" content="<?php echo $abstract; ?>">     

</head>     

<body>     

<h1><?php echo $headline; ?></h1>     

<a href="index.php">back to main</a>     

<p>by <a href="mailto:<?php echo $email; ?>"><?php echo $author; ?></a></p>     

<p><?php echo $abstract; ?></p>     

<?php     

foreach ($para as $k => $v){     

  echo "<p>".$v."</p>n";     

}     

?>     

</body>     

</html>     

<?php     

}     

?>
```

***搜索文章***

搜索 XML 文章存档很像搜索数据库表，只是使用 XML 时，需要打开每个文件，将搜索词与要搜索的每个节点进行比较，收集相关数据(如文件名、标题和摘要)，然后在页面上显示这些数据。

在我们的例子中，我将使用快速简单的方法——按顺序打开所有文件，检查它们是否是活动的(如果它们不是活动的，我将继续下一个文件),并将标题、摘要和文件名存储在一个多维数组中。

当我将结果发送到屏幕上时，我确保计算了这个数组中的记录数。如果计数为 0，那么我会显示一条消息，说明没有文件与搜索词匹配。否则，我会为我找到的每篇文章显示一个链接的标题和摘要。

这个搜索算法是三分钟思考和十分钟实现的结果。它又快又脏，如果你有少量的文章，它就像一个魔咒一样起作用。在以后的文章中，我们将研究更健壮的搜索 XML 文件的方法。

代码如下:

```
<?php      

session_start();      

$results = array();      

//this is a very simple, potentially very slow search      

function extractText($array){      

  if(count($array) <= 1){      

    //we only have one tag to process!      

    for ($i = 0; $i<count($array); $i++){      

      $node = $array[$i];      

      $value = $node->get_content();      

    }      

    return $value;      

  }       

}        

$dh = opendir('./xml/');      

while ($file = readdir($dh)){      

  if (eregi("^\.\.?$", $file)) {      

    continue;      

  }      

  $open = "./xml/".$file;      

  $xml = domxml_open_file($open);      

  //we need to pull out all the things from this file that we will need to       

  //build our links      

  $root = $xml->root();      

  $stat_array = $root->get_elements_by_tagname("status");      

  $status = extractText($stat_array);      

  $k_array = $root->get_elements_by_tagname("keywords");      

  $keywords = extractText($k_array);      

  $h_array = $root->get_elements_by_tagname("headline");      

  $headline = extractText($h_array);      

  $ab_array = $root->get_elements_by_tagname("abstract");      

  $abstract = extractText($ab_array);      

  if ($status != "live"){      

    continue;      

  }      

  if (eregi($searchTerm, $keywords) or eregi($searchTerm,$headline)){      

    $list['abstract'] = $abstract;      

    $list['headline'] = $headline;      

    $list['file'] = $file;      

    $results[] = $list;      

  }      

}      

$results = array_unique($results);      

?>      

<h1>Search Results</h1>      

<a href="index.php">back to main</a>      

<p>You searched for: <i><?php echo $searchTerm ?></i></p>      

<hr>      

<?php      

if (count($results)>0){      

  echo "<p>Your search results:</p>";      

  foreach ($results as $key => $listing){      

    echo "<br><a href="showArticle.php?file=".$listing["file"]."">" . $listing["headline"]."</a>n";      

    echo "<br>". $listing["abstract"];      

    echo "<br>";      

  }      

} else {      

  echo "<p>Sorry, no articles matched your search term.";      

}      

?>
```

##### 结论

就是这样！干得好——我们已经覆盖了很多领域。我们了解到:

*   开发 XML 结构的一些基本工具
*   如何建立一个非常简单和安全的管理工具来处理基本的网站功能
*   如何建立一个主页，文章浏览页面和一个简单的搜索引擎

在每种情况下，都有改进、细化和健壮性的空间。关键是，在使用这些工具时，您接触到了 XML 数据结构、应用程序工作流和简单 CMS 设计的一些基本概念。

## 分享这篇文章