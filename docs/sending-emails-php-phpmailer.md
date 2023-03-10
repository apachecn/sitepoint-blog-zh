# 用 PHPMailer 用 PHP 发送电子邮件

> 原文：<https://www.sitepoint.com/sending-emails-php-phpmailer/>

PHPMailer 可能是用来发送电子邮件的最流行的开源 PHP 库。它早在 2001 年首次发布，从那时起，除了其他一些粉丝喜欢的方式，如 [Swiftmailer](https://www.sitepoint.com/sending-email-with-swift-mailer/) ，它已经成为 PHP 开发人员最喜欢的编程发送电子邮件的方式。

在本文中，我们将讨论为什么应该使用 PHPMailer 而不是 PHP 的`mail()`函数，并且我们将展示一些关于如何使用这个库的代码示例。

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

## 是 PHP 的`mail()`函数的替代品吗？

在大多数情况下，它是 PHP 的`mail()`函数的替代，但是还有许多其他情况，其中的`mail()`函数不够灵活，无法实现您需要的功能。

首先，PHPMailer 提供了面向对象的接口，而`mail()`不是面向对象的。PHP 开发人员通常讨厌在使用`mail()`函数发送电子邮件时创建`$headers`字符串，因为它们需要大量的转义。PHPMailer 让这变得轻而易举。当使用`mail()`函数时，开发人员还需要编写脏代码(转义字符、编码和格式化)来发送附件和基于 HTML 的电子邮件，而 PHPMailer 使这变得没有痛苦。

此外，`mail()`功能需要一个本地邮件服务器来发送电子邮件，设置起来并不容易。如果您有认证，PHPMailer 可以使用非本地邮件服务器(SMTP)。

其他优势包括:

*   当无法发送电子邮件时，它可以用 40 多种语言打印各种错误信息。
*   它通过 SSL 和 TLS 集成了 SMTP 协议支持和身份验证。
*   它可以为非 HTML 电子邮件客户端发送替代的纯文本版本的电子邮件。
*   它有一个非常活跃的开发人员社区，使它保持安全和最新。

PHPMailer 也被流行的 PHP 内容管理系统使用，如 WordPress、Drupal 和 Joomla。

## 安装 PHPMailer

您可以使用 Composer 安装 [PHPMailer](https://github.com/PHPMailer/PHPMailer) :

```
composer require phpmailer/phpmailer 
```

### 使用 PHPMailer 从本地 Web 服务器发送电子邮件

下面是使用 PHPMailer 从本地 web 服务器发送电子邮件的最简单的例子:

```
<?php
use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\Exception;

require_once "vendor/autoload.php";

//PHPMailer Object
$mail = new PHPMailer(true); //Argument true in constructor enables exceptions

//From email address and name
$mail->From = "from@yourdomain.com";
$mail->FromName = "Full Name";

//To address and name
$mail->addAddress("recepient1@example.com", "Recepient Name");
$mail->addAddress("recepient1@example.com"); //Recipient name is optional

//Address to which recipient will reply
$mail->addReplyTo("reply@yourdomain.com", "Reply");

//CC and BCC
$mail->addCC("cc@example.com");
$mail->addBCC("bcc@example.com");

//Send HTML or Plain Text email
$mail->isHTML(true);

$mail->Subject = "Subject Text";
$mail->Body = "<i>Mail body in HTML</i>";
$mail->AltBody = "This is the plain text version of the email content";

try {
    $mail->send();
    echo "Message has been sent successfully";
} catch (Exception $e) {
    echo "Mailer Error: " . $mail->ErrorInfo;
} 
```

代码和注释应该足够清楚地解释正在发生的一切。

### 发送带有附件的电子邮件

以下是如何使用 PHPMailer 发送带有附件的电子邮件的示例:

```
<?php
use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\Exception;

require_once "vendor/autoload.php";

$mail = new PHPMailer;

$mail->From = "from@yourdomain.com";
$mail->FromName = "Full Name";

$mail->addAddress("recipient1@example.com", "Recipient Name");

//Provide file path and name of the attachments
$mail->addAttachment("file.txt", "File.txt");        
$mail->addAttachment("images/profile.png"); //Filename is optional

$mail->isHTML(true);

$mail->Subject = "Subject Text";
$mail->Body = "<i>Mail body in HTML</i>";
$mail->AltBody = "This is the plain text version of the email content";

try {
    $mail->send();
    echo "Message has been sent successfully";
} catch (Exception $e) {
    echo "Mailer Error: " . $mail->ErrorInfo;
} 
```

这里，我们附加了两个文件——`file.txt`,它驻留在与脚本相同的目录中，以及`images/profile.png`,它驻留在脚本目录的`images`目录中。

要在邮件中添加附件，我们只需要通过传递文件路径作为参数来调用 PHPMailer 对象的函数`addAttachment`。为了附加多个文件，我们需要多次调用它。

### 解决纷争

在我们的两个例子中，我们使用 PHPMailer 的[异常类](https://github.com/PHPMailer/PHPMailer/blob/master/src/Exception.php)进行调试，因此抛出的任何错误都将帮助我们调试可能出现的任何问题。我们还将参数`true`添加到 [PHPMailer 构造函数](https://github.com/PHPMailer/PHPMailer/blob/master/src/PHPMailer.php#L819)中，以输出更高级、更具描述性的异常。

根据我们使用的系统，我们可能会看到的最常见的错误与在后台运行`mail()`函数有关:

> 邮件程序错误:无法实例化邮件功能。

如果我们需要关于错误的更多细节，我们还可以在 catch 子句中添加如下内容:

```
print_r(error_get_last()); 
```

通常，邮件函数的问题与缺少邮件服务器设置有关，在这种情况下,`error_get_last`函数将返回如下内容:

```
Array (
    [type] => 2
    [message] => mail(): Failed to connect to mailserver at "localhost" port 25, verify your "SMTP" and "smtp_port" setting in php.ini or use ini_set()
    [file] => OUR_PATH \vendor\phpmailer\phpmailer\src\PHPMailer.php
    [line] => 863
) 
```

这可能是我们最常遇到的问题，我们可以通过使用 SMTP 轻松解决它。

### 显示本地化的错误消息

可以用 43 种不同的语言返回错误信息。

要用不同的语言显示错误消息，请将 PHPMailer 源代码中的`language`目录复制到项目目录中。

例如，要用俄语返回错误消息，请使用下面的方法调用将 PHPMailer 对象设置为俄语:

```
$mail->setLanguage("ru"); 
```

您也可以将自己的语言文件添加到`language`目录中。

### 使用 SMTP

您可以使用另一台主机的邮件服务器发送电子邮件，但为此您首先需要进行身份验证。例如，要从 Gmail 的邮件服务器发送电子邮件，您需要有一个 Gmail 帐户。

SMTP 是邮件客户端用来向邮件服务器发送电子邮件发送请求的协议。邮件服务器验证电子邮件后，会将其发送到目的邮件服务器。

这是一个从您的域中的 Gmail 邮件服务器发送电子邮件的示例。您不需要本地邮件服务器来运行代码。我们将使用 SMTP 协议:

```
<?php
use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\SMTP;
use PHPMailer\PHPMailer\Exception;

require_once "vendor/autoload.php";

$mail = new PHPMailer(true);

//Enable SMTP debugging.
$mail->SMTPDebug = 3;                               
//Set PHPMailer to use SMTP.
$mail->isSMTP();            
//Set SMTP host name 
$mail->Host = "smtp.gmail.com";
//Set this to true if SMTP host requires authentication to send email
$mail->SMTPAuth = true;                          
//Provide username and password 
$mail->Username = "name@gmail.com";                 
$mail->Password = "super_secret_password";                           
//If SMTP requires TLS encryption then set it
$mail->SMTPSecure = "tls";                           
//Set TCP port to connect to
$mail->Port = 587;                                   

$mail->From = "name@gmail.com";
$mail->FromName = "Full Name";

$mail->addAddress("name@example.com", "Recepient Name");

$mail->isHTML(true);

$mail->Subject = "Subject Text";
$mail->Body = "<i>Mail body in HTML</i>";
$mail->AltBody = "This is the plain text version of the email content";

try {
    $mail->send();
    echo "Message has been sent successfully";
} catch (Exception $e) {
    echo "Mailer Error: " . $mail->ErrorInfo;
} 
```

Gmail 要求通过 SMTP 进行 TLS 加密，所以我们相应地进行了设置。在通过 SMTP 发送之前，您需要找出主机名、端口号、加密类型(如果需要),如果需要身份验证，您还需要用户名和密码。请注意，在 Gmail 上启用双因素认证不会让您使用他们的带有用户名/密码的 SMTP。相反，将需要[额外的配置](http://webapps.stackexchange.com/questions/31297/gmail-rejects-smtp-password-with-2-factor-authentication)。

与本地邮件相比，使用远程 SMTP 的一大优势是，如果您使用 PHP 的`mail()`函数发送电子邮件，并且将`from`地址域设置为除本地域名(服务器名称)之外的任何名称，那么收件人的电子邮件服务器的攻击过滤器会将其标记为垃圾邮件。例如，如果你从一个实际主机名为`example.com`的服务器发送一封电子邮件，地址为`name@gmail.com`到`name@yahoo.com`，那么雅虎的服务器会将其标记为垃圾邮件，或者向用户显示一条信息，不要相信该邮件，因为该邮件的来源是`example.com`，但它看起来好像来自`gmail.com`。虽然你拥有`name@gmail.com`，但雅虎没有办法找到答案。

### 使用 POP3 检索电子邮件

PHPMailer 还允许 [POP-before-SMTP](http://en.wikipedia.org/wiki/POP_before_SMTP) 验证发送邮件。换句话说，您可以使用 POP 进行身份验证，并使用 SMTP 发送电子邮件。遗憾的是，PHPMailer 不支持使用 POP3 协议从邮件服务器检索电子邮件。仅限于发邮件。

### 结论

如果你是一名 PHP 开发人员，几乎不可能避免通过编程发送电子邮件。虽然你可能会选择像 Mandrill 或 SendGrid 这样的第三方服务，但有时这并不是一个选项，开发自己的电子邮件发送库更是如此。这就是 PHPMailer 及其替代品(Zend Mail、Swift Mailer 等等)的用武之地。

你可以在知识库维基或[官方文档](http://phpmailer.github.io/PHPMailer/)中了解这个库的 API[。](https://github.com/PHPMailer/PHPMailer/wiki)

你是否陷入了 PHP 库依赖的泥潭？[观看我们的截屏视频](https://www.sitepoint.com/premium/screencasts/php-dependencies-made-easy-with-composer?utm_source=blog&utm_medium=articles)，了解 Composer 如何帮助您实现这一点。

## 分享这篇文章