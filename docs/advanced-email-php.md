# PHP 高级电子邮件

> 原文：<https://www.sitepoint.com/advanced-email-php/>

这篇文章写于 2002 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于电子邮件和网络邮件的知识，你可能会对这篇[关于电子邮件即服务](https://www.sitepoint.com/email-as-a-service-sendgrid-mailgun-and-postmark-compared/)的文章很感兴趣。

我希望我能记得我发出的第一封电子邮件。不幸的是，事实是我已经把电子邮件视为理所当然；例如，我发送的电子邮件比我打电话的数量要多。因为新鲜感早已褪去，那第一封邮件的记忆对我来说，就像第一个电话一样失去了；但我怀疑当时我的好奇心是否还没有完全消失。对于以前从未见过电子邮件的人来说，第一条信息可能是不可思议的。

在本文中，我将为那些从未创建过发送电子邮件的网站的人重新体验一下这种魔力。我们将了解如何设置 PHP 服务器端脚本语言来发送电子邮件，并探索如何发送复杂的消息类型，如 HTML 电子邮件或带有文件附件的电子邮件。

注意:这篇文章是在 HTML 电子邮件和文件附件比现在更难用 PHP 实现的时候写的。在深入研究本文中介绍的从头开始的解决方案之前，您可能会考虑研究一下 [PHPMailer](http://phpmailer.sourceforge.net/) ，这是一个免费的 PHP 库，它以最少的麻烦和痛苦提供了所有这些特性。

## PHP 电子邮件设置

在我们可以用 PHP 发送电子邮件之前，我们需要设置它这样做，就像你需要设置你的电子邮件程序才能发送信息一样。用 PHP 发送电子邮件的配置是通过`php.ini`文件完成的，所以在你通常使用的编辑器中打开你的 Web 服务器的`php.ini`。

如果您没有运行自己的服务器，而是有一个配有 PHP 的 Web 主机，那么您可以放心地假设本节中的所有内容都已经为您完成了，然后直接跳过。

在`php.ini`文件中标题为`[mail function]`的部分，你会发现三个设置:`SMTP`、`sendmail_from`和`sendmail_path`。如果您的服务器运行在 Windows 机器上，您将希望设置`SMTP`选项指向您的 SMTP 服务器(或者您的 ISP 的 SMTP 服务器，如果您正在您的家庭机器上设置 PHP 的话)。相反，如果您在 Linux(或其他基于 Unix 的 OS)服务器上设置 PHP，您将希望设置`sendmail_path`选项指向服务器上的`sendmail`程序，并传递给它`-t`选项。如果您没有设置`sendmail`，您可以使用 Linux 中的 SMTP 选项。

在这两种情况下，您都需要将`sendmail_from`选项设置为您的电子邮件地址，或者任何您希望显示为 PHP 脚本发送的电子邮件的默认“发件人”地址的地址。

下面是该部分在典型的 Windows 服务器上或在没有 sendmail 的 Linux 服务器上的外观:

```
[mail function]
; Setup for Windows systems
SMTP = *smtp.my.isp.net*
sendmail_from = *me@myserver.com*
```

下面是它在安装了 sendmail 的 Linux 服务器上的样子:

```
[mail function]
; Setup for Linux systems
sendmail_path = /usr/sbin/sendmail -t
sendmail_from = me@myserver.com
```

有了这些设置，重启你的网络服务器，你就可以开始了！

## 发送普通电子邮件

没有比用 PHP 发送纯文本邮件更简单的了。事实上，您可以在 PHP 脚本的一行代码中完成:

```
<?php
mail('recipient@some.net', 'Subject', 'Your message here.');
?>
```

上面的行将向`recipient@some.net`发送一封电子邮件，主题行为“`Subject`”，正文为“`Your message here.`”。正如你所看到的，PHP 的`mail`函数使发送电子邮件变得非常简单，但是我们可以使用一些高级技巧来从这个简单的函数中获得更多。

首先，如果您在`php.ini`文件中配置的邮件系统拒绝了您尝试发送的邮件(例如，如果“收件人”地址不是有效的电子邮件地址)，该功能将在用户的浏览器中显示一条错误消息。然而，像大多数 PHP 函数一样，可以通过在函数名前面加上@符号来隐藏错误消息。结合邮件函数根据邮件发送系统是否接受电子邮件而返回 true 或 false 的事实，您就有了一个发送带有适当错误检查的电子邮件的公式:

```
<?php
if (@mail($to, $subject, $message)) {
echo('<p>Mail sent successfully.</p>');
} else {
echo('<p>Mail could not be sent.</p>');
}
?>
```

请注意，仅仅因为一封电子邮件可以发送，并不保证它会到达其目的地。电子邮件地址可能有效(即格式正确)，但实际上并不存在。例如，您可以成功地向`nonexistant.user@hotmail.com`发送一条消息——也就是说，mail 函数将返回 true——但是这条消息将被退回，因为不存在这样的用户。PHP 没有提供检测这种情况发生的内置方法。

当您想要向多个收件人发送消息时，您可以在第一个参数中依次列出他们的电子邮件地址，用逗号分隔。例如:

```
<?php
mail('recipient1@some.net, recipient2@some.net',
'An email to two people', 'Message goes here.');
?>
```

这大概涵盖了`mail`函数的基础知识；现在，让我们真正发挥想象力，探索邮件标题以及我们可以用它们做什么！

##### HTML 电子邮件和其他标题

所以现在你可以用你的 PHP 脚本发送邮件了。多刺激啊！虽然我确信你已经陶醉于权力，你也想知道如何发送 HTML 电子邮件吗？当然，你会！

要理解 HTML 电子邮件，首先你需要理解邮件标题。任何给定的电子邮件都由两部分组成:邮件头和邮件正文。当您的电子邮件程序从 ISP 处获取一条简单的邮件时，它看起来是这样的:

```
Return-Path: <sender@elsewhere.com>
Delivered-To: you@some.net
Received: ...several lines like this...
From: Sender <sender@elsewhere.com>
To: You <you@some.net>
Subject: A Simple Message
Date: Mon, 11 Feb 2002 16:08:19 -0500
Organization: Sender's Company
X-Mailer: Microsoft Outlook, Build 10.0.2616
```

```
Hi there! <tap> <tap> Is this thing on?
```

直到空白行的所有内容构成了该消息的标题。事实上，大多数电子邮件的标题行会比这多得多；然而，为了保持我们的重点，我把这个例子精简到最基本的部分。

如您所见，每一行标题都以标题的名称开始(`From:`、`To:`、`Subject:`、`Date:`等)。)，后面是一些值。大多数邮件头都是标准化的，对你的邮件程序或负责将邮件发送给你的邮件服务器都有特定的含义。非标准的头也存在，它们都以`X-`开始(例如`X-Mailer:`，一个非标准的头，经常出现来指示用来发送消息的程序)。

<q>**注意:**如果一个表头的值需要多行，额外的行要以空格开头。我们将在下一节看到这样的例子。</q>

一旦你的邮件程序到达一个空行，它就知道邮件头结束了，邮件的其余部分是邮件正文，它应该显示出来。在这种情况下，消息体是上面的最后一行。

PHP 的`mail`函数允许你指定你自己的头，它把你的头添加到它自动生成的头中，让你的消息到达它需要去的地方。例如，下面对`mail`函数的调用将向外发的消息添加一个`X-Mailer:`头，将 PHP 4.x 标识为发送程序:

```
<?php
mail('recipient@some.net', 'Subject', 'Your message here.',
'X-Mailer: PHP 4.x');
?>
```

这个可选的第四个参数最常用于指定一个“发件人”地址，而不是在`php.ini`中指定的默认地址。让我们添加一个 From:头来实现这一点:

```
<?php
mail('recipient@some.net', 'Subject', 'Your message here.',
"From: sender@some.netnX-Mailer: PHP 4.x");
?>
```

注意，由于每个标题都出现在一行中，我们必须用一个新的行字符(`n`)分隔两个标题，这意味着我需要在标题字符串周围使用双引号(单引号字符串不识别像`n`这样的特殊字符代码)。

附加标题还允许您通过以格式`name <email>`指定名称来为电子邮件地址指定名称。这又是我们的例子，但是这次名称“发送者”和“接收者”附加到相关的地址:

```
<?php
mail('recipient@some.net', 'Subject', 'Your message here.',
"To: The Receiver <recipient@some.net>n" .
"From: The Sender <sender@some.net>n" .
"X-Mailer: PHP 4.x");
?>
```

注意，要做到这一点，我们必须手动指定`To:`头，因为 PHP 的`mail`函数的第一个参数不支持这种更高级的地址格式。但是，我们仍然必须在第一个参数中列出邮件的所有收件人，作为裸露的电子邮件地址。

`cc:`和`Bcc:`标题还提供了发送消息的抄送和密件抄送的能力:

```
<?php
mail('recipient@some.net, someone@some.net, metoo@some.net',
'Subject', 'Your message here.',
"To: The Receiver <recipient@some.net>n" .
"From: The Sender <sender@some.net>n" .
"cc: Interested <someone@some.net>n" .
"Bcc: Me Too <metoo@some.net>n" .
"X-Mailer: PHP 4.x");
?>
```

看看如何在第一个参数中列出所有收件人的电子邮件地址，无论是实际收件人(To)、抄送人(Cc)还是密件抄送人(Bcc)？这在任何地方都没有记录，但是在我的测试中，我发现如果你想让消息到达它应该到达的每个人，这是绝对重要的，特别是在 Windows 服务器上，PHP 的`mail`功能特别敏感。

<q>**BUG ALERT:**PHP`mail`函数中有两个明显的 BUG，这是我最近在 PHP 4.1.0 中观察到的，你必须注意。首先，`Cc:`标题必须键入“`cc:`”(如上)或“【T3”)，即全部大写或小写。混合案例版本(`Cc:`)应该有效，但它没有。其次，在 Windows 服务器上，`Bcc:`头文件不能正常工作。`Bcc:`收件人确实会收到邮件的副本，但是在发送过程中不会从邮件中去掉`Bcc:`标题，因此收件人可以通过查看标题中的这些行来查看`Bcc:`收件人。</q>

