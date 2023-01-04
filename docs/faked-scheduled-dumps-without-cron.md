# 我如何在没有 Cron 的情况下伪造预定的数据库转储

> 原文：<https://www.sitepoint.com/faked-scheduled-dumps-without-cron/>

所以我被一些问题困住了。我必须找到一种方法来使用我可用的平台(Windows XP)，对用户隐藏敏感的密码，并启用定期的 cron 操作，如备份数据库。

我最喜欢的服务器捆绑包 [XAMPP](http://www.apachefriends.org/en/xampp.html) 没有为 Windows 提供 cron 服务。我研究了一下 [UniServer](http://www.uniformserver.com/) ，虽然它看起来很酷，但它需要额外的支持才能在 XP 上运行，我需要为不同的平台改变 [ionCube](http://www.ioncube.com/) 加载器。更糟糕的是，只要用户能够访问物理服务器，他们就可以通过单击 UniServer GUI 中的 mysql 选项卡轻松看到根 MySQL 密码(我没有时间学习如何禁用它)。

我差点用了 [WampServer](http://www.wampserver.com/en/) ，但后来我想起了上次我用它时它给我带来的所有头痛。

我偶然发现了一个叫做 [MySqlDumper](http://www.mysqldumper.net/) 的开源程序，我很高兴找到它，但它也不是没有问题。例如，在执行数据库恢复时，它能够显示服务器上所有数据库的所有信息，并提供删除它们的选项。该程序将被安装在不同的机构，并使用相同的根密码。

我把笔记本电脑放在一边，走向冰箱，考虑在我的项目第一次发布时放弃 cron 的想法。但是客户端的数据备份也不容忽视。我打开冰箱，抓起最近的一瓶果汁；无视架子上的玻璃杯，我喝了一大口果汁，严重呛到了。当我转身环顾这个无聊的房间，试图屏住呼吸时，突然一个想法像闪电一样袭来。

我的程序需要一个准确的用户名和密码来向用户展示它的特性，并且有一个预定义的用户，他大部分时间都是空闲的，只做两件事:清除陈旧的数据库条目和在需要时恢复数据库。根据我的登录程序，每次用户成功登录时，系统会自动将上次登录日期更新为当前日期。这是我迫切需要的暗示。

解决方案是跟踪空闲超级管理员用户的最后登录日期。如果他最后一次登录是在昨天，那么系统会调用 mysqldump 来备份数据库。因此，我创建了以下代码:

```
<?php
define("MYSQL_DUMP", "C:\xampp\mysql\bin\mysqldump.exe");
define("MYSQL_DUMP_ARGS", "--user=user --password=pass --opt database");
define("MYSQL_DUMP_OUT", "..\system\" . date("l") . ".sql");

$result = $db->query("SELECT last_login FROM users WHERE username = 'superadmin'");
$check = $result->fetchColumn();
$result->closeCursor();

if ($check < strtotime(date("Y-m-d 00:00:00"))) {
    $backup = MYSQL_DUMP . " " . MYSQL_DUMP_ARGS . " > " .
        MYSQL_DUMP_OUT;
    exec($backup, $out);

    if ($_POST["username"] != "superadmin") {
        $db->query("UPDATE USER SET last_login = NOW() WHERE username = 'superadmin'");
    }
}
```

这几行代码是我的解决方案。例程的第一部分检查 superadmin 帐户的最后登录时间。如果最后一次登录发生在今天，那么第二部分将被忽略。否则，将进行备份，并将 superadmin 帐户的上次登录时间更新为今天。

除了可能清除整个数据库之外，superadmin 用户还能够恢复备份，这就是为什么我在更新上次登录时间之前检查了是谁在登录。当 superadmin 帐户定期登录时，他的最后登录时间将由登录代码更新，因此无论采用哪种方式，该字段都将为他更新。

令人惊讶的是，有时最复杂的问题也有简单而优雅的解决方案。我在 stack 的时候做的一件事就是遍历程序的所有进程，寻找方便的钩子或者漏洞。这种特殊的解决方案可能不适合所有人，但对于我的问题来说，它足够好，并允许我使用现有的平台，并且仍然不让普通用户访问敏感的密码。感谢您让我分享，如果有什么的话，我希望它能鼓励您在自己的日常编程问题中找到创造性的解决方案。

图片 via [非洲工作室](http://www.shutterstock.com/gallery-137002p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章