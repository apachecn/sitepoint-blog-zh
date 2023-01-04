# 探索 PHP IMAP 库，第 2 部分

> 原文：<https://www.sitepoint.com/exploring-phps-imap-library-2/>

在本系列的第一部分中，我讨论了如何使用 PHP IMAP 扩展连接到 IMAP 服务器。在这一部分中，我们将通过讨论使用文件夹和阅读电子邮件内容来完成这一系列。我们开始吧！

## 使用电子邮件标志

每封邮件通常都有一个标记列表:未读、已回复、已标记、草稿等。我们可以检查消息的`Unseen`属性来识别它是否被阅读过。如果消息已经被查看，那么我们将得到值“U”。所以回到第一部分的代码，让我们修改它来显示已读/未读状态。

```
<?php
$numMessages = imap_num_msg($imap);
for ($i = $numMessages; $i > ($numMessages - 20); $i--) {
    $header = imap_header($imap, $i);

    ...

    $uid = imap_uid($imap, $i);
    $class = ($header->Unseen == "U") ? "unreadMsg" : "readMsg";

    echo "<ul class="' . $class . '">';
    echo "<li><strong>From:</strong>" . $details["fromName"];
    echo " " . $details["fromAddr"] . "</li>";
    echo "<li><strong>Subject:</strong> " . $details["subject"] . "</li>";
    echo '<li><a href="mail.php?folder=' . $folder . '&uid=' . $uid . '&func=read">Read</a>';
    echo " | ";
    echo '<a href="mail.php?folder=' . $folder . '&uid=' . $uid . '&func=delete">Delete</a></li>';
    echo "</ul>";
}
```

我们可以检查`Unseen`属性的状态，为标记分配不同的 CSS 类，以不同的方式显示已读/未读邮件信息。

```
.unreadMsg {
    color: #000;
    font-weight: bold;
}

.readMsg {
    color: #999;
}
```

我们还可以在电子邮件上创建特殊的标志。例如，假设我们想将一封邮件标记为已加星标。为此，我们使用`Flagged`属性，如果消息被标记，该属性的值为“F”。

```
<?php
if ($header->Flagged == "F") {
    $class .= " flaggedMsg";
}
```

为了在消息上设置标志，我们使用了`impa_setflag_full()`函数。

```
<?php
$status = imap_setflag_full($imap, $uid, "\Seen \Flagged", ST_UID);
```

上面的代码将邮件标记为“已读(\Seen)”，并将标记状态设置为“F”。我总是喜欢使用 UID 而不是电子邮件序列号作为第二个参数，所以我必须用常量`ST_UID`设置可选的第四个参数。您也可以使用此功能提供其他标志，如草稿、已删除和已回复。

虽然我只在一条消息上设置了标志，但是如果您愿意，您可以提供一个类似“1，10”的范围作为在多条消息上设置标志的第二个参数。

## 删除电子邮件

`imap_delete()`功能用于删除消息。它会将它们标记为删除，但实际上并不会将它们从您的帐户中删除。`imap_expunge()`函数负责实际删除已标记的消息。

```
<?php
imap_delete($imap, $uid, FT_UID);
imap_expunge($imap);
```

我用 UID 而不是序列号调用了 delete 函数。否则，我可能会因为序列号的变化而丢失重要的消息(请记住，从第一部分开始，序列号就不是惟一的)。

## 查看电子邮件附件

除了阅读和发送电子邮件，处理电子邮件附件可能是电子邮件客户端的下一个最重要的功能。我们将重点检查电子邮件附件，显示它们，并下载它们。

