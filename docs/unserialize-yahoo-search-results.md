# 取消雅虎序列化！搜索结果

> 原文：<https://www.sitepoint.com/unserialize-yahoo-search-results/>

约翰·考克斯。已经为他们的搜索 API 开放了一个 [PHP 开发中心](http://developer.yahoo.net/blog/archives/2006/02/yahoo_releases.html)，更有趣的是，已经开始将他们的搜索数据公开为序列化的 PHP 字符串。就像在[序列化](http://www.php.net/serialize)函数中那样被“序列化”。

这非常酷，但是在使用它时需要小心，因为它不是为有线格式而设计的，而是为可信环境中 PHP 数据的本地存储而设计的。

**这种格式安全吗？**

首先是信任问题和潜在的安全问题。我想我们可以信任雅虎！好吧，但他们需要确保他们以这种方式正确地逃避他们发布的数据——确保他们收集的搜索结果中没有人能够在那里注入任何东西。为什么？

对于原始数据类型(strings、int、PHP 数组),或多或少没有问题——你可以[解序列化](http://www.php.net/unserialize)你得到的结果而没有问题。也就是说，也许 [Hardened-PHP 项目](http://www.hardened-php.net/)需要关注这个问题——是否存在深度嵌套数组、无限递归或非常大的数据结构等问题？

潜在的(但低风险的)安全问题是，如果任何*对象*以这种方式被序列化。当您取消序列化时，PHP 将尝试创建这些对象的实例(假设它可以找到相应的类)，这将导致~~构造器被触发~~<ins>[_ _ wake up](http://www.php.net/manual/en/language.oop.magic-functions.php)函数(如果类定义中存在的话)，以及 PHP5 中的析构函数</ins>。鉴于 PHP5 的内置类越来越多，这有可能成为一个严重的问题。

我以前在 [JPSpan](http://sourceforge.net/projects/jpspan) 中处理过这个问题，它也使用这种格式，并在这里向[Josh](http://blog.joshuaeichorn.com)T6 提到过。我采用的解决方案是首先用一些正则表达式筛选序列化的字符串，我 99%确定这种方法有效——实现[在这里](http://cvs.sourceforge.net/viewcvs.py/jpspan/jpspan/JPSpan/Unserializer/PHP.php?view=auto) ( `JPSpan_Unserializer_PHP::getClasses`)和单元测试[在这里](http://cvs.sourceforge.net/viewcvs.py/jpspan/jpspan/tests/php/unserializer_php.test.php?view=auto)——参见`TestOfJPSpan_Unserializer_PHP_getClasses`。

为了雅虎！，如果下面的函数可以做到这一点；

```
 function hasObjects($string) {

    // Stip any string representations (which might contain object syntax)
    $string = preg_replace('/s:[0-9]+:".*"/Us','',$string);

    // Pull out the class named
    preg_match_all('/O:[0-9]+:"(.*)"/U',$string,$matches,PREG_PATTERN_ORDER);

    return count($matches[1]) > 0;
}

$serializedString = file_get_contents('http://api.search.yahoo.com ... (etc.) ... &output=php');

if ( hasObjects($serializedString) ) {
    die ("Its got objects. I object!");
}

$result = unserialize($serializedString); 

```

鉴于大多数人将从雅虎获取这些数据。在明文中，攻击者有可能在传输过程中修改它，因此调用该函数来检查对象可能是一个明智的举动。

**字符编码？**

现在雅虎！聪明地将数据编码为 UTF 8 号。下面是我发出的一个请求的 HTTP 响应头(补充说明:可能是 Yahoo！如果这是 REST，可以考虑使用一些 HTTP 缓存吗？);

```
 HTTP/1.x 200 OK
Date: Thu, 23 Feb 2006 08:14:38 GMT
P3p: policyref="http://p3p.yahoo.com/w3c/p3p.xml", CP="[snip]"
Connection: close
Content-Type: text/php; charset="utf-8" 

```

检查了一些多字节的结果，Yahoo 在他们返回的 PHP 结果中根据字节正确地计算了多字符串长度，所以没有问题。不过，您可能确实需要知道您在显示结果的站点上使用的是什么编码。例如，约翰正在使用 ISO-8859-1(这是非常常见的)，所以你要使用 [utf8_decode](http://www.php.net/utf8_decode) 将 UTF-8 转换成 ISO-8859-1(这纯粹是为了与 ISO-8859-1 一起使用！)上的数据元素，*在*后调用 unserialize()。拥有更奇特字符编码的人将需要求助于 [iconv](http://www.php.net/iconv) 。在 Firefox 中，打开你网站上的一个页面，右击并选择“页面信息”——这将告诉你你的字符编码。

**以 PEAR::HTTP_Request** 为例

雅虎！用 [curl](http://www.php.net/curl) 和 [file_get_contents()](http://www.php.net/file_get_contents) 提供例子。这里有一个使用 [PEAR::HTTP_Request](http://pear.php.net/package/HTTP_Request) 的替代示例；

```
 < ?php
// Include PEAR::HTTP_Request
require_once 'HTTP/Request.php';

// Something to search for
$searchword = 'Zürich';

// Build Yahoo! web search URL
$yahoo_url = 'http://api.search.yahoo.com/WebSearchService'.
    '/V1/webSearch?appid=YahooDemo&results=10&output=php'.
    '&query='.$searchword;

// Create the HTTP_Request object, specifying the URL
$Request = &new HTTP_Request($yahoo_url);

// Set proxy server as necessary
// $Request->setProxy('proxy.myisp.com', '8080', 'harryf', 'secret');

// Send the request for the feed to the remote server
$status = $Request->sendRequest();

// Check for errors
if (PEAR::isError($status)) {
    // Do something friendlier than die...
    die("Connection problem: " . $status->toString()."<br />");
}

// Check we got an HTTP 200 status code (if not there's a problem)
if ($Request->getResponseCode() != '200') {
    // Do something friendlier than die...
    die("Request failed: " . $Request->getResponseCode()."<br />");
}

// Get the PHP serialized string from Yahoo!
$phps = $Request->getResponseBody();

// Function to test that the serialized string doesn't contain any objects
function hasObjects($string) {

    // Stip any string representations (which might contain object syntax)
    $string = preg_replace('/s:[0-9]+:".*"/Us','',$string);

    // Pull out the class named
    preg_match_all('/O:[0-9]+:"(.*)"/U',$string,$matches,PREG_PATTERN_ORDER);

    return count($matches[1]) > 0;
}

// Check this before unserializing
if ( hasObjects($phps) ) {
    // Do something friendlier than die...
    die("Serialized string contains objects<br />");
}

// Unserialize...
$data = unserialize($phps);

// Note the charset!
header('Content-type: text/html; charset=utf-8');

echo '<pre>';
foreach ( $data['ResultSet']['Result'] as $row ) {
    print_r($row);
}
echo '</pre>'; 

```

**不只是 PHP**

这种格式的编码器和解码器实际上已经用几种不同的语言实现了。一些我知道的。

*   Perl: [这里](http://www.cpan.org/modules/by-module/PHP/JBROWN/php-serialization/)和[这里](http://hurring.com/code/perl/serialize/)
*   Python: [这里](http://hurring.com/code/python/serialize/)。
*   Ruby: [这里](http://www.aagh.net/projects/ruby-php-serialize/)。
*   Java: [这里](http://hurring.com/code/java/phpserialize/)。
*   C#: [这里](http://sourceforge.net/projects/csphpserial/)。
*   Macromedia ActionScript: [此处](http://sourceforge.net/projects/serializerclass/)。

移动经过[编码的 SOAP](http://en.wikipedia.org/wiki/SOAP)–`text/php`正在接管:-)

## 分享这篇文章