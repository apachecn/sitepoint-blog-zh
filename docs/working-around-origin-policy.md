# 使用和围绕同源策略工作

> 原文：<https://www.sitepoint.com/working-around-origin-policy/>

作为一名 JavaScript 开发人员，您可能会大量使用 Ajax 与服务器交换数据或更新网页而无需刷新。尽管向您的服务器发送 Ajax 请求是一个非常简单的请求，但是与另一个域中的服务器交换数据则完全是另一回事。让我们来试试吧！

让我们在 Chrome 32 上从`http://www.mysite.com`(或者`127.0.0.1` / `localhost`)开始运行以下内容。

```
request = new XMLHttpRequest;
request.open('GET', 'http://myothersite.com/', true);
request.send();
```

您将收到一个错误。`XMLHttpRequest`无法加载`http://myothersite.com/`。请求的资源上不存在`'Access-Control-Allow-Origin'`标头。原点`http://www.mysite.com`因此不允许访问。

为什么会这样？难道我们没有做好每一件事吗？

## 同源政策

同源策略允许在浏览器中运行的脚本只能向同一域中的页面发出请求。这意味着请求必须具有相同的 URI 方案、主机名和端口号。[Mozilla 开发者网络上的这个](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Same_origin_policy_for_JavaScript)帖子清楚地定义了一个起源以及请求何时导致失败。如果从`http://www.mysite.com/`发送请求，以下类型的请求会导致失败。

*   `https://www.mysite.com/`–不同的协议(或 URI 方案)。
*   `http://www.mysite.com:8080/myUrl`–不同的端口(因为默认情况下 HTTP 请求在端口`80`上运行)。
*   `http://www.myothersite.com/`–不同的域。
*   `http://mysite.com/`–被视为不同的域，因为它需要完全匹配(注意没有`www.`)。

## 改变原点

有时，相同的原始策略可能会阻止同一域上的子域之间的请求。解决这个问题最简单的方法是从 JavaScript 内部设置`document.domain`。例如:

```
document.domain = 'mysite.com';
```

请注意，端口号是单独存储的。让一个域在不同的端口上与另一个域交互(聊天应用程序就是这种情况)，需要不同的东西。即使设置`document.domain = document.domain`，将端口号覆盖为`null`，也无助于完成这项工作。

## 使用 Web 代理

虽然指定`document.domain`可以帮助你联系你自己网站的子域名，但是如果你需要从一个完全不同的域名访问数据，你会怎么做？一个有趣而又容易理解的方法是在您自己的服务器上使用 web 代理。

不是直接从您的域(`http://www.mysite.com/`)向一个新的域(`http://www.myothersite.com/`)发送请求，而是向您自己的服务器(`http://www.mysite.com/connect/`)发送一个请求，服务器再向新的域(`http://www.myothersite.com/`)发送一个请求。对浏览器来说，你似乎是在和你自己的服务器交换数据。实际上，在后台，您已经从服务器访问了新域中的数据。解释该过程的流程图如下所示。

![alt text](img/53c0f804b8861c1cd6623e6233027a72.png "Using a proxy server for cross domain requests")
来源:[雅虎开发者](http://developer.yahoo.com/javascript/howto-proxy.html)

## 使用 JSONP

实现跨浏览器请求的另一种方式是使用 JSONP，即“带填充的 JSON”JSONP 利用了`<script>`标签不受同源策略约束的事实。例如，你可以在你的页面上包含一个像 jQuery 这样的库，即使它托管在 Google 的 CDN 上。

JSONP 请求是通过动态请求一个`<script>`标记来发出的。有趣的是，响应是包装在函数调用中的 JSON。发出请求时，将函数名指定为回调函数。当服务器响应时，回调函数(必须存在于您的页面上)将使用服务器返回的数据来执行。

例如，典型的 JSON 响应可能如下所示:

```
{
  "id": "123",
  "name": "Captain Jack Sparrow"
}
```

同样的响应可以在一个脚本标签中用回调函数`myFunction`来模拟，如下所示。

```
<script src="http://www.myothersite.com/get_data?callback=myFunction"></script>
```

浏览器通常从指定的 URL 下载数据，并将其作为 JavaScript 进行评估。响应可能如下所示:

```
myFunction({"id": "123", "name": "Captain Jack Sparrow"});
```

然后调用`myFunction`，允许您的页面处理从`myothersite.com`返回的 JSON。

### 安全问题-跨站点请求伪造

由于`<script>`标签不遵守同源策略，恶意网站有可能使用相同的 URL 获取敏感数据。使用上面的例子，恶意页面可以下载相同的 JSON 数据，并对其执行一些不友好的操作。这被称为跨站点请求伪造(CSRF)攻击。防止 CSRF 攻击的一些对策包括使用令牌或 cookies 进行验证，以及限制此类令牌的生存期。

## 跨域资源共享

虽然 JSONP 可以用来相对容易地完成大多数任务，但是它有几个缺点。您只能使用 JSONP 发送 HTTP GET 请求。这排除了使用 JSONP 干净地进行 CRUD 操作的任何可能性。尽管代理方法消除了这种安全顾虑，但还有另一种有趣的方法可以帮助我们与 RESTful APIs 进行交互，而无需费力。

跨域资源共享，或称 CORS，通过修改请求中的 HTTP 头来访问不同域上的资源。在 IE8+中，允许使用`XDomainRequest`(而不是`XMLHttpRequest`)的简单 CORS 请求。下面是一个简单的例子。

```
request = new XDomainRequest();
request.open(method, url);
request.onload = function() {
  callback(req.responseText);
};
request.send(data);
```

我们一直在谈论“简单”的请求。不那么简单的请求指的是预先触发的请求，它首先向另一个域发送一个 HTTP 请求，以确定执行该操作是否安全。在 CORS 上的这篇 [MDN 帖子中解释了一个预先请求以及服务器和客户端之间信息交换的详细示例。](https://developer.mozilla.org/en-US/docs/HTTP/Access_control_CORS#Preflighted_requests)

## 结论

尽管 CORS 看起来像是前端编程的未来，但是您仍然应该小心使用它，因为它不支持非常旧的浏览器(IE7 和更早的版本)。对 CORS 的支持是一个小问题，但你一定要去尝试一下！为了进一步阅读，我建议你浏览一下 [MDN 关于 CORS](https://developer.mozilla.org/en-US/docs/HTTP/Access_control_CORS) 的详细帖子。

## 分享这篇文章