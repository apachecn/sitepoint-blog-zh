# 面向 WordPress 开发者的 XML-RPC

> 原文：<https://www.sitepoint.com/xml-rpc-for-wordpress-developers/>

XML-RPC 是一种远程过程调用(一个进程通过远程连接调用另一个进程的功能)协议，它使用 XML 表示数据，使用 HTTP 进行调用。尽管应用程序可以明确地为 RPC 提供它们自己的 REST APIs，但是标准协议可以帮助提高安全性并提供许多其他好处。例如，开发人员不必从头开始设计 REST API 体系结构，并且单个客户端也可以用于对支持标准协议的各种服务器应用程序进行远程过程调用。因此，XML-RPC 作为 RPC 的标准协议被引入。

![XML-RPC for WordPress](img/7c81df121ce03113683306b6ad807c97.png)

在本教程中，我们将看看不同的核心 WordPress 函数，这些函数可以使用 XML-RPC 远程执行。这可以帮助我们构建能够在 WordPress 安装上执行各种操作的工具。一个最好的例子是 WordPress 移动应用程序。

## XML-RPC 协议请求和响应概述

要发出 XML-RPC 请求，您需要用 XML 格式包装远程函数名和参数，然后使用 HTTP 发送 POST 请求。

这是一个 XML-RPC HTTP 请求的示例:

```
POST /xmlrpc HTTP 1.0
User-Agent: myXMLRPCClient/1.0
Host: 192.168.124.2
Content-Type: text/xml
Content-Length: 169
<?xml version="1.0"?>
<methodCall>
<methodName>circleArea</methodName>
<params>
<param>
<value><double>2.41</double></value>
</param>
</params>
</methodCall>
```

以下是对上述请求的示例响应:

```
HTTP/1.1 200 OK
Date: Sat, 06 Oct 2001 23:20:04 GMT
Server: Apache.1.3.12 (Unix)
Connection: close
Content-Type: text/xml
Content-Length: 124

<?xml version="1.0"?>
<methodResponse>
<params>
<param>
<value><double>18.24668429131</double></value>
</param>
</params>
</methodResponse>
```

在本教程中，我们将使用 PHP 向 WordPress 发送 XML-RPC 请求，并显示原始响应。

