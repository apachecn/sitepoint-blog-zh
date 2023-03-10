# 用 PHP 缓冲刷新加速你的网站

> 原文：<https://www.sitepoint.com/faster-web-pages-php-buffer-flush/>

PHP 输出缓冲通常是默认启用的。在 PHP 的旧版本中，每当解释器遇到 PHP 分隔符之外的 [echo](http://php.net/manual/en/function.echo.php) 语句或文本时，就会向浏览器发送一个字符串。

输出缓冲使这个过程更快更有效。缓冲区本质上是一个大的内存驻留字符串。当文本输出时，它被附加到缓冲区，而不是立即返回到浏览器。然后缓冲器被“刷新”，即其内容被传输并且字符串被重置以等待进一步的输出。冲洗发生在以下情况:

1.  PHP 解释器到达页面的末尾
2.  缓冲区超过了 PHP 的 [output_buffering](http://www.php.net/manual/en/outcontrol.configuration.php#ini.output-buffering) 配置设置中指定的字节数，或者
3.  调用 [flush()](http://www.php.net/manual/en/function.flush.php) 或 [ob_flush()](http://www.php.net/manual/en/function.ob-flush.php) 函数。

有一些注意事项，但是，假设它在您的环境中工作，您应该考虑在页面的标记之后立即刷新缓冲区，例如

```
 <!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>Buffer flushing in action</title>
<link rel="stylesheet" type="text/css" href="styles.css" />
<link rel="shortcut icon" href="favicon.ico" />
</head>
<?php
// flush the buffer
flush();
?>
<body>
&hellip; 
```

(如果你使用 WordPress，你可以在你的主题的 header.php 文件中使用类似的代码。)

一旦浏览器收到 HTML `head`，它就可以开始下载所有链接的 CSS 文件、favicons 和其他资源。这些下载可以在浏览器空闲并等待主要`body`内容时发生。

速度的提高将取决于所需的服务器端处理、页面的权重、CSS 文件的数量和大小，以及浏览器是否缓存了任何资源。然而，这是一个如此简单的优化，没有理由不这样做。

我很想知道这种技术是否会在你的网站或应用程序上产生明显的速度差异。

## 分享这篇文章