你会问，所有这些与 HTML 电子邮件有什么关系？嗯，一些特殊的标题会导致邮件正文被支持它的电子邮件客户端解释为 HTML:

`<?php
mail('recipient@some.net', 'Subject',
'<html><body><p>Your <i>message</i> here.</p></body></html>',
"To: The Receiver <recipient@some.net>n" .
"From: The Sender <sender@some.net>n" .
"MIME-Version: 1.0n" .
"Content-type: text/html; charset=iso-8859-1");
?>`

注意 HTML 格式的消息以及两个新的头:`Mime-Version:`和`Content-type:`。

`MIME-Version:`头表示将使用标准的扩展邮件格式(MIME 代表多用途互联网邮件扩展),它允许以非纯文本的内容类型发送邮件。然后，`Content-type:`头指定消息包含 HTML(并将字符集设置为该格式的标准)。

##### 混杂的信息

在生活中，传递混杂的信息很少是好的。至少，我上一任女朋友是这么跟我说的！然而，当涉及到电子邮件时，混合信息提供了大量的能量。一封电子邮件可以包含文本和 HTML 版本的信息。这使得它可以在几乎任何电子邮件程序中查看，并且您不会为适当配备的读者牺牲 HTML 格式的能力。

要知道混合信息有其弱点。首先，由于你要发送两种版本的信息，信息通常会比你只发送一种格式的信息大很多。还要注意，不识别混合邮件格式的旧电子邮件程序可能会将邮件的两个版本都显示为文件附件(一个文本，另一个 HTML)。

