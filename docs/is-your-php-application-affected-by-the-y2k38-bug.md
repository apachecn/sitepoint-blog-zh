# 你的 PHP 应用被 Y2K38 Bug 影响了吗？

> 原文：<https://www.sitepoint.com/is-your-php-application-affected-by-the-y2k38-bug/>

我不想太危言耸听，但请尝试在您的系统上运行以下 PHP 代码:

```
 <?php
$date = '2040-02-01';
$format = 'l d F Y H:i';

$mydate1 = strtotime($date);
echo '<p>', date($format, $mydate1), '</p>';
?> 
```

幸运的话，您会在浏览器中看到*“2040 年 2 月 1 日星期三 00:00”*。如果你看到一个 60 年代末或 70 年代初的日期，你的 PHP 应用程序可能有 Y2K38 bug 的风险！

## Y2K38 bug 是什么？

Y2K38 或 Unix 千年虫影响了 PHP 和许多其他语言和系统，这些语言和系统使用有符号的 32 位整数来表示自 1970 年 1 月 1 日 00:00:00 UTC 以来的秒数。可以存储的最远日期是世界协调时 2038 年 1 月 19 日 03:14:07。除此之外，最左边的位被置位，整数变成负十进制数——或者纪元之前的时间。

是的，这是 28 年后的事了，我相信你们中的许多人会认为现在担心这个问题是很可笑的。然而，开发人员在 20 世纪 70 年代和 80 年代对千年虫也是这样想的。此外，任何处理长期未来事件的 web 应用程序都可能处于风险之中。例如，典型的抵押贷款期限为 25 年。养老金和储蓄计划可以更长久。

## 64 位会拯救我们吗？

大概吧。如果您使用的是 64 位操作系统和编译的 64 位 PHP 版本，您的应用程序应该不会受到影响。不过，我还是建议你测试一下。一个有符号的 64 位数字给出了一个最大的未来日期，比宇宙的当前年龄大 21 倍——2920 亿年，误差一两天。

如果您确信您的财务应用程序将始终安装在 64 位系统上，您可能会睡不着觉。

## 有其他选择吗？

幸运的是，PHP 在 5.2 版本中引入了一个新的 [DateTime 类](http://www.php.net/manual/en/class.datetime.php)(在 5.1 版本中提供了实验性支持，请注意一些方法是在 5.3 版本中引入的)…

```
 <?php
$date = '2040-02-01';
$format = 'l j F Y H:i';

$mydate2 = new DateTime($date);
echo '<p>', $mydate2->format($format), '</p>';
?> 
```

DateTime 不会遇到 Y2K38 问题，并且可以很好地处理 9999 年 12 月 31 日之前的日期。到那时我可能已经还清了我的抵押贷款！

升级现有的应用程序可能不值得，但是在计划下一个项目时，您当然应该考虑 DateTime 类。

你在应用中遇到过 Y2K38 问题吗？你是怎么修好的？

## 分享这篇文章