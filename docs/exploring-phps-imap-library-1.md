# 探索 PHP IMAP 库，第 1 部分

> 原文：<https://www.sitepoint.com/exploring-phps-imap-library-1/>

一些 web 应用程序可能需要向用户提供电子邮件客户端的功能。在这些情况下，我们可以自己编写或者定制开源客户端，比如 SquirrelMail 或者 Roundcube。无论您选择什么，使用 PHP IMAP 邮件扩展的知识都是有帮助的。

在这个由两部分组成的系列中，我将解释如何使用 IMAP 扩展。这是讨论连接邮件服务器和阅读邮件的必要功能的第一部分。第二部分将讨论使用电子邮件的操作，如删除邮件、下载附件等。

为了演示功能，我将使用代码示例来开始编写您自己的邮件客户端。我将假设一个 web 脚本使用这些 URL 参数来调用必要的函数:

*   `func`–所需功能的类型(例如:阅读电子邮件、查看收件箱、删除消息)
*   `folder`–要连接的邮箱文件夹的名称(例如:收件箱、已发送邮件、垃圾邮件)
*   `uid`–目标电子邮件的唯一 ID

可以使用`$_GET`检索参数，并且可以使用`switch`语句来调用适当的动作。

```
<?php
$func = (!empty($_GET["func"])) ? $_GET["func"] : "view";
$folder = (!empty($_GET["folder"])) ? $_GET["folder"] : "INBOX";
$uid = (!empty($_GET["uid"])) ? $_GET["uid"] : 0;

// connect to IMAP
// ...

switch ($func) {
    case "delete":
        deleteMail($imap, $folder, $uid);
        break;

    case "read":
        deleteMail($imap, $folder, $uid);
        break;

    case "view":
    default:
        viewMail($imap, $folder);
        break;
}
```

## 连接到 IMAP

为了建立到 IMAP 服务器的连接，我们使用如下所示的`imap_connect()`函数:

```
<?php
$imap = imap_open($mailboxPath, $username, $password);
```

