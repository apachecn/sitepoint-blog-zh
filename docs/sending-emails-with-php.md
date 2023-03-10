# 用 PHP 发送电子邮件

> 原文：<https://www.sitepoint.com/sending-emails-with-php/>

我们知道你为什么在这里…你想写一个脚本，用有趣的回复地址给你的朋友发送电子邮件。我是来告诉你你可以做得更好。在你的生活中，你可以做更多的事情！但是，你还能有什么其他原因呢？

好吧，也许你有一个 cron 作业，你想通知你一些问题，有一个用户启动的脚本，你想在运行时得到通知，有一个“联系我们”的表单转发消息给你，想要展示你的 PHP 肌肉和编写你自己的基于 web 的邮件客户端，需要设置一个通过电子邮件确认的脚本——还有很多其他原因用 PHP 发送电子邮件。另外，这太简单了！

在大多数情况下，你安装的 PHP 将能够发送电子邮件。如果您使用的是共享主机，或者如果您使用 apt-get 这样的包管理系统安装了 PHP，那么很可能您已经准备好了。如果你从源代码编译 PHP 或者在 Windows 上运行它，你真的只需要担心额外的配置。不管是哪种情况，网上都有很多资源可以帮助你。因为这超出了本文的范围，所以我假设您已经准备好了。如果没有，谷歌会是你的朋友。

## 超级基本的例子

要发送一封非常简单的电子邮件，代码是:

```
<?php
mail($address, $subject, $message);
```

实际上，这就是发送基本的纯文本电子邮件的全部内容(如果这对您不起作用，请再次使用 Google 查看如何正确配置 PHP)。

现在让我们看看这在脚本中会是什么样子。例如，假设您希望脚本在每次查询失败时都向您发送电子邮件:

```
<?php
$query = "SELECT left_arm AS arm_in, right_leg AS leg_in, front_head AS head_in FROM hokey_pokey WHERE its_about = 'all'";
try {
    $result = $db->query($query);
    // ...
}
catch (PDOException $e) {
    mail("bad_things@my_database.com", "Error in " . $_SERVER["SCRIPT_NAME"], $e->getMessage());
}
```

如果在执行查询的过程中出现了一些不可预见的错误，那么您将会收到一封电子邮件，通知您哪个脚本出现了错误以及错误是什么。

## 一个成熟的 HTML 邮件示例

现在，让我们来看一个完整的多部分`mail()`示例，它有一个 HTML 主体，带有一个纯文本替代项和一个文件附件:

```
<?php
$emailList = array("first_sucker@wazoo.com",
                   "nextSucker@hemail.com",
                   "othersucker32@clotmail.com",
                   "sckrfnl@mayohell.com");

$headers = "From: "Fluffy Mumsy" <sincere@dumsing.com>rn" .
   "Reply-To: weregonnaberich@shhhsecret.comrn" .
   "MIME-Version: 1.0rn" .
   "Content-Type: multipart/mixed; boundary="YaGottaKeepEmSeparated"rn";
$subject = "Please donate all your moneys to us";
$goodAttachment = chunk_split(base64_encode(file_get_contents( "novyrus.zip")));
$body = "--YaGottaKeepEmSeparatedrn" .
    "Content-Type: multipart/alternative; boundary="EachEmailAlternative"rn" .
     "--EachEmailAlternativern" .
      "Content-Type: text/plain; charset="iso-8859-1"rn" .
      "Content-Transfer-Encoding: 7bitrn" .
      "You have cheap text email you have no money. Please ignore.rn" .
     "--EachEmailAlternativern" .
      "Content-Type: text/html; charset="iso-8859-1"rn" .
       "<html>
 <head>
  <title>We need money to give you</title>
 </head>
 <body>
  <p>We found some <span style='color:green'>money</span>. And we need you give us money to give some you. We is good people. You can trust. Please install contact information attachment</p>
 </body>
</html>rn" .
    "--YaGottaKeepEmSeparatedrn" .
     "Content-Type: application/zip; name="novyrus.zip"rn" .
     "Content-Transfer-Encoding: base64rn" .
     "Content-Disposition: attachmentrn" .
     $goodAttachment . "rn" .
    "--YaGottaKeepEmSeparated--";

foreach ($emailList AS $addy) {
   $success = mail($addy, $subject, $body, $headers);
   if (!$success) {
       echo "Mail to " . $addy . " is fail.";
   }
}
```

更典型的电子邮件脚本的某些方面已经被简化，以使一般概念更容易掌握，尽管我将在这次分类中触及这些。

首先，`$emailList`数组填充了一些我想与之分享消息的电子邮件地址。该数组在脚本的末尾被迭代，每个地址都将收到一份我的电子邮件。

