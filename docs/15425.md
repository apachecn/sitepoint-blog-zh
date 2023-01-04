# 使用 PHP 的 Apache HTTP 认证

> 原文：<https://www.sitepoint.com/http-authentication-php/>

我们都曾不得不登录一个有密码保护的网站。当建立一个网站时，你可能会出于几个原因要求你的访问者这样做。第一点，也是最明显的一点，是保护安全信息，使其只能被选定的少数人查看。但是您也可以选择将用户名和密码分配给临时访问者。这样做可能是为了跟踪谁在浏览你的网站，或者为你的访问者提供个性化的选择和服务。

对站点进行密码保护的最简单方法是使用 HTTP 身份验证，如果浏览器对受保护页面的请求没有附带正确的用户名和密码，Web 服务器会回复一个 HTTP 401 错误，这意味着“未经授权的访问”，并邀请浏览器使用正确的用户名和密码重新提交请求。从用户的角度来看，这种对话大部分是隐藏的。在第一次请求失败后，浏览器提示用户(在一个对话框中)输入用户名和密码，然后重新提交请求，这次附加了身份验证信息。假设用户名/密码组合在允许的用户列表中，Web 服务器就会发送所请求的页面。网络浏览器将同样继续在所有后续请求中发送该用户名/密码。

建立 HTTP 认证方案最常见的方法是使用 Apache“htaccess”文件(见[http://hoohoo.ncsa.uiuc.edu/docs/tutorials/user.html](http://hoohoo.ncsa.uiuc.edu/docs/tutorials/user.html)),但是这种方法有缺点。使授权用户列表动态化(例如，这样用户可以注册自己并立即访问您的站点)可能涉及一些非常复杂的服务器端脚本，这些脚本必须操纵 htaccess 文件来适当地添加和删除用户。在大多数 Web 服务器中，使用 HTTP 认证的基本支持，几乎不可能保存任何类型的记录，如谁使用哪个用户名/密码组合访问了什么。

进入 PHP，一种免费、开源、跨平台、服务器端脚本语言。当作为 Apache 模块安装时(这不适用于 CGI 和 ISAPI 版本)，PHP 允许您自己处理 HTTP 认证，使用您喜欢的任何方式来决定是接受还是拒绝对网站的访问。从现在开始，我假设你熟悉 PHP 的基础知识。如果这种语言对您来说是新的，或者如果您需要复习，我的[构建数据库驱动的网站](http://www.webmasterbase.com/article.php?aid=228)系列的第 3 部分应该会让您快速上手。

当作为 Apache 模块安装时，PHP 提供了两个特殊的全局变量:`$PHP_AUTH_USER`和`$PHP_AUTH_PW`。它们分别包含当前 HTTP 请求附带的用户名和密码。使用 PHP 的`header()`函数，当用户名、密码或两者都不正确时，您可以用 HTTP 401 错误进行响应。

让我们来看一些只有当用户输入用户名`"myuser"`和密码`"mypass"`时才可以查看的页面示例代码:

```
<?php  

if ($PHP_AUTH_USER != "mysuser"  

    or $PHP_AUTH_PW != "mypass"):  

  // Bad or no username/password.  

  // Send HTTP 401 error to make the  

  // browser prompt the user.  

  header("WWW-Authenticate: " .  

         "Basic realm="Protected Page: " .  

         "Enter your username and password " .  

         "for access."");  

  header("HTTP/1.0 401 Unauthorized");  

  // Display message if user cancels dialog  

  ?>  

  <HTML>  

  <HEAD><TITLE>Authorization Failed</TITLE></HEAD>  

  <BODY>  

  <H1>Authorization Failed</H1>  

  <P>Without a valid username and password,  

     access to this page cannot be granted.  

     Please click 'reload' and enter a  

     username and password when prompted.  

  </P>  

  </BODY>  

  </HTML>  

<?php else: ?>  

  ...page contents here...  

<?php endif; ?>
```

如您所见，检查输入的用户名和密码就像检查变量`$PHP_AUTH_USER`和`$PHP_AUTH_PW`一样简单。当检测到不正确的用户/口令组合时，您用两个 HTTP 头来响应(使用 PHP `[header](http://www.php.net/manual/function.header.php)`函数):

```
WWW-Authenticate: Basic realm="Prompt the user here."  

HTTP/1.0 401 Unauthorized
```

第一行通知 Web 浏览器将使用基本身份验证。这仅仅意味着身份验证是通过用户名和密码来完成的。realm 选项让浏览器在浏览一组网页时知道何时应该使用特定的用户名/密码。所有应该使用相同用户名/密码的页面(从而使用户不必为每个页面重新输入用户名/密码)都应该指定相同的领域。因为该字符串显示在提示用户的对话框中，所以它是放置消息的理想位置(例如:“如果您是新用户，请输入‘guest’作为您的用户名，将密码留空。”).注意，这一行中的双引号必须用反斜杠转义，以防止它们干扰 PHP 代码中字符串周围的双引号。

第二行是标准的 HTTP 响应代码，让浏览器知道输入的用户名/密码(如果有的话)不正确，应该提示用户重新输入。

为了保护整个网站，你通常会使用 PHP 的`[include](http://www.php.net/manual/function.include.php)`函数来执行用户名/密码检查，而不需要在每个页面上重新输入代码。

我最近在一个网站上使用了这种技术，这个网站是我为一个项目中的一小群人建立的。我发布了一个用户名/密码组合，允许他们访问注册页面，每个人都可以创建一个个人用户名/密码组合。注册页面会将这些组合存储在一个 MySQL 数据库中(有关这方面的更多信息，请参见我的[构建一个数据库驱动的网站](http://www.webmasterbase.com/article.php?aid=228)系列文章)。该站点上的所有其他页面将访问该数据库，以确定是否允许给定的用户名/密码组合访问该站点。

这一点以及其他使您的密码保护系统更加灵活的创造性可能性，使使用 PHP 的 HTTP 认证成为您的武器库中一个极其方便的工具。

## 分享这篇文章