邮箱路径、用户名和密码字符串是连接到服务器所需的参数。您可以在手册中了解可选参数[。](http://php.net/manual/en/function.imap-open.php "PHP: imap_open - Manual")

邮箱路径是一个字符串，它用大括号标识服务器和端口信息，后跟所需邮件文件夹的名称。以下是常见邮件提供商收件箱文件夹的几个字符串:

*   Gmail `{imap.gmail.com:993/imap/ssl}INBOX`
*   雅虎`{imap.mail.yahoo.com:993/imap/ssl}INBOX`
*   美国在线`{imap.aol.com:993/imap/ssl}INBOX`

有些服务器没有启用 SSL，在这种情况下，您可以省略字符串中的“SSL”。其他服务器可能会使用自签名证书，其中包括“novalidate-cert”。

```
<?php
$imap = imap_open("{localhost:993/imap/ssl/novalidate-cert}", "username", "password");
```

打开与邮件服务器的连接，现在我们可以看看用于以下活动的函数:

*   显示您的电子邮件帐户中的邮箱文件夹列表
*   显示文件夹中的电子邮件列表
*   查看电子邮件的内容

## 列出文件夹

几乎每个电子邮件帐户中都可以看到收件箱、已发送邮件、回收站和垃圾邮件文件夹，用户也可以经常创建自定义文件夹。为了查看这些文件夹中的邮件，我们需要更改我们的连接字符串。例如，我之前在路径字符串中使用了“INBOX”。如果我想连接到垃圾邮件文件夹，我可能想使用类似“垃圾邮件”的东西。但是，即使当通过邮件客户端查看时，它可能会在您的电子邮件帐户中被列为垃圾邮件，真实的文件夹名称在幕后可能会有所不同。我们可以使用`imap_list()`列出一个帐户中所有可用的文件夹。

```
<?php
$folders = imap_list($imap, "{imap.gmail.com:993/imap/ssl}", "*");
echo "<ul>";
foreach ($folders as $folder) {
    $folder = str_replace("{imap.gmail.com:993/imap/ssl}", "", imap_utf7_decode($folder));
    echo '<li><a href="mail.php?folder=' . $folder . '&func=view">' . $folder . '</a></li>';
}
echo "</ul>";
```

我们必须将使用`imap_open()`作为初始参数获得的连接句柄传递给`imap_list()`。我们还需要传递一个空的路径字符串(没有文件夹，例如“INBOX”)。星号作为第三个参数请求所有可用的文件夹。

## 列出电子邮件

每个文件夹都有一个可用电子邮件列表，所以让我们看看如何生成收件箱中的邮件列表。

我们需要首先使用`imap_num_msg()`获得可用消息的数量。然后我们可以使用`imap_header()`函数来获取每条消息的头信息。

假设我们想要最后 20 封邮件:

```
<?php
$numMessages = imap_num_msg($imap);
for ($i = $numMessages; $i > ($numMessages - 20); $i--) {
    $header = imap_header($imap, $i);

    $fromInfo = $header->from[0];
    $replyInfo = $header->reply_to[0];

    $details = array(
        "fromAddr" => (isset($fromInfo->mailbox) && isset($fromInfo->host))
            ? $fromInfo->mailbox . "@" . $fromInfo->host : "",
        "fromName" => (isset($fromInfo->personal))
            ? $fromInfo->personal : "",
        "replyAddr" => (isset($replyInfo->mailbox) && isset($replyInfo->host))
            ? $replyInfo->mailbox . "@" . $replyInfo->host : "",
        "replyName" => (isset($replyTo->personal))
            ? $replyto->personal : "",
        "subject" => (isset($header->subject))
            ? $header->subject : "",
        "udate" => (isset($header->udate))
            ? $header->udate : ""
    );

    $uid = imap_uid($imap, $i);

    echo "<ul>";
    echo "<li><strong>From:</strong>" . $details["fromName"];
    echo " " . $details["fromAddr"] . "</li>";
    echo "<li><strong>Subject:</strong> " . $details["subject"] . "</li>";
    echo '<li><a href="mail.php?folder=' . $folder . '&uid=' . $uid . '&func=read">Read</a>';
    echo " | ";
    echo '<a href="mail.php?folder=' . $folder . '&uid=' . $uid . '&func=delete">Delete</a></li>';
    echo "</ul>";
}
```

`$imap`连接应该打开到所需的文件夹。然后，我们可以使用`imap_num_msg()`收到的消息数遍历最后 20 封电子邮件。将连接和电子邮件号码交给`imap_header()`来检索标题信息，然后可以解析出有趣的细节，如发件人的电子邮件地址和姓名、主题等。

请注意，我们通过使用邮件总数获得的电子邮件号码不是邮件的唯一 ID。如果你的收件箱里有 100 封邮件，那么最后一个数字将是 100，前一个数字将是 99，以此类推。但这不是唯一的。如果您删除了编号为 100 的消息，然后收到一条新消息，它的编号也将是 100。

我们必须获得电子邮件的唯一 ID，以便继续其他操作。每封电子邮件都有一个惟一的 ID，称为 UID，我们可以通过向`imap_uid()`函数提供电子邮件号来获得它。UID 是唯一的，不会随时间而改变。

## 查看邮件内容

阅读电子邮件实际上不像前面的任务那么简单，所以我将使用 Mitul Koradia 开发的 [Receive Mail 类](http://www.phpclasses.org/package/2934-PHP-Retrieve-and-parse-messages-from-a-POP3-mailbox.html "Receive Mail, Retrieve and parse messages from a POP3 mailbox - PHP Classes")作为起点，以帮助简化工作。我从这个类中提取并改编了以下三个函数用于我们的示例:

```
<?php
function getBody($uid, $imap) {
    $body = get_part($imap, $uid, "TEXT/HTML");
    // if HTML body is empty, try getting text body
    if ($body == "") {
        $body = get_part($imap, $uid, "TEXT/PLAIN");
    }
    return $body;
}

function get_part($imap, $uid, $mimetype, $structure = false, $partNumber = false) {
    if (!$structure) {
           $structure = imap_fetchstructure($imap, $uid, FT_UID);
    }
    if ($structure) {
        if ($mimetype == get_mime_type($structure)) {
            if (!$partNumber) {
                $partNumber = 1;
            }
            $text = imap_fetchbody($imap, $uid, $partNumber, FT_UID);
            switch ($structure->encoding) {
                case 3: return imap_base64($text);
                case 4: return imap_qprint($text);
                default: return $text;
           }
       }

        // multipart 
        if ($structure->type == 1) {
            foreach ($structure->parts as $index => $subStruct) {
                $prefix = "";
                if ($partNumber) {
                    $prefix = $partNumber . ".";
                }
                $data = get_part($imap, $uid, $mimetype, $subStruct, $prefix . ($index + 1));
                if ($data) {
                    return $data;
                }
            }
        }
    }
    return false;
}

function get_mime_type($structure) {
    $primaryMimetype = array("TEXT", "MULTIPART", "MESSAGE", "APPLICATION", "AUDIO", "IMAGE", "VIDEO", "OTHER");

    if ($structure->subtype) {
       return $primaryMimetype[(int)$structure->type] . "/" . $structure->subtype;
    }
    return "TEXT/PLAIN";
}
```

`getBody()`函数通过传递 UID 和 IMAP 连接来获取电子邮件的内容。在函数内部，我们调用内容类型为 TEXT/HTML 的`get_part()`函数。纯文本邮件更容易阅读。所以我们首先试图找到电子邮件中的 HTML 内容。

然后我们使用`imap_fetchstructure()`函数读取邮件的结构。默认情况下，这个函数会采用电子邮件号码，但是我们通过传递`FT_UID`常量，将库函数改为使用 UID。

然后，我们使用`get_mime_type()`函数获得电子邮件消息的 mime 类型。该函数以整数形式返回八种 mime 类型:

*   0–文本
*   1–多部分
*   2–消息
*   3–应用
*   4–音频
*   5–图像
*   6–视频
*   7–其他

我们使用 mime 类型数组将返回的整数转换成实际的 mime 类型字符串。

多部分消息可以有大量子类型，因此我们使用部分编号递归遍历所有子类型，并在使用 mime 类型找到正确部分时使用`imap_fetchBody()`检索电子邮件内容。

然后，我们根据消息的编码类型使用适当的解码函数并返回内容。此处显示了可用编码类型的完整列表:

*   0–7 位
*   1–8 位
*   二进制
*   3–BASE64
*   4-报价-可打印
*   5–其他

## 摘要

我们通过回顾连接到 IMAP 服务器的基础知识开始了这个系列，然后继续列出可用文件夹中的邮件，最后阅读电子邮件的内容。在下一部分中，我将讨论可用于实现电子邮件客户端的附加功能的函数，如删除邮件和处理附件。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章