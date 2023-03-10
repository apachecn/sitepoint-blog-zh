# 如何同步你的 PHP 和 MySQL 时区

> 原文：<https://www.sitepoint.com/synchronize-php-mysql-timezone-configuration/>

PHP 和 MySQL 是独立的进程，有各自的默认时区配置。在开发过程中，或者在服务器上部署一个 web 应用程序时，您不太可能遇到时区问题。在这种情况下，PHP 和 MySQL 可以使用服务器的时间设置。

但是，如果:

1.  您的应用程序可以设置为用户的时区？
2.  您正在运行两个或更多具有不同时区要求的应用程序？
3.  您的应用程序安装在共享服务器上，您不能设置默认时区？

许多开发人员通过将所有日期/时间责任转移给 PHP 来解决这个问题。它们也可以将日期转换为 Unix 时间戳整数，以便更简单地处理，尽管您应该警惕 [Y2K38 Bug](https://www.sitepoint.com/is-your-php-application-affected-by-the-y2k38-bug) 。

虽然这可能是一个可靠的解决方案，但有几个缺点:

*   你在为自己创造额外的工作。
*   您的应用程序需要额外的处理来翻译日期。
*   直接检查表中的日期并不容易。
*   一些基于日期的 SQL 查询变得很困难——如果不是不可能的话，例如，返回在星期一保存的所有记录。
*   你不能使用 [MySQL 的日期/时间函数](http://dev.mysql.com/doc/refman/5.5/en/date-and-time-functions.html)，比如 [NOW()](http://dev.mysql.com/doc/refman/5.5/en/date-and-time-functions.html#function_now) 。

幸运的是，您可以在更新或查询数据库之前同步 PHP 和 MySQL 时区。您的应用程序将需要一个定义 [PHP 时区](http://php.net/manual/en/timezones.php)的配置变量，例如

```
 <?php
define('TIMEZONE', 'America/New_York'); 
```

我们将用它来设置 PHP 的默认时区:

```
 date_default_timezone_set(TIMEZONE); 
```

MySQL 呢？可以在每个会话开始时设置它的默认时区——我建议在连接到数据库后设置。命令是:

```
 SET time_zone='offset'; 
```

其中*偏移*是表示 UTC/GMT 的差值的字符串值，如'-4:00 '、'+3:00 '、'+10:30 '等。请注意，+/-符号是必不可少的——即使是零也是如此——时区偏移量不一定是一整小时。

所以让我们写一点 PHP 来解析时区偏移量并格式化一个 MySQL 可以理解的字符串。首先，我们将创建一个新的 DateTime 对象，找到以秒为单位的偏移量，并将其转换为分钟:

```
 $now = new DateTime();
$mins = $now->getOffset() / 60; 
```

我们现在可以计算整小时和整分钟。第一行确定偏移量是正还是负，然后将该值转换为正数以便于计算:

```
 $sgn = ($mins < 0 ? -1 : 1);
$mins = abs($mins);
$hrs = floor($mins / 60);
$mins -= $hrs * 60; 
```

PHP 的 [sprintf 函数](http://php.net/manual/en/function.sprintf.php)可以用来格式化字符串:

```
 $offset = sprintf('%+d:%02d', $hrs*$sgn, $mins); 
```

最后，我们执行`SET time_zone`命令。下面几行提供了一个示例，但是您应该尽可能使用自己的数据库库:

```
 $db = new PDO('mysql:host=localhost;dbname=test', 'dbuser', 'dbpassword');
$db->exec("SET time_zone='$offset';"); 
```

PHP 和 MySQL 时区现在在您的应用程序中是同步的。

**tip:** What’s with the backslashes?

在创建 DateTime 和 PDO 基类的实例时，您可能已经注意到了最初的反斜杠。如果您在 PHP 应用程序中使用[名称空间](https://www.sitepoint.com/php-53-namespaces-basics)，这些可能是必要的。如果你不是，你不需要删除它们。

## 分享这篇文章