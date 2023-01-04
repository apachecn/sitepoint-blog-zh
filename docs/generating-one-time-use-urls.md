# 生成一次性使用的 URL

> 原文：<https://www.sitepoint.com/generating-one-time-use-urls/>

一次性 URL 是特制的地址，只能使用一次。它通常提供给用户，以便在有限的时间内获得对文件的特权访问，或者作为特定活动的一部分，例如用户帐户验证。在本文中，我将展示如何生成、实现和终止一次性 URL。

## 创建 URL

假设我们的任务是编写一个用户管理组件，该项目在数据库中为一个新用户帐户创建一条记录。注册后，用户会收到一封确认电子邮件，其中提供了激活其帐户的一次性 URL。我们可以通过包含一个被跟踪的令牌参数来一次性创建这个 URL，这意味着 URL 将如下所示:

```
http://example.com/activate?token=*ee97780...*
```

毫不奇怪，跟踪一次性 URL 是通过将信息存储在数据库表中来完成的。因此，让我们从表定义开始:

```
CREATE TABLE pending_users (
    token CHAR(40) NOT NULL,
    username VARCHAR(45) NOT NULL,
    tstamp INTEGER UNSIGNED NOT NULL,
    PRIMARY KEY(token)
);
```

该表存储相关的用户名、唯一的令牌和时间戳。我将展示如何使用`sha1()`函数生成令牌，该函数返回 40 个字符的字符串，因此令牌字段的容量为 40。`tstamp`字段是一个无符号整数字段，用于存储指示令牌何时生成的时间戳，如果我们想要实现一种机制，使令牌在一定时间后过期，则可以使用该字段。

有许多方法可以生成令牌，但是这里我将简单地使用`uniqid()`和`sha1()`函数。无论您选择如何生成令牌，您都希望它们是不可预测的(随机的)并且重复(冲突)的几率低。

```
<?php
$token = sha1(uniqid($username, true));
```

`uniqid()`接受一个字符串，并根据该字符串和当前时间(以微秒计)返回一个唯一的标识符。该函数还接受一个可选的布尔参数来增加额外的熵，以使结果更加独特。`sha1()`函数使用美国安全哈希算法 1 计算给定字符串的哈希。

一旦函数执行完毕，我们就得到了一个唯一的 40 个字符的字符串，我们可以用它作为令牌来创建一次性的 URL。我们希望将令牌与用户名和时间戳一起记录在数据库中，这样我们可以在以后引用它。

```
<?php
$query = $db->prepare(
    "INSERT INTO pending_users (username, token, tstamp) VALUES (?, ?, ?)"
);
$query->execute(
    array(
        $username,
        $token,
        $_SERVER["REQUEST_TIME"]
    )
);
```

显然，我们希望存储令牌，但是我们也存储用户名以记住哪个用户被设置为活动的，以及一个时间戳。在现实世界的应用程序中，您可能会在单独的用户表中存储用户 ID 和引用用户记录，但是为了举例，我使用了用户名字符串。

将信息安全地放入数据库后，我们现在可以构造一次性 URL，它指向接收令牌并相应处理它的脚本。

```
<?php
$url = "http://example.com/activate.php?token=$token";
```

URL 可以通过电子邮件或其他方式传播给用户。

```
<?php
$message = <<<ENDMSG
Thank you for signing up at our site.  Please go to
$url to activate your account.
ENDMSG;

mail($address, "Activate your account", $message);
```

## 使用 URL

我们需要一个脚本来激活帐户，一旦用户按照链接。事实上，是处理脚本强制 URL 一次性使用。这意味着脚本需要从调用 URL 收集令牌，并根据数据库表中存储的数据进行快速检查。如果它是一个有效的令牌，我们可以执行我们想要的任何操作，在这种情况下，将用户设置为活动并使令牌过期。

```
<?php
// retrieve token
if (isset($_GET["token"]) && preg_match('/^[0-9A-F]{40}$/i', $_GET["token"])) {
    $token = $_GET["token"];
}
else {
    throw new Exception("Valid token not provided.");
}

// verify token
$query = $db->prepare("SELECT username, tstamp FROM pending_users WHERE token = ?");
$query->execute(array($token));
$row = $query->fetch(PDO::FETCH_ASSOC);
$query->closeCursor();

if ($row) {
    extract($row);
}
else {
    throw new Exception("Valid token not provided.");
}

// do one-time action here, like activating a user account
// ...

// delete token so it can't be used again
$query = $db->prepare(
    "DELETE FROM pending_users WHERE username = ? AND token = ? AND tstamp = ?",
);
$query->execute(
    array(
        $username,
        $token,
        $tstamp
    )
);
```

更进一步，我们可以对 URL buy 实施 24 小时 TTL(生存时间),检查存储在令牌旁边的表中的时间戳。

```
<?php
// 1 day measured in seconds = 60 seconds * 60 minutes * 24 hours
$delta = 86400;

// Check to see if link has expired
if ($_SERVER["REQUEST_TIME"] - $tstamp > $delta) {
    throw new Exception("Token has expired.");
}
// do one-time action here, like activating a user account
// ...
```

在 Unix 时间戳的范围内工作，到期日期将被表示为以秒为单位的偏移量。如果 URL 只在 24 小时内有效，我们有 86，400 秒的时间。然后，确定链接是否过期就变成了比较当前时间和原始时间戳并查看它们之间的差异是否小于过期增量的简单事情。如果差值大于增量，则该链路应该过期。如果差值小于或等于 delta，则链接仍然是“新鲜的”

## 结论

一次性 URL 有几种应用。本文中的示例是向用户发送验证链接来激活帐户的场景。您还可以使用一次性使用的 URL 来为其他活动提供确认，提供对时间敏感的信息访问，或者创建在特定时间后过期的定时用户帐户。

作为一般的内务处理，您可以编写一个二级脚本来防止过期的令牌在数据库中累积，如果用户从未遵循它们的话。该脚本可以由管理员定期运行，或者最好设置为计划任务或 cron 作业并自动运行。

在应用程序中实现该功能时，将它包装成可重用的组件也是明智的。这很容易做到，所以我将把它作为一个练习留给读者。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章