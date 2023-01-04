# 5 个鼓舞人心(并且有用)的 PHP 代码片段

> 原文：<https://www.sitepoint.com/5-inspiring-and-useful-php-snippets/>

“X PHP 片段”类型的文章在互联网上比比皆是，为什么还要再写一篇呢？好吧，让我们面对现实吧…它们里面的 PHP 代码片段一般都很蹩脚。为客户端的 IP 地址生成随机字符串或返回`$_SERVER["REMOTE_ADDR"]`的代码片段真的没什么意思，用处也不大。相反，这里有五个片段，你肯定会觉得有趣和有用，并与激发它们的问题一起呈现。我希望他们中的许多人的创造力能激发你在日常工作中写出更好、更有创造力的代码。

## 1.生成 CSV

我们经常看到试图将多维数据数组转换为 CSV 格式的代码，如下所示:

```
<?php
$csv = "";
foreach ($data as $row) {
    $csv .= join(",", $row) . "n";
}
echo $csv;
```

问题是单个元素没有正确转义，带有引号或逗号的单个值可能会导致解析器稍后尝试使用 CSV 数据。用内置的`fputcsv()`函数就好多了；它应该执行得更快，因为它是用 C 代码实现的，并且它为您处理必要的引用/转义。

以下代码包装逻辑以从数据数组构造 CSV 输出。它有可选参数，允许标题列，以及是否将 CSV 直接刷新到浏览器或以字符串形式返回输出。这里的优雅之处在于使用带有`fputcsv()`的流，因为该函数需要一个打开的文件句柄来操作。

```
<?php  
function toCSV(array $data, array $colHeaders = array(), $asString = false) {  
	$stream = ($asString)
	    ? fopen("php://temp/maxmemory", "w+")
	    : fopen("php://output", "w");  

	if (!empty($colHeaders)) {
		fputcsv($stream, $colHeaders);  
	}

	foreach ($data as $record) {
		fputcsv($stream, $record);
	}

	if ($asString) {
		rewind($stream);  
		$returnVal = stream_get_contents($stream);  
		fclose($stream);  
		return $returnVal;  
	}
	else {
		fclose($stream);  
	}  
}
```

有了您的武器库中的`toCSV()`函数，生成 CSV 就变得简单而容易了。

## 2.自动加载类

自动加载类文件是常见的，但是也许你不喜欢各种 PHP 框架提供的一些臃肿、笨重的自动加载器，或者也许你只是喜欢推出自己的解决方案。幸运的是，有可能推出你自己的最小化加载器，同时仍然符合由 [PHP 标准工作组](https://groups.google.com/forum/#!forum/php-standards "PHP Standards Working Group - Google Groups")采用的 [PSR-0 标准](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md "fig-standards/accepted/PSR-0.md at master - php-fig/fig-standards")，我在自己的博客上首次展示了[。](http://zaemis.blogspot.com/2012/05/writing-minimal-psr-0-autoloader.html "Zaemis: Writing a Minimal PSR-0 Autoloader")

该标准没有描述兼容 PSR-0 的自动装弹机必须提供什么支持功能(注册方法、配置选项等)。).如果它能在`<Vendor Name>(<Namespace>)`模式中自动找到一个类定义，那么它就是 PSR-0 兼容的。此外，它没有指定`<Vendor Name>`的父目录。如果您需要通过代码指定位置，大多数 autoloader 实现的额外“繁琐”是很方便的，但是如果您只是使用 PHP 包含路径中已经存在的目录，就没有必要了。

```
<?php
spl_autoload_register(function ($classname) {
    $classname = ltrim($classname, "\");
    preg_match('/^(.+)?([^\\]+)$/U', $classname, $match);
    $classname = str_replace("\", "/", $match[1])
        . str_replace(["\", "_"], "/", $match[2])
        . ".php";
    include_once $classname;
});
```

这里的神奇之处在于正则表达式，它将传入的名称拆分成其组成部分；类名总是在`$match[2]`中，而`$match[1]`是名称空间名，可能是也可能不是空字符串。有必要识别这些部分，因为下划线在名称空间部分没有特殊含义，这使得对下划线和反斜杠的盲替换不正确。