接下来，`$headers`字符串是用各种邮件头构建的。根据定义电子邮件格式的标准 [RFC 2822](http://www.ietf.org/rfc/rfc2822.txt "Internet Message Format") ，字符串中的每个报头都用 CRLF ( `rn`)分隔。

`"From: "Fluffy Mumsy" <sincere@dumsing.com>rn"`
“发件人”标题指定收件人将看到的邮件发件人的电子邮件地址。

`"Reply-To: weregonnaberich@shhhsecret.comrn"`
“回复”标题是电子邮件回复应该发送到的电子邮件地址。通过使其不同于“发件人:”标题，该电子邮件被识别为垃圾邮件的机会增加(尽管如果这是导致电子邮件客户端发出红色标记的唯一原因，则该邮件是垃圾邮件，那么它可能会通过)。

`"MIME-Version: 1.0rn"`
MIME-Version 头告诉服务器在正文中期待*多用途互联网邮件扩展*，这允许您拥有比简单文本更高级的电子邮件。

`"Content-Type: multipart/mixed; boundary="YaGottaKeepEmSeparated"rn"`
“Content-Type”头实际上做了两件事:它表明在各种类型的主体中将有多个部分，它指定用什么字符串来划分每个部分。此边界字符串不能出现在电子邮件的任何其他地方，否则邮件客户端将无法正确解析邮件。例如，您可以使用“12”作为您的界限，尽管它可能会出现在消息的其他地方。我选择了“YaGottaKeepEmSeparated”。大多数人分配一个随机生成的散列作为边界，像`$boundary = md5(time())`，因为冲突的几率非常低。

`novyrus.zip`的内容恰好位于与脚本相同的目录中，它是 base64 编码的，并被分成“块”,以便邮件客户端更容易理解。结果存储在`$goodAttachment`中，稍后会出现。

最后，电子邮件的正文由…

`"--YaGottaKeepEmSeparatedrn"`
这是使用前面定义的边界的第一个实例，并对邮件客户端说，“嘿，这是电子邮件消息的第一部分的开始。”，它总是以您选择的边界字符串前面的双破折号开始。

`"Content-Type: multipart/alternative; boundary="EachEmailAlternative"rn"`
除了电子邮件标题中给出的“多部分/混合”之外，您还可以在正文中使用带有“多部分/替代”的内容类型标题，并使用特定于此细分的不同边界来为消息提供替代格式。

`"--EachEmailAlternativern"`
这是嵌套边界的第一个实例，并开始消息的第一个替代版本。

`"Content-Type: text/plain; charset="iso-8859-1"rn"`
这个 Content-Type 头告诉邮件客户端这个选项是纯文本。如果客户端不能显示更复杂的格式，比如 HTML，那么它将使用这个版本的消息。

`"Content-Transfer-Encoding: 7bitrn"`
Content-Transfer-Encoding 头指定消息中使用的编码方案。出于历史原因，“7 位”是默认值，因此可以省略。我把它包括进来只是为了让你知道。

`"You have cheap text email you have no money. Please ignore.rn"`
这是消息的纯文本版本，使用非 HTML 功能的读者将会看到。

`"--EachEmailAlternativern"`
已到达第一个备选版本的末尾，您准备开始下一个备选版本。

`"Content-Type: text/html; charset="iso-8859-1"rn"`
这个 Content-Type 头通知客户端这个版本被格式化为 HTML，以及所使用的字符集。

`"<html> ... </html>rn"`
注意，除了包含 HTML 标签之外，这个版本的内容与纯文本版本有很大不同。一些垃圾邮件过滤器可能认为这是阻止我的邮件进入收件箱的又一个原因。

`"--YaGottaKeepEmSeparatedrn"`
这是多部分/混合边界，表示您已经到达了包含所有替代项的消息正文部分的末尾。

`"Content-Type: application/zip; name="novyrus.zip"rn"`
内容类型标题表示电子邮件的下一部分是附件(`novyrus.zip`文件),并且是 ZIP 文件。

`"Content-Transfer-Encoding: base64rn"`
7 位编码将字符限制为 7 位，可能无法忠实地表示 ZIP 文件中所有必要的二进制字符，这就是该文件采用 base64 编码和分块的原因。这里的 Content-Transfer-Encoding 头让客户机知道如何解码附件文件。

`"Content-Disposition: attachmentrn"`
Content-Disposition 头详细说明了内容应该如何呈现；有两个可能的值:*附件*和*内联*。虽然在邮件中显示 ZIP 文件内联元素几乎没有意义，但嵌入图像很方便。

`$goodAttachment . "rn"`
附加文件的内容被简单地转储到 mix 中。

这是最后的边界，以最后一组双破折号结束，宣告不再有任何东西跟随。

## 摘要

你有它！您已经看到了如何发送超级基本文本电子邮件消息，以及带有附件的完整 HTML 电子邮件。简单的邮件只需调用 mail()函数即可。对于 HTML 邮件，您需要使用 MIME 标准将您的电子邮件分成多个部分，并根据您选择的边界进行划分。然后，您定义内容是什么，如何编码，可能还有处置，然后是内容本身。根据你打算向谁发送电子邮件，你会希望认真考虑可能导致你的邮件更有可能被标记为垃圾邮件的因素，以防万一你真的想发送一些严肃的东西。

图片通过[Photosani](http://www.shutterstock.com/gallery-54706p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章