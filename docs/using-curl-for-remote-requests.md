# 对远程请求使用 cURL

> 原文：<https://www.sitepoint.com/using-curl-for-remote-requests/>

如果你是 Linux 用户，那么你可能用过 cURL。这是一个强大的工具，从发送电子邮件到下载最新的《我的小马》字幕。在这篇文章中，我将解释如何在 PHP 中使用 cURL 扩展。该扩展为我们提供了舒适的 PHP 世界中控制台实用程序的功能。我将讨论发送 GET 和 POST 请求、处理登录 cookies 和 FTP 功能。

在我们开始之前，确保您已经安装了扩展(和 libcURL 库)。默认情况下不会安装。在大多数情况下，它可以用你系统的软件包管理器来安装，但是你可以在 PHP 手册中找到说明。

## 它是如何工作的？

所有 cURL 请求都遵循相同的基本模式:

1.  首先，我们通过调用`curl_init()`函数初始化 cURL 资源(通常缩写为 *ch* 表示“cURL 句柄”)。
2.  接下来，我们设置各种选项，如 URL、请求方法、有效负载数据等。可以用`curl_setopt()`单独设置选项，或者我们可以将一组选项传递给`curl_setopt_array()`。
3.  然后我们通过调用`curl_exec()`来执行请求。
4.  最后，我们释放资源来清空内存。

因此，发出请求的样板代码如下所示:

```
<?php
// init the resource
$ch = curl_init();

// set a single option...
curl_setopt($ch, OPTION, $value);
// ... or an array of options
curl_setopt_array($ch, array(
OPTION1 => $value1,
OPTION2 => $value2
));

// execute
$output = curl_exec($ch);

// free
curl_close($ch);
```

对于请求，唯一改变的是设置了什么选项，这当然取决于你对 cURL 做了什么。

## 检索网页

我能想到的使用 cURL 的最基本的例子就是简单地获取网页的内容。所以，让我们以英国广播公司的主页为例。

```
<?php
curl_setopt_array(
$ch, array(
CURLOPT_URL => 'http://www.bbc.co.uk/',
CURLOPT_RETURNTRANSFER => true
));

$output = curl_exec($ch);
echo $output;
```

检查浏览器中的输出，您应该会看到 BBC 网站显示出来。我们很幸运，因为网站显示正确，因为它绝对链接到样式表和图像。

我们刚才使用的选项是:

*   `CURLOPT_URL`–指定请求的 URL
*   `CURLOPT_RETURNTRANSFER`–设置为 false 时，`curl_exec()`根据请求的成功与否返回 true 或 false。当设置为 true 时，`curl_exec()`返回响应的内容。

## 登录网站

cURL 执行了一个 GET 请求来检索 BBC 页面，但是 cURL 也可以使用其他方法，比如 POST 和 PUT。对于这个例子，让我们模拟登录一个 WordPress 支持的网站。登录是通过向*http://example.com/wp-login.php*发送一个 POST 请求来完成的，其中包含以下详细信息:

*   `login`–用户名
*   `pwd`–密码
*   `redirect_to`–登录后我们想去的网址
*   `testcookie`–应该设置为 1(这只是针对 WordPress)

当然，这些参数是针对每个站点的。您应该经常亲自检查输入名称，这可以通过在浏览器中查看 HTML 页面的源代码来轻松完成。

```
<?php
$postData = array(
'login' => 'acogneau',
'pwd' => 'secretpassword',
'redirect_to' => 'http://example.com',
'testcookie' => '1'
);

curl_setopt_array($ch, array(
CURLOPT_URL => 'http://example.com/wp-login.php',
CURLOPT_RETURNTRANSFER => true,
CURLOPT_POST => true,
CURLOPT_POSTFIELDS => $postData,
CURLOPT_FOLLOWLOCATION => true
));

$output = curl_exec($ch);
echo $output;
```

新选项包括:

*   `CURLOPT_POST`–如果您想要发送发布请求，请将此项设为 true
*   `CURLOPT_POSTFIELDS`–将在请求正文中发送的数据
*   `CURLOPT_FOLLOWLOCATION`–如果设置为真，cURL 将跟随重定向

啊哦！但是，如果您测试上述内容，您会看到一条错误消息:“错误:Cookies 被阻止或不被您的浏览器支持。您必须启用 cookies 才能使用 WordPress。这是正常的，因为我们需要启用 cookies 来使会话工作。为此，我们增加了两个选项。

```
<?php
curl_setopt_array($ch, array(
CURLOPT_URL => 'http://example.com/wp-login.php',
CURLOPT_RETURNTRANSFER => true,
CURLOPT_POST => true,
CURLOPT_POSTFIELDS => $postData,
CURLOPT_FOLLOWLOCATION => true,
CURLOPT_COOKIESESSION => true,
CUROPT_COOKIEJAR => 'cookie.txt'
));
```

新选项包括:

*   `CURLOPT_COOKIESESSION`–如果设置为 true，cURL 将启动一个新的 cookie 会话，并忽略任何以前的 cookie
*   `CURLOPT_COOKIEJAR`–这是 cURL 保存 cookie 信息的文件名。请确保您拥有写入该文件的正确权限！

既然我们已经登录，我们只需要为后续请求引用 cookie 文件。

## 使用 FTP

使用 cURL 通过 FTP 下载和上传文件也很容易。让我们来看看下载一个文件:

```
<?php
curl_setopt_array($ch, array(
CURLOPT_URL => 'ftp://ftp.example.com/test.txt',
CURLOPT_RETURNTRANSFER => true,
CURLOPT_USERPWD => 'username:password'
));

$output = curl_exec($ch);
echo $output;
```

注意，由于安全原因，允许匿名上传和下载的公共 FTP 服务器并不多，所以上面的 URL 和凭证只是占位符。

这几乎与发送 HTTP 请求相同，但只有几个微小的区别:

*   `CURLOPT_URL`–文件的 URL，注意使用“ftp://”而不是“http://”
*   `CURLOT_USERPWD`–FTP 服务器的登录凭证

通过 FTP 上传文件稍微复杂一些，但是仍然可以管理。看起来是这样的:

```
<?php
$fp = fopen('test.txt', 'r');
curl_setopt_array($ch, array(
CURLOPT_URL => 'ftp://ftp.example.com/test.txt',
CURLOPT_USERPWD => 'username:password'
CURLOPT_UPLOAD => true,
CURLOPT_INFILE => $fp,
CURLOPT_INFILESIZE => filesize('test.txt')
));
curl_exec($ch);

fclose($fp);
curl_close($ch);
```

这里的重要选项有:

*   `CURLOPT_UPLOAD`–明显的布尔值
*   `CURLOPT_INFILE`–我们想要上传的文件的可读流
*   `CURLOPT_INFILESIZE`–我们要上传的文件的大小，以字节为单位

## 发送多个请求

假设我们必须执行五个请求来检索所有必要的数据。请记住，有些事情是我们无法控制的，比如网络延迟和目标服务器的响应速度。很明显，当发出五个连续的调用时，任何延迟都会累积起来！缓解这个问题的一种方法是异步发出请求。

异步技术在 JavaScript 和 Node.js 社区中更常见，但简单地说，我们不是等待耗时的任务完成，而是将任务分配给不同的线程或进程，并在此期间继续做其他事情。当任务完成时，我们回来看结果。重要的是我们没有浪费时间等待结果；我们用它来独立执行其他代码。

执行多个异步 cURL 请求的方法与以前略有不同。我们以同样的方式开始——我们初始化每个通道，然后设置选项——但是然后我们初始化一个 multihandler `using curl_multi_init()`并用`curl_multi_add_handle()`将我们的通道添加到它里面。我们通过遍历它们并检查它们的状态来执行处理程序。最后我们用`curl_multi_getcontent()`得到一个回应的内容。

```
<?php
// URLs we want to retrieve
$urls = array(
'http://www.google.com',
'http://www.bing.com',
'http://www.yahoo.com',
'http://www.twitter.com',
'http://www.facebook.com'
);

// initialize the multihandler
$mh = curl_multi_init();

$channels = array();
foreach ($urls as $key => $url) {
// initiate individual channel
$channels[$key] = curl_init();
curl_setopt_array($channels[$key], array(
CURLOPT_URL => $url,
CURLOPT_RETURNTRANSFER => true,
CURLOPT_FOLLOWLOCATION => true
));

// add channel to multihandler
curl_multi_add_handle($mh, $channels[$key]);
}

// execute - if there is an active connection then keep looping
$active = null;
do {
$status = curl_multi_exec($mh, $active);
}
while ($active && $status == CURLM_OK);

// echo the content, remove the handlers, then close them
foreach ($channels as $chan) {
echo curl_multi_getcontent($chan);
curl_multi_remove_handle($mh, $chan);
curl_close($chan);
}

// close the multihandler
curl_multi_close($mh);
```

上面的代码在我的笔记本电脑上运行了大约 1100 毫秒。在没有多接口的情况下，按顺序执行请求大约需要 2000 毫秒。想象一下，如果您发送数百个请求，您会获得多大的收益！

存在多个抽象和包装多接口的项目。讨论它们超出了本文的范围，但是如果您计划异步发出多个请求，那么我建议您看一看它们:

*   [github.com/petewarden/ParallelCurl](https://github.com/petewarden/ParallelCurl)
*   [SEM labs . co . uk/journal/object-oriented-curl-class-with-multi-threading](http://semlabs.co.uk/journal/object-oriented-curl-class-with-multi-threading)

## 解决纷争

如果您正在使用 cURL，那么您可能正在执行对第三方服务器的请求。你无法控制它们，很多事情都会出错:服务器可能会离线，目录结构可能会改变，等等。我们需要一种有效的方法来找出某个东西不工作时的问题所在，幸运的是 cURL 为此提供了两个函数:`curl_getinfo()`和`curl_error()`。

`curl_getinfo()`返回一个数组，其中包含关于频道的所有信息，因此如果您想检查是否一切正常，可以使用:

```
<?php
var_dump(curl_getinfo($ch));
```

如果弹出一个错误，您可以使用`curl_error()`来检查它:

```
<?php
if (!curl_exec($ch)) {
// if curl_exec() returned false and thus failed
echo 'An error has occurred: ' . curl_error($ch);
}
else {
echo 'everything was successful';
}
```

## 结论

cURL 提供了一种强大而有效的方式来进行远程调用，因此如果您需要一个爬虫或其他工具来访问外部 API，cURL 是一个很好的工具。它为我们提供了一个很好的界面和一个相对简单的方法来执行请求。更多信息，请查看 PHP 手册和 T2 cURL 网站。下次见！

对这篇文章的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章