让我们看一个简单的混合电子邮件消息的例子，然后看发送它的 PHP 代码:

```
Date: Mon, 11 Feb 2002 16:08:19 -0500
To: The Receiver <recipient@some.net>
From: The Sender <sender@some.net>
Subject: A simple mixed message
MIME-Version: 1.0
Content-Type: multipart/alternative;
boundary="==Multipart_Boundary_xc75j85x"
```

```
This is a multi-part message in MIME format.

--==Multipart_Boundary_xc75j85x
Content-Type: text/plain; charset="iso-8859-1"
Content-Transfer-Encoding: 7bit

This is the text portion of the mixed message.

--==Multipart_Boundary_xc75j85x
Content-Type: text/html; charset="iso-8859-1"
Content-Transfer-Encoding: 7bit

<html>
<body>
<p>This is the <b>HTML portion</b> of the mixed message.</p>
</body>
</html> 
```

`--==Multipart_Boundary_xc75j85x--`

在邮件顶部最初的标准标题之后，我们有了`MIME-Version: 1.0`标题，它支持我们需要的高级电子邮件功能。`Content-Type:`标题是事情开始变得古怪的地方:

`Content-Type: multipart/alternative;
boundary="==Multipart_Boundary_xxc75885"`

我们指定了一个内容类型`multipart/alternative`，这是一个特殊的类型，它允许我们发送一个包含两个或更多备选版本的消息(收件人的电子邮件程序将从中选择最适合显示的版本)。此外，我们使用`Content-Type`头来设置一个*边界字符串*。

