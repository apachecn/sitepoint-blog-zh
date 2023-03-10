# PHP 会话

> 原文：<https://www.sitepoint.com/php-sessions/>

`$_SESSION`是一个特殊的数组，用于存储用户在访问您的网站或 web 应用程序时发出的页面请求信息。解释会话是什么样子的最基本方法是想象以下场景:

> 您正在使用一个应用程序。你打开它，做一些修改，然后关闭它。

这是一次最简单的会议。

示例场景让人想起使用登录系统时发生的过程。这个过程可以非常复杂，也可以非常简单，只要在请求之间有一个持久的值。在打开的会话期间，可以随时调用存储在会话中的信息。

虽然可能有许多用户同时访问该站点，每个用户都有自己的会话，但这要感谢 PHP 为每个会话分配和管理的唯一 id，它允许每个用户的会话只对他自己可用。会话信息存储在服务器上，而不是用户的计算机上(就像存储 cookie 数据一样)，这使得会话在页面请求之间传递信息时比传统的 cookie 更安全。

在这篇文章中，我将告诉你如何在 PHP 中使用会话——如何创建它们，如何销毁它们，以及如何确保它们的安全。

## 使用会话

在会话中存储信息之前，必须启动 PHP 的会话处理。这是在 PHP 代码开始时完成的，并且必须在任何文本、HTML 或 JavaScript 被发送到浏览器之前完成。要启动会话，您需要在第一个文件中调用`session_start()`函数:

```
<?php
// start them engines!
session_start();
// store session data
$_SESSION["username"] = "Callum";
```

`session_start()`启动用户和服务器之间的会话，并允许以后在其他脚本中访问存储在`$_SESSION`中的值。

在第二个文件中，再次调用`session_start()`，这一次继续会话，然后可以从`$_SESSION`中检索值。

```
<?php
// continue the session
session_start();
// retrieve session data
echo "Username = " . $_SESSION["username"];
```

这个例子是在会话中存储和检索数据的一个非常基本的演示。在第一个脚本中，值“Callum”与`$_SESSION`数组中的键“username”相关联。在第二个脚本中，使用键从`$_SESSION`数组请求回信息。`$_SESSION`允许你跨用户的活动浏览会话的页面请求存储和检索信息。

## 结束会话

开始一个会话很重要，结束一个会话也很重要。尽管会话只是存储数据的一种临时方式，但在处理潜在的敏感信息时，为了确保最大的安全性，事后清理是非常重要的。这也是一种很好的做法，可以避免在服务器上存放大量过时的会话数据。

要删除单个会话值，您可以使用`unset()`功能:

```
<?php
session_start();
// delete the username value
unset($_SESSION["username"]);
```

要取消设置会话的所有值，您可以使用`session_unset()`函数:

```
<?php
session_start();
// delete all session values
session_unset();
```

这两个示例只影响存储在会话中的数据，而不影响会话本身。如果您愿意，在调用其他值后，您仍然可以将它们存储到`$_SESSION`。如果您希望完全停止使用会话，例如用户注销，您可以使用`session_destroy()`功能。

```
<?php
session_start();
// terminate the session
session_destroy();
```

我强烈建议当您确定不再需要这个会话时，使用`session_destroy()`销毁它，而不是仅仅使用`session_unset()`取消设置它的所有值。如果您只是取消设置所有值，会话本身仍然是活动的，恶意代码可能会给这些会话带来有害的值。

简而言之，这就是会话，PHP 中非常基本但非常强大的功能，为在网页之间传递数据的问题提供了一个优雅的解决方案。

## 会话安全提示

尽管很简单，但是使用会话仍然会出错。下面是一些安全技术的快速概述，您可以使用这些技术来确保安全地使用会话。

### 会话超时

如果您正在处理登录到您的网站或应用程序的用户，超时会话是一个非常重要的操作。如果用户在网吧登录到您的站点，然后没有注销就离开了电脑和网吧，您如何阻止该电脑上的下一个用户仍然可以访问上一个用户的会话？你可以使用下面的代码:

```
<?php
session_start();
// set time-out period (in seconds)
$inactive = 600;

// check to see if $_SESSION["timeout"] is set
if (isset($_SESSION["timeout"])) {
    // calculate the session's "time to live"
    $sessionTTL = time() - $_SESSION["timeout"];
    if ($sessionTTL > $inactive) {
        session_destroy();
        header("Location: /logout.php");
    }
}

$_SESSION["timeout"] = time();
```

该代码确保如果超过 600 秒(10 分钟)没有活动，请求将被重定向到注销页面，该页面将成功注销用户。

### 重新生成会话 ID

`session_regenerate_id()`函数为创建一个新的唯一 ID 来表示当前用户的会话。每当执行任何重要的身份验证操作(如登录或更新用户配置文件数据)时，都应该重新生成该信息。在这样的操作之后给会话一个新的 ID，通过降低被称为“会话劫持”的特定攻击的风险，使您的应用程序更加安全

```
<?php
session_start();

if ($_POST["username"] == "admin" && $_POST["password"] == sha1("password")) {
    $_SESSION["authorized"] = true;
    session_regenerate_id();
}
```

### 销毁会话

正如我前面提到的，一旦不再需要使用会话，就应该使用`session_destory()`。这阻止了攻击者劫持陈旧的会话，再次增加了网站与会话相关的安全性。

### 使用永久存储

在你知道数据将是持久的时候，尽早使用数据库存储数据；不要让它作为会话的一部分停留太久，因为这可能会导致攻击。认真考虑数据是否应该存储在`$_SESSION`中，因为会话数据应该是瞬态的。

## 摘要

在本文中，您已经学习了什么是会话，以及如何在 PHP 中创建、使用和销毁会话。您还看到了一些确保它们安全的技巧。有关会话和会话安全性的更多信息，请查看以下推荐文章和网页:

*   [PHP 手册–会话](http://www.php.net/manual/en/book.session.php "PHP Manual - Sessions")
*   [PHP 安全联盟-PHP 安全指南:会议](http://phpsec.org/projects/guide/4.html "PHP Security Consortium - PHP Security Guide: Sessions")
*   [Wikibooks–PHP 编程:会话](http://en.wikibooks.org/wiki/PHP_Programming/Sessions "Wikibooks - PHP Programming: Sessions")

图片 via[Kokhanchikov](http://www.shutterstock.com/gallery-137605p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start PHP](https://learnable.com/books/jump-start-php?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章