## 3.使用 unpack()解析固定宽度的数据

在当今充斥着 XML 和 JSON 的现代世界中，您可能认为固定宽度格式已经灭绝了……但是您错了。还有大量的定宽数据，比如一些日志条目、 [MARC 21](http://www.loc.gov/marc/bibliographic/ "MARC 21 Format for Bibliographic Data: Table of Contents") (书目信息)、(财务信息)等。而且在你我之间，我还是对固定宽度的数据情有独钟。

在 C 之类的语言中，固定宽度的数据相对容易处理，因为加载到内存中的数据与访问的数据结构完全一致。但是对于一些人来说，在像 PHP 这样的动态语言中处理固定数据可能是一场斗争；这种语言的松散类型使得这种内存访问成为不可能。因此，我们经常会看到类似这样的代码:

```
<?php
// Parse a NACHA header record
$row = fread($fp, 94);
$header = array();
$header["type"] = substr($row, 0, 1);
$header["priority"] = substr($row, 1, 2);
$header["immDest"] = substr($row, 3, 10);
$header["immOrigin"] = substr($row, 13, 10);
$header["date"] = substr($row, 23, 6);
$header["time"] = substr($row, 29, 4);
$header["sequence"] = substr($row, 33, 1);
$header["size"] = substr($row, 34, 3);
$header["blockFactor"] = substr($row, 37, 2);
$header["format"] = substr($row, 39, 1);
$header["destName"] = substr($row, 40, 23);
$header["originName"] = substr($row, 63, 23);
$header["reference"] = substr($row, 86, 8);
print_r($header);
```

你可能在畏缩。没关系，我也不希望这样的代码出现在我的应用程序中！它很冗长，索引也容易出错。幸运的是，有一个更好的选择:`unpack()`。

PHP 手册中的`unpack()` 的[文档说:“根据给定的格式将二进制字符串解包到一个数组中”，并展示了使用二进制数据转义的用法示例。可能不会立即显而易见的是，由于格式说明符“A”代表一个字符，该函数可用于解析固定宽度的字符串(毕竟，字符串不只是一系列位和字节吗？).](http://www.php.net/unpack "PHP: unpack - Manual")

使用`unpack()`，上面的例子可以更优雅地改写如下:

```
<?php
// Parse a NACHA header record
$row = fread($fp, 94);
$header = unpack("A1type/A2priority/A10immDest/A10immOrigin/"
    . "A6date/A4time/A1sequence/A3size/A2blockFactor/A1format/"
    . "A23destName/A23originName/A8reference", $row);
print_r($header);
```

在这种情况下，格式字符串只是一系列指定字符数据的、特定字段的字符数以及检索到的数据将在最终数组中分配的键名，用斜杠分隔。例如，`A6date`解析出 6 个字符，并使它们作为`$header["date"]`可用。

## 4.模板化 HTML

PHP 社区对模板化从来没有达成一致意见。我们都同意将 HTML 和 PHP 分开是可取的，但是在使用模板库的合适性上发生了冲突，例如 [Smarty](http://www.smarty.net/ "PHP Template Engine | Smarty") 或 [Twig](http://twig.sensiolabs.org/ "Twig - the flexible, fast, and secure PHP templating engine") 。一些人指出 PHP 本身是一个模板引擎，并反对库的速度、语法等。其他人声称使用模板系统提供的 DSL 受益匪浅。一个折中的办法是使用一个用 PHP 编写的非常简单的类来模板化你的 HTML 以保持整洁。

```
<?php
class Template
{
    protected $dir;
    protected $vars;

    public function __construct($dir = "") {
        $this->dir = (substr($dir, -1) == "/") ? $dir : $dir . "/";
        $this->vars = array();
    }

    public function __set($var, $value) {
        $this->vars[$var] = $value;
    }

    public function __get($var) {
        return $this->vars[$var];
    }

    public function __isset($var) {
        return isset($this->vars[$var]);
    }

    public function set() {
        $args = func_get_args();
        if (func_num_args() == 2) {
            $this->__set($args[0], $args[1]);
        }
        else {
            foreach ($args[0] as $var => $value) {
                $this->__set($var, $value);
            }
        }
    }

    public function out($template, $asString = false) {
        ob_start();
        require $this->dir . $template . ".php";
        $content = ob_get_clean();

        if ($asString) {
            return $content;
        }
        else {
            echo $content;
        }
    }
}
```

它不是一个成熟的模板引擎；而是一个简洁的帮助器类，充当“桶”来收集键/值数据对，您可以在指定为模板的包含文件中访问这些数据对。首先在视图中创建一个`Template`类的实例，可选地传递一个用于查找后续模板文件的目录名(允许您对相关文件进行分组)。然后，将应该填充模板的值提供给`set()`方法或作为一个空属性。一旦指定了所有的值，就调用`out()`方法来呈现模板。

```
<?php
$t = new Template();
// setting a value as if it were a property
$t->greeting = "Hello World!";
// setting a value with set()
$t->set("number", 42);
// setting multiple values with set()
$t->set(array(
    "foo" => "zip",
    "bar" => "zap"
));
// render template
$t->out("mytemplate");
```

示例中的`mytemplate.php`文件可能如下所示:

```
<!DOCTYPE html>
<html lang="en">
 <head>
  <meta charset="utf-8">
...
 </head>
 <body>
  <div role="main">
   <h1><?=$this->greeting;?></h1>
...
  </div>
 </body>
</html>
```

在模板文件中，您可以使用 PHP 的全部功能来格式化值、过滤值等。你看着办吧。

`out()`的第二个可选参数可以指定将模板内容作为字符串返回，而不是将其直接刷新到浏览器，您可以利用这个参数将一个模板中的占位符替换为之前填充的模板的结果。

## 5.使用 file_get_contents 作为 cURL 替代

cURL 是用于通过各种协议进行通信的健壮库。它的功能当然非常全面，而且有时候其他功能都做不到。如果您明确需要 cURL 提供的功能来完成您的任务，那么就使用 cURL 吧！但是 PHP 中大多数日常 cURL 的使用都围绕着发出 HTTP GET 和 POST 请求，这可以通过内置的 PHP 函数轻松完成。

依赖 cURL 发出 HTTP 请求的问题有两个方面:1)经常有许多选项需要设置，甚至对于最简单的事务也是如此；2)根据您的主机和安装情况，它是一个可能可用也可能不可用的扩展；这是一个常见的扩展，但默认情况下是不启用的。

`file_get_contents()`和`stream_context_create()`是两个 PHP 原生函数，从 4.3 天开始就可以使用了。它们一起可以用来执行许多通常通过 cURL 完成的相同类型的请求。

对于基本的 GET 请求，`file_get_contents()`可以单独使用:

```
<?php
$html = file_get_contents("http://example.com/product/42");
```

对于需要指定 HTTP 头的请求，无论是 GET 还是任何其他 HTTP 方法，您都可以通过向`stream_context_create()`传递一个特殊的数组来创建一个上下文，然后将该上下文传递给`file_get_contents()`。

```
<?php
$context = stream_context_create(array(
    "http" => array(
        "method" => "POST",
        "header" => "Content-Type: multipart/form-data; boundary=--foorn",
        "content" => "--foorn"
            . "Content-Disposition: form-data; name="myFile"; filename="image.jpg"rn"
            . "Content-Type: image/jpegrnrn"
            . file_get_contents("image.jpg") . "rn"
            . "--foo--"
    )
));

$html = file_get_contents("http://example.com/upload.php", false, $context);
```

上面的例子显示了通过 POST 上传文件，上下文数组使用关键字“method”、“header”和“content”指定事务的必要信息。

当使用`file_get_contents()`处理复杂的请求(比如文件上传)时，首先创建一个模拟的 web 表单，并在启用了 firebug 的 Firefox 或类似的平台上运行它，然后检查请求中包含了什么，这会很有帮助。从这里您可以推断出要包含的重要标题元素。

## 摘要

希望您已经发现本文中的片段很有趣。他们展示了创造性的问题解决和使用 PHP 的内置功能的新效果。我希望它们对你有用，并能给你启发。如果你有自己鼓舞人心的片段，欢迎在下面的评论中分享。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章