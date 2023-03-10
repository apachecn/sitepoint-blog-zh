# 如何用 PHP 和闭包编译器运行自己的 JavaScript 压缩器

> 原文：<https://www.sitepoint.com/compress-javascript-with-php/>

在我之前的文章中，我讨论了[闭包编译器的 REST API](https://www.sitepoint.com/compress-javascript-closure-compiler-rest-api/) 。在本文中，我们将开发一个小的 PHP 程序，展示如何在需要时使用 API 压缩 JavaScript 代码。

## 为什么要写自己的系统？

您会发现有几个免费的工具可以处理这项任务；第一批 PHP JavaScript 压缩器之一是由 Ed Eliot 编写的。但是，有时它们需要您不使用的技术，如 Java，或者可能不适合内部工作流程，包括:

*   将未压缩的 JavaScript 分发给开发人员的电脑
*   与源代码控制系统集成
*   自动化构建，等等

**Important:** This is not production-level code!

下面的代码用 PHP 实现了一个基本的 JavaScript 压缩器来说明基本原理。每次发出请求时，代码都会调用闭包编译器压缩 API，因此它可能比多个未压缩的 JavaScript 文件要慢。您应该添加自己的函数来处理文件或数据库的缓存。

## HTML

假设您通常在 HTML 底部包含以下`script`标签:

```
<script src="script/file1.js"></script><script src="script/file2.js"></script><script src="script/file3.js"></script>
```

我们将用引用所有三个文件的单个`script`标记替换这个块。
`.js`被删除，文件名用&符号分开:

```
<script src="script/?file1&file2&file3"></script>
```

## PHP

我们现在需要在我们的`script`文件夹中有一个`index.php` 文件。第一个代码块将`GET`参数转换成一个数组(文件 1，文件 2，文件 3)并初始化变量:

```
<?php// fetch JavaScript files to compress$jsfiles = array_keys($_GET);$js = '';		// code to compress$jscomp = '';	        // compressed JS$err = '';	        // error string
```

我们现在遍历 JS 文件，读取内容，并将其附加到$js 字符串中。如果无法找到或读取某个文件，其名称会附加到$err 字符串中:

```
// fetch JavaScript filesfor ($i = 0, $j = count($jsfiles); $i < $j; $i++) {  $fn = $jsfiles[$i] . '.js';  $jscode = @file_get_contents($fn);  if ($jscode !== false) {    $js .= $jscode . "n";  }  else {    $err .= $fn . '; ';  }}
```

如果有任何文件丢失，我们可以生成一个 JavaScript 警告来通知开发人员:

```
if ($err != '') {  // error: missing files  $jscomp = "alert('The following JavaScript files could not be read:\n$err');";}
```

如果没有错误，并且我们有一些 JavaScript 代码，我们可以继续压缩。`$apiArgs`数组包含一列[闭包编译器 API 选项](http://code.google.com/closure/compiler/docs/api-ref.html)——您可以根据需要添加、删除或修改这些选项。参数被编码并附加到`$args`字符串中:

```
else if ($js != '') {  // REST API arguments  $apiArgs = array(    'compilation_level'=>'ADVANCED_OPTIMIZATIONS',    'output_format' => 'text',    'output_info' => 'compiled_code'   );   $args = 'js_code=' . urlencode($js);   foreach ($apiArgs as $key => $value) {     $args .= '&' . $key .'='. urlencode($value);   }
```

我们现在可以使用 PHP 的 cURL 库调用闭包编译器 API。压缩后的 JavaScript 返回到`$jscomp`字符串:

```
 // API call using cURL  $call = curl_init();  curl_setopt_array($call, array(    CURLOPT_URL => 'http://closure-compiler.appspot.com/compile',    CURLOPT_POST => 1,    CURLOPT_POSTFIELDS => $args,    CURLOPT_RETURNTRANSFER => 1,    CURLOPT_HEADER => 0,    CURLOPT_FOLLOWLOCATION => 0  ));  $jscomp = curl_exec($call);  curl_close($call);
```

最后，我们用适当的 MIME 类型将压缩代码返回给浏览器:

```
}// output content header('Content-type: text/javascript'); echo $jscomp; ?> 
```

## 下载代码

省去自己一些打字和 [**下载**](https://blogs.sitepointstatic.com/examples/tech/jscompress/php-js-compress.zip) 的例子代码。它包括一个缩小到原来三分之一大小的小 JavaScript 库，并且 HTTP 请求更少。

## 交给你了…

现在，您可以修改这个基本代码来实现如下特性:

*   错误处理——您的代码应该检查闭包编译器报告的 API 调用失败或压缩问题。
*   缓存——一旦你有了压缩的代码，你就可以把它保存到一个文件中，所以没有必要重复调用 API。您可以通过比较创建日期来检查 JavaScript 文件自上次缓存以来是否发生了更改。
*   浏览器缓存—可以设置 HTTP 到期头，以便浏览器无限期缓存压缩的 JavaScript 文件。您可以向`script`标签 URL 添加一个“last-updated”参数，以确保总是加载更新的代码。
*   JavaScript 代码报告——闭包编译器 API 可以用来突出显示浏览器解析器没有报告的问题；例如，未使用的变量或数组中最后一项后的逗号。
*   构建过程——您的系统可以将未压缩的 JavaScript 代码分发给开发人员，将压缩的代码分发给其他人。

希望你觉得有用。您会使用闭包编译器 API 来自动化您的 JavaScript 压缩过程吗？请通过下面的博客条目链接进行回复。

## 分享这篇文章