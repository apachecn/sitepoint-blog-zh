# 在 PHP 和 MySQL 中处理日期和时间

> 原文：<https://www.sitepoint.com/working-with-dates-and-times/>

在使用任何编程语言时，处理日期和时间通常是一项琐碎而简单的任务。也就是说，直到必须支持时区。幸运的是，PHP 拥有一套最强大的日期/时间工具，可以帮助您处理各种与时间相关的问题:Unix 时间戳、格式化日期以供人们使用、显示时区时间、现在和下个月第二个星期二之间的差异等等。在本文中，我将向您介绍 PHP 的时间函数(`time()`、`mktime()`和`date()`)及其面向对象的对应物的基础知识，然后看一看 MySQL 日期，并向您展示如何让它们很好地与 PHP 配合使用。

## PHP 日期和时间函数

本文的大部分内容将使用 Unix 时间，或者 POSIX 或 epoch 时间。时间表示为自 UTC 1970 年 1 月 1 日午夜以来流逝的秒数的偏移量。如果您对 Unix 时间的完整历史感兴趣，请查看维基百科上的 [Unix 时间文章。](http://en.wikipedia.org/wiki/Unix_time)

UTC 的全称是协调世界时，也称为 GMT，有时是祖鲁时间，是经度 0 度的时间。世界上所有其他时区都以相对于该时间的正负偏移量来表示。当你需要处理时区的时候，用 UTC 和 Unix 时间处理时间会让你的生活更轻松。稍后我会对此进行更多的讨论，但是现在让我们忽略时区问题，来看看一些时间函数。

### 获取当前 Unix 时间

`time()`不接受任何参数，返回自 Unix 纪元以来的秒数。为了说明这一点，我将使用 PHP [交互式 CLI shell](http://php.net/manual/en/features.commandline.interactive.php) 。

```
sean@beerhaus:~$ php -a
php > print time();
1324402770
```

如果您需要 Unix 时间的数组表示，请使用`getdate()`函数。它采用一个可选的 Unix 时间戳参数，但是如果没有提供，则默认为`time()`的值。

```
php > $unixTime = time();
php > print_r(getdate($unixTime));
Array
(
   [seconds] => 48
   [minutes] => 54
   [hours] => 12
   [mday] => 20
   [wday] => 2
   [mon] => 12
   [year] => 2011
   [yday] => 353
   [weekday] => Tuesday
   [month] => December
   [0] => 1324403688
)
```

### 格式化 Unix 时间

Unix 时间可以很容易地被格式化成人们想要阅读的任何字符串。`date()`用于将 Unix 时间戳格式化为人类可读的字符串，并带有一个格式化参数和一个可选的时间参数。如果没有提供可选的时间戳，则使用`time()`的值。

```
php > print date("r", $unixTime);
Tue, 20 Dec 2011 12:54:48 -0500
```

“r”格式字符串返回 RFC 2822 指定格式的时间。当然，您可以使用其他说明符来定义自己的定制格式。

```
php > print date("m/d/y h:i:s a", $unixTime);
12/20/11 12:54:48 pm
php > print date("m/d/y h:i:s a");
12/20/11 01:12:11 pm
php > print date("jS of F Y", $unixTime);
20th of December 2011
```

有关可接受的格式字符的完整列表，请参见 PHP 文档中`date()` 的[页。不过，当与`mktime()`和`strtotime()`函数结合使用时，这个函数会变得更加有用，正如您将在接下来的示例中看到的那样。](http://www.php.net/manual/en/function.date.php)

### 从给定时间创建 Unix 时间

`mktime()`用于创建一个 Unix 时间戳，给出一个值列表，对应于日期的每个部分(秒、分、小时、年等)。按照以下顺序设置日期的各个部分需要一些整数参数:

```
mktime(hour, minute, second, month, day, year, isDST)
```

如果夏令时生效，将`isDST`设置为 1，如果不生效，则设置为 0，如果未知，则设置为-1(默认值)。

```
php > print date("r", mktime(12, 0, 0, 1, 20, 1987));
Tue, 20 Jan 1987 12:00:00 -0500
php > print date("r", mktime(0, 0, 0, date("n"), date("j"), date("Y")));
Tue, 20 Dec 2011 00:00:00 -0500
php > print date("r", mktime(23, 59, 59, date("n"), date("j"), date("Y")));
Tue, 20 Dec 2011 23:59:59 -0500
```

您可以看到，在处理使用用户定制的日期范围的数据库查询时，`mktime()`非常有用。例如，如果你在 MySQL 中将时间戳存储为整数(Unix 时间)(预示着任何人？)，设置一个通用的年初至今查询范围非常简单。

```
<?php
$startTime = mktime(0, 0, 0, 1, 1, date("y"));
$endTime   = mktime(0, 0, 0, date("m"), date("d"), date("y"));
```

### 将英语日期解析为 Unix 时间

这个近乎神奇的函数`strtotime()`将一串日期/时间格式作为第一个参数，并将一个 Unix 时间戳用作转换的基础。关于可接受的日期格式，参见[文档。](http://www.php.net/manual/en/datetime.formats.php)

```
php > print strtotime("now");
1324407707
php > print date("r", strtotime("now"));
Tue, 20 Dec 2011 14:01:51 -0500
php > print strtotime("+1 week");
1325012569
php > print date("r", strtotime("+1 week"));
Tue, 27 Dec 2011 14:03:03 -0500
php > print date("r", strtotime("next month"));
Fri, 20 Jan 2012 14:04:20 -0500
php > print date("r", strtotime("next month", mktime(0, 0, 0)));
Fri, 20 Jan 2012 00:00:00 -0500
php > print date("r", strtotime("next month", mktime(0, 0, 0, 1, 31)));
Thu, 03 Mar 2011 00:00:00 -0500
```

## PHP 的 DateTime 和 DateTimeZone 对象

PHP 的`DateTime`对象是处理日期和时区的面向对象方法。构造函数方法接受时间的字符串表示，与上面的`strtotime()`非常相似，有些人可能会觉得这样更好用。如果没有提供参数，默认值为“now”。

```
php > $dt = new DateTime("now"); 
php > print $dt->format("r");
Tue, 20 Dec 2011 16:28:32 -0500
php > $dt = new DateTime("December 31 1999 12:12:12 EST");
php > print $dt->format("r");
Fri, 31 Dec 1999 12:12:12 -0500
```

`DateTime`的`format()`方法就像上面的 date()函数一样工作，并且接受所有相同的格式字符。`DateTime`对象也有一些有用的常量，可以提供给`format()`方法。

```
php > print $dt->format(DATE_ATOM);
2011-12-20T15:57:45-05:00
php > print $dt->format(DATE_ISO8601);
2011-12-20T15:57:45-0500
php > print $dt->format(DATE_RFC822);
Tue, 20 Dec 11 15:57:45 -0500
php > print $dt->format(DATE_RSS);
Tue, 20 Dec 2011 15:57:45 -0500
```

常数的完整列表可在 [`DateTime`文档页](http://www.php.net/manual/en/class.datetime.php)中找到。

因为我们很快就要处理时区问题，所以让我们给 PHP 一个默认时区来使用。在您的`php.ini`配置文件中(我有一个用于 CLI，一个用于 Apache ),找到如下所示的部分:

```
[Date]
; Defines the default timezone used by the date functions
; http://php.net/date.timezone
; date.timezone =
```

当没有给`date.timezone`赋值时，PHP 将尽力确定你的服务器上设置的系统时区。你可以用`date_default_timezone_get()`检查 PHP 使用的是哪个值。

```
php > print date_default_timezone_get();
America/New_York
```

让我们将服务器的时区设置为 UTC 时间(`date.timezone = UTC`)并保存配置文件。您必须重新启动 Apache 或 CLI shell 才能看到更改。

PHP `DateTime`对象包含一个内部的`DateTimeZone`类实例来跟踪时区。当您创建一个新的`DateTime`实例时，内部的`DateTimeZone`应该设置为您在`php.ini`中提供的默认值。

```
php > $dt = new DateTime();
php > print $dt->getTimeZone()->getName();
UTC
```

可接受时区名称的完整列表可在[时区文档页面](http://www.php.net/manual/en/timezones.php)上找到。

现在，您可以看到两个`DateTime`对象被赋予不同时区的时间差异。例如，下面是一个将 UTC 转换为美国/纽约(EST)时间的示例。

```
php > $dt = new DateTime();
php > print $dt->format("r");
Tue, 20 Dec 2011 20:57:45 +0000
php > $tz = new DateTimeZone("America/New_York");
php > $dt->setTimezone($tz);
php > print $dt->gt;format("r");
Tue, 20 Dec 2011 15:57:45 -0500
```

请注意 12 月的偏移量为-0500。如果您将时间值更改为夏季日期，如 7 月 1 日，您将看到它是夏令时(EDT)。

```
php > $tz = new DateTimeZone("America/New_York");
php > $july = new DateTime("7/1/2011");
php > $july->setTimezone($tz);
php > print $july->>format("r");
Thu, 30 Jun 2011 20:00:00 -0400
```

## 在 MySQL 和 PHP 中使用日期

如果您在任何级别使用过 MySQL，您可能已经注意到开箱即用的`DATETIME`类型。它看起来和闻起来都像一次约会，如果你说这是一次约会，那么你是对的。但是一旦你把它从 MySQL 中取出来放到 PHP 中，你真正拥有的只是一个看起来像日期的字符串。它没有时区意识，并且在人们需要查看它之前就已经被格式化以供人们使用。*

是的，我知道 MySQL 有大量的日期格式化函数，但是我们也已经在处理 PHP 了，正如你所看到的，PHP 在处理日期格式方面非常出色。无论如何，我们为什么要将它直接从数据库中格式化出来呢？我们可能有一些不同的转换和格式，我们想应用于它。最好只在别人将要看到你的日期时才格式化它。

```
<?php
$db = new PDO("mysql:host=localhost;dbname=testdb", "dbuser", "dbpassword");

$result = $db->query("SELECT dt_date FROM some_table");
while ($row = $result->fetch(PDO::FETCH_ASSOC)) {
    print_r($row);
    var_dump($row["dt_date"]);
}
$result->closeCursor();
```

运行这个简单的脚本，您可以看到从`DATETIME`字段得到的只是一个没有时区信息的格式化字符串。

```
Array
(
   [dt_date] => 2012-01-16 13:03:49
)
string(19) "2012-01-16 13:03:49"
```

`DATETIME`值是运行 MySQL 的服务器在任何时区的当地时间。如果你所做的一切只与一个时区的一台服务器有关，`DATETIME`可能会满足你的大部分需求，我非常嫉妒你。

那么用 PHP 和 MySQL 怎么处理日期和时区呢？将日期存储为 Unix 时间戳。

您已经知道 Unix 时间是从 UTC 的 1970 年 1 月 1 日开始的秒数，所以这为您提供了一个恒定的时区，希望您已经注意到 PHP 的许多日期/时间函数都是基于 Unix 时间戳的。

当使用 MySQL 时，我通常创建将日期存储为`INTEGER UNSIGNED`的表列。当插入日期时，你可以使用 PHP 中的`time()`或者 MySQL 中的`UNIX_TIMESTAMP()`。

```
mysql> SELECT UNIX_TIMESTAMP();
+------------------+
| UNIX_TIMESTAMP() |
+------------------+
|       1326738918 |
+------------------+
```

如果您希望 MySQL 格式化日期，您可以。但是时间将在服务器的时区内，我建议你推迟任何类型的格式，直到你在你的 web 应用程序中处于模板/视图级别，并且准备好让人类的眼睛看到它。

```
mysql> SELECT FROM_UNIXTIME(UNIX_TIMESTAMP());
+---------------------------------+
| FROM_UNIXTIME(UNIX_TIMESTAMP()) |
+---------------------------------+
| 2012-01-16 13:37:16             |
+---------------------------------+
```

在任何跨时区的应用程序中，通常会有一个记录用户自定义时区设置的表，该表将被读入一个`$_SESSION`值。假设您有一个如下所示的会话条目:

```
$_SESSION["userTZ"] => "America/Chicago"
```

您可以轻松地将存储的 Unix 时间(UTC)转换为特定用户时区中的任何日期。

```
<?php
$result = $db->query("SELECT int_date FROM some_table");
while ($row = $result->fetch(PDO::FETCH_ASSOC)) {
    $dt = new DateTime();
    $tz = new DateTimeZone($_SESSION["userTZ"]); 
    $dt->setTimestamp($row["int_date"]);
    $dt->setTimezone($tz); 
    print $dt->format("r");
}
```

这将导致日期“2012 年 1 月 16 日，星期一，12:03:49 -0600”。-0600 告诉您它比 UTC 晚 6 小时，UTC 的时差为 0。

如果我们将时区设置更改为美国/洛杉矶，结果日期将是:

```
Mon, 16 Jan 2012 10:03:49 -0800
```

美国/纽约会产生:

```
Mon, 16 Jan 2012 13:03:49 -0500
```

## 摘要

处理日期和时区是许多程序员日常生活的一部分，但是如果有 PHP 强大且易于使用的日期库，就没什么好担心的了。

您已经看到了获取 Unix 时间戳是多么容易，如何将日期格式化为任何可以想象的格式，如何将日期的英文表示解析为时间戳，如何为时间戳添加时间段，以及如何在时区之间进行转换。如果这篇文章有两个要点，那就是:1)坚持 Unix 时间，2)在使用 PHP 和 MySQL 时，坚持使用 UTC 作为所有日期的基本时区。

将所有时间基于 UTC 的想法不仅仅适用于 PHP 和 MySQL 在任何语言中，这都被认为是很好的练习。如果你发现自己在用另一种语言工作，很有可能你会对自己说“该死，为什么他们不能像 PHP 那样做呢？”

图片 via[Yakobchuk Vasyl](http://www.shutterstock.com/gallery-79658p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章