为了保持标题行的简短，标题的这一部分出现在第二行(正如上面的注释中提到的，第二行必须以一个或多个空格开始，以表明它是前一行的延续)。

在这种情况下，我选择了“`==Multipart_Boundary_xc75j85x`”作为边界字符串。除了不太可能作为消息本身的一部分出现之外，该字符串没有任何特殊意义。我使用了等号和下划线等字符，以及半随机的字母和数字字符串来帮助确保这一点。然后，我们使用这个字符串将消息分成几个部分。

文本“`This is a multi-part message in MIME format.`”是为旧邮件程序的好处而包含的，以便用户对为什么电子邮件可能不像预期的那样出现有所了解。排除免责声明后，我们使用边界来标记消息第一部分的开始:

```
--==Multipart_Boundary_xc75j85x
Content-Type: text/plain; charset="iso-8859-1"
Content-Transfer-Encoding: 7bit
```

这是混合消息的文本部分。

注意，当我们实际使用边界字符串时，我们在它的开头添加了两个破折号(`--`)。

在第一个边界之后，我们开始消息的文本版本。消息的每个部分都以几个头开始，以指示其内容类型和编码。对于文本部分，内容类型为`text/plain`(使用标准字符集，`iso-8859-1`)，编码为`7bit`(纯 ASCII 文本)。一个空行标记了邮件头的结尾，后面是邮件正文。

接下来是 HTML 版本:

```
--==Multipart_Boundary_xc75j85x
Content-Type: text/html; charset="iso-8859-1"
Content-Transfer-Encoding: 7bit
```

```
<html>
<body>
<p>This is the <b>HTML portion</b> of the mixed message.</p>
</body>
</html>
```

标题几乎与文本部分相同，但是这次我们指定`text/html`作为内容类型。在 HTML 文档的主体之后，剩下的就是结束边界，我们在结尾额外添加两个破折号来标记消息的结束:

`--==Multipart_Boundary_xc75j85x--`

正如您所看到的，混合消息看起来可能很复杂，但是当您仔细观察时，它们实际上非常简单。从想要发送这样一条消息的 PHP 开发人员的角度来看，唯一棘手的部分是生成一个边界字符串的任务。

我喜欢这样做:

```
<?php
$semi_rand = md5(time());
$mime_boundary = "==Multipart_Boundary_x{$semi_rand}x";
?>
```

我们获取当前的 Unix 时间戳(自 1970 年 1 月 1 日以来的秒数)，由`time()`给出，并使用 MD5 算法将其转换为半随机字符串。然后，该字符串被用于构成边界字符串的一部分。随意使用你喜欢的任何方法来生成你的边界字符串。

记住所有这些，您应该能够用 PHP 生成混合消息。由于混合消息相对不常见，我将把实际代码留给您来编写，作为练习。然而，当涉及到文件附件(我们最后一节的主题)时，用边界字符串将消息分成几个部分是很重要的。

## 文件附件

文件附件的工作方式就像混合电子邮件一样，只是整个邮件使用了不同的内容类型(`multipart/mixed`而不是`multipart/alternative`)，并且有一个新的`Content-Disposition`头，告诉电子邮件客户端如何处理邮件的每个部分。

让我们编写一个 PHP 脚本来处理一个表单提交，该表单提交包含一个要发送的电子邮件，可能带有文件附件，然后发送出去。我将一行一行地告诉你，这样到最后你不仅会有一个有用的 PHP 代码片段，还会理解文件附件是如何工作的。如果你想亲自尝试一下，你可以下载脚本(和它的格式)。

首先，我们获取提交的值，并将它们放在 PHP 变量中。大多数人都将他们的服务器设置为自动为提交的值创建全局变量，但从 PHP 4.1 开始，这不再是默认设置，所以我们手动创建以防万一。因为我们希望接受文件附件，所以可以安全地假设表单将与 POST 请求一起提交:

```
<?php
// Read POST request params into global vars
$to      = $_POST['to'];
$from    = $_POST['from'];
$subject = $_POST['subject'];
$message = $_POST['message'];
```