阅读邮件结构和识别附件有多种方法。第一部分提到的库，由 Mitul Koradia 开发的[接收邮件类](http://www.phpclasses.org/package/2934-PHP-Retrieve-and-parse-messages-from-a-POP3-mailbox.html "Receive Mail, Retrieve and parse messages from a POP3 mailbox - PHP Classes")，也有下载它们的特性。但是这里我将使用注释部分中包含的函数[作为`imap_fetchstructure()`函数，我认为这是最简单的方法。](http://php.net/manual/en/function.imap-fetchstructure.php "PHP: imap-fetchstructure - Manual")

在看一些代码之前，我想向您展示一下由`imap_fetchstructure()`返回的带有附件的电子邮件的结构。

```
stdClass Object
(
  [type] => 1
  [encoding] => 0
  [ifsubtype] => 1
  [subtype] => MIXED
  [ifdescription] => 0
  [ifid] => 0
  [ifdisposition] => 0
  [ifdparameters] => 0
  [ifparameters] => 1
  [parameters] => Array
    (
      [0] => stdClass Object
        (
            [attribute] => BOUNDARY
            [value] => bcaec54b516462cef304c7e9d5c3
        )
    )
  [parts] => Array
    (
      [0] => stdClass Object
        (
          [type] => 1
          [encoding] => 0
          [ifsubtype] => 1
          [subtype] => ALTERNATIVE
          [ifdescription] => 0
          [ifid] => 0
          [ifdisposition] => 0
          [ifdparameters] => 0
          [ifparameters] => 1
          [parameters] => Array
            (
              [0] => stdClass Object
                (
                  [attribute] => BOUNDARY
                  [value] => bcaec54b516462ceeb04c7e9d5c1
                )
            )
          [parts] => Array
            (
              [0] => stdClass Object
                (
                  [type] => 0
                  [encoding] => 0
                  [ifsubtype] => 1
                  [subtype] => PLAIN
                  [ifdescription] => 0
                  [ifid] => 0
                  [lines] => 1
                  [bytes] => 2
                  [ifdisposition] => 0
                  [ifdparameters] => 0
                  [ifparameters] => 1
                  [parameters] => Array
                    (
                      [0] => stdClass Object
                        (
                          [attribute] => CHARSET
                          [value] => ISO-8859-1
                        )
                    )
                )
              [1] => stdClass Object
                (
                  [type] => 0
                  [encoding] => 0
                  [ifsubtype] => 1
                  [subtype] => HTML
                  [ifdescription] => 0
                  [ifid] => 0
                  [lines] => 1
                  [bytes] => 6
                  [ifdisposition] => 0
                  [ifdparameters] => 0
                  [ifparameters] => 1
                  [parameters] => Array
                    (
                      [0] => stdClass Object
                        (
                          [attribute] => CHARSET
                          [value] => ISO-8859-1
                        )
                    )
                )
            )
        )
      [1] => stdClass Object
        (
          [type] => 3
          [encoding] => 3
          [ifsubtype] => 1
          [subtype] => ZIP
          [ifdescription] => 0
          [ifid] => 0
          [bytes] => 115464
          [ifdisposition] => 1
          [disposition] => ATTACHMENT
          [ifdparameters] => 1
          [dparameters] => Array
            (
              [0] => stdClass Object
                (
                  [attribute] => FILENAME
                  [value] => weekly-reports.zip
                )
            )
          [ifparameters] => 1
          [parameters] => Array
            (
              [0] => stdClass Object
                (
                  [attribute] => NAME
                  [value] => weekly-reports.zip
                )
            )
        )
    )
)
```

如果你仔细观察这个结构，你会发现附件是一个部分，它的`disposition`等于“附件”。这封邮件有 1 个附件，但也完全有可能有多个附件，从而有多个带“附件”的部分。通过检查这个参数，我们可以很容易地识别附件。

```
<?php
$mailStruct = imap_fetchstructure($imap, $i);
$attachments = getAttachments($imap, $i, $mailStruct, "");
```

在`viewMailbox()`函数中，我已经包含了上面几行。首先，我们使用 imap_fetchstructure()函数获得每封邮件的结构。它将返回一个类似前面所示的对象。然后我们调用`getAttachments()`函数，它将提供附件细节。

```
<?php
function getAttachments($imap, $mailNum, $part, $partNum) {
    $attachments = array();

    if (isset($part->parts)) {
        foreach ($part->parts as $key => $subpart) {
            if($partNum != "") {
                $newPartNum = $partNum . "." . ($key + 1);
            }
            else {
                $newPartNum = ($key+1);
            }
            $result = getAttachments($imap, $mailNum, $subpart,
                $newPartNum);
            if (count($result) != 0) {
                 array_push($attachments, $result);
             }
        }
    }
    else if (isset($part->disposition)) {
        if ($part->disposition == "ATTACHMENT") {
            $partStruct = imap_bodystruct($imap, $mailNum,
                $partNum);
            $attachmentDetails = array(
                "name"    => $part->dparameters[0]->value,
                "partNum" => $partNum,
                "enc"     => $partStruct->encoding
            );
            return $attachmentDetails;
        }
    }

    return $attachments;
}
```

首先，我们检查是否为当前电子邮件设置了部分，然后我们必须递归地遍历每个部分。我们必须修改零件号，并将其传递给递归调用。如你所见，子部件号被分解成虚线部分。如果您有 3 个级别的零件号，它将类似于 1.0.1。

当更多的部件不可用时，我们检查处置参数是否可用，以及它的值是否为“附件”。在这种情况下，我们使用`imap_bodystruct()`得到给定零件的结构。`imap_bodystruct()`和`imap_fetchstructure()`都提供相同的输出。两者唯一的区别是，我们可以使用`imap_bodystruct()`来获取特定的部分信息，而不是整个结构。

现在我们有了给定电子邮件的附件详细信息列表，我们遍历所有附件并显示它们的下载链接:

```
<?php
echo "Attachments: ";
foreach ($attachments as $attachment) {

echo '<a href="mail.php?func=' . $func . '&folder=' . $folder . '&uid=' . $uid .
    '&part=' . $attachment["partNum"] . '&enc=' . $attachment["enc"] . '">' .
    $attachment["name"] . "</a>";
}
```

## 下载附件

要下载附件，我们需要电子邮件的 UID、部件号和附件的编码类型。我已经在上面创建的下载链接中包含了这些参数。单击链接后，可以调用以下函数:

```
<?php
function downloadAttachment($imap, $uid, $partNum, $encoding, $path) {
    $partStruct = imap_bodystruct($imap, imap_msgno($imap, $uid), $partNum);

    $filename = $partStruct->dparameters[0]->value;
    $message = imap_fetchbody($imap, $uid, $partNum, FT_UID);

    switch ($encoding) {
        case 0:
        case 1:
            $message = imap_8bit($message);
            break;
        case 2:
            $message = imap_binary($message);
            break;
        case 3:
            $message = imap_base64($message);
            break;
        case 4:
            $message = quoted_printable_decode($message);
            break;
    }

    header("Content-Description: File Transfer");
    header("Content-Type: application/octet-stream");
    header("Content-Disposition: attachment; filename=" . $filename);
    header("Content-Transfer-Encoding: binary");
    header("Expires: 0");
    header("Cache-Control: must-revalidate");
    header("Pragma: public");
    echo $message;
}
```

首先，我们需要获取给定部分的结构来标识附件名称，这是用`imap_bodystruct()`完成的。你可以看到我用`imap_msgno(`从 UID 中获取序列号；这是因为 imap_bodystruct()不接受 UID，所以我们必须将 UID 转换成序列号。

接下来，我们使用`imap_fetchbody()`获取附件内容。它将只接收给定零件号的内容。然后，我们根据给定附件的编码类型使用适当的内容解码函数对其进行解码。

最后，我们输出带有适当标题的附件内容，以便浏览器下载文件。

## 摘要

我们已经看完了 PHP 的 IMAP 函数，现在你应该有足够的理解来组装一个简单的工作邮件阅读器。请务必了解其他可用的功能，并探索它们以拓宽您的理解。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章