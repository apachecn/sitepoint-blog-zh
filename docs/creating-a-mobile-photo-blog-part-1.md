# 创建移动照片博客，第 1 部分

> 原文：<https://www.sitepoint.com/creating-a-mobile-photo-blog-part-1/>

似乎现在每个人都在用手机发短信，或者每 5 分钟更新一次社交网络状态。可以随时随地访问互联网的便利让分享信息和图片变得如此流行，这一点也不奇怪。我无法想象不带手机去任何地方，万一有什么有趣的事情发生，我可以记录下来，就好像我是第一个到达现场的新闻记者一样。

这是由两部分组成的系列文章的第一部分，在这个系列文章中，我将向您展示如何创建一个照片博客，作为您个人网站的一部分，您只需通过发送电子邮件，就可以从您的手机上更新它。您将编写一个脚本来使用 POP3 检查电子邮件帐户的收件箱中是否有新邮件；该脚本将提取邮件的主题行、正文和附件，并相应地更新数据库。然后，您可以从数据库中提取信息，显示在您的博客、侧边栏或其他您认为合适的地方。

这些代码将在网络服务器上运行，实际上是独立于平台的，而不是为 Android 或 iPhone 开发移动应用程序。关于 PHP，我将使用 [IMAP](http://php.net/imap "PHP: IMAP - Manual") 和 [Imagick](http://php.net/imagick "PHP: Imagick - Manual") 扩展，所以在开始之前，你需要确保它们已经安装。

## 安全考虑

出于安全原因，您显然需要某种类型的身份验证。要做到这一点，你首先需要为网络服务器建立一个只有你知道的私人电子邮件帐户。这是将接收更新的帐户。然后，您将需要第二个电子邮件帐户，以便您的移动设备发送更新。在整篇文章的代码中，我将使用私人邮箱*而不是@liberty2.say* 和公共邮箱*mobile@example.com*。

您可以应用到服务器电子邮件帐户的安全级别将根据您拥有的管理权限而有所不同。在最好的情况下，您希望您的邮件服务器拒绝该帐户上的所有传入邮件，但来自您手机公共地址的邮件除外。在最坏的情况下，你至少要确保它的垃圾邮件过滤器设置为最高可能的设置，并将你的手机地址添加到白名单中。

作为一个附加的安全层，每一封由*而不是@liberty2.say* 收到的电子邮件都将被分配一个唯一的令牌。令牌将会发送到您的手机上，同时还会附带一份简短的信息摘要，请您批准。要更新您的博客，您只需发送一封电子邮件，等待自动回复，然后打开提供的链接以批准更新。自动回复将由每隔几分钟作为 cron 作业执行的脚本发送，或者，如果你可以访问邮件服务器本身，你可以选择使用类似于 [procmail](http://www.procmail.org/ "Procmail Homepage") 或 [maildrop](http://www.courier-mta.org/maildrop/ "maildrop - mail delivery agent with filtering abilities") 的东西。

有了这些安全措施，你可以放心，没有你的同意，任何东西都不会被发表。

## 数据库ˌ资料库

这个项目至少需要三个表:blog_posts 表将存储博客 id、博客标题、正文和时间戳；images 表将存储图像 id、对博客 id 的引用以及图像在服务器上存储的文件路径；和 pending 表，该表将存储引用收件箱中消息号的消息 id、唯一的 32 字符 MD5 令牌和指示批准状态的标志。

以下是数据库的 CREATE TABLE 语句:

```
CREATE TABLE blog_posts (
  post_id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  title VARCHAR(100) NOT NULL,
  body TEXT NOT NULL,
  create_ts TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,

  PRIMARY KEY (post_id)
);

CREATE TABLE images (
  image_id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  post_id INTEGER UNSIGNED NOT NULL,
  image_path VARCHAR(200) NOT NULL,

  PRIMARY KEY (image_id),
  FOREIGN KEY (post_id) REFERENCES blog_posts(post_id)
);

CREATE TABLE pending (
  message_id INTEGER UNSIGNED NOT NULL,
  token CHAR(32) NOT NULL,
  is_valid ENUM('Y','N') DEFAULT 'N',

  PRIMARY KEY (message_id)
);
```

## 检索电子邮件

在检索电子邮件时，你可以选择使用 IMAP 或 POP3。两者的主要区别在于邮件存储的方法。POP3 希望你在自己的电脑上存储邮件，所以它只提供了一种方法，让你下载新邮件，然后服务器的副本就被删除了。另一方面，IMAP 更像“网络邮件”,因为所有的邮件都存储和保留在服务器上，你可以通过远程连接来访问，所以它提供了附加的功能，如文件夹管理。IMAP 要复杂得多，这是可以理解的，您可以通过比较两种协议的 RFC 来了解这一点。

对于所有的意图和目的，POP3 将完成这个任务，因为你不需要任何特殊的功能。PHP 提供了使用电子邮件协议的功能，包括 POP3 及其 IMAP 扩展。

关于该扩展的许多功能的文档是不完整的，因此它们不容易使用。幸运的是，Wil Barath 编写了一组包装函数并发布在[用户贡献的笔记中，提供了一个更易于使用的 API。以下类基于 Barath 的代码，应该复制并保存为 POP3.php:](http://www.php.net/manual/en/book.imap.php#96414 "PHP: IMAP - Manual")

```
<?php
class POP3
{
    private $conn;

    // make connection
    public function __construct($host, $user, $pass, $folder = "INBOX", $port = 110, $useSSL = false) {
        $ssl = ($useSSL) ? "" : "/novalidate-cert";
        $mailbox = sprintf("{%s:%d/pop3%s}%s", $host, $port, $ssl, $folder);
        $this->conn = imap_open($mailbox, $user, $pass);
    }

    // close connection and trigger expunge
    public function __destruct() {
        imap_close($this->conn, CL_EXPUNGE);
    }

    // retrieve a list of messages
    public function listMessages($msgNum = "") {
        $msgList = array();
        if ($msgNum) {
            $range = $msgNum;
        }
        else {
            $info = imap_check($this->conn);
            $range = "1:" . $info->Nmsgs;
        }
        $response = imap_fetch_overview($this->conn, $range);
        foreach ($response as $msg) {
            $msgList[$msg->msgno] = (array)$msg;
        }
        return $msgList;
    }

    // delete a message
    public function deleteMessage($msgNum) {
        return imap_delete($this->conn, $msgNum);
    }

    // parse headers into usable code
    public function parseHeaders($headers) {
        $headers = preg_replace('/rns+/m', "", $headers);
        preg_match_all('/([^: ]+): (.+?(?:rns(?:.+?))*)?rn/m',
            $headers, $matches);
        foreach ($matches[1] as $key => $value) {
            $result[$value] = $matches[2][$key];
        }
        return $result;
    }

    // separate MIME types
    public function mimeToArray($msgNum, $parseHeaders = false) {
        $mail = imap_fetchstructure($this->conn, $msgNum);
        $mail = $this->getParts($msgNum, $mail, 0);
        if ($parseHeaders) {
            $mail[0]["parsed"] = $this->parseHeaders($mail[0]["data"]);
        }
        return $mail;
    }

    // separate mail parts
    public function getParts($msgNum, $part, $prefix) {
        $attachments = array();
        $attachments[$prefix] = $this->decodePart($msgNum, $part, $prefix);

        // multi-part
        if (isset($part->parts)) {
            $prefix = ($prefix) ? $prefix . "." : "";
            foreach ($part->parts as $number => $subpart) {
                $attachments = array_merge($attachments, $this->getParts($msgNum, $subpart, $prefix . ($number + 1)));
            }
        }
        return $attachments;
    }

    // fetch the body of an email with one part
    public function fetchBody($msgNum = "") {
        return imap_fetchbody($this->conn, $msgNum, 1);
    }

    // decode attachments
    public function decodePart($msgNum, $part, $prefix) {
        $attachment = array();

        if ($part->ifdparameters) {
            foreach ($part->dparameters as $obj) {
                $attachment[strtolower($obj->attribute)] = $obj->value;
                if (strtolower($obj->attribute) == "filename") {
                    $attachment["is_attachment"] = true;
                    $attachment["filename"] = $obj->value;
                }
            }
        }

        if ($part->ifparameters) {
            foreach ($part->parameters as $obj) {
                $attachment[strtolower($obj->attribute)] = $obj->value;
                if (strtolower($obj->attribute) == "name") {
                    $attachment["is_attachment"] = true;
                    $attachment["name"] = $obj->value;
                }
            }
        }

        $attachment["data"] = imap_fetchbody($this->conn, $msgNum, $prefix);
        // 3 is base64
        if ($part->encoding == 3) {
            $attachment["data"] = base64_decode($attachment["data"]);
        }
        // 4 is quoted-printable
        elseif ($part->encoding == 4) {
            $attachment["data"] = quoted_printable_decode($attachment["data"]);
        }
        return($attachment);
    }
}
```

该类的构造函数建立一个到邮件服务器的连接。如果您有证书，也可以选择使用 SSL 加密。析构函数关闭连接。

`listMessages()`方法返回一个数组，其中包含当前收件箱中所有消息的列表，或者您可以通过提供一个消息 ID 作为参数来请求特定消息的信息。返回的列表不包含电子邮件的实际内容，而是发件人地址、收件人地址、主题行和日期。

方法将消息标记为删除。消息实际上不会被删除，直到用适当的标志关闭连接，这是在类的析构函数中完成的。

`parseHeaders()`方法使用正则表达式来解析作为单个文本块出现在电子邮件中的电子邮件标题，并将信息作为数组返回，以便于访问。

`mimeToArray()`方法将找到的每个 MIME 类型分开，并以数组的形式返回信息。如果发现多种 MIME 类型，那么这很好地表明有附件。

`getParts()`方法提取电子邮件的不同部分，如正文和附件信息，并将信息作为数组返回。

`fetchBody()`方法返回只有一部分或者没有附件的电子邮件正文。

`decodeParts()`方法获取 base64 或可引用打印的编码附件数据，对其进行解码，然后返回，这样您就可以用它做一些有用的事情。

有了这个类，您现在可以连接到服务器并检索收件箱中的完整邮件列表，如下所示。

```
<?php
require_once "POP3.php";

define("EMAIL_HOST", "pop.liberty2.say");
define("EMAIL_USER", "not@liberty2.say");
define("EMAIL_PASSWD", "********");

$pop3 = new POP3(EMAIL_HOST, EMAIL_USER, EMAIL_PASSWD);
$msgList = $pop3->listMessages();

foreach ($msgList as $msg) {
    echo "<pre>" . print_r($msg, true) . "</pre>";
}
```

## 摘要

这就把我们带到了第 1 部分的结尾。到目前为止，您已经有了一个构建应用程序并确保只发布您发送的消息的游戏计划。您还拥有数据库模式和一个允许您连接到 POP3 服务器并检索邮件的类。请务必回来参加第二部分，看看这些部分是如何组合在一起实现项目的。

图片 via [安吉拉·韦彤](http://www.shutterstock.com/gallery-273049p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章