# 保护您的 Web 应用程序的 8 种做法

> 原文：<https://www.sitepoint.com/8-practices-to-secure-your-web-app/>

说到应用程序安全性，除了保护您的硬件和平台，您还需要安全地编写代码。这篇文章将解释如何保持你的应用程序的安全和不容易被黑客攻击。为了保护他或她的应用程序免受攻击，程序员可以养成以下最佳习惯:

*   输入数据验证
*   防范 XSS 袭击
*   防范 CSRF 袭击
*   防止 SQL 注入攻击
*   保护文件系统
*   保护会话数据
*   适当的错误处理
*   保护包含的文件

## 输入数据验证

在设计你的应用程序时，你应该努力保护你的应用程序免受不良输入的影响。要遵循的经验法则是:不要相信用户输入。虽然你的应用是为好人设计的，但是总有一些坏用户会试图通过输入错误的输入来攻击你的应用。如果您总是验证和过滤传入的数据，您可以构建一个安全的应用程序。

总是在你的 PHP 代码中验证数据。如果您使用 JavaScript 来验证用户输入，那么用户很可能已经在浏览器中关闭了 JavaScript。在这种情况下，您的应用程序将无法验证输入。用 JavaScript 验证是可以的，但是为了防止这类问题，你也应该用 PHP 重新验证数据。

## 防范 XSS 袭击

跨站脚本攻击(XSS 攻击)是一种基于向易受攻击的网页中注入代码的攻击。危险是接受未经检查的输入数据并将其显示在浏览器中的结果。

假设您的应用程序中有一个允许用户输入数据的注释表单，在成功提交后，它会显示所有的注释。用户可能会输入包含恶意 JavaScript 代码的注释。提交表单时，数据被发送到服务器并存储到数据库中。之后，从数据库中获取注释并显示在 HTML 页面中，JavaScript 代码将运行。恶意 JavaScript 可能会将用户重定向到不良网页或钓鱼网站。

为了保护您的应用程序免受这类攻击，请通过`strip_tags()`运行输入数据，删除其中存在的任何标签。在浏览器中显示数据时，对数据应用`htmlentities()`功能。

## 防范 CSRF 袭击

在跨站点请求伪造(CSRF)攻击中，攻击者欺骗受害者在他们不知情的情况下加载敏感信息或进行交易。这主要发生在使用 GET 请求触发业务逻辑的 web 应用程序中。

理想情况下，GET 请求本质上是幂等的。幂等性意味着同一个页面可以被多次访问，而不会产生任何副作用。因此，GET 请求应该只用于访问信息，而不用于执行事务。

下面的例子展示了一个编写不当的应用程序是如何在不知不觉中支持 CSRF 攻击的:

```
<?php
if (isset($_REQUEST["name"], $_REQUEST["amount"])) {
    // process the request and transfer the amount from
    // from the logged in user to the passed name.
}
```

假设 Bob 想要对 Alice 进行 CSRF 攻击，他构建了一个如下所示的 URL，并通过电子邮件将其发送给 Alice:

```
<a href="http://example.com/process.php?name=Bob&amount=1000">Visit My WebSite</a>
```

如果 Alice 点击了这个链接，并且已经登录到该网站，这个请求将从她的帐户中扣除 1000 美元，并将其转到 Bob 的帐户中！或者，Bob 可以创建一个图像链接，其 src 属性指向 URL。

```
<img src="http://example.com/process.php?name=Bob&amount=1000" width="1" height="1"/>
```

浏览器无法按预期显示任何图像，但它仍会使用 URL 发出请求，这将在不通知 Alice 的情况下进行交易。

解决方案是在 POST 请求中处理任何改变数据库状态的函数，避免使用`$_REQUEST`。使用`$_GET`检索 GET 参数，使用`$_POST`检索 POST 参数。

此外，应该有一个名为 CSRF 令牌的随机令牌与每个 POST 请求相关联。当用户登录到他/她的帐户时，应用程序应该生成一个随机令牌并将其存储在会话中。每当向用户显示任何表单时，令牌都应该作为隐藏的输入字段出现在页面中。应用程序逻辑必须检查令牌，并确保它与会话中存在的令牌相匹配。

