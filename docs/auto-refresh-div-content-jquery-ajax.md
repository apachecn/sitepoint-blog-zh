# 使用 jQuery 和 AJAX 自动刷新 Div 内容

> 原文：<https://www.sitepoint.com/auto-refresh-div-content-jquery-ajax/>

所以今天我需要 div 中的**内容每 5 秒刷新一次**，所以我决定做一个快速演示来告诉你如何做到这一点。它最初使用 AJAX 速记方法加载内容。load()，然后简单地设置每 5 秒钟对数据进行一次循环调用。

## 演示

演示程序**在我的博客上显示来自 rss 提要**的最新博客文章，并显示最新数据刷新的日期和时间。我还使用 [$为 AJAX 请求连接了一个加载图像。ajaxSetup()](http://www.jquery4u.com/ajax/ajaxsetup-loading-image/) 但是这是可选的，如果你不想看到加载图像，你可以删除这段代码，甚至显示一个更小的加载图像，这样当新数据被异步加载时，数据在屏幕上仍然可见。

![data-refresh-x-seconds2](img/7eb7446caafac202a33096fbff2bb94f.png "data-refresh-x-seconds2")
查看演示

## jQuery 和 AJAX 调用代码

```
(function($)
{
    $(document).ready(function()
    {
        $.ajaxSetup(
        {
            cache: false,
            beforeSend: function() {
                $('#content').hide();
                $('#loading').show();
            },
            complete: function() {
                $('#loading').hide();
                $('#content').show();
            },
            success: function() {
                $('#loading').hide();
                $('#content').show();
            }
        });
        var $container = $("#content");
        $container.load("rss-feed-data.php");
        var refreshId = setInterval(function()
        {
            $container.load('rss-feed-data.php');
        }, 9000);
    });
})(jQuery);
```

![data-refresh-x-seconds](img/95d137b58cc407efc48438f18eeda5de.png "data-refresh-x-seconds")

## PHP 数据脚本代码

这个 PHP 脚本加载博客 RSS 提要，并发回数据以及当前日期和时间。

```
<?php
$feed_url = 'http://blogoola.com/blog/feed/';
$content = file_get_contents($feed_url);
$x = new SimpleXmlElement($content);
$feedData = '';
$date = date("Y-m-d H:i:s");

//output
$feedData .=  "<ul>";
foreach($x->channel->item as $entry) {
    $feedData .= "<li><a href='$entry->link' title='$entry->title'>" . $entry->title . "</a></li>";
}
$feedData .= "";
$feedData .= "<p>Data current as at: ".$date."</p>";

echo $feedData;
?>
```

## 完整代码

```
<html>
<head>
<title>Auto Refresh Div Content Demo | jQuery4u</title>
<!-- For ease i'm just using a JQuery version hosted by JQuery- you can download any version and link to it locally -->
<script src="https://code.jquery.com/jquery-latest.js"></script>
<script>
(function($)
{
    $(document).ready(function()
    {
        $.ajaxSetup(
        {
            cache: false,
            beforeSend: function() {
                $('#content').hide();
                $('#loading').show();
            },
            complete: function() {
                $('#loading').hide();
                $('#content').show();
            },
            success: function() {
                $('#loading').hide();
                $('#content').show();
            }
        });
        var $container = $("#content");
        $container.load("rss-feed-data.php");
        var refreshId = setInterval(function()
        {
            $container.load('rss-feed-data.php');
        }, 9000);
    });
})(jQuery);
</script>
</head>
<body>

<div id="wrapper">
    <div id="content"></div>
    <img src="loading.gif" id="loading" alt="loading" style="display:none;" />
</div>

</body>
</html>
```

## 分享这篇文章