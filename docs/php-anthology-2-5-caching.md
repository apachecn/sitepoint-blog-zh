# PHP 选集第 2 卷，第 5 章——缓存

> 原文：<https://www.sitepoint.com/php-anthology-2-5-caching/>

在过去的好时光里，当建立网站就像敲出几个 HTML 页面一样简单的时候，将网页发送到浏览器只是让网络服务器获取一个文件的简单事情。除非使用特别慢的调制解调器，否则一个网站的访问者几乎可以立即看到它的小的纯文本页面。页面下载后，浏览器会将其缓存在本地计算机上的某个地方，这样，如果再次请求该页面，在与服务器进行快速检查以确保页面没有更新后，浏览器可以显示本地缓存的版本。页面被尽可能快速有效地送达，每个人都很高兴(除了那些使用 9600 bps 调制解调器的人)。

动态网页的出现破坏了聚会，通过引入两个问题有效地“打破”了这种提供页面的模型:

*   当服务器收到对动态网页的请求时，必须完成一些中间处理，比如 PHP 引擎对脚本的解析。这在 Web 服务器开始将输出传递给浏览器之前引入了一个延迟。对于简单的 PHP 脚本来说，这可能并不重要，但是对于更复杂的应用程序来说，在页面最终交付之前，PHP 引擎可能还有很多工作要做。这种额外的工作导致用户的请求和页面在浏览器中的实际显示之间存在明显的滞后。
*   典型的 Web 服务器(如 Apache)使用文件修改时间来正确地通知 Web 浏览器所请求页面的缓存状态。对于动态 Web 页面，实际的 PHP 脚本可能只是偶尔更改，而它显示的内容(可能是从数据库中获取的)将会频繁更改。然而，Web 服务器无法知道数据库的更新，所以它不发送最后修改日期。如果客户机(浏览器)不知道数据的有效期有多长，它将进行猜测，这通常意味着它将再次请求相同的页面。不管数据是否已经改变，Web 服务器总是用页面的新版本来响应。为了避免这个缺点，大多数 Web 开发人员使用 meta 标记或 HTTP 头来告诉浏览器不要使用页面的缓存版本。然而，这否定了 Web 浏览器缓存网页的自然能力，并且涉及一些严重的缺点。例如，动态页面提供的内容可能一天改变一次，所以让浏览器缓存页面肯定有好处——即使只有 24 小时。

对于一个小的 PHP 应用程序来说，这两个问题通常都可以解决，但是随着站点的复杂性和流量的增加，你可能会遇到困难。然而，这两个问题都可以解决，第一个是服务器端缓存，第二个是从应用程序内部控制客户端缓存。你用来解决这个问题的确切方法将取决于你的应用程序，但是在这一章中，我们将看到你如何使用 PHP 和 PEAR 的一些类库来解决这个问题。

