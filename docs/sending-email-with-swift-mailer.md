# 使用 Swift Mailer 发送电子邮件

> 原文：<https://www.sitepoint.com/sending-email-with-swift-mailer/>

以编程方式发送电子邮件是程序员必须经常处理的常见任务。虽然您可以使用 PHP 的本地函数，但是它们可能太低级了，尤其是当您想要在消息中附加一个或多个文件时。如果您不想使用本机函数，或者如果您想使用面向对象的方法发送邮件，那么这就是适合您的文章。我将向您介绍 Swift Mailer，这是一个强大的基于组件的库，可以让您轻松发送电子邮件。

成立于 2005 年的 [Swift Mailer](http://swiftmailer.org/ "Swift Mailer official website") 是一个库，它提供了几个类，允许你从 PHP 脚本或 web 应用程序中发送电子邮件。Swift Mailer 有一些要求，但是它们是最小的；该库需要 PHP 5.2 或更高版本，具有 SPL 扩展，最小内存限制为 8MB。

引用[官方文档](http://swiftmailer.org/docs/installing.html "Installing the Library - Documentation - Swift Mailer")，安装 Swift Mailer 很琐碎。通常只是将提取的源文件上传到你的 web 服务器上。安装这个库最简单的方法是通过 PEAR 或者使用 FTP 上传文件。

从 PEAR 通道安装只需要键入两个命令:

```
pear channel-discover pear.swiftmailer.org
pear install swift/swift
```

第二种方法可能是最简单的一种，如果你在一个共享主机上，并且你没有访问命令行的权限。只需[从官网下载库](http://swiftmailer.org/download "download page Swift Mailer")，上传`lib`文件夹即可。

## Swift Mailer 系列课程

Swift Mailer 由 150 多个类和接口组成，这些类和接口组合在几个组件中。他们每个人都有特定的目的。

*   **Mailer**:Mailer 由`Swift_Mailer`类实现，是库中的中心类，也是通过`send()`方法实际发送邮件的类。它的构造函数接受一个实现接口`Swift_Transport`的类的实例，该接口允许您使用自定义 SMTP 发送电子邮件。
*   **Transporters** :他们实现了接口`Swift_Transport`，他们的作用范围是与服务进行通信以传递电子邮件消息。主要的类是使用 SMTP 协议的`Swift_SmtpTransport`、与本地 sendmail 可执行文件通信的`Swift_SendmailTransport`和依赖 PHP 本地`mail()`函数的`Swift_MailTransport`。
*   MIME 实体:这是库中的一个关键组件。您将用来指定接收者、主题、主体等的类称为`Swift_Message`，由几个组合在一起的实体组成。实体的两个例子是附件和电子邮件标题。
*   编码器:这个组件不是你要处理的东西，因为它在幕后工作。有两种类型的编码器:Base64 和报价可打印。他们的目标是将内容转换成符合 RFC 规范的格式。
*   **插件**:用于扩展 Swift Mailer 的基本功能。一些插件是反 Flood，允许你限制在一个 SMTP 连接中发送的消息数量，Logger，用于记录发送过程中发生的事情，以及 Decorator，一个惊人的插件，允许你使用电子邮件模板，只改变电子邮件文本中的一些差异。

## 一个基本例子

跟我到目前为止？很好。现在，您已经对库中的组件是如何组织的有了一个概念，让我们动手看看如何利用 Swift Mailer 的强大功能和灵活性。在第一个非常简单的例子中，我将向您展示如何创建一封包含两个收件人、一个主题和一个纯文本正文的电子邮件。一旦创建，它将被发送给收件人。

下面的代码使用了上一节提到的三个类:`Swift_MailTransport`、`Swift_Mailer`和`Swift_Message`。此外，请注意代码的第一行,`require_once`,它获取使用库所需的 Swift Mailer 的自动加载程序。

```
<?php
require_once 'lib/swift_required.php';

// Create the mail transport configuration
$transport = Swift_MailTransport::newInstance();

// Create the message
$message = Swift_Message::newInstance();
$message->setTo(array(
  "hello@gmail.com" => "Aurelio De Rosa",
  "test@fake.com" => "Audero"
));
$message->setSubject("This email is sent using Swift Mailer");
$message->setBody("You're our best client ever.");
$message->setFrom("account@bank.com", "Your bank");

// Send the email
$mailer = Swift_Mailer::newInstance($transport);
$mailer->send($message);
```

如您所见，`Swift_MailTransport`用于创建一个传输层实例，该实例将使用原生 PHP `mail()`函数。然后我创建了一个`Swift_Message`实例，你可以把它看作电子邮件对象。在下面几行中，我使用`setTo()`方法设置了收件人，使用`setSubject()`设置了邮件主题，使用`setBody()`设置了邮件正文，使用`setFrom()`方法设置了发件人。定义了层并创建了电子邮件之后，是时候实际发送电子邮件了，这是使用`Swift_Mailer`类的`send()`方法完成的。

## 带附件的示例

毫无疑问，附件是当今电子邮件系统的主要组成部分。没有他们谁也活不下去。这个稍微复杂一点的例子将向你展示如何发送一封不仅有附件还有其他功能的电子邮件。

*   两个附加接收器，一个在 CC，另一个在 BCC，使用`setCc()`和`setBcc()`。
*   使用`attach()`方法从磁盘中检索一个附件。
*   使用`Swift_SmtpTransport`类的自定义 SMTP 配置。
*   使用`send()`方法的第二个参数知道哪些收件人没有收到邮件。

```
<?php
require_once 'lib/swift_required.php';

// Create the SMTP configuration
$transport = Swift_SmtpTransport::newInstance("smtp.fake.com", 25);
$transport->setUsername("Username");
$transport->setPassword("Password");

// Create the message
$message = Swift_Message::newInstance();
$message->setTo(array(
   "hello@gmail.com" => "Aurelio De Rosa",
   "test@fake.com" => "Audero"
));
$message->setCc(array("another@fake.com" => "Aurelio De Rosa"));
$message->setBcc(array("boss@bank.com" => "Bank Boss"));
$message->setSubject("This email is sent using Swift Mailer");
$message->setBody("You're our best client ever.");
$message->setFrom("account@bank.com", "Your bank");
$message->attach(Swift_Attachment::fromPath("path/to/file/file.zip"));

// Send the email
$mailer = Swift_Mailer::newInstance($transport);
$mailer->send($message, $failedRecipients);

// Show failed recipients
print_r($failedRecipients);
```

我使用了不同的传输层，一个使用`Swift_SmtpTransport`类设置的 <abbr title="Simple Message Transfer Protocol">SMTP</abbr> 。它接受两个参数:SMTP 服务器和连接端口。您使用实例来设置适当的用户名和密码，以便使用`setUsername()`和`setPassword()`方法访问服务器。然后，就像第一个例子一样，我创建了一个`Swift_Message`对象，并设置了收件人、主题等等。然而，这次我还利用了`setCc()`和`setBcc()`方法，正如你可能猜到的，它们允许你设置抄送和盲抄送接收者。

这个例子的关键方法是`attach()`，它使用静态方法`fromPath()`附加一个从硬盘上获取的文件，该静态方法将您想要附加的文件的路径作为其参数。注意，这次我还打印了使用`send()`方法的第二个参数检索到的失败收件人的数量。

## 使用模板

第三个也是最后一个例子展示了如何使用 Decorator 插件，这是向订阅者发送时事通讯的理想选择。它允许您将同一封电子邮件发送给几个有细微差别的收件人，如电子邮件正文中的收件人姓名。该插件将查看模板(主体)内部，并用设置值替换占位符。

要使用插件，首先需要使用`registerPlugin()`方法注册它，然后构建并使用替换值数组。在下面的代码中，我将放置两个占位符，*用户名*和*交易*，这两个占位符将由引用用户电子邮件的值以编程方式替换。

```
<?php
require_once 'lib/swift_required.php';

// Usually you want to replace the following static array
// with a dynamic one built retrieving users from the database
$users = array(
  array(
    "fullname" => "Aurelio De Rosa",
    "operations" => 100,
    "email" => "hello@gmail.com"
  ),
  array(
    "fullname" => "Audero",
    "operations" => 50,
    "email" => "test@fake.com"
  )
);

// Create the replacements array
$replacements = array();
foreach ($users as $user) {
  $replacements[$user["email"]] = array (
    "{fullname}" => $user["fullname"],
    "{transactions}" => $user["operations"]
  );
}

// Create the mail transport configuration
$transport = Swift_MailTransport::newInstance();

// Create an instance of the plugin and register it
$plugin = new Swift_Plugins_DecoratorPlugin($replacements);
$mailer = Swift_Mailer::newInstance($transport);
$mailer->registerPlugin($plugin);

// Create the message
$message = Swift_Message::newInstance();
$message->setSubject("This email is sent using Swift Mailer");
$message->setBody("You {fullname}, are our best client ever thanks " .
    " to the {transactions} transactions you made with us.");
$message->setFrom("account@bank.com", "Your bank");

// Send the email
foreach($users as $user) {
  $message->setTo($user["email"], $user["fullname"]);
  $mailer->send($message);
}
```

装饰插件构造函数接受一个参数:一个值数组来替换占位符。这个数组的每个值都使用用户的电子邮件作为它的键和一个包含占位符替换对的子数组。这正是`$replacements`数组的目的。在上面的例子中，作为占位符，我在两个括号内使用了一个字符串(即`{fullname}`)，但是你可以使用任何你想要的。这一次，我没有像以前一样在同一个语句中设置所有的接收者，而是使用了一个`for`循环。这样做是因为插件拦截发送过程，读取收件人电子邮件，并使用替换数组的值替换占位符。

## 结论

在本文中，我展示了如何使用 Swift Mailer 轻松发送电子邮件。这是一个非常强大的库，如你所见，它允许你使用 OOP 方法做很多事情。当然，Swift Mailer 还有很多其他的方法和类，我没有在本文中介绍。你可以在官方文档中对它们进行研究，但这应该足以让你开始发送电子邮件而不会有任何麻烦。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章