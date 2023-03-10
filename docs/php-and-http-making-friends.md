# PHP 和 HTTP 交友

> 原文：<https://www.sitepoint.com/php-and-http-making-friends/>

如果你在寻找 15 分钟的消遣， [pecl_http](http://pecl.php.net/package/pecl_http) 是值得的，尤其是下载时附带的`tutorial.txt`文件。

基本上给了 PHP 一个很大的 HTTP 提升。它做的一些特别的事情。

*   对于外发响应，增加了一些函数，减少了通常使用[头](http://www.php.net/header)的工作量，例如 http_date()从 Unix 时间戳生成 RFC 822/1123 日期。
*   对于传入的请求，添加了其他函数来使您通常通过 [get_headers()](http://www.php.net/get_headers) 做的事情变得更容易，例如 http_negotiate_charset()(一旦我们点击 [PHP6](http://derickrethans.nl/files/php6-unicode.pdf) [PDF]，这实际上可能会很有用)。
*   提供一个包装器 round [curl](http://www.php.net/curl) ，它有一个友好的 API(例如注册像 HTTPRequest 和 http message–API 这样的类以及 Perl 的 [LWP](http://cpan.uwinnipeg.ca/htdocs/libwww-perl/HTTP/Request.html) )。
*   使用一个类 HttpRequestPool，支持并行请求，甚至有一个机制允许你的代码在等待请求完成的时候做一些事情。想想 Javascript 中的异步 XMLHttpRequests)这里提供了一个构建 RSS 聚合器的很好的例子，这将是克里斯蒂安[最近的问题](http://blog.bitflux.ch/archive/2005/10/24/fetching-a-looot-of-feeds-with-php.html)的另一个解决方案[。](http://blog.bitflux.ch/archive/2005/10/28/how-to-fetch-a-lot-of-feeds.html)
*   也许最酷的是，提供了一个类 HTTPResponse，它似乎与 PHP 紧密集成，并为您处理 HTTP 缓存(条件 get、ETAGs 等)。

最后一点特别有趣——它看起来就像你只需要激活 HTTPResponse 类，然后它会自动检查你的内容，并计算出有什么变化。pecl_http 这样描述；

> HttpResponse 的主要关键特性之一是 HTTP 缓存。HttpResponse 将根据 http.etag_mode INI 设置计算 ETag，并确定发送实体的最后修改时间。它使用这两个指示符来决定客户端的缓存条目是否仍然有效，并在适用的情况下发出“304 Not Modified”响应。

我还没有验证这一点(HTTPResponse 类需要 PHP 5.1.x，而我现在还没有这个版本)，但是教程示例看起来像这样；

```
 <?php
HttpResponse::setCacheControl('public');
HttpResponse::setCache(true);
HttpResponse::capture();

print "This will be cached until content changes!n";
print "Note that this approach will only save the clients download time.n";
?> 
```

虽然这个*不会*首先将你的服务器从生成内容的工作中拯救出来，但是它将帮助你节省带宽。

注意，目前，据我所知，pecl_http 不能通过[http://pecl4win.php.net](http://pecl4win.php.net)获得，所以除非你知道你在做什么，否则你不能在 Windows 下安装它(旁注:需要看看类似 [this](http://www.vrplumber.com/programming/mstoolkit/) 的东西是否适用于 PHP)。

15 分钟到了。如果你正在寻找进一步的转移，尝试 [HTTP 缓存&内容发布者的缓存破坏](http://public.yahoo.com/~radwin/talks/http-caching.htm) …

## 分享这篇文章