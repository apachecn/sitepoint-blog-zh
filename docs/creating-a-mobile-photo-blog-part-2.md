# 创建移动照片博客，第 2 部分

> 原文：<https://www.sitepoint.com/creating-a-mobile-photo-blog-part-2/>

这是由两部分组成的系列文章中的第二篇，在这篇文章中，我将向您展示如何创建一个从您的移动设备更新的照片博客。[第 1 部分](https://www.sitepoint.com/creating-a-mobile-photo-blog-part-1/ "Creating a Mobile Photo Blog, Part 1")展示了构建应用程序的计划，设置了数据库需求，并向您介绍了 PHP 手册中的一些用户贡献函数，这些函数使从 POP3 邮件服务器检索邮件变得很容易。在这一期中，我将向你展示如何将所有东西整合在一起，这样你就可以随时随地开始写照片博客了。

## 请求批准

在第 1 部分中，您看到了如何从服务器检索电子邮件列表。当然，如果你的秘密电子邮件地址被发现，立即发布它们是不明智的，这就是为什么更新应该得到明确的批准。在继续之前，我想再设置一些常量并连接到数据库。

```
<?php
// miscellaneous
define("PUBLIC_EMAIL", "mobile@example.com");
define("MAX_WIDTH", 240);
define("ROOT_PATH", "/var/www/");
define("IMG_PATH", "photoblog/");

// database connection
define("DB_HOST", "localhost");
define("DB_USER", "root");
define("DB_PASSWORD", "********");
define("DB_NAME", "test");

// connect to database
$db = new PDO("mysql:host=" . DB_HOST . ";dbname=" . DB_NAME, DB_USER, DB_PASSWORD);
```

`PUBLIC_EMAIL`常量是您的移动设备的电子邮件地址，批准消息将被发送到该地址。`MAX_WIDTH`是将图像调整为缩略图时允许的最大宽度。`ROOT_PATH`是一个网络可访问位置的绝对路径，`IMG_PATH`是一个可写文件夹，您的图像将存储在其中。注意`IMG_PATH`开头没有正斜杠；这不是错误，因为它是`ROOT_PATH`内的相对路径。

在迭代电子邮件列表时，脚本检查每条消息是否有与之相关联的令牌。如果消息没有，脚本会创建一个新令牌，并发送一封电子邮件请求批准。检查具有令牌的邮件，以查看它们是否已被批准。请参见以下示例:

```
<?php
require_once "POP3.php";

// prepared SQL statements
$sqlSelectPending = "SELECT is_valid FROM pending WHERE message_id = :msgno";
$stmSelectPending = $db->prepare($sqlSelectPending);

$sqlInsertPending = "INSERT INTO pending (message_id, token) VALUES (:msgno, :token)";
$stmInsertPending = $db->prepare($sqlInsertPending);

// retrieve messages
$pop3 = new POP3(EMAIL_HOST, EMAIL_USER, EMAIL_PASSWORD);
$msgList = $pop3->listMessages();

if (!empty($msgList)) {
    foreach ($msgList as $value) {
        // see if a token exists
        $stmSelectPending->execute(array(":msgno" => $value["msgno"]));
        $isValid = $stmSelectPending->fetchColumn();

        // message has been approved
        if ($isValid == "Y") {
            // ...
        }
        // message has no token
        elseif ($isValid === false)  {
            // create a unique token
            $token = md5(uniqid(mt_rand(), true));
            $stmInsertPending->execute(array(":msgno" => $value["msgno"], ":token" => $token));

            // send email for approval
            $title = htmlentities($value["subject"], ENT_QUOTES);
            $subject = "Pending Post Notification: " . $title;
            $message = '<a href="http://www.example.com/approve.php?token=' . $token . '">Click Here To Approve</a>';
            mail(PUBLIC_EMAIL, $subject, $message);
        }
    }
}
```

如果`$isValid`具有值`Y`,则消息已被批准。如果`$isValid`具有不同于`Y`的值，则该消息未被批准，因此您无需进一步操作。如果`$isValid`没有值，那么你需要创建一个令牌。

通知电子邮件中引用的`approve.php`页面只是更改了令牌的状态:

```
<?php
// connect to database
// ...

// receive incoming token
$token = isset($_GET["token"]) && ctype_alnum($_GET["token"]) ? $_GET["token"] : null;

if (!empty($token)) {
    // verify token
    $sql = "SELECT message_id FROM pending WHERE token = :token AND is_valid = 'N'";
    $stm = $db->prepare($sql);
    $stm->execute(array(":token" => $token));
    $pendID = $stm->fetchColumn();
    $stm->closeCursor();

    if (!empty($pendID)) {
        // set the entry to be published
        $sql = "UPDATE pending SET is_valid = 'Y' WHERE message_id = :pend_id";
        $stm = $db->prepare($sql);
        $stm->execute(array(":pend_id" => $pendID));
        echo "<p>Publishing...</p>";
    }
    else {
        echo "<p>Invalid token.</p>";
    }
}
```

因为第一个脚本将被设置为每隔几分钟作为 cron 作业运行一次，所以下一次它执行时将会看到一个有效的标志，这是因为获得了批准。如果你曾经收到一封你没有提交的通知邮件，那么你知道私人接收邮件的地址不知何故被泄露了，你应该改变它。在任何情况下，未经批准，任何东西都不会出版。

## 发布博客

现在您已经将令牌标记为待批准，您需要提取电子邮件内容，将附加的图像复制到服务器并更新数据库。这段代码是之前在 cron 下运行的脚本的延续，它在`$isValid`表示消息已经被批准的地方开始执行。

```
<?php
// prepared SQL statements
// ...

$sqlUpdatePending = "UPDATE pending SET message_id = message_id - 1 WHERE message_id > :msgno";
$stmUpdatePending = $db->prepare($sqlUpdatePending);

$sqlDeletePending = "DELETE FROM pending WHERE message_id = :msgno";
$stmDeletePending = $db->prepare($sqlDeletePending);

$sqlInsertPost = "INSERT INTO blog_posts (title, body, create_ts) VALUES (:title, :body, FROM_UNIXTIME(:timestamp))";
$stmInsertPost = $db->prepare($sqlInsertPost);

$sqlInsertImage = "INSERT INTO images (post_id, image_path) VALUES (:post_id, :img_path)";
$stmInsertImage = $db->prepare($sqlInsertImage);

// ...

// message has been approved
if ($isValid == "Y") {
    // get message contents
    $msg = $pop3->mimeToArray($value["msgno"], true);

    // convert date to timestamp
    $timestamp = strtotime($value["date"]);
    if ($timestamp === false) {
        $timestamp = null;
    }
    $title = $value["subject"];

    if(sizeof($msg) > 1) {
        $body = (isset($msg["1.1"])) ? $msg["1.1"]["data"] : $msg[1]["data"];
    } else {
        $body = $pop3->fetchBody($value["msgno"]);
    }

    // copy images to server
    $files = array();
    foreach ($msg as $parts) {
        if (isset($parts["filename"])) {
            $dir = ROOT_PATH . IMG_PATH;
            $ext = strtolower(pathinfo($parts["filename"], PATHINFO_EXTENSION));
            // only accept jpg or png
            if (in_array($ext, array("jpg","png"))) {
                // give the file a unique name
                $hash = sha1($parts["data"]);
                $file = $hash . "." . $ext;
                $thumb = $hash . "_t." . $ext;

                if (!file_exists($dir . $file)) {
                    // copy image and make thumbnails
                    $img = new Imagick();
                    $img->readimageblob($parts["data"]);
                    $img->writeImage($dir . $file);
                    $img->thumbnailImage(MAX_WIDTH, 0);
                    $img->writeImage($dir . $thumb);
                    $img->clear();
                    $img->destroy();
                }
                $files[] = IMG_PATH . $file;
            }
        }
    }

    // update database
    if (isset($timestamp, $title, $body)) {
        // insert post
        $stmInsertPost->execute(array(":title" => $title, ":body" => $body, ":timestamp" => $timestamp));
        $postID = $db->lastInsertId();

        // insert images
        $stmInsertImage->bindParam(":post_id", $postID);
        $stmInsertImage->bindParam(":img_path", $path);
        foreach($files as $path) {
            $stmInsertImage->execute();
        }

        // delete token
        $stmDeletePending->execute(array(":msgno" => $value["msgno"]));

        // update existing tokens
        $stmUpdatePending->execute(array(":msgno" => $value["msgno"]));
    }
    // mark email for deletion
    $pop3->deleteMessage($value["msgno"]);
    break;
}
// message has no approval token
elseif ($isValid === false)  {
    // ...
```

在上面的例子中，电子邮件客户端提供的日期对数据库没有好处，所以它被转换为 unix 时间戳。正文有点棘手，因为根据您使用的电子邮件客户端，它可能会发送多个版本，所以我们试图剔除纯文本版本，忽略其他版本。

要复制图像，首先要定义图像存储的完整路径。接下来，您获取扩展名并确保它是允许的图像格式，然后计算图像的`sha1()`散列以获得唯一的文件名。尽管手机会自动给图片指定唯一的文件名，但你总有可能会更换手机，所以在将图片复制到服务器之前，你仍然希望给图片指定完全唯一的文件名，以确保图片不会被覆盖。缩略图具有相同的名称，并在末尾添加了“`_t`”。

虽然 Imagick 在内存使用方面相对较快且高效，但如果您试图上传多个非常大的图像，可能仍会遇到问题。如果您确实遇到了问题，那么您将需要进行一些测试，以确保您的脚本不会耗尽内存、超时，或者 cron 作业不会在上次运行仍在处理时尝试再次运行脚本。

从消息体中提取博客内容并复制图像后，更新数据库并删除令牌，因为不再需要它了。此时，您还需要重新索引您的令牌，因为一旦从收件箱中删除了一封电子邮件，任何剩余的邮件都会自动重新编号。例如，如果您的收件箱中有以下邮件:

```
message_id		subject
===================================
1			hello world
2			foo
3			bar
```

如果您处理并删除了留言 1，其余留言将按如下方式重新编号:

```
message_id		subject
===================================
1			foo
2			bar
```

由于令牌是通过引用特定的消息 id 来存储的，如果您不为令牌重新编制索引以适应这种行为，那么您将会以一片混乱而告终。

在这个例子的最后，您将电子邮件标记为删除，并调用 break 命令来停止循环通过`$msgList`。这不仅确保了每次执行只更新一个博客，这有助于节省内存，而且因为令牌 id 已经更新，所以这个循环中的剩余条目无论如何都是无效的。

当脚本终止时，`POP3`类的析构函数将被调用，被处理的邮件将被删除。

## 显示博客

当显示内容时，选项是无穷无尽的。您可以进一步改进博客，添加 BBCode、评论、分页等等。无论您的需求是什么，下面的例子应该可以帮助您开始:

```
<?php
// connect to database
// ...

// prepared SQL statements
$sqlSelectImages = "SELECT * FROM images WHERE post_id = :post_id";
$stmSelectImages = $db->prepare($sqlSelectImages);

// output each blog post
$sqlSelectPosts = "SELECT * FROM blog_posts ORDER BY create_ts DESC";
$result = $db->query($sqlSelectPosts);
while ($row = $result->fetch(PDO::FETCH_ASSOC)) {
    $stmSelectImages->execute(array(":post_id" => $row["post_id"]));
    $images = $stmSelectImages->fetchAll();

    echo "<div>";
    echo "<h2>" . htmlspecialchars($row["title"]) . "</h2>";
    echo "<p>" . htmlspecialchars($row["body"]) . "</p>";
    if (!empty($images)) {
        // output thumbnail and link for each image
        foreach ($images as $img) {
            $ext = "." . pathinfo($img["image_path"], PATHINFO_EXTENSION);
            $thumb = str_replace($ext, "_t" . $ext, $img["image_path"]);
            echo '<a href="' . $img["image_path"] . '">';
            echo '<img src="' . $thumb . '" alt="' . basename($img["image_path"]) . '">';
            echo "</a>";
        }
    }
    echo "</div>";
}
```

## 摘要

现在，您知道如何从头开始编写自己的照片博客应用程序，以便直接从移动设备上发布图像和文本。这个项目的源代码可以从 GitHub 下载。

我希望这篇文章对你有所帮助，就像我写它的乐趣一样。像这样的兼职项目可以真正激励我们坚持写博客，即使这感觉像是一件苦差事。作为一个额外的奖励，它的娱乐内容为您的访客；我是说，谁不喜欢看图呢？

图片 via [安吉拉·韦彤](http://www.shutterstock.com/gallery-273049p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章