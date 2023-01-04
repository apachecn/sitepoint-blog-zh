# jQuery 的 JSONP 举例说明

> 原文：<https://www.sitepoint.com/jsonp-examples/>

**这篇广受欢迎的文章于 2016 年 6 月 23 日更新，以解决质量问题。删除了与旧条款相关的评论。**

如果您正在开发一个基于 web 的应用程序，并试图从不受您控制的域中加载数据，那么您很可能在浏览器的控制台中看到以下消息:

XMLHttpRequest 无法加载 http://external-domain/service。请求的资源上没有“Access-Control-Allow-Origin”标头。因此，不允许访问源“http://my-domain”。

在本文中，我们将研究导致这个错误的原因，以及如何通过使用 jQuery 和 JSONP 进行跨域 Ajax 调用来解决这个问题。

## 同源政策

常规网页可以使用 [XMLHttpRequest 对象](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)来发送和接收来自远程服务器的数据，然而它们被[同源策略](https://en.wikipedia.org/wiki/Same-origin_policy)所限制。这是浏览器安全模型中的一个重要概念，它规定 web 浏览器只能允许页面 A 上的脚本访问页面 B 上的数据，前提是这两个页面具有相同的来源。页面的来源由它的*协议*、*主机*和*端口号*定义。比如这个页面的原点是' https '，' www.sitepoint.com '，' 80 '。

同源策略是一种安全机制。它阻止脚本从您的域中读取数据并将其发送到服务器。如果我们没有这一点，恶意网站很容易将您的会话信息抓取到另一个网站(如 Gmail 或 Twitter)并代表您执行操作。不幸的是，它也导致了我们上面看到的错误，并且经常给试图完成合法任务的开发人员带来头痛。

### 失败的例子

我们来看看哪些是行不通的。这里有一个 [JSON 文件](http://run.plnkr.co/plunks/v8xyYN64V4nqCshgjKms/data-1.json)驻留在不同的域中，我们希望使用 jQuery 的 [getJSON](http://api.jquery.com/jquery.getjson/) 方法加载它。

```
$.getJSON(
  "http://run.plnkr.co/plunks/v8xyYN64V4nqCshgjKms/data-1.json",
  function(json) { console.log(json); }
); 
```

如果您在打开控制台的浏览器中尝试这样做，您会看到类似上面的消息。那么我们能做什么呢？

## 一种可能的变通办法

幸运的是，并非所有东西都受到同源政策的影响。例如，很有可能将不同域的图像或脚本加载到您的页面中——例如，当您包含来自 CDN 的 jQuery 时，这正是您正在做的事情。

这意味着我们能够创建一个`<script>`标签，将`src`属性设置为 JSON 文件的属性，并将其注入页面。

```
var script = $("<script />", {
    src: "http://run.plnkr.co/plunks/v8xyYN64V4nqCshgjKms/data-1.json",
    type: "application/json"
  }
);

$("head").append(script); 
```

尽管这可行，但它对我们帮助不大，因为我们没有办法获取它包含的数据。

## 输入 JSONP

JSONP (代表带填充的 JSON)建立在这种技术之上，为我们提供了一种访问返回数据的方法。它通过让服务器返回封装在函数调用中的 JSON 数据(“填充”)来实现这一点，然后浏览器可以解释这些数据。这个函数必须在评估 JSONP 响应的页面中定义。

让我们看看前面的例子会是什么样子。这里有一个更新的 [JSON 文件](http://run.plnkr.co/plunks/v8xyYN64V4nqCshgjKms/data-2.json)，它将原始 JSON 数据包装在一个`jsonCallback`函数中。

```
function jsonCallback(json){
  console.log(json);
}

$.ajax({
  url: "http://run.plnkr.co/plunks/v8xyYN64V4nqCshgjKms/data-2.json",
  dataType: "jsonp"
}); 
```

这会将预期的结果记录到控制台。我们现在有了(尽管相当有限)跨域 Ajax。

## 第三方 API

一些第三方 API 允许您为回调函数指定一个名称，当请求返回时应该执行这个函数。一个这样的 API 是 [GitHub API](https://developer.github.com/v3/#json-p-callbacks) 。

在下面的例子中，我们正在获取 John Resig (jQuery 创建者)的用户信息，并使用一个`logResults`回调函数来记录对控制台的响应。

```
function logResults(json){
  console.log(json);
}

$.ajax({
  url: "https://api.github.com/users/jeresig",
  dataType: "jsonp",
  jsonpCallback: "logResults"
}); 
```

这也可以写成:

```
$.getJSON("https://api.github.com/users/jeresig?callback=?",function(json){
  console.log(json);
}); 
```

URL 末尾的`?`告诉 jQuery 它正在处理一个 JSONP 请求，而不是 JSON。然后，jQuery 自动注册回调函数，当请求重新运行时，它会调用这个回调函数。

如果你想了解更多关于 jQuery 的`getJSON`方法，请查看: [Ajax/jQuery.getJSON 简单示例](https://www.sitepoint.com/ajaxjquery-getjson-simple-example/)

## 警告

但是正如您现在可能已经意识到的，这种方法有一些缺点。

例如，JSONP 只能执行跨域 GET 请求，并且服务器必须明确支持它。JSONP 也不是没有它的[安全问题](http://security.stackexchange.com/questions/23438/security-risks-with-jsonp)，所以让我们简单看看其他一些解决方案。

### 使用代理

服务器端代码不受同源策略的约束，可以毫无问题地执行跨源请求。因此，您可以制作某种代理，并使用它来检索您需要的任何数据。参照我们的第一个例子:

```
/* proxy.php */
$url = "http://run.plnkr.co/plunks/v8xyYN64V4nqCshgjKms/data-1.json";
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$result = curl_exec ($ch);
curl_close ($ch);
echo $result; 
```

在客户端:

```
$.getJSON("http://my-domain.com/proxy.php", function(json) {
  console.log(json);
}) 
```

但是这种方法也有它的缺点。例如，如果第三方网站使用 cookies 进行身份验证，这将不起作用。

### 克-奥二氏分级量表

跨源资源共享(CORS)是 W3C 规范，允许从浏览器进行跨域通信。这是通过在响应中包含一个新的`Access-Control-Allow-Origin` HTTP 头来实现的。

参考我们的第一个例子，您可以将以下内容添加到一个`.htaccess`文件中(假设是 Apache ),以允许来自不同来源的请求:

```
Header add Access-Control-Allow-Origin "http://my-domain.com" 
```

(*如果你的服务器运行的不是 Apache，看这里:[http://enable-cors.org/server.html](http://enable-cors.org/server.html)T3)*

你可以在我们最近的教程中找到更多关于 CORS 的信息:[深入了解 CORS](https://www.sitepoint.com/an-in-depth-look-at-cors/)

## 结论

JSONP 允许您回避同源策略，并在某种程度上进行跨域 Ajax 调用。它不是灵丹妙药，当然也有它的问题，但在某些情况下，当从不同的来源获取数据时，它可以证明是非常宝贵的。

JSONP 还使得从不同的服务获取各种内容成为可能。许多著名的网站都提供 JSONP 服务(例如 [Flickr](https://www.sitepoint.com/load-flickr-photos-using-jsonapi/) )，允许你通过预定义的 API 访问它们的内容。你可以在 [ProgrammableWeb API 目录](http://www.programmableweb.com/category/all/apis?search_id=137822&data_format=21174)中找到它们的完整列表。

## 分享这篇文章