注意，在本章关于缓存的讨论中，我们将只看那些用 PHP 实现的解决方案。这些不应该与一些基于优化和缓存编译的 PHP 脚本的脚本缓存解决方案相混淆。这一组中包括 [Zend 加速器](http://www.zend.com/store/products/zend-performance-suite.php)、 [iconCube PHP 加速器](http://www.phpaccelerator.co.uk/)和 [Turck MMCache](http://www.turcksoft.com/en/e_mmc.htm) ，后者是目前唯一准备好用于基于 Windows 的 PHP 安装的加速器。

##### 如何防止网页浏览器缓存网页？

在我们研究客户端和服务器端缓存的方法之前，我们首先需要了解的是如何防止 Web 浏览器(和代理服务器)缓存页面。最基本的方法是利用 HTML `meta`标签:

```
<meta http-equiv="Expires" content="Mon, 26 Jul 1997 05:00:00 GMT" 

/> 

<meta http-equiv="Pragma" content="no-cache" />
```

通过在 Expires meta 标记中插入一个过去的日期，我们可以告诉浏览器页面的缓存副本总是过期的。这意味着浏览器不应该缓存页面。Pragma: no-cache meta 标记是一个相当受支持的约定，大多数 Web 浏览器都遵循这个约定。一旦遇到这个标签，他们通常不会缓存页面(虽然不能保证；这只是一个约定)。

这听起来不错，但是有两个问题与元标签的使用有关:

1.  If a tag wasn’t present when the page was first requested by a browser, but appears later (for example, you modified the included pageheader.php file, which contains the top of every Web page), the browser will remain blissfully ignorant and keep its cached copy of the original.
2.  缓存网页的代理服务器，如 ISP 常用的代理服务器，通常不会检查 HTML 文档本身。相反，它们完全依赖于文档来自的 Web 服务器和 HTTP 协议。换句话说，Web 浏览器可能知道它不应该缓存页面，但浏览器和您的 Web 服务器之间的代理服务器可能不知道，它将继续向客户端提供相同的过期页面。

更好的方法是使用 HTTP 协议本身，在 PHP 的`header`函数的帮助下，产生上面两个`meta`标记的等价物:

```
<?php 

header('Expires: Mon, 26 Jul 1997 05:00:00 GMT'); 

header('Pragma: no-cache'); 

?>
```

我们可以更进一步，使用支持 HTTP 1.1 的浏览器支持的缓存控制头:

```
<?php 

header('Expires: Mon, 26 Jul 1997 05:00:00 GMT'); 

header('Cache-Control: no-store, no-cache, must-revalidate'); 

header('Cache-Control: post-check=0, pre-check=0', FALSE); 

header('Pragma: no-cache'); 

?>
```

这基本上保证了没有 Web 浏览器或代理服务器会缓存页面，因此访问者将总是收到最新的内容。事实上，第一个头应该自己完成这个任务；这是确保页面不被缓存的最佳方式。出于“保险”目的，添加了 Cache-Control 和 Pragma 头。虽然它们不能在所有的浏览器或代理上工作，但是它们会捕捉到一些 Expires 头不能正常工作的情况(例如，如果客户端计算机的日期设置不正确)。

当然，完全禁止缓存会引入我们在本章开始时讨论的问题。我们一会儿就来看看这些问题的解决方案。

***浏览器和文件下载缓存***

我们在《第 3 章，替代内容类型》中对 PDF 呈现的讨论解释了在处理缓存和文件下载时可能会出现的问题。在通过使用诸如`Content-Disposition: attachment, filename=myFile.pdf`或`Content-Disposition: inline, filename=myFile.pdf`这样的头的 PHP 脚本提供文件下载时，如果您告诉浏览器不要缓存页面，那么 Internet Explorer 就会出现问题。

Internet Explorer 以一种相当不寻常的方式处理下载，向网站发出两个请求。第一个请求下载文件，并在发出第二个请求之前将其存储在缓存中(不存储响应)。该请求根据文件类型调用将文件交付给最终用户的过程(例如，如果文件是 PDF 文档，则启动 Acrobat Reader)。这意味着，如果您发送指示浏览器不缓存页面的缓存头，Internet Explorer 将在第一次和第二次请求之间删除该文件，结果最终用户什么也得不到。如果通过 PHP 脚本提供的文件不会改变，一个解决方案就是禁用下载脚本的“不缓存”头。

如果文件下载会定期改变(例如，你希望浏览器下载一个最新的版本)，你需要使用 last-modified 头，这将在本章后面讨论，并确保修改时间在两个连续的请求中保持不变。您应该能够做到这一点，而不会影响正确处理下载的浏览器的用户。最后一个解决方案是将文件写入您的 Web 服务器，并简单地提供一个到它的链接，让 Web 服务器为您报告缓存头。当然，如果文件应该由 PHP 脚本保护，这可能不是一个可行的选择，PHP 脚本需要一个有效的会话来为用户提供对文件的访问；使用这种解决方案，可以直接下载编写的文件。

##### 如何捕获服务器端输出进行缓存？

是时候看看如何通过缓存输出来减少服务器端的延迟了。一般的方法是从正常呈现页面开始，用 PHP 执行数据库查询等等。然而，在将它发送到浏览器之前，我们捕获并在某个地方存储完成的页面，例如，在一个文件中。下次请求页面时，PHP 脚本首先检查页面的缓存版本是否存在。如果是这样，脚本会将缓存的版本直接发送给浏览器，从而避免了重新构建页面所带来的延迟。

*模板缓存呢？*

*模板引擎如 [Smarty](http://smarty.php.net/) 经常谈论模板缓存。通常，这些引擎提供了一种内置的机制来存储模板的编译版本(即从模板生成的原生 PHP)，这使得我们不必在每次请求页面时都重新编译模板。这不应该与输出缓存混淆，输出缓存是指 PHP 发送给浏览器的渲染 HTML(或其他输出)的缓存。您可以在同一个站点上成功地同时使用这两种类型的缓存。*

在这里，我们将看看 PHP 的内置缓存机制，即输出缓冲区，它可以用于您喜欢的任何页面呈现系统(有模板或没有模板)。假设您的脚本使用`echo`或`print`显示结果，而不是将数据直接发送到浏览器。在这些情况下，您可以使用 PHP 的输出控制函数将数据存储在内存缓冲区中，您的 PHP 脚本可以访问和控制该缓冲区。

这里有一个简单的例子:

```
Example 5.1\. 1.php  

<?php  

// Start buffering the output  

ob_start();  

// Echo some text (which is stored in the buffer);  

echo '1\. Place this in the buffer<br />';  

// Get the contents of  

$buffer = ob_get_contents();  

// Stop buffering and clean out the buffer  

ob_end_clean();  

// Echo some text normally  

echo '2\. A normal echo<br />';  

// Echo the contents from the buffer  

echo $buffer;  

?>
```

缓冲区本身将输出存储为字符串。因此，在上面的脚本中，我们用`ob_start`开始缓冲，并使用 echo 显示一些东西。然后，我们使用`ob_get_contents`获取 echo 语句放在缓冲区中的数据，并将其存储在一个字符串中。`ob_end_clean`函数停止输出缓冲并清空内容；另一个选项是`ob_end_flush`，它显示缓冲区的内容。

上面的脚本显示:

```
2\. A normal echo  

1\. Place this in the buffer
```

换句话说，我们捕获了第一个`echo`的输出，然后在第二个`echo`之后将其发送到浏览器。正如这个简单的例子所表明的，在构建站点时，输出缓冲可能是一个非常强大的工具；它提供了一个缓存的解决方案，正如我们马上会看到的，并且是一个对你的站点的访问者隐藏错误的极好的方法(见第 10 章，错误处理)。它甚至在用户认证等情况下为浏览器重定向提供了一种可能的替代方案。

*HTTP 头和输出缓冲*

*输出缓冲可以帮助解决与标题功能相关的最常见问题，更不用说`session_start`和`set_cookie`了。通常，如果您在页面输出开始后调用这些函数中的任何一个，您都会得到一个令人讨厌的错误消息。打开输出缓冲后，唯一可以逃离缓冲区的输出类型是 HTTP 头。在应用程序执行的最开始使用`ob_start`,你可以在任何你喜欢的点发送消息头，而不会遇到常见的错误。然后，当您确定不再需要 HTTP 头时，可以一次性写出缓冲的页面内容。*

***使用输出缓冲进行服务器端缓存***

现在，您已经看到了输出缓冲的基本示例，下面是下一步，其中缓冲存储为文件:

```
Example 5.2\. 2.php  

<?php  

// If a cached version exists use it...  

if (file_exists('./cache/2.cache')) {  

  // Read and display the file  

  readfile('./cache/2.cache');  

  exit();  

}  

// Start buffering the output  

ob_start();  

// Display some HTML  

?>  

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  

  "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  

<html >  

<head>  

<title> Cached Page </title>  

<meta http-equiv="Content-Type"  

  content="text/html; charset=iso-8859-1" />  

</head>  

<body>  

This page was cached with PHP's  

<a href="http://www.php.net/outcontrol">Output Control  

Functions</a>  

</body>  

</html>  

<?php  

// Get the contents of the buffer  

$buffer = ob_get_contents();  

// Stop buffering and display the buffer  

ob_end_flush();  

// Write a cache file from the contents  

$fp = fopen('./cache/2.cache', 'w');  

fwrite($fp, $buffer);  

fclose($fp);  

?>
```

首先，上面的脚本检查页面的缓存版本是否存在，如果存在，脚本读取并显示它。否则，它使用输出缓冲来创建页面的缓存版本。它将其存储为一个文件，同时使用`ob_end_flush`向访问者显示页面。

文件 2.cache 看起来与脚本呈现的 HTML 完全一样:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  

  "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  

<html >  

<head>  

<title> Cached Page </title>  

<meta http-equiv="Content-Type"  

  content="text/html; charset=iso-8859-1" />  

</head>  

<body>  

This page was cached with PHP's  

<a href="http://www.php.net/outcontrol">Output Control  

Functions</a>  

</body>  

</html>
```

***分块缓冲***

输出缓冲的一种简单方法是缓存整个页面。然而，这种方法放弃了 PHP 的输出控制功能所提供的真正的机会，以一种与内容的不同生命周期相关的方式来提高站点的性能。

毫无疑问，你发送给访问者的页面的某些部分很少改变，比如页面的页眉、菜单和页脚。但是其他部分，如包含论坛讨论的表格，可能会经常更改。输出缓冲可用于在单独的文件中缓存页面的各个部分，然后从这些部分重建页面——这种解决方案消除了重复数据库查询、`while`循环等的需要。您可以考虑为页面的每个块指定一个过期日期，在此日期之后重新创建缓存文件，或者，您可以在应用程序中构建一种机制，在每次存储的内容发生更改时删除缓存文件。

这里有一个例子来说明这个原理:

```
Example 5.3\. 3.php (excerpt)  

<?php  

/**  

 * Writes a cache file  

 * @param string contents of the buffer  

 * @param string filename to use when creating cache file  

 * @return void  

 */  

function writeCache($content, $filename)  

{  

  $fp = fopen('./cache/' . $filename, 'w');  

  fwrite($fp, $content);  

  fclose($fp);  

}  

/**  

 * Checks for cache files  

 * @param string filename of cache file to check for  

 * @param int maximum age of the file in seconds  

 * @return mixed either the contents of the cache or false  

 */  

function readCache($filename, $expiry)  

{  

  if (file_exists('./cache/' . $filename)) {  

    if ((time() - $expiry) > filemtime('./cache/' . $filename)) {  

      return FALSE;  

    }  

    $cache = file('./cache/' . $filename);  

    return implode('', $cache);  

  }  

  return FALSE;  

}
```

我们定义的前两个函数，`writeCache`和`readCache`，分别用于创建缓存文件和检查它们是否存在。`writeCache`函数将渲染输出作为其第一个参数，以及创建缓存文件时应该使用的文件名。`readCache`函数将缓存文件的文件名作为其第一个参数，以及缓存文件应该被视为过期的时间(以秒为单位)。如果找到有效的缓存文件，脚本将返回它；否则，它返回`FALSE`来指示调用文件要么不存在缓存文件，要么它已经过期。

出于这个例子的目的，我使用了一种过程化的方法。但是，我不建议在实践中这样做，因为这将导致非常混乱的代码(请参阅后面的解决方案以获得更好的替代方案)，并且很可能导致文件锁定问题(例如，当有人在更新缓存的时候访问缓存时会发生什么？).

让我们继续这个例子。输出缓冲器启动后，处理开始。首先，脚本调用`readCache`查看文件`3_header.cache`是否存在；这包含了页面的顶部 HTML 头部分和正文的开始部分。我们使用 PHP 的 date 函数来显示页面实际呈现的时间，所以当页面显示时，您将能够看到不同的缓存文件在工作。

```
Example 5.4\. 3.php (excerpt)  

// Start buffering the output  

ob_start();  

// Handle the page header  

if (!$header = readCache('3_header.cache', 604800)) {  

  // Display the header  

  ?>  

  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  

  <html >  

  <head>  

  <title> Chunked Cached Page </title>  

  <meta http-equiv="Content-Type"  

    content="text/html; charset=iso-8859-1" />  

  </head>  

  <body>  

  The header time is now: <?php echo date('H:i:s'); ?><br />  

  <?php  

  $header = ob_get_contents();  

  ob_clean();  

  writeCache($header,'3_header.cache');  

}
```

请注意找不到缓存文件时会发生什么。一些内容被输出并用`ob_get_contents`赋给一个变量，之后`ob_clean`函数清空缓冲区。这允许我们以“块”的形式捕获输出，并用`writeCache`将它分配给单独的缓存文件。页面的标题现在存储为一个文件，无需重新呈现页面就可以重用。让我们回顾一下 if 条件的开始。当我们调用`readCache`时，我们给它一个 604800 秒(一周)的到期时间；`readCache`使用缓存文件的文件修改时间来确定缓存是否仍然有效。

对于页面的主体，我们将使用与之前相同的过程。然而，这一次，当我们调用`readCache`时，我们将使用 5 秒的到期时间；缓存文件将在超过五秒后更新:

```
Example 5.5\. 3.php (excerpt)  

// Handle body of the page  

if (!$body = readCache('3_body.cache', 5)) {  

  echo 'The body time is now: ' . date('H:i:s') . '<br />';  

  $body = ob_get_contents();  

  ob_clean();  

  writeCache($body, '3_body.cache');  

}
```

页脚实际上和页眉是一样的。此后，停止输出缓冲，并显示保存页面数据的三个变量的内容:

```
Example 5.6\. 3.php (excerpt)  

// Handle the footer of the page  

if (!$footer = readCache('3_footer.cache', 604800)) {  

  ?>  

  The footer time is now: <?php echo date('H:i:s'); ?><br />  

  </body>  

  </html>  

  <?php  

  $footer = ob_get_contents();  

  ob_clean();  

  writeCache($footer, '3_footer.cache');  

}  

// Stop buffering  

ob_end_clean();  

// Display the contents of the page  

echo $header . $body . $footer;  

?>
```

最终结果如下所示:

```
The header time is now: 17:10:42  

The body time is now: 18:07:40  

The footer time is now: 17:10:42
```

页眉和页脚每周更新一次，而正文则在超过五秒后更新。

图 5.1 中的图表总结了分块缓冲方法。

![1284_image1](img/2b0c2932d64748bbfaa20d6e6e47a797.png)
图 5.1。分块缓冲流程图

*嵌套缓冲区*

*你可以通过多次调用`ob_start`将一个缓冲区无限嵌套在另一个缓冲区中。如果您有多个使用输出缓冲区的操作，比如一个捕获 PHP 错误消息，另一个处理缓存，这将非常有用。需要注意确保每次使用`ob_start`时都调用`ob_end_flush`或`ob_end_clean`。*

##### 我如何实现一个简单的服务器端缓存系统？

现在我们已经掌握了输出缓冲背后的思想，是时候看看如何以一种易于维护的方式将这个过程付诸实施了。为此，我们将使用 PEAR::Cache_Lite 的一点帮助(这里的示例中使用的是 1.1 版)。

正如我提到的，为了保持代码的可维护性和可靠的缓存机制，将缓存逻辑的责任委托给你信任的类是一个好主意。Cache_Lite 提供了一个可靠但易于使用的库来进行缓存，处理文件锁定、创建、检查和删除缓存文件等问题，控制输出缓冲区，并直接缓存函数和类方法调用的结果。更重要的是，Cache_Lite 应该相对容易地应用于现有的应用程序，只需要对代码进行少量的修改。

Cache_Lite 中有三个主要的类。第一个是基类`Cache_Lite`，它纯粹处理创建和获取缓存文件，但是不使用输出缓冲。这个类可以单独用于不需要输出缓冲的缓存操作，比如存储用 PHP 解析的模板内容。这里的例子不会直接使用`Cache_Lite`，而是集中在两个子类上。`Cache_Lite_Function`可用于调用函数或类方法并缓存结果；例如，这对于存储 MySQL 查询结果集可能很有用。`Cache_Lite_Output`类使用 PHP 的输出控制函数来捕捉脚本生成的输出，并将其存储在缓存文件中；它允许您执行诸如我们在之前的解决方案中完成的任务。

这里有一个例子，说明如何使用 Cache_Lite 来完成我们在上一个解决方案中完成的任务。当实例化任何 Cache_Lite 的类时，我们必须首先提供一组选项来决定 Cache_Lite 的行为。我们一会儿会详细讨论这些。请注意，指定的 cacheDir 目录必须是脚本可以读写的目录。

```
Example 5.7\. 4.php (excerpt)   

<?php   

// Include the PEAR::Cache_Lite Output class   

require_once 'Cache/Lite/Output.php';   

// Define options for Cache_Lite   

$options = array(   

  'cacheDir'        => './cache/',   

  'writeControl'    => 'true',   

  'readControl'     => 'true',   

  'readControlType' => 'md5'   

);   

// Instantiate Cache_Lite_Output   

$cache = new Cache_Lite_Output($options);
```

对于我们想要缓存的每个块，我们需要设置一个生命周期(以秒为单位),缓存在刷新之前应该存在这个生命周期。接下来，我们使用仅在`Cache_Lite_Output`类中可用的`start`方法来打开输出缓冲。传递给 start 方法的两个参数是这个特定缓存文件的标识值和缓存组。这是允许对缓存文件集合进行操作的标识符；例如，可以删除一个给定组中的所有缓存文件(稍后会详细介绍)。一旦这个块的输出完成，我们使用`end`方法停止缓冲并将内容存储为文件。

```
Example 5.8\. 4.php (excerpt)   

// Set lifetime for this "chunk"   

$cache->setLifeTime(604800);   

// Start the cache with an id and group for this chunk   

if (!$cache->start('header', 'Static')) {   

  ?>   

  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"   

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">   

  <html >   

  <head>   

  <title> PEAR::Cache_Lite example </title>   

  <meta http-equiv="Content-Type"   

    content="text/html; charset=iso-8859-1" />   

  </head>   

  <body>   

  <h2>PEAR::Cache_Lite example</h2>   

  The header time is now: <?php echo date('H:i:s'); ?><br />   

  <?php   

  // Stop and write the cache   

  $cache->end();   

}
```

缓存正文和页脚的过程与缓存页眉的过程相同。请注意，在缓存主体时，我们再次指定了五秒的生存期:

```
Example 5.9\. 4.php (excerpt)   

$cache->setLifeTime(5);   

if (!$cache->start('body', 'Dynamic')) {   

  echo 'The body time is now: ' . date('H:i:s') . '<br />';   

  $cache->end();   

}   

$cache->setLifeTime(604800);   

if (!$cache->start('footer', 'Static')) {   

  ?>   

  The footer time is now: <?php echo date('H:i:s'); ?><br />   

  </body>   

  </html>   

  <?php   

  $cache->end();   

}   

?>
```

在查看页面时，Cache_Lite 在`cache`目录中创建具有以下名称的文件:

```
./cache/cache_Static_header   

./cache/cache_Dynamic_body   

./cache/cache_Static_footer
```

当以后请求相同的页面时，如果缓存文件有效并且没有过期，上面的代码将使用该缓存文件。

*保护您的缓存文件*

确保你放置缓存文件的目录不是公开的，否则你可能会让你网站的访问者获得比你意识到的更多的信息。

***Cache_Lite 选项***

当实例化`Cache_Lite`(或者它的任何子类，比如`Cache_Lite_Output`)时，有许多方法可以控制它的行为。这些应该放在一个数组中，并像前面的示例一样传递给构造函数:

```
Example 5.10\. 4.php (excerpt)   

// Define options for Cache_Lite   

$options = array(   

  'cacheDir'        => './cache/',   

  'writeControl'    => TRUE,   

  'readControl'     => TRUE,   

  'readControlType' => 'md5'   

);   

// Instantiate Cache_Lite_Output   

$cache = new Cache_Lite_Output($options);
```

在当前版本(1.1)中，可用选项有:

*   `cacheDir`–这是放置缓存文件的目录。这默认为当前脚本执行目录。
*   `caching`–该选项打开或关闭`Cache_Lite`的缓存行为。例如，如果您的代码中有大量的`Cache_Lite`调用，并且想要禁用调试缓存，这将非常重要。默认值为`TRUE`(启用缓存)。
*   `lifetime`–这表示缓存文件的默认生命周期(秒)。可以使用`setLifeTime`方法进行更改。默认值为`3600`(一小时)。
*   `fileNameProtection`–激活此选项后，Cache_Lite 使用 MD5 加密哈希生成缓存文件的文件名。这可以防止您在尝试使用包含无效文件名字符的 id 或组名时出错；使用`Cache_Lite_Function`时必须开启。默认为`TRUE`(启用)。
*   `fileLocking`–用于打开或关闭文件锁定机制。默认为`TRUE`(使能)。
*   `writeControl`–检查缓存文件在创建后是否被正确写入，如果发现问题，抛出`PEAR::Error`。显然，这将允许您的代码尝试重写一个不正确创建的缓存文件，但会以性能为代价。默认为`TRUE`(启用)。
*   `readControl`–检查正在读取的缓存文件是否损坏。Cache_Lite 能够在文件中放置一个值，比如文件的字符串长度，它可以用来确认缓存文件没有损坏。有三种替代机制来检查文件是否有效，它们是使用`readControlType`选项指定的。这些机制是以性能为代价的，但应该有助于保证你的访问者不会看到混乱的页面。默认值为`TRUE`(启用)。
*   `readControlType`–指定要使用的读取控制机制的类型。可用的机制有使用 PHP 的`crc32`函数的循环冗余校验(`crc32`，默认值)，使用 PHP 的`md5`函数的 MD5 散列(`md5`)，或者简单快速的字符串长度校验(`strlen`)。请注意，这种机制不是为了防止有人篡改您的缓存文件；这只是一种发现损坏文件的方法。
*   `pearErrorMode`–这告诉`Cache_Lite`它应该如何向调用脚本返回 PEAR 错误。默认是`CACHE_LITE_ERROR_RETURN`，也就是说 Cache_Lite 会返回一个/#c#？PEAR::错误对象。
*   `memoryCaching`–启用内存缓存后，每次将文件写入缓存时，它都会存储在 Cache_Lite 中的一个数组中。`saveMemoryCachingState`和`getMemoryCachingState`方法可用于在请求之间存储和访问内存缓存数据。这样做的好处是，完整的缓存文件集可以存储在单个文件中，通过将缓存文件直接重建到代码可以访问的数组中，减少了磁盘读/写次数。在这里，我们将坚持使用普通的 Cache_Lite 机制，但是如果您运行一个大型站点，那么`memoryCaching`可能值得进一步研究。默认值为`TRUE`(禁用)。
*   `onlyMemoryCaching`–如果启用，将只使用内存缓存机制。默认值为`TRUE`(禁用)。
*   `memoryCachingLimit`–这限制了存储在内存缓存阵列中的缓存文件的数量。您拥有的缓存文件越多，内存缓存使用的内存就越多，因此强制实施一个限制来防止您的服务器工作过度可能是个好主意。当然，这并没有限制每个缓存文件的大小，所以一两个大文件可能会导致问题。默认值为`1000`。

***清除缓存***

Cache_Lite 内置的缓存文件生存期机制为保持缓存文件最新提供了良好的基础，但是在某些情况下，您需要立即更新文件。对于这种情况，方法`remove`和`clean`就派上了用场。remove 方法用于删除特定的缓存文件；它获取文件的缓存 ID 和组名。要删除上面创建的页面主体缓存文件，我们可以使用:

```
$cache->remove('body', 'Dynamic');
```

使用`clean`方法，我们只需调用不带参数的方法就可以删除我们缓存目录中的所有文件；或者，我们可以指定一组要删除的缓存文件。如果我们想删除上面创建的页眉和页脚，我们可以这样做:

```
$cache->clean('Static');
```

显然应该调用`remove`和`clean`方法来响应应用程序中的事件。例如，如果您有一个论坛应用程序，您可能希望在访问者发布新消息时删除相关的缓存文件。虽然看起来这个解决方案需要大量的代码修改，但是只要小心，它可以以全局的方式应用到您的应用程序中。如果你有一个包含在访问者浏览的每个页面中的核心脚本，你可以简单地观察进来的事件(例如像`$_GET['newPost']`这样的变量)并让一些代码通过删除所需的缓存文件来响应。这使得缓存文件删除机制保持在中心位置，并且更易于维护。你也可以考虑使用`php.ini`设置`auto_prepend_file`在每个 PHP 脚本中包含这段代码。

***缓存函数调用***

在第 2 章 XML 中，我们看了用 SOAP 和 XML-RPC 访问远程 Web 服务。因为 Web 服务是通过网络访问的，所以缓存结果以便可以在本地获取它们，而不是多次重复相同的缓慢请求，这通常是一个非常好的主意。一个简单的方法可能是使用 PHP 会话，就像我们在那一章中考虑的那样，但是由于这个解决方案是基于每个访问者操作的，所以每个访问者的打开请求仍然会很慢。这就是 Cache_Lite 可以派上用场的地方。

*PEAR 使用 Cache_Lite*

*PEAR Web 安装程序(参见附录 D，使用 PEAR)通过缓存它向 PEAR Web 服务器发出的 XML-RPC 请求来利用 Cache_Lite。*

在“我如何用 PHP 消费 SOAP Web 服务？”，我们基于 SOAP Web 服务的 WSDL 文件为其构建了一个客户端；这项服务为世界各地的机场提供天气信息。下面是从远程服务器获取数据的代码:

```
$countries = $stationInfo->listCountries();
```

和

```
$country = $stationInfo->searchByCountry($_GET['country']);
```

在这两种情况下，这些调用对应于通过网络发出的数据请求。使用`Cache_Lite_Function`，我们可以缓存结果，这样从服务返回的数据可以被重用；这将避免不必要的网络调用，并显著提高性能。注意，这里我们只关注相关的代码。在顶部，我们包括`Cache_Lite_Function`:

```
Example 5.11\. 5.php (excerpt)   

// Include PEAR::Cache_Lite_Function   

require_once 'Cache/Lite/Function.php';
```

再往下，我们用一些选项实例化了`Cache_Lite_Function`类:

```
Example 5.12\. 5.php (excerpt)   

// Define options for Cache_Lite_Function   

// NOTE: fileNameProtection = TRUE!   

$options = array(   

  'cacheDir'           => './cache/',   

  'fileNameProtection' => TRUE,   

  'writeControl'       => TRUE,   

  'readControl'        => TRUE,   

  'readControlType'    => 'strlen',   

  'defaultGroup'       => 'SOAP'   

);   

// Instantiate Cache_Lite_Function   

$cache = new Cache_Lite_Function($options);
```

重要的是将`fileNameProtection`选项设置为`TRUE`(这实际上是默认值，但在这种情况下，我已经手动设置了它以强调这一点)。如果设置为`FALSE`，文件名将无效，因此数据不会被缓存。

下面是我们调用 SOAP 客户端类的方法:

```
Example 5.13\. 5.php (excerpt)   

  $countries = $cache->call('stationInfo->listCountries');
```

并且:

```
Example 5.14\. 5.php (excerpt)   

  $country = $cache->call('stationInfo->searchByCountry',   

    $_GET['country']);
```

如果请求是第一次发出的，`Cache_Lite_Function`将结果以序列化数组的形式存储在缓存文件中(您不必担心这一点)，这个文件将用于以后的请求，直到它过期。`setLifeTime`方法可以再次用于指定缓存文件在被刷新之前应该存在多长时间；现在，使用默认值 3，600 秒(1 小时)。

一般来说，Cache_Lite 为解决缓存问题提供了一个可靠的、易于实现的库。当我们进入缓存的“下一个层次”时，对于流量特别高的网站，有必要研究一下 Cache_Lite 的老大哥 [PEAR::Cache](http://pear.php.net/package/CACHE) 。PEAR::Cache 是一个完整的缓存框架，提供了比 Cache_Lite 更大的灵活性，并与数据库抽象库(如 [PEAR::DB](http://pear.php.net/package/DB) )相结合。它还提供了一些高级功能，比如缓存到共享内存，作为文件系统的替代，或者在 [Msession PHP 扩展](http://www.php.net/msession)的帮助下，在负载平衡的会话中存储缓存数据，这对负载平衡的 Web 服务器特别有用。本章末尾推荐了更多的阅读材料。然而，Cache_Lite 提供了足够的功能来满足大多数站点的需求。

##### 如何用 PHP 控制客户端缓存？

已经了解了如何禁用客户端缓存和处理服务器端缓存，现在是时候看看一种允许我们以一种可以从 PHP 脚本中控制的方式利用客户端缓存的机制了。这种方法只有在使用 Apache Web 服务器运行 PHP 时才有效，因为它需要使用函数`getallheaders`来获取 Web 浏览器发送的 HTTP 头。这个函数只适用于 Apache。

*新功能名称*

*如果你在 Apache 上使用的是 PHP 4 . 3 . 0+版本，HTTP 头可以通过函数 [`apache_request_headers`](http://www.php.net/apache_request_headers) 和 [`apache_response_headers`](http://www.php.net/apache_response_headers) 获得。功能`getallheaders`已经成为新功能`apache_request_headers`的别名。*

处理 Web 浏览器缓存的机制还是 HTTP。在指示 Web 浏览器和代理服务器是否缓存一个页面时，涉及到许多头；事实上，有些仅在 HTTP 1.1 中可用，这使得情况变得更加复杂。

*在浏览器中检查 HTTP 标题*

一个简单但非常方便的检查请求和响应头的工具是 LiveHttpHeaders，它是 Mozilla 浏览器的一个附件。确切地知道脚本发送的头是什么是值得的，尤其是在处理 HTTP 缓存头的时候。

从保持简单的角度来看，这里我们将只关注 HTTP 1.0 缓存头，即 Expires、Last-Modified 和 If-Modified-Since，以及 HTTP 状态代码 304(未修改)。

HTTP 1.1 中可用的那些头，如 Cache-Control 和 ETAG，旨在提供一种可以与 Web 会话状态结合使用的高级机制；换句话说，向未登录的访问者显示的给定页面的版本可能与向登录用户显示的版本有很大不同。添加 HTTP 1.1 头主要是为了允许缓存这样的页面。

***页面到期***

最容易使用的标题是 Expires 标题，它设置了页面过期的日期(大概是在将来)。在那之前，网页浏览器可以使用网页的缓存版本。

一个例子:

```
Example 5.15\. 6.php    

<?php    

/**    

 * Sends the Expires HTTP 1.0 header.    

 * @param int number of seconds from now when page expires    

 */    

function setExpires($expires)    

{    

  header('Expires: ' .    

    gmdate('D, d M Y H:i:s', time() + $expires) . 'GMT');    

}    

// Set the Expires header    

setExpires(10);    

// Display a page    

echo 'This page will self destruct in 10 seconds<br />';    

echo 'The GMT is now ' . gmdate('H:i:s') . '<br />';    

echo '<a href="' . $_SERVER['PHP_SELF'] .    

  '">View Again</a><br />';    

?>
```

`setExpires`函数将 HTTP Expires 报头设置为以秒为单位定义的未来时间。以上示例显示了 GMT 中的当前时间，并提供了一个链接，允许您再次查看该页面。使用浏览器的刷新按钮，您可以告诉浏览器刷新缓存。使用这个链接，你会发现时间每十秒钟更新一次。

*HTTP 中的日期和时间*

*HTTP 日期总是相对于格林威治标准时间(GMT)计算的。PHP 函数`gmdate`与`date`函数完全相同，除了它根据服务器的系统时钟和区域设置自动将时间偏移到 GMT。*

当浏览器遇到 Expires 标头时，它会缓存页面。在指定的过期时间之前对该页的所有进一步请求都使用该页的缓存版本；不会向 Web 服务器发送任何请求。

Expires 标题具有易于实现的优点，但是在大多数情况下，除非你是一个高度有条理的人，否则你不会确切地知道你的站点上的给定页面何时会被更新。因为浏览器只有在页面过期后才会联系服务器，所以没有办法告诉浏览器他们缓存的页面过期了。您还会丢失一些关于网站流量的信息，因为浏览器在请求已缓存的页面时不会联系服务器。

***页面修改时间***

更有用的方法是利用 Last-Modified 和 If-Modified-Since 头，这两个头在 HTTP 1.0 中都有。从技术上讲，这被称为执行条件 GET 是否返回任何内容取决于传入的 If-Modified-Since 请求头的条件。

使用这种方法，您需要在每次访问 PHP 脚本时发送一个 Last-Modified 头。下次浏览器请求页面时，它会发送一个包含时间的 If-Modified-Since 头；然后，您的脚本可以识别自提供的时间以来页面是否已经更新。如果没有，您的脚本会发送一个 HTTP 304 状态代码来表明页面没有被修改，并在发送页面正文之前退出。

提供一个简单的条件 get 的例子是很棘手的，但是 PEAR::Cache_Lite 是一个方便的工具来展示这是如何工作的。但是不要混淆；这并不意味着显示服务器端缓存；它只是提供一个定期更新的文件。

代码如下:

```
Example 5.16\. 7.php (excerpt)    

<?php    

// Include PEAR::Cache_Lite    

require_once 'Cache/Lite.php';    

// Define options for Cache_Lite    

$options = array(    

  'cacheDir' => './cache/'    

);    

// Instantiate Cache_Lite    

$cache = new Cache_Lite($options);    

// Some dummy data to store    

$id = 'MyCache';    

// Initialize the cache if first time the page is requested    

if (!$cache->get($id)) {    

  $cache->save('Dummy', $id);    

}    

// A randomizer...    

$random = array(0, 1, 1);    

shuffle($random);    

// Randomly update the cache    

if ($random[0] == 0) {    

  $cache->save('Dummy', $id);    

}    

// Get the time the cache file was last modified    

$lastModified = filemtime($cache->_file);    

// Issue an HTTP last modified header    

header('Last-Modified: ' .    

  gmdate('D, d M Y H:i:s', $lastModified) . ' GMT');    

// Get client headers - Apache only    

$request = getallheaders();    

if (isset($request['If-Modified-Since'])) {    

  // Split the If-Modified-Since (Netscape < v6 gets this wrong)    

  $modifiedSince = explode(';', $request['If-Modified-Since']);    

  // Turn the client request If-Modified-Since into a timestamp    

  $modifiedSince = strtotime($modifiedSince[0]);    

} else {    

  // Set modified since to 0    

  $modifiedSince = 0;    

}    

// Compare time the content was last modified with client cache    

if ($lastModified <= $modifiedSince) {    

  // Save on some bandwidth!    

  header('HTTP/1.1 304 Not Modified');    

  exit();    

}    

echo 'The GMT is now ' . gmdate('H:i:s') . '<br />';    

echo '<a href="' . $_SERVER['PHP_SELF'] .    

  '">View Again</a><br />';    

?>
```

运行此示例时，请记住使用“再次查看”链接(单击刷新通常会清空浏览器的缓存)。如果你反复点击链接，最终缓存会被更新；您的浏览器将丢弃其缓存版本，并获取一个由 PHP 呈现的新页面。

在上面的例子中，我们使用 PEAR::Cache_Lite 创建了一个随机更新的缓存文件。我们用下面这行代码确定缓存文件的文件修改时间:

```
$lastModified = filemtime($cache->_file);
```

从技术上讲，这是一种黑客行为，因为 PEAR::Cache_Lite 希望它的`$_file`成员变量是私有的。但是，我们必须使用它来获取缓存文件的名称，这样我们就可以获取它的修改时间。

接下来，我们使用缓存文件的修改时间发送最后修改的头。我们需要为我们呈现的每个页面发送这个消息，以使访问浏览器在每次请求时向我们发送 If-modified-Since 头。

```
// Issue an HTTP last modified header    

header('Last-Modified: ' .    

  gmdate('D, d M Y H:i:s', $lastModified) . ' GMT');
```

使用`getallheaders`函数确保 PHP 将所有传入的请求头作为一个数组提供给我们。然后我们需要检查 If-Modified-Since 头是否确实存在；如果是这样，我们必须处理由旧的 Mozilla 浏览器(版本 6 以下)引起的特殊情况，它在 If-Modified-Since 头后面附加了一个(非法的)额外字段。使用 PHP 的`strtotime`函数，我们从浏览器发送给我们的日期生成一个时间戳。如果没有这样的头，我们将这个时间戳设置为零，强制 PHP 给访问者一个最新的页面副本。

```
// Get client headers - Apache only    

$request = getallheaders();    

if (isset($request['If-Modified-Since'])) {    

    // Split the If-Modified-Since (Netscape < v6 gets this wrong)    

    $modifiedSince = explode(';', $request['If-Modified-Since']);    

    // Turn the client request If-Modified-Since into a timestamp    

    $modifiedSince = strtotime($modifiedSince[0]);    

} else {    

    // Set modified since to 0    

    $modifiedSince = 0;    

}
```

最后，我们检查自从访问者最后一次收到这个页面以来，缓存是否被修改过。如果没有，我们只需发送一个`Not Modified`响应头并退出脚本，通过指示浏览器显示页面的缓存副本来节省带宽和处理时间。

```
// Compare the time the content was last modified with cache    

if ($lastModified <= $modifiedSince) {    

  // Save on some bandwidth!    

  header('HTTP/1.1 304 Not Modified');    

  exit();    

}
```

如果您将最后修改的方法与应用程序中已经可用的时间值(例如，最新新闻文章的时间，或者我们在上一个解决方案中看到的服务器端缓存系统的到期时间)结合起来，您应该能够利用 Web 浏览器缓存并节省带宽，同时能够收集站点的流量信息并提高其感知性能。

但是，要非常小心地测试以这种方式执行的任何缓存；如果你做错了，你可能会导致你的访问者永久地拥有你的站点的过期副本。

##### 进一步阅读

*   [Caching Tutorial for Web Authors and Webmasters](http://www.mnot.net/cache_docs/)
    This article represents the definitive discussion of Web caching.
*   [Issuing Correct HTTP Headers](http://perl.apache.org/docs/general/correct_headers/correct_headers.html)
    This tutorial provides a useful discussion of HTTP headers in Perl, which can be readily applied to PHP.
*   [HTTP 1.1 RFC 2616 on Cache Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
    Here, you’ll find a precise description of HTTP 1.1 cache control headers.
*   [Output Buffering, and how it can Change Your Life](http://www.zend.com/zend/art/buffering.php)
    Zeev Suraski gives a short tour of what can be done with PHP’s output buffering in this great article.
*   [Output Buffering with PHP](http://www.devshed.com/Server_Side/PHP/OutputBuffering/)
    This article provides another look at PHP’s output buffering, with notes on using it to capture PHP errors.
*   用 PEAR 缓存 PHP 程序
    塞巴斯蒂安·博格曼介绍 PEAR::Cache。

这就是 PHP 选集的样本章节！

要了解完整的故事，包括您每天面临的 PHP 开发问题的大约 100 种解决方案——从图像处理到 N 层应用程序设计——以及完整的代码档案，[单击此处订购您的副本](https://www.sitepoint.com/books/phpant1/)！

## 分享这篇文章