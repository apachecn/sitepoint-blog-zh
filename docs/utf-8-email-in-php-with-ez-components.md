# UTF-8 电子邮件在 PHP 与 eZ 组件

> 原文：<https://www.sitepoint.com/utf-8-email-in-php-with-ez-components/>

上周我提到的一个话题是你如何在电子邮件中处理 UTF-8，因为我不知道解决这个问题的最佳方式。维基百科对[哑剧](http://en.wikipedia.org/wiki/MIME)的讨论很好地总结了这个根本问题；

> 基本的互联网电子邮件传输协议 SMTP 仅支持 7 位 ASCII 字符[…]。这有效地将因特网电子邮件限制为在传输时仅包括足以书写少量语言(主要是英语)的字符的消息。基于拉丁字母的其他语言通常包含 7 位 ASCII 不支持的音调符号，这意味着这些语言的文本在基本电子邮件中无法正确表示。

部分问题是有不同的方法来解决这个问题。你发送编码为[引用可打印](http://en.wikipedia.org/wiki/Quoted-printable)或 [Base64](http://en.wikipedia.org/wiki/Base64) 的原始 UTF-8 文本正文吗？或者你可以把它转换成 UTF-7？或者将文本作为 html 发送，并将 HTML 实体用于任何非 ASCII 的内容是否更容易？这是否限制了可以阅读邮件的客户端数量？像主题或发送者/接收者姓名这样的头的编码又如何呢(如在 [iconv_mime_encode()](http://www.php.net/iconv_mime_encode) 中)？这里有很多陷阱，而且显然比你默认使用 PHP 的 [mail](http://www.php.net/mail) 函数得到的更多(这是在你为[电子邮件注入](http://www.securephpwiki.com/index.php/Email_Injection)修复代码之后)。换句话说，采取一种“[不是这里发明的](http://en.wikipedia.org/wiki/Not_Invented_Here)”观点会给你留下很大的工作量。

总之，在 [PHP London](http://www.phpconference.co.uk/) 看到 [Dericks](http://derickrethans.nl/) 在 [eZ components](http://ez.no/products/ez_components) 上的演讲，所以建议大家看看上周[演讲](https://www.sitepoint.com/utf-8-survival-at-webtuesdaych/)上的 [Mail](http://ez.no/doc/components/view/1.1/(file)/classtrees_Mail.html) 类。

通过浏览源代码(被[贾德·马迪人的博客](http://syntux.net/blog/2006/08/14/ez-components-mail-to-save-the-day/)提醒去看一看)，好消息是看起来 [eZ 系统](http://ez.no/)已经很好地解决了这个问题。ezcMail 类和相关类默认为(假设)ASCII，但是您可以明确地告诉它对主题、收件人、发件人和正文使用 UTF-8(注意 [iconv](http://www.php.net/iconv) 是必需的)。事实上，它是一个非常令人印象深刻的邮件库，处理解析以及生成多部分消息等等。

看起来您仍然需要知道如何处理消息的主体(这留下了一些关于最佳方法的未决问题),但是标题已经自动地为您进行了 mime 编码。如果我对 ezcMail 理解正确的话，下面这个(未经测试的)例子应该可以说明这一点，允许它被不是 [8 位 clean](http://en.wikipedia.org/wiki/8-bit_clean) 的 [MTA 的](http://en.wikipedia.org/wiki/Mail_transfer_agent)处理；

```
 <?php

// ...

$mail = new ezcMail();

// There are automatically encoded using iconv_mime_encode
$mail->from = new ezcMailAddress( 'hgraeser@xyz.com', 'Hans Gräser', 'UTF-8' );
$mail->addTo( new ezcMailAddress( 'werner@abc.com', 'Werner Dröge-Modelmog', 'UTF-8' ) );

$mail->subject = 'Ich bin spät...';

// Flag as UTF-8 - will also be automatically encoded with iconv_mime_encode
$mail->subjectCharset = 'UTF-8';

// Encode the UTF-8 as base64 for 7 bit MTAs
$mail->body = new ezcMailText(
    base64_encode('...noch 30 minuten bis Zürich'), 'UTF-8', ezcMail::BASE64
    );

// Send the mail
$transport = new ezcMailMtaTransport();
$transport->send( $mail ); 

```

否则，Patrice 在上周的会议上提出了一个一般性的问题，我也很想听听大家的意见:SMTP 的 7 位限制在多大程度上仍然是一个问题？现在,[已经被广泛使用，我们可以不用担心了吗？](http://en.wikipedia.org/wiki/8BITMIME)

## 分享这篇文章