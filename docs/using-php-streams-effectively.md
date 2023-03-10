# 有效使用 PHP 流

> 原文：<https://www.sitepoint.com/using-php-streams-effectively/>

在我的[上一篇文章](https://www.sitepoint.com/understanding-streams-in-php/)中，我们已经发现了 [PHP 流](http://php.net/manual/en/book.stream.php)的基础，以及它们有多强大。在本教程中，我们将在现实世界中使用这种能力。首先，我将向您展示如何构建您的自定义过滤器并将它们附加到一个流中，然后我们将在一个文档解析器应用程序中打包我们的过滤器。

如果您还没有阅读过[上一篇文章](https://www.sitepoint.com/understanding-streams-in-php/),那么我们鼓励您去阅读，因为理解引言对于理解这一部分至关重要。

本文的完整源代码可以从 Github 上的[获得。](https://github.com/phpmasterdotcom/VitoTardia-StreamsPlus)

## 使用过滤器

如前所述，过滤器是可以附加到流上的代码片段，以便在读取或写入时对数据执行操作。PHP 有一套很好的内置过滤器，比如`string.toupper`、`string.tolower`或`string.strip_tags`。一些 PHP 扩展也提供了自己的过滤器。例如，`mcrypt`扩展安装了`mcrypt.*`和`mdecrypt.*`过滤器。我们可以使用函数`stream_get_filters()`来获取您机器上可用的过滤器列表。

一旦我们知道我们可以依赖哪些过滤器，我们就可以使用`stream_filter_append()`将任意数量的过滤器添加到流资源中:

```
 $h = fopen('lorem.txt', 'r');
    stream_filter_append($h, 'convert.base64-encode');
    fpassthru($h);
    fclose($h);
```

或者使用`php://filter`元包装器打开一个流:

```
 $filter = 'convert.base64-encode';
    $file = 'lorem.txt';
    $h = fopen('php://filter/read=' . $filter . '/resource=' . $file,'r'); 
    fpassthru($h);
    fclose($h);
```

在上面的示例中，函数`fpassthru()`将输出示例文件的相同编码版本。很简单，不是吗？让我们看看可以用`php_user_filter`类做些什么。

## 在读取时过滤数据:降价过滤器

我们的第一个定制过滤器将被附加到一个读取流中，以便将来自源的 markdown 格式的数据转换成 HTML 标记。PHP 提供了我们正在扩展的基类 [`php_user_filter`](http://us3.php.net/manual/en/class.php-user-filter.php) 。这个基类有两个属性:`filtername`和`params`。`filtername`包含用于向`stream_filter_register()`注册我们的过滤器的标签，而`params`可以被`stream_filter_append()`用来向过滤器传递数据。

我们必须覆盖的主要 worker 方法是`filter()`。此方法由父流调用，并接收四个参数:

*   `$in`:指向一组包含待过滤数据的桶对象的指针。
*   `$out`:指向另一组存储转换数据的桶的指针。
*   `$consumed`:引用传递的计数器，必须按转换后的数据长度递增。
*   `$closing`:一个布尔标志，如果我们处于最后一个周期并且流即将关闭，则该标志被设置为`TRUE`

另外两个可选的方法，`onCreate()`和`onClose()`，分别在我们的类被创建和销毁时被调用。如果我们的过滤器需要实例化资源，比如必须在转换结束时释放的其他流或数据缓冲区，那么它们就很有用。

我们的过滤器使用这些方法来处理由私有属性`$bufferHandle`管理的临时数据流。如果缓冲流不可用，`onCreate()`方法将无法返回`false`，而`onClose()`方法关闭资源。我们的`MarkdownFilter`用的是[米歇尔·福廷的解析器](https://github.com/michelf/php-markdown)。

```
<?php
namespace MarkdownFilter;

use \Michelf\MarkdownExtra as MarkdownExtra;

class MarkdownFilter extends \php_user_filter
{

    private $bufferHandle = '';

    public function filter($in, $out, &$consumed, $closing)
    {
        $data = '';

        while ($bucket = stream_bucket_make_writeable($in)) {
            $data .= $bucket->data;
            $consumed += $bucket->datalen;
        }

        $buck = stream_bucket_new($this->bufferHandle, '');

        if (false === $buck) {
            return PSFS_ERR_FATAL;
        }

        $parser = new MarkdownExtra;
        $html = $parser->transform($data);
        $buck->data = $html;

        stream_bucket_append($out, $buck);
        return PSFS_PASS_ON;
    }

    public function onCreate()
    {
        $this->bufferHandle = @fopen('php://temp', 'w+');
        if (false !== $this->bufferHandle) {
            return true;
        }
        return false;
    }

    public function onClose()
    {
        @fclose($this->bufferHandle);
    }
}
```

在主`filter()`方法中，我将所有内容收集到一个`$data`变量中，稍后将对其进行转换。第一个循环遍历输入流，使用`stream_bucket_make_writeable()`来检索当前的数据桶。每个桶的内容(`$bucket->data`)被添加到我们的容器中，并且`$consumed`参数按照检索数据的长度递增(`$bucket->datalen`)。

当收集完所有数据后，我们需要创建一个新的空桶，用于将转换后的内容传递给输出流。我们使用`stream_bucket_new()`来完成这项工作，如果操作失败，我们将返回常量`PSFS_ERR_FATAL`来触发过滤器错误。因为我们需要一个资源指针来创建一个桶，所以我们使用了`$bufferHandle`属性，这个属性已经在前面使用`php://temp`内置的流包装器进行了初始化。

现在我们有了数据和输出桶，我们可以实例化一个 Markdown 解析器，转换所有数据并将其存储在桶的`data`属性中。最后，使用`stream_bucket_append()`将结果附加到`$out`资源指针，并且函数返回常量`PSFS_PASS_ON`来通知数据被成功处理。

我们现在可以这样使用过滤器:

```
 // Require the MarkdownFilter or autoload

    // Register the filter
    stream_filter_register("markdown", "\MarkdownFilter\MarkdownFilter")
        or die("Failed to register filter Markdown");

    // Apply the filter
    $content = file_get_contents(
        'php://filter/read=markdown/resource=file:///path/to/somefile.md'
    );

    // Check for success...
    if (false === $content) {
        echo "Unable to read from source\n";
        exit(1);
    }

    // ...and enjoy the results
    echo $content, "\n";
```

请注意，`use`指令不起作用，在注册自定义过滤器时必须提供完全限定的类名。

## 写时过滤数据:模板过滤器

一旦我们将内容从 Markdown 转换成 HTML，我们需要将它打包到一个页面模板中。这可以是从基本的 HTML 结构到复杂的 CSS 样式的页面布局。因此，与我们使用输入过滤器执行读取和转换操作的方式相同，我们将编写一个转换和保存操作，将我们选择的模板引擎嵌入到输出流中。在本教程中，我选择了 [RainTPL 解析器](https://github.com/rainphp/raintpl3)，但是您可以根据自己的喜好自由修改代码。

模板过滤器的结构类似于我们的输入过滤器。首先，我们将以这种方式注册过滤器:

```
stream_filter_register("template.*", "\TemplateFilter\TemplateFilter")
    or die("Failed to register filter Template");
```

我们使用格式`filtername.*`作为过滤器标签，这样我们就可以使用那个`*`向我们的类传递一些数据。这是必要的，因为据我所知，使用`php://filter`包装器无法将参数传递给应用的过滤器。如果你知道一种方法，请在下面的评论中发表。

然后以这种方式应用过滤器:

```
 $result = file_put_contents(
        'php://filter/write=template.' .
        base64_encode('Some Document Title') . '/resource=file:///path/to/destination.html',
        $content
    );
```

文档的标题使用过滤器名称的第二部分传递，并将由`onCreate()`方法处理。更进一步，我们可以使用这个技巧为模板引擎传递带有定制配置设置的序列化数据数组。

`TemplateFilter`类:

```
<?php
namespace TemplateFilter;

use \Rain\Tpl as View;

class TemplateFilter extends \php_user_filter
{

    private $bufferHandle = '';
    private $docTitle = 'Untitled';

    public function filter($in, $out, &$consumed, $closing)
    {
        $data = '';

        while ($bucket = stream_bucket_make_writeable($in)) {
            $data .= $bucket->data;
            $consumed += $bucket->datalen;
        }

        $buck = stream_bucket_new($this->bufferHandle, '');

        if (false === $buck) {
            return PSFS_ERR_FATAL;
        }

        $config = array(
            "tpl_dir"       => dirname(__FILE__) . "/templates/",
            "cache_dir"     => sys_get_temp_dir() . "/",
            "auto_escape"   => false
        );
        View::configure($config);

        $view = new View();
        if (!$closing) {
            $matches = array();
            if (preg_match('/<h1>(.*)<\/h1>/i', $data, $matches)) {

                if (!empty($matches[1])) {
                    $this->docTitle = $matches[1];
                }
            }
            $view->assign('title', $this->docTitle);
            $view->assign('body', $data);
            $content = $view->draw('default', true);
            $buck->data = $content;
        }

        stream_bucket_append($out, $buck);
        return PSFS_PASS_ON;
    }

    public function onCreate()
    {
        $this->bufferHandle = @fopen('php://temp', 'w+');
        if (false !== $this->bufferHandle) {

            $info = explode('.', $this->filtername);
            if (is_array($info) && !empty($info[1])) {
                $this->docTitle = base64_decode($info[1]);
            }

            return true;
        }
        return false;
    }

    public function onClose()
    {
        @fclose($this->bufferHandle);
    }
}
```

我们仍然有指向临时流的`$bufferHandle`参数，我们还有一个名为`$docTitle`的参数，它将包含(按优先级):

1.  已解析文档的第一个`H1`标签(如果存在)的内容，或者
2.  过滤器名称第二部分的解码内容，或
3.  默认回退值“无标题”。

在`onCreate()`方法中，在缓冲流初始化之后，我们正在处理第二个选项:

```
 $info = explode('.', $this->filtername);
    if (is_array($info) && !empty($info[1])) {
        $this->docTitle = base64_decode($info[1]);
    }
```

这里主要的`filter()`方法可以分为五步。前两步与 Markdown 过滤器相同:所有数据都从输入桶中取出并存储在变量`$data`中，然后创建一个空的输出桶来存储已处理的内容。

在第三步中，加载并配置模板解析器类。我向系统请求一个临时目录用于缓存，禁用 HTML 标签转义特性并设置`templates`目录。

这里使用的默认模板非常简单，变量定义为`{$VarName}`:

```
 <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="utf-8">
            <title>{$title}</title>
        </head>
        <body>
            {$body}
        </body>
    </html>
```

第四步是实际解析发生的地方。首先，我在一个`H1`标签中搜索一个文档标题。然后我设置模板中定义的`body`和`title`变量，最后处理文档。`draw()`方法的第一个参数是模板名，第二个参数告诉返回字符串而不是打印它。

最后一步是将解析后的内容放入输出桶，并将其附加到输出资源，返回`PSFS_PASS_ON`。

## 将所有这些放在一起:一个文档解析器

现在我们已经有了基本的模块，是时候构建我们的文档解析器实用程序了。实用程序位于自己的目录`mddoc`中。我们的定制过滤器位于使用`PSR-0`目录和名称空间结构的`lib`目录下。我已经使用[作曲家](http://getcomposer.org)来跟踪依赖关系

```
 "require": {
        "php": ">=5.3.0",
        "michelf/php-markdown": "*",
        "rain/raintpl": "3.*"
    },
```

和自动加载:

```
 "autoload": {
        "psr-0": { 
            "MarkdownFilter": "lib",
            "TemplateFilter": "lib"
        }
    }
```

主应用文件是`mddoc`，可以这样执行:

```
 $ /path/to/mddoc -i /path/to/sourcedir -o /path/to/destdir
```

应用程序文件如下所示:

```
#!/usr/bin/env php
<?php
/**
 * Markdown Tree Converter
 * 
 * Recursive converts all markdown files from a source directory to HTML
 * and places them in the destination directory recreating the structure
 * of the source and applying a template parser.
 */

// Composer autoloader
require_once dirname(__FILE__) . '/vendor/autoload.php';

// Deals with command-line input arguments
function usage()
{
    printf(
        "Usage: %s -i %s -o %s\n",
        basename(__FILE__),
        '/path/to/sourcedir',
        '/path/to/destdir'
    );
}

if (5 > $argc) {
    usage();
    exit;
}

$in = array_search('-i', $argv);
$src = realpath($argv[$in+1]);
if (!is_dir($src) || !is_readable($src)) {
    echo "[ERROR] Invalild source directory.\n";
    usage();
    exit(1);
}

$out = array_search('-o', $argv);
$dest = realpath($argv[$out+1]);
if (!is_dir($dest) || !is_writeable($dest)) {
    echo "[ERROR] Invalild destination directory.\n";
    usage();
    exit(1);
}

// Register custom read-time MarkdownFilter
stream_filter_register("markdown", "\MarkdownFilter\MarkdownFilter")
    or die("Failed to register filter Markdown");

// Register custom write-time TemplateFilter
stream_filter_register("template.*", "\TemplateFilter\TemplateFilter")
    or die("Failed to register filter Template");

// Load directory iterator for source
$it = new RecursiveIteratorIterator(
    new RecursiveDirectoryIterator($src),
    RecursiveIteratorIterator::SELF_FIRST
);

// For every valid item
while ($it->valid()) {

    // Exclude dot items (., ..)
    if (!$it->isDot()) {

        // If current item is a directory, the same empty directory
        // is created on destination
        if ($it->isDir()) {
            $path = $dest . '/' . $it->getFileName();
            if ((!@is_dir($path)) && !@mkdir($path, 0777, true)) {
                echo "Unable to create folder {$path}\n";
                exit(1);
            }
        }

        // If current item is a markdown (*.md) file it's processed and
        // saved at the coresponding destination path
        if ($it->isFile() && 'md' == $it->getExtension()) {
            $path = $it->key();
            if (!is_readable($path)) {
                echo "Unable to read file {$path}\n";
                exit(2);
            }

            $content = file_get_contents(
                'php://filter/read=markdown/resource=file://' . $path
            );
            if (false === $content) {
                echo "Unable to read from source '" . $path . "'\n";
                exit(3);
            }

            $pathinfo = pathinfo($dest . '/' . $it->getSubPathName());
            $target = $pathinfo['dirname']
                . '/' . $pathinfo['filename'] . '.html';

            $result = file_put_contents(
                'php://filter/write=template.' .
                base64_encode(basename($path)) . '/resource=file://' . $target,
                $content
            );
            if (false === $result) {
                echo "Unable to write file '" . $target . "'\n";
                exit(4);
            }

        }
    }

    $it->next();
}

exit(0);
```

首先，我们包括我们的自动加载器，然后我们继续进行参数验证:

*   命令行必须与上面的示例相匹配，
*   源目录必须存在并且可读，
*   目标目录必须存在且可写。

然后我们注册自定义过滤器(记住，使用完整的类路径)并实例化一个`RecursiveIteratorIterator`对象来递归遍历源目录。主循环遍历迭代器获取的所有有效元素。除了点文件之外，所有元素的处理如下:

*   如果当前元素是一个目录，尝试从目标路径开始用相同的名称重新创建相对路径。
*   如果当前元素是降价文件(*)。md)使用`markdown`读取过滤器将文件的内容读入一个变量，然后从带有模板的目标目录开始，在相同的相对路径上写入一个带有`.html`扩展名的新文件。*`应用过滤器。

结果是您的文档目录结构通过一个命令完全转换成 HTML。还不错。

## 摘要

我们在这里讨论了很多有用的内容，我们也有一个全功能的实用程序来… ehm，添加到我们的工具链中。我将把它留给您，让您进一步开发并创建类似这样其他工具和组件来增强我们的项目。编码快乐！

## 分享这篇文章