PHP 4.1 中的文件上传被放在一个特殊的`$_FILES`数组中，所以我们从中获取我们需要的值:

```
// Obtain file upload vars
$fileatt      = $_FILES['fileatt']['tmp_name'];
$fileatt_type = $_FILES['fileatt']['type'];
$fileatt_name = $_FILES['fileatt']['name'];
```

为了简洁起见，我们假设必需的参数(`$to`和`$from`)现在都有有效值(电子邮件地址)。通常我们会用正则表达式检查它们的格式。

接下来，我们使用`$from`值开始为电子邮件构建额外的标题:

`$headers = "From: $from";`

接下来我们检查`$fileatt`变量，它可能包含也可能不包含上传文件附件的路径和文件名。我们使用 PHP 的`is_uploaded_file`函数来找出:

```
if (is_uploaded_file($fileatt)) {
// Read the file to be attached ('rb' = read binary)
$file = fopen($fileatt,'rb');
$data = fread($file,filesize($fileatt));
fclose($file);
```

读入文件附件的数据后，我们需要设置消息头来发送一条`multipart/mixed`消息:

```
 // Generate a boundary string
$semi_rand = md5(time());
$mime_boundary = "==Multipart_Boundary_x{$semi_rand}x";
```

```
// Add the headers for a file attachment
$headers .= "nMIME-Version: 1.0n" .
"Content-Type: multipart/mixed;n" .
" boundary="{$mime_boundary}"";
```

现在来看消息体本身。这就像我们在前一节中看到的混合消息的文本部分一样:

```
 // Add a multipart boundary above the plain message
$message = "This is a multi-part message in MIME format.nn" .
"--{$mime_boundary}n" .
"Content-Type: text/plain; charset="iso-8859-1"n" .
"Content-Transfer-Encoding: 7bitnn" .
$message . "nn";
```

现在，考虑到二进制文件类型，我们需要使用 Base64 编码将(可能是二进制的)文件附件数据转换为适合通过电子邮件发送的纯文本格式。所有流行的电子邮件程序都支持文件附件的 Base64 编码，所以这是最好的方法。幸运的是，PHP 为 Base64 编码提供了一个函数:

```
 // Base64 encode the file data
$data = chunk_split(base64_encode($data));
```

现在，我们已经具备了编写包含文件附件的消息部分所需的一切。代码如下:

```
 // Add file attachment to the message
$message .= "--{$mime_boundary}n" .
"Content-Type: {$fileatt_type};n" .
" name="{$fileatt_name}"n" .
"Content-Disposition: attachment;n" .
" filename="{$fileatt_name}"n" .
"Content-Transfer-Encoding: base64nn" .
$data . "nn" .
"--{$mime_boundary}--n";
}
```

这就完成了容纳文件附件所需的修改。我们现在可以通过快速呼叫向`mail`发送消息:

```
// Send the message
$ok = @mail($to, $subject, $message, $headers);
if ($ok) {
echo "<p>Mail sent! Yay PHP!</p>";
} else {
echo "<p>Mail could not be sent. Sorry!</p>";
}
?>
```

这就是我们用 PHP 发送带有文件附件的电子邮件的方式！

## 摘要

在这篇文章中，你可能学到了比你想知道的更多的关于电子邮件的知识。关于电子邮件运行原理的深入知识让我们可以用 PHP 看似简单的`mail`函数做一些非常特别的事情。

*   我们学习了如何操纵邮件标题来玩弄邮件的收件人
*   我们看到了允许您发送 HTML 电子邮件的标题
*   我们将一封电子邮件的 HTML 和文本版本合并成一条混合消息

最后…

*   我们把它们结合在一起，用一些新的技巧来发送带有文件附件的电子邮件。

如果您想接受挑战，可以尝试扩展本文中的文件附件脚本，以允许多个文件附件，或者允许 HTML 消息。如果您是面向对象的 PHP 类型，尝试开发一个封装了我们在本文中探索的功能的类(如果您想要一些现成的东西，可以在互联网上找到许多这样的脚本)。

如果你*真的*感兴趣，去看看 MIME 扩展的 RFC([RFC 2045](http://www.ietf.org/rfc/rfc2045.txt))来发现电子邮件能做的一切，然后尽情发挥吧！

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [PHP & MySQL 初学者网络开发](https://learnable.com/courses/php-mysql-web-development-for-beginners-13)。

## 分享这篇文章