我们通常会编写代码，将函数名和参数包装成 XML 格式，然后使用 cURL 发出 HTTP 请求，但是从头开始编写代码非常冗长。相反，我们可以使用 [PHPXMLRPC](http://gggeek.github.io/phpxmlrpc/) 库，它提供了所有这些步骤的抽象，让我们更容易地发出 XML-RPC 请求。

XML-RPC 库适用于所有流行的编程语言，你可以使用你最喜欢的搜索引擎找到你最喜欢的语言。

## WordPress XML-RPC 函数

WordPress 通过 XML-RPC 公开了许多核心功能。所有 XML-RPC 公开的功能被分为 9 类:帖子、分类法、媒体、评论、选项、用户、类别、标签和页面。

### 函数列表

以下是所有功能的完整列表:

[帖子功能](http://codex.wordpress.org/XML-RPC_WordPress_API/Posts):可从 WordPress 3.4 获得。以下是属于帖子类别的功能列表:

```
wp.getPost
wp.getPosts
wp.newPost
wp.editPost
wp.deletePost
wp.getPostType
wp.getPostTypes
wp.getPostFormats
wp.getPostStatusList 
```

[分类法功能](http://codex.wordpress.org/XML-RPC_WordPress_API/Taxonomies):可从 WordPress 3.4 获得。以下是属于分类法类别的函数列表:

```
wp.getTaxonomy
wp.getTaxonomies
wp.getTerm
wp.getTerms
wp.newTerm
wp.editTerm
wp.deleteTerm 
```

[媒体功能](http://codex.wordpress.org/XML-RPC_WordPress_API/Media):可从 WordPress 3.1 获得。以下是属于分类法类别的函数列表:

```
wp.getMediaItem
wp.getMediaLibrary
wp.uploadFile 
```

[评论功能【WordPress 2.7 提供。以下是属于注释类别的函数列表:](http://codex.wordpress.org/XML-RPC_WordPress_API/Comments)

```
wp.getCommentCount
wp.getComment
wp.getComments
wp.newComment
wp.editComment
wp.deleteComment
wp.getCommentStatusList 
```

[选项功能](http://codex.wordpress.org/XML-RPC_WordPress_API/Options):可从 WordPress 2.6 获得。以下是属于选项类别的功能列表:

```
wp.getOptions
wp.setOptions 
```

[用户功能](http://codex.wordpress.org/XML-RPC_WordPress_API/Users):可从 WordPress 3.5 获得。以下是属于用户类别的功能列表:

```
wp.getUsersBlogs
wp.getUser 
wp.getUsers 
wp.getProfile 
wp.editProfile
wp.getAuthors 
```

[分类功能](http://codex.wordpress.org/XML-RPC_WordPress_API/Categories_%26_Tags):可从 WordPress 3.4 获得。以下是属于类别的函数列表:

```
wp.getCategories
wp.suggestCategories
wp.newCategory
wp.deleteCategory 
```

[标签功能](http://codex.wordpress.org/XML-RPC_WordPress_API/Categories_%26_Tags#wp.getTags):可从 WordPress 3.4 获得。以下是属于标签类别的函数列表:

```
wp.getTags 
```

[页面功能](http://codex.wordpress.org/XML-RPC_WordPress_API/Pages):可从 WordPress 3.4 获得。以下是属于页面类别的功能列表:

```
wp.getPage
wp.getPages
wp.getPageList
wp.newPage
wp.editPage
wp.deletePage
wp.getPageStatusList
wp.getPageTemplates 
```

所有的类别名和函数名，以及用途和目的都是不言自明的。

让我们看看上述函数的一些例子:

### 获取 WordPress 作者列表

下面是使用 PHP 获得远程 WordPress 安装的所有作者列表的代码:

```
<?php
include("lib/xmlrpc.inc");
$function_name = "wp.getAuthors";
$url = "https://www.sitepoint.com/xmlrpc.php";

$client = new xmlrpc_client($url);
$client->return_type = "phpvals";
$message = new xmlrpcmsg($function_name, array(new xmlrpcval(0, "int"), new xmlrpcval("username", "string"), new xmlrpcval("password", "string")));
$resp = $client->send($message);

if ($resp->faultCode()) echo 'KO. Error: '.$resp->faultString(); else foreach ($resp->val as $key => $value) {
echo "User id: " . $value["user_id"];
echo "<br><br>";
echo "Username: " . $value["user_login"];
echo "<br><br>";
echo "Display name: " . $value["display_name"];
echo "<br><br>";
};
?>
```

让我们看看上面的代码是如何工作的:

*   首先，我们包含了 PHPXMLRPC 库。
*   然后我们创建一个变量`$function_name`来保存函数名。
*   我们创建了另一个变量，指向 WordPress 安装的`xmlrpc.php`文件。这个文件总是存在于 WordPress 的根目录中。
*   然后，我们创建一个 XML-RPC 客户端对象，并将 URL 传递给构造函数。
*   然后，我们指示这个库将响应数据转换成 PHP 数组变量，以便于读取和处理响应数据。处理原始的 XML 响应数据会很困难，因为我们必须解析 XML。
*   然后我们用`wp.getAuthors`函数的参数构造一个请求消息对象。第一个参数是博客 id，另外两个参数是管理员的用户名和密码。
*   接下来，我们发送 XML-RPC 请求。
*   最后我们得到了回应。如果有错误，我们显示错误，否则我们循环响应对象的 value 属性来打印作者的基本信息。

### 创建帖子

我们刚刚看到检索作者列表是多么容易，下面是创建帖子的方法:

```
<?php
include("lib/xmlrpc.inc");
$function_name = "wp.newPost";
$url = "https://www.sitepoint.com/xmlrpc.php";

$client = new xmlrpc_client($url);
$client->return_type = 'phpvals';

$message = new xmlrpcmsg(
$function_name,
array(
new xmlrpcval(0, "int"),
new xmlrpcval("my_cool_username", "string"),
new xmlrpcval("my_super_secret_password", "string"),
new xmlrpcval(
array(
"post_type" => new xmlrpcval("post", "string"),
"post_status" => new xmlrpcval("draft", "string"),
"post_title" => new xmlrpcval("Sitepoint is Awesome", "string"),
"post_author" => new xmlrpcval(1, "int"),
"post_excerpt" => new xmlrpcval("excerpt", "string"),
"post_content" => new xmlrpcval("content", "string")
),
"struct"
)
)
);

$resp = $client->send($message);

if ($resp->faultCode()) echo 'KO. Error: '.$resp->faultString(); else echo "Post id is: " . $resp->value();
?>
```

在这里，我们调用了函数`wp.newPost`。以及博客 id、用户名和密码。我们还传递了一个包含文章类型、状态、标题、内容、作者和摘录的结构类型。

### 注意:检测 XML-RPC 请求

一个小提示:如果你是一个插件或主题开发者，那么你可能希望你的代码为 XML-RPC 请求提供不同的功能。WordPress 允许主题和插件检测 WordPress 是否正在处理 XML-RPC 请求。

下面是检测 XML-RPC 请求的代码:

```
if(defined('XMLRPC_REQUEST'))
{
// XML-RPC request
}
else
{
// Normal HTTP request
}
```

### 结论

在本文中，我们介绍了 WordPress 的 XML-RPC 的基础知识，包括 XML-RPC 的基础知识以及 WordPress 如何公开这个协议。我们还演示了如何使用 XML-RPC 在 WordPress 安装上执行各种操作。现在你可以为 WordPress 创建一个移动的、桌面的或者 web 应用程序 XML-RPC 客户端。

## 分享这篇文章