## 防止 SQL 注入攻击

要执行数据库查询，您应该使用 PDO。通过参数化查询和预处理语句，可以防止 SQL 注入。

看一下下面的例子:

```
<?php
$sql = "SELECT * FROM users WHERE name=:name and age=:age";
$stmt = $db->prepare($sql);
$stmt->execute(array(":name" => $name, ":age" => $age));
```

在上面的代码中，我们提供了命名参数`:name`和`:age`到`prepare()`，通知数据库引擎预编译查询，并在以后将值附加到命名参数。当调用`execute()`时，使用指定参数的实际值执行查询。如果您以这种方式编码，攻击者就不能注入恶意的 SQL，因为查询已经编译好了，您的数据库将是安全的。

## 保护文件系统

作为一名开发人员，您应该始终以这样一种方式编写代码，即您的任何操作都不会将您的文件系统置于风险之中。考虑下面根据用户提供的参数下载文件的 PHP:

```
<?php
if (isset($_GET['filename']) {
    $filename = $_GET['filename'];
    header('Content-Type: application/x-octet-stream');
    header('Content-Transfer-Encoding: binary');
    header('Content-Disposition: attachment; filename="' . $filename . '";');
    echo file_get_contents($filename);
}
```

该脚本非常危险，因为它可以从它可以访问的任何目录提供文件，如会话目录和系统目录。解决方案是确保脚本不会试图从任意目录访问文件。

## 保护会话数据

默认情况下，会话信息被写入临时目录。在共享主机服务器的情况下，除了您之外的其他人可以很容易地编写脚本和读取会话数据。因此，您不应该在会话中保留密码或信用卡号等敏感信息。

保护会话数据的一个好方法是加密存储在会话中的信息。这并没有完全解决问题，因为加密的数据并不完全安全，但至少信息是不可读的。您还应该考虑将会话数据存储在其他地方，比如数据库。PHP 提供了一个名为`session_set_save_handler()`的方法，可以用来以你自己的方式在会话中持久化数据。

从 PHP 5.4 开始，你可以将类型为`SessionHandlerInterface`的对象传递给`session_set_save_handler()`。查看 PHP 文档，了解如何通过实现`SessionHandlerInterface`来实现定制会话持久性。

## 适当的错误处理

当我们开发一个应用程序时，知道所有发生的错误是很好的，但是当我们让最终用户可以访问应用程序时，我们应该注意隐藏错误。如果向用户显示错误，可能会使我们的应用程序易受攻击。因此，最好的方法是为开发和生产环境配置不同的服务器。

在生产模式下，我们需要关闭`display_errors`和`display_start_up_errors`设置。`error_reporting`和`log_errors`应该打开，这样我们可以记录错误，同时对最终用户隐藏这些错误。

您可以使用 set_error_handler 来定义自定义错误处理程序。但是，它有局限性。自定义错误处理程序绕过了 PHP 的标准错误处理机制。它不能在定义错误处理程序的同一个文件中捕获像`E_CORE_ERROR`、`E_STRICT`或`E_COMPILER_ERROR`这样的错误。此外，它将无法处理处理程序本身可能发生的错误。

为了优雅地处理错误，您应该通过 try/catch 块执行异常处理。异常由`Exception`类及其子类表示。如果 try 块中出现任何错误，您可以抛出一个异常，并在 catch 块中处理它。

## 保护包含的文件

PHP 脚本通常包含其他 PHP 文件，这些文件包含连接数据库等代码。一些开发人员为包含的文件指定了. inc .这样的扩展名。默认情况下，如果直接调用，PHP 不会解析带有此扩展名的文件，这些文件将作为纯文本提供给用户。如果攻击者直接访问包含数据库凭证的 include 文件，他现在就可以访问应用程序的所有数据。始终使用。包含的代码文件的 php 扩展名，并将它们放在用户可以直接访问的目录之外。

## 摘要

记住以上 8 点，就有可能在很大程度上保证 PHP 应用程序的安全。到目前为止，最好的建议是不要相信用户输入，但是也要确保保护好你的文件系统和数据库。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章