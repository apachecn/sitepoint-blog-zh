# 如何用 PHP 创建一个 XML 到 JSON 的代理服务器

> 原文：<https://www.sitepoint.com/php-xml-to-json-proxy/>

除非你是 web 开发新手，否则你会知道“AJAX”中的“X”代表[XML——可扩展标记语言](https://www.sitepoint.com/ajax-data-formats-xml-soap-html/)。但是您可能没有使用 XML。如果你是，你可能不喜欢。所有酷孩子都在使用 [JSON 或 JSON-P](https://www.sitepoint.com/ajax-data-formats-json-jsonp/) :它的有效载荷更小，更容易使用，处理速度更快。

这并不是说 XML *不应该被使用。它已经存在了很长时间，并且得到了大多数语言的良好支持——包括 JavaScript。如果要在各种系统和平台之间共享数据，XML 几乎肯定是最实用的选择。但是这并没有减轻您的客户端编码工作。*

幸运的是，有几个解决方案可以让您保留 XML 数据交换的好处，同时提供 JavaScript 中 JSON 的便利性。在本文中，我们将使用 PHP 创建一个 XML 到 JSON 的代理服务器。

## 听起来很复杂？

别担心，不是的。本质上，代理位于客户机和服务器之间，在两者之间传递消息。你现在可能正坐在代理服务器后面——它们在互联网上被用来缓存数据和减少网络流量。然而，它们也可以处理数据；我们将创建一个简单的 PHP 代理，在 XML 消息到达您的 JavaScript 代码之前将它们转换成 JSON。

## 代理计划

我们的系统将:

1.  从调用 PHP 代理的 JavaScript 发送一个 Ajax 请求。它将传递一个编码的 url 作为名为“URL”的 GET 参数。
2.  代理将获取作为字符串传递的 URL 的内容，将其解析为 XML 并转换为 JSON。
3.  JSON 字符串将被返回给调用的 JavaScript 进程。

如果有必要，我们可以创建一个完整的 REST 感知代理来解析 POST、PUT 和 DELETE 参数。但是这个简单的解决方案对于 99%的 Ajax 查询来说已经足够了，而且它还有其他一些优点:

*   在外部域上调用 web 服务是可能的——对于纯 JavaScript 的解决方案，这并不总是可能的。
*   如果有必要，代理可以从消息中删除不必要的数据，以减少负载。
*   我们将需要更少的 JavaScript 代码，它将执行得更快。

## PHP

PHP 同时支持 XML 和 JSON，因此创建我们的代理 xmlproxy.php 非常简单。

也就是说，很多事情都可能出错。我们的脚本可能会失败，远程服务可能会关闭，或者返回的 XML 可能格式不正确。我们不希望 PHP 错误被发送回 JavaScript，所以我们将定义一个异常处理程序来隐藏它们:

```
 <?php
ini_set('display_errors', false);
set_exception_handler('ReturnError'); 
```

我们现在需要两个变量用于响应($r)和传递的 URL ($url):

```
 $r = '';
$url = (isset($_GET['url']) ? $_GET['url'] : null); 
```

PHP 的 [cURL 库](http://php.net/manual/en/book.curl.php)用于从 URL 获取内容并将其传递给 string $r:

```
 if ($url) {

	// fetch XML
	$c = curl_init();
	curl_setopt_array($c, array(
		CURLOPT_URL => $url,
		CURLOPT_HEADER => false,
		CURLOPT_TIMEOUT => 10,
		CURLOPT_RETURNTRANSFER => true
	));
	$r = curl_exec($c);
	curl_close($c);

} 
```

假设返回了一些内容，我们将把它作为 SimpleXMLElement 对象加载，并返回一条 JSON 编码的消息:

```
 if ($r) {
	// XML to JSON
	echo json_encode(new SimpleXMLElement($r));
} 
```

如果没有返回任何内容，我们将调用异常处理函数 ReturnError()，它输出一个 JSON 编码的错误标志:

```
 else {
	// nothing returned?
	ReturnError();
}

// return JSON error flag
function ReturnError() {
	echo '{"error":true}';
} 
```

## JavaScript

我们的 JavaScript 必须定义要调用的远程 URL，例如

```
 // example XML feed
var url = "http://domain.com/example.xml?status=123&date=2011-01-01"; 
```

url 作为“URL”参数附加到 PHP 代理地址，并传递给我们的 XMLHttpRequest 调用(Ajax)的 open()方法:

```
 // AJAX request
var xhr = (window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHTTP"));
xhr.onreadystatechange = XHRhandler;
xhr.open("GET", "xmlproxy.php?url=" + escape(url), true);
xhr.send(null); 
```

最后，我们的 xmlhttprequestonreadystatechange 处理程序接收数据，并将 JSON 字符串转换为真实的 JavaScript 对象:

```
 // handle response
function XHRhandler() {

	if (xhr.readyState == 4) {

		// parse response as JSON
		var json;
		if (JSON && JSON.parse) {
			json = JSON.parse(xhr.responseText);
		}
		else {
			eval("var json = " + xhr.responseText);
		}

		// do something with our returned JSON data...
		console.log(json);

		xhr = null;

	}

} 
```

请 [**下载**](https://blogs.sitepointstatic.com/examples/tech/xml2json/xmlproxy.zip) 代码，将文件解压到你的支持 PHP 的 web 服务器，并在浏览器中打开 proxy.html。

## 关于 XML 属性编码的一个注记

XML 具有比 JSON 更丰富的语法，数据可以被编码为元素或属性——甚至是同名的，例如

```
 <?xml version="1.0"?>
<statuses>
	<status id="one">
		<id>1</id>
	</status>
</statuses> 
```

PHP json_encode 函数将属性翻译成一个单独的“@attributes”对象，即

```
 {
	"status": {
		"@attributes": { "id": "one" },
		"id": "1"
	}
} 
```

我希望这些代码对您有用。它允许您拥有自己的 XML 蛋糕，并将其作为 JSON 来使用！

## 分享这篇文章