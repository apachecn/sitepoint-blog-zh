# 理解 PHP 中的流

> 原文：<https://www.sitepoint.com/understanding-streams-in-php/>

流是 PHP 提供的资源，我们经常透明地使用它，但它也可以是非常强大的工具。通过学习如何利用它们的能力，我们可以将我们的应用提高到一个更高的水平。

PHP 手册对流有很好的描述:

> PHP 4.3.0 引入了流，作为一种概括文件、网络、数据压缩和其他共享一组公共功能和用途的操作的方式。用最简单的定义来说，一个**流**是一个**资源**对象，它表现出可流式传输的行为。也就是说，它可以以线性方式被读取或写入，并且能够`fseek()`到流中的任意位置。

每个流都有一个实现包装器，其中有处理特定协议或编码所需的额外代码。PHP 提供了一些内置的包装器，我们可以很容易地创建和注册自定义的包装器。我们甚至可以使用上下文和过滤器来修改或增强包装器的行为。

## 流基础

流被引用为`<scheme>://<target>`。`<scheme>`是包装器的名称，`<target>`将根据包装器的语法而变化。

默认的包装器是`file://`，这意味着我们每次访问文件系统时都使用一个流。例如，我们可以写`readfile('/path/to/somefile.txt')`或`readfile('file:///path/to/somefile.txt')`，得到相同的结果。如果我们改为使用`readfile('http://google.com/')`，那么我们是在告诉 PHP 使用 HTTP 流包装器。

正如我之前说过的，PHP 提供了一些内置的包装器、协议和过滤器。要知道我们的机器上安装了哪些包装器，我们可以使用:

```
<?php
print_r(stream_get_transports());
print_r(stream_get_wrappers());
print_r(stream_get_filters());
```

我的安装输出以下内容:

```
Array
(
    [0] => tcp
    [1] => udp
    [2] => unix
    [3] => udg
    [4] => ssl
    [5] => sslv3
    [6] => sslv2
    [7] => tls
)
Array
(
    [0] => https
    [1] => ftps
    [2] => compress.zlib
    [3] => compress.bzip2
    [4] => php
    [5] => file
    [6] => glob
    [7] => data
    [8] => http
    [9] => ftp
    [10] => zip
    [11] => phar
)
Array
(
    [0] => zlib.*
    [1] => bzip2.*
    [2] => convert.iconv.*
    [3] => string.rot13
    [4] => string.toupper
    [5] => string.tolower
    [6] => string.strip_tags
    [7] => convert.*
    [8] => consumed
    [9] => dechunk
    [10] => mcrypt.*
    [11] => mdecrypt.*
)
```

不错的布景，你不觉得吗？

此外，我们可以编写或使用第三方流，用于亚马逊 S3、T2、MS Excel、谷歌存储、甚至推特。

## php://包装器

PHP 有自己的包装器来访问该语言的 I/O 流。有基本的`php://stdin`、`php://stdout`和`php://stderr`包装器，它们映射默认的 I/O 资源，我们还有`php://input`，它是一个只读流，带有 POST 请求的原始主体。当我们处理将数据有效负载放在 POST 请求主体中的远程服务时，这很方便。

让我们用 cURL 做一个快速测试:

```
curl -d "Hello World" -d "foo=bar&name=John" http://localhost/dev/streams/php_input.php
```

响应 PHP 脚本中的`print_r($_POST)`的结果将是:

```
Array
(
    [foo] => bar
    [name] => John
)
```

注意，第一个数据包不能从`$_POST`数组中访问。但是如果我们用`readfile('php://input')`来代替，我们会得到:

```
Hello World&foo=bar&name=John
```

PHP 5.1 引入了用于读写临时数据的`php://memory`和`php://temp`流包装器。顾名思义，数据分别存储在内存或底层系统管理的临时文件中。

还有`php://filter`，一个元包装器，设计用来在打开一个流时使用类似`readfile()`或`file_get_contents()` / `stream_get_contents()`的功能来应用过滤器。

```
<?php
// Write encoded data
file_put_contents("php://filter/write=string.rot13/resource=file:///path/to/somefile.txt","Hello World");

// Read data and encode/decode
readfile("php://filter/read=string.toupper|string.rot13/resource=http://www.google.com");
```

第一个示例使用过滤器对写入磁盘的数据进行编码，而第二个示例应用两个级联过滤器从远程 URL 读取数据。在我们的应用中，结果可以从非常基本到非常强大。

## 流上下文

上下文是一组特定于流的参数或选项，可以修改和增强包装器的行为。一个常见的使用上下文是修改 HTTP 包装器。这让我们避免在简单的网络操作中使用 cURL。

```
<?php
$opts = array(
'http'=>array(
'method'=>"POST",
'header'=> "Auth: SecretAuthTokenrn" .
"Content-type: application/x-www-form-urlencodedrn" .
"Content-length: " . strlen("Hello World"),
'content' => 'Hello World'
)
);
$default = stream_context_get_default($opts);
readfile('http://localhost/dev/streams/php_input.php');
```

首先，我们定义选项数组，这是一个格式为`$array['wrapper']['option_name']`的数组数组(可用的上下文选项因具体的包装器而异)。然后我们调用`stream_context_get_default()`，它返回默认的上下文并接受一组可选的选项来应用。`readfile()`语句使用这些设置来获取内容。

在这个例子中，内容是在请求体中发送的，所以远程脚本将使用`php://input`来读取它。我们可以使用`apache_request_headers()`访问标题，并获得:

```
Array
(
    [Host] => localhost
    [Auth] => SecretAuthToken
    [Content-type] => application/x-www-form-urlencoded
    [Content-length] => 11
)
```

我们已经修改了默认的上下文选项，但是我们也可以创建单独使用的替代上下文。

```
<?php
$alternative = stream_context_create($other_opts);
readfile('http://localhost/dev/streams/php_input.php', false, $alternative);
```

## 结论

我们如何在现实世界中利用流的力量？我们能从这里去哪里？正如我们所看到的，流共享一些或所有与文件系统相关的功能，所以我想到的第一个用途是一系列虚拟文件系统包装器，用于 PaaS 提供商，如 Heroku 或 AppFog，它们没有真正的文件系统。我们可以不费吹灰之力将我们的应用从标准托管服务移植到这些云服务上，并享受其中的好处。此外——我将在后续文章中展示——我们可以为实现自定义文件格式和编码的应用程序构建自定义包装器和过滤器。

## 分享这篇文章