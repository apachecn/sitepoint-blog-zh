# 如何用 PHP 读取大文件(不杀死你的服务器)

> 原文：<https://www.sitepoint.com/performant-reading-big-files-php/>

作为 PHP 开发人员，我们并不经常需要担心内存管理。PHP 引擎做了出色的清理工作，短期执行上下文的 web 服务器模型意味着即使最草率的代码也不会有持久的效果。

有时候，我们可能需要走出这个舒适的界限，比如当我们试图在我们能创建的最小的 VPS 上为一个大项目运行 Composer 时，或者当我们需要在同样小的服务器上读取大文件时。

我们将在本教程中讨论后一个问题。

本教程的代码可以在 [GitHub](https://github.com/sitepoint-editors/sitepoint-performant-reading-of-big-files-in-php) 上找到。

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

## 衡量成功

确保我们对代码进行任何改进的唯一方法是测量一个糟糕的情况，然后在我们应用了我们的修正后将该测量与另一个进行比较。换句话说，除非我们知道一个“解决方案”对我们有多大帮助(如果有的话)，否则我们无法知道它是否真的是一个解决方案。

我们可以关注两个指标。首先是 CPU 使用率。我们想要工作的过程有多快或多慢？第二个是内存使用。脚本执行需要多少内存？这些通常是成反比的——这意味着我们可以以 CPU 使用为代价来卸载内存使用，反之亦然。

在异步执行模型中(比如多进程或多线程 PHP 应用程序)，CPU 和内存使用都是重要的考虑因素。在传统的 PHP 架构中，当其中一个达到服务器的极限时，这些*通常会成为一个问题。*

*测量 PHP 内部的 CPU 使用率是不切实际的。如果那是你想关注的领域，考虑在 Ubuntu 或 macOS 上使用类似`top`的东西。对于 Windows，可以考虑使用 Linux 子系统，这样就可以在 Ubuntu 中使用`top`。*

出于本教程的目的，我们将测量内存使用情况。我们将看看“传统”脚本中使用了多少内存。我们将实施一些优化策略，并对它们进行测量。最后，我希望你能够做出明智的选择。

我们将使用以下方法来查看使用了多少内存:

```
// formatBytes is taken from the php.net documentation

memory_get_peak_usage();

function formatBytes($bytes, $precision = 2) {
    $units = array("b", "kb", "mb", "gb", "tb");

    $bytes = max($bytes, 0);
    $pow = floor(($bytes ? log($bytes) : 0) / log(1024));
    $pow = min($pow, count($units) - 1);

    $bytes /= (1 << (10 * $pow));

    return round($bytes, $precision) . " " . $units[$pow];
} 
```

我们将在脚本的末尾使用这些函数，这样我们就可以看到哪个脚本一次使用了最多的内存。

## 我们有什么选择？

我们可以采取许多方法来有效地读取文件。但是也有两种可能的情况我们可以使用它们。我们可能希望同时读取和处理数据，输出处理后的数据或根据我们读取的内容执行其他操作。我们可能还想在不真正需要访问数据的情况下转换数据流。

让我们想象一下，对于第一个场景，我们希望能够读取一个文件，并每 10，000 行创建一个单独的排队处理作业。我们需要在内存中保存至少 10，000 行，并将它们传递给排队的作业管理器(无论采取什么形式)。

对于第二个场景，假设我们想要压缩一个特别大的 API 响应的内容。我们不关心它说什么，但我们需要确保它以压缩形式备份。

在这两种情况下，我们都需要读取大文件。首先，我们需要知道数据是什么。第二，我们不关心数据是什么。让我们探索这些选项…

## 逐行读取文件

有许多处理文件的功能。让我们将几个合并成一个简单的文件阅读器:

```
// from memory.php

function formatBytes($bytes, $precision = 2) {
    $units = array("b", "kb", "mb", "gb", "tb");

    $bytes = max($bytes, 0);
    $pow = floor(($bytes ? log($bytes) : 0) / log(1024));
    $pow = min($pow, count($units) - 1);

    $bytes /= (1 << (10 * $pow));

    return round($bytes, $precision) . " " . $units[$pow];
}

print formatBytes(memory_get_peak_usage()); 
```

```
// from reading-files-line-by-line-1.php

function readTheFile($path) {
    $lines = [];
    $handle = fopen($path, "r");

    while(!feof($handle)) {
        $lines[] = trim(fgets($handle));
    }

    fclose($handle);
    return $lines;
}

readTheFile("shakespeare.txt");

require "memory.php"; 
```

我们正在阅读包含莎士比亚全集的文本文件。文本文件约为 **5.5MB** ，内存使用峰值为 **12.8MB** 。现在，让我们使用生成器来读取每一行:

```
// from reading-files-line-by-line-2.php

function readTheFile($path) {
    $handle = fopen($path, "r");

    while(!feof($handle)) {
        yield trim(fgets($handle));
    }

    fclose($handle);
}

readTheFile("shakespeare.txt");

require "memory.php"; 
```

文本文件大小相同，但内存使用峰值为 **393KB** 。在我们对正在读取的数据做些什么之前，这没有任何意义。也许每当我们看到两行空行时，我们可以将文档分成几块。大概是这样的:

```
// from reading-files-line-by-line-3.php

$iterator = readTheFile("shakespeare.txt");

$buffer = "";

foreach ($iterator as $iteration) {
    preg_match("/\n{3}/", $buffer, $matches);

    if (count($matches)) {
        print ".";
        $buffer = "";
    } else {
        $buffer .= $iteration . PHP_EOL;
    }
}

require "memory.php"; 
```

你猜我们现在用了多少内存？如果您知道，即使我们将文本文档分成 **1，216** 个块，我们仍然只使用 **459KB** 的内存，您会感到惊讶吗？鉴于生成器的性质，我们将使用的最大内存是我们在一次迭代中需要存储的最大文本块。在这种情况下，最大的块是 **101，985** 个字符。

*我已经写了关于使用生成器和[尼基塔·波波夫的迭代器库](https://github.com/nikic/iter)的[性能提升，所以如果你想了解更多，就去看看吧！](https://www.sitepoint.com/memory-performance-boosts-with-generators-and-nikiciter/)*

生成器还有其他用途，但是这一个对于大文件的高性能读取来说显然是很好的。如果我们需要处理数据，生成器可能是最好的方式。

## 文件之间的管道

在不需要对数据进行操作的情况下，我们可以将文件数据从一个文件传递到另一个文件。这就是通常所说的**管道**(大概是因为我们看不到管道内的东西，除了两端……当然，只要它是不透明的！).我们可以通过使用流方法来实现这一点。让我们首先编写一个从一个文件传输到另一个文件的脚本，以便我们可以测量内存使用情况:

```
// from piping-files-1.php

file_put_contents(
    "piping-files-1.txt", file_get_contents("shakespeare.txt")
);

require "memory.php"; 
```

不出所料，这个脚本运行时使用的内存比它复制的文本文件稍多。这是因为它必须读取(并保存)内存中的文件内容，直到它写入新文件。对于小文件，这可能没问题。当我们开始使用更大的文件时，没有那么多…

让我们尝试从一个文件流式传输(或管道传输)到另一个文件:

```
// from piping-files-2.php

$handle1 = fopen("shakespeare.txt", "r");
$handle2 = fopen("piping-files-2.txt", "w");

stream_copy_to_stream($handle1, $handle2);

fclose($handle1);
fclose($handle2);

require "memory.php"; 
```

这段代码有点奇怪。我们打开两个文件的句柄，第一个是读模式，第二个是写模式。然后我们从第一个复制到第二个。我们通过再次关闭这两个文件来结束。知道使用的内存是 **393KB** 可能会让你大吃一惊。

这似乎很熟悉。这不就是生成器代码在读取每一行时用来存储的吗？这是因为`fgets`的第二个参数指定了每行要读取多少字节(默认为`-1`或直到它到达一个新行)。

`stream_copy_to_stream`的第三个参数是完全相同类型的参数(具有完全相同的默认值)。`stream_copy_to_stream`从一个流中读取，一次一行，并将其写入另一个流。它跳过了生成器产生一个值的部分，因为我们不需要处理这个值。

管道这个文本对我们没有用，所以让我们想想其他可能有用的例子。假设我们想从我们的 CDN 输出一个图像，作为一种重定向的应用程序路由。我们可以用类似下面的代码来说明它:

```
// from piping-files-3.php

file_put_contents(
    "piping-files-3.jpeg", file_get_contents(
        "https://github.com/assertchris/uploads/raw/master/rick.jpg"
    )
);

// ...or write this straight to stdout, if we don't need the memory info

require "memory.php"; 
```

想象一下，一条应用程序路线将我们带到了这段代码。但是，我们不是从本地文件系统提供文件，而是想从 CDN 获取文件。我们可以用更优雅的东西来代替`file_get_contents`(比如[狂饮](http://docs.guzzlephp.org/en/stable/))，但本质上是一样的。

内存使用量(对于此图像)大约为 **581KB** 。现在，不如我们来试试这个吧？

```
// from piping-files-4.php

$handle1 = fopen(
    "https://github.com/assertchris/uploads/raw/master/rick.jpg", "r"
);

$handle2 = fopen(
    "piping-files-4.jpeg", "w"
);

// ...or write this straight to stdout, if we don't need the memory info

stream_copy_to_stream($handle1, $handle2);

fclose($handle1);
fclose($handle2);

require "memory.php"; 
```

内存使用量略少(在 **400KB** )，但结果是一样的。如果我们不需要内存信息，我们也可以打印成标准输出。事实上，PHP 提供了一种简单的方法来做到这一点:

```
$handle1 = fopen(
    "https://github.com/assertchris/uploads/raw/master/rick.jpg", "r"
);

$handle2 = fopen(
    "php://stdout", "w"
);

stream_copy_to_stream($handle1, $handle2);

fclose($handle1);
fclose($handle2);

// require "memory.php"; 
```

### 其他流

我们还可以通过管道传输和/或写入和/或读取其他一些流:

*   `php://stdin`(只读)
*   `php://stderr`(只写，如 php://stdout)
*   `php://input`(只读)，它允许我们访问原始请求体
*   `php://output`(只写)，允许我们写入输出缓冲区
*   `php://memory`和`php://temp`(读写)是我们可以临时存储数据的地方。不同之处在于，`php://temp`会在文件系统变得足够大时将数据存储在文件系统中，而`php://memory`会一直存储在内存中，直到内存用完。

## 过滤

我们可以对流使用另一个叫做**过滤器**的技巧。它们是一种介于两者之间的步骤，提供了对流数据的一点点控制，而不会暴露给我们。假设我们想要压缩我们的`shakespeare.txt`。我们可以使用 Zip 扩展:

```
// from filters-1.php

$zip = new ZipArchive();
$filename = "filters-1.zip";

$zip->open($filename, ZipArchive::CREATE);
$zip->addFromString("shakespeare.txt", file_get_contents("shakespeare.txt"));
$zip->close();

require "memory.php"; 
```

这是一段简洁的代码，但是它大约有 **10.75MB** 。我们可以做得更好，使用过滤器:

```
// from filters-2.php

$handle1 = fopen(
    "php://filter/zlib.deflate/resource=shakespeare.txt", "r"
);

$handle2 = fopen(
    "filters-2.deflated", "w"
);

stream_copy_to_stream($handle1, $handle2);

fclose($handle1);
fclose($handle2);

require "memory.php"; 
```

在这里，我们可以看到`php://filter/zlib.deflate`过滤器，它读取并压缩资源的内容。然后，我们可以将这些压缩数据通过管道传输到另一个文件中。这只使用了 **896KB** 。

我知道这不是同一种格式，或者制作 zip 存档有好处。你不得不怀疑:如果你可以选择不同的格式，并节省 12 倍的内存，你不会吗？

为了解压缩数据，我们可以通过另一个 zlib 过滤器运行压缩后的文件:

```
// from filters-2.php

file_get_contents(
    "php://filter/zlib.inflate/resource=filters-2.deflated"
); 
```

在理解 PHP 中的流和“[有效地使用 PHP 流](https://www.sitepoint.com/using-php-streams-effectively/)”中已经广泛地涉及了流。如果你想要不同的视角，去看看吧！

## 自定义流

`fopen`和`file_get_contents`有它们自己的默认选项，但是它们是完全可定制的。为了定义它们，我们需要创建一个新的流上下文:

```
// from creating-contexts-1.php

$data = join("&", [
    "twitter=assertchris",
]);

$headers = join("\r\n", [
    "Content-type: application/x-www-form-urlencoded",
    "Content-length: " . strlen($data),
]);

$options = [
    "http" => [
        "method" => "POST",
        "header"=> $headers,
        "content" => $data,
    ],
];

$context = stream_content_create($options);

$handle = fopen("https://example.com/register", "r", false, $context);
$response = stream_get_contents($handle);

fclose($handle); 
```

在这个例子中，我们试图向一个 API 发出一个`POST`请求。API 端点是安全的，但是我们仍然需要使用`http`上下文属性(用于`http`和`https`)。我们设置了一些头，并打开了一个 API 的文件句柄。我们可以以只读方式打开句柄，因为上下文负责写操作。

我们可以定制很多东西，所以如果你想了解更多，最好查阅一下[文档](https://php.net/function.stream-context-create)。

## 定制协议和过滤器

在我们结束之前，让我们讨论一下定制协议。如果您查看文档中的[，您可以找到一个要实现的示例类:](https://php.net/manual/en/class.streamwrapper.php)

```
Protocol {
    public resource $context;
    public __construct ( void )
    public __destruct ( void )
    public bool dir_closedir ( void )
    public bool dir_opendir ( string $path , int $options )
    public string dir_readdir ( void )
    public bool dir_rewinddir ( void )
    public bool mkdir ( string $path , int $mode , int $options )
    public bool rename ( string $path_from , string $path_to )
    public bool rmdir ( string $path , int $options )
    public resource stream_cast ( int $cast_as )
    public void stream_close ( void )
    public bool stream_eof ( void )
    public bool stream_flush ( void )
    public bool stream_lock ( int $operation )
    public bool stream_metadata ( string $path , int $option , mixed $value )
    public bool stream_open ( string $path , string $mode , int $options ,
        string &$opened_path )
    public string stream_read ( int $count )
    public bool stream_seek ( int $offset , int $whence = SEEK_SET )
    public bool stream_set_option ( int $option , int $arg1 , int $arg2 )
    public array stream_stat ( void )
    public int stream_tell ( void )
    public bool stream_truncate ( int $new_size )
    public int stream_write ( string $data )
    public bool unlink ( string $path )
    public array url_stat ( string $path , int $flags )
} 
```

我们不打算实现其中的一个，因为我认为它值得有自己的教程。有许多工作需要做。但是一旦这项工作完成，我们就可以很容易地注册我们的流包装器了:

```
if (in_array("highlight-names", stream_get_wrappers())) {
    stream_wrapper_unregister("highlight-names");
}

stream_wrapper_register("highlight-names", "HighlightNamesProtocol");

$highlighted = file_get_contents("highlight-names://story.txt"); 
```

类似地，也可以创建定制的流过滤器。文档中有一个示例过滤器类:

```
Filter {
    public $filtername;
    public $params
    public int filter ( resource $in , resource $out , int &$consumed ,
        bool $closing )
    public void onClose ( void )
    public bool onCreate ( void )
} 
```

这可以很容易地注册:

```
$handle = fopen("story.txt", "w+");
stream_filter_append($handle, "highlight-names", STREAM_FILTER_READ); 
```

`highlight-names`需要匹配新过滤器类的`filtername`属性。也可以在`php://filter/highligh-names/resource=story.txt`字符串中使用自定义过滤器。定义过滤器比定义协议容易得多。原因之一是协议需要处理目录操作，而过滤器只需要处理每个数据块。

如果你有勇气，我强烈建议你尝试创建定制的协议和过滤器。如果您可以将过滤器应用于`stream_copy_to_stream`操作，那么您的应用程序将几乎不使用任何内存，即使在处理非常大的文件时也是如此。想象一下写一个`resize-image`过滤器或者和`encrypt-for-application`过滤器。

## 摘要

虽然这不是我们经常遇到的问题，但在处理大文件时很容易出错。在异步应用程序中，当我们不注意内存使用时，很容易导致整个服务器瘫痪。

本教程有望向您介绍一些新的想法(或者刷新您对它们的记忆)，以便您可以更多地考虑如何高效地读写大文件。当我们开始熟悉流和生成器，并停止使用像`file_get_contents`这样的函数时:一整类错误就会从我们的应用程序中消失。这似乎是一个很好的目标！

## 分享这篇文章