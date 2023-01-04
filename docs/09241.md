# PHP 超级全局变量简介

> 原文：<https://www.sitepoint.com/introducin-superglobals/>

超全局变量是 PHP 中特别定义的数组变量，它使你很容易获得关于请求或其上下文的信息。它们被称为*超全局*，因为它们总是可访问的，不管作用域如何——也就是说，你可以从任何函数、类或文件访问它们，而无需做任何特殊的事情。超全局变量有:`$GLOBALS`、`$_SERVER`、`$_GET`、`$_POST`、`$_FILES`、`$_COOKIE`、`$_SESSION`、`$_REQUEST`和`$_ENV`。虽然您可以通过阅读 [PHP 文档](http://www.php.net/language.variables.superglobals.php "PHP: Superglobals - Manual")来了解更多，但我还是想向您展示我认为您可能最常用的那些。

## $_GET 和$_POST

`$_GET`和`$_POST`用于获取页面请求中发送的信息。在 URL 中发送的参数由`$_GET`提供。例如，此 URL:

```
http://example.com/profile.php?uid=12345
```

…将参数`uid`发送到`profile.php`，然后您可以访问`profile.php`中的值作为`$_GET["uid"]`。

当您有一个使用`method="get"`发送的表单提交时，这些值被附加到 URL 上，这样就可以使用`$_GET as well`了。使用`method="post"`提交的表单在 HTTP 请求的主体中发送它们的值；通过使用`$_POST`，提交的数据在您的脚本中可用。下面是一个示例 web 表单，它将用户名和密码发布到一个登录脚本:

```
<form action="login.php" method="post">
Username: <input type="text" name="uname"><br>
Password: <input type="password" name="pass"><br>
<input type="submit" value="Login">
</form>
```

`uname`值和`pass`值在`login.php`中将分别作为`$_POST["uname"]`和`$_POST["pass"]`出现。

`$_REQUEST`超级全球是`$_POST`和`$_GET`数据的混合体。这是为了方便起见，但一般来说，我建议人们避免使用它。如果你不小心的话，它会产生一些和几年前注册的 globals 一样的安全风险。(如果你不知道我说的注册全局用户是什么意思，那你就认为自己很幸运了。忘了`$_REQUEST`就没事了。)

## $ _ 会话

HTTP 协议是浏览器请求和接收页面的机制，最初的设计是为了使每个请求都独立于所有其他对服务器的请求。然而，这不是我们今天使用网络的方式。现在，人们希望他们在一个网站中访问的每个页面都能“记住”之前发生的事情，无论是登录还是在他们的在线购物车中添加新的视频游戏。页面之间的这种逻辑链接称为*会话*。

要启用会话，在输出被发送到浏览器之前，必须在任何使用会话的 PHP 脚本的顶部调用`session_start()`函数。一旦会话被激活，您就可以使用`$_SESSION`来存储和检索数据。这里有一个例子…

`page1.php`启动会话，在`$_SESSION`数组中存储一个值，然后提供一个到`page2.php`的链接:

```
<?php
session_start();
$_SESSION["videogame"] = "Battletoads";
?>
<a href="page2.php">Go to page 2</a>
```

当用户点击到`page2.php`的链接时，它调用`session_start()`来恢复会话，从`$_SESSION["videogame"]`中检索值并显示它:

```
<?php
session_start();
echo $_SESSION["videogame"];
```

## $ _ 服务器

`$_SERVER`超级全局数组存储关于 web 服务器和对它的请求的信息。一些更有用的`$_SERVER`值是:

*   `$_SERVER["PHP_SELF"]`–当前运行的 PHP 脚本的名称
*   `$_SERVER["REQUEST_METHOD"]`–使用了哪种 HTTP 方法来请求页面(`GET`、`POST`等)
*   `$_SERVER["REQUEST_TIME"]`–接收页面请求的时间，用一个称为 *unix 时间戳*的整数表示
*   `$_SERVER["HTTP_REFERER"]`–先前访问页面的地址(如果有)

可以在联机文档中找到所有可用密钥的列表。请记住，并不是列出的每一个人都保证在`$_SERVER`中；这取决于您的服务器配置和用户发送的请求。

## 摘要

正如我所说的，这些只是 PHP 中可用的超级全局变量中的几个，我认为你会用得最多。`$_GET`和`$_POST`允许你的脚本从外界获取信息。`$_SESSION`让你在访问者浏览会话期间记住页面请求之间的数据。`$_SERVER`保存关于 web 服务器和页面请求的信息。理解这些并学会有效地使用它们，无疑会帮助你相对容易地用 PHP 编写出优秀的、交互式的网站。

<small>图片 via[Tom Wang](http://www.shutterstock.com/gallery-572953p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")</small>

## 分享这篇文章