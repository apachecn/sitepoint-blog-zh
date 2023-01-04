# GET 和 POST 之间的 jQuery AJAX 差异

> 原文：<https://www.sitepoint.com/key-differences-post/>

很多人问我这样一个问题“当我指定一个 AJAX 请求时，GET 和 POST 之间的区别是什么？”。当您使用 jQuery 指定 AJAX 请求时，GET 和 POST 之间的**关键区别在于。
**

相关帖子:

*   [**jQuery Ajax 函数示例**](http://www.jquery4u.com/function-demos/ajax)
*   [**如何使用 Net Panel 分析 HTTP 请求，HTTP Fox&fiddler 2**](http://www.jquery4u.com/testing/http-request-net-panel-httpfox-fiddler2/)

## 获取与发布

*   GET 请求用于从服务器获取数据。
*   POST 请求用于修改服务器上的数据。

### 何时使用 GET

> 如果表单的处理是幂等的(即它对世界的状态没有持久的可观察的影响)，那么表单方法应该是 GET。许多数据库搜索没有明显的副作用，是查询表单的理想应用。

**GET 的特性:**

*   对安全操作使用 GET，对不安全操作使用 POST。
*   GET 请求可以被缓存
*   GET 请求可以保留在浏览器历史记录中
*   GET 请求可以加入书签
*   GET 请求可以被分发和共享
*   GET 请求可能会被黑客攻击

[W3.org 获取方法定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9.3)

### 何时使用 POST

> 如果与表单处理相关的服务有副作用(例如，修改数据库或订阅服务)，方法应该是 POST。

*   处理长时间的请求时使用 POST——如果你要通过 HTTPS 发送大量数据或敏感数据，你会想要使用 POST。一些浏览器，如 Internet Explorer，对 URL 字符串有限制，因此如果您使用 GET，这可能会中断某些表单的操作。

**您可以考虑使用 POST 进行以下操作:**

*   将消息张贴到公告栏、新闻组、邮件列表或类似的文章组
*   向数据处理过程提供数据块，例如提交表单的结果
*   通过追加操作扩展数据库
*   现有资源的注释

[W3.org 邮政法定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9.5)

## AJAX 调用中的 GET vs POST

除非您将敏感数据发送到服务器或调用在服务器上处理数据的脚本，否则更常见的是使用 GET 进行 AJAX 调用。这是因为当使用 XMLHttpRequest 时，浏览器将 POST 实现为一个两步过程(首先发送头，然后发送数据)。这意味着 **GET 请求响应更快——这是 AJAX 环境中所需要的！**因为“Ajax”请求受同源策略的约束，所以当使用 GET 而不是 POST 时存在有限的安全风险。使用 GET 从服务器“获取”信息，比如加载一个 JavaScript 文件(AJAX 速记函数$。getScript()可以用来做这个)或者加载一个 JSON 文件(AJAX 速记函数$。getJSON()可以用来做这件事)。

默认使用 GET 的 jQuery AJAX 函数:$。get()， [$。getScript()](http://www.jquery4u.com/function-demos/getscript) ， [$。](http://www.jquery4u.com/function-demos/getjson)，[。load()](http://www.jquery4u.com/function-demos/load)

默认使用 POST 的 jQuery AJAX 函数:$。帖子()

示例 GET AJAX 调用——调用一个 PHP 脚本来获取 twitter 关注者的数量。

```
$.ajax({
  url: 'getTwitterFollowers.php',
  type: 'GET',
  data: 'twitterUsername=jquery4u',
  success: function(data) {
	//called when successful
	$('#ajaxphp-results').html(data);
  },
  error: function(e) {
	//called when there is an error
	//console.log(e.message);
  }
});
```

查看演示

【POST AJAX 调用示例——提交登录表单。

```
var $form = $("#myForm");
    var url = $form.attr("action") + "?" + $form.serialize();
    $("#" + id).html(url);

$.ajax({
	type: "POST",
	url: action,
	data: $form,
	success: function(response)
	{
		if(response == 'success')
			$("#myForm").slideUp('slow', function() {
				$("#msg").html("

您已成功登录！

");
			});
		else
			$("#msg").html("

无效的用户名和/或密码。

");
	}
});
```

## 进一步阅读

**表单提交示例**
这个示例并不真正适用于 AJAX，因为这些请求发生在幕后，但是可以帮助您进一步理解不同请求类型之间发生了什么。

当使用 GET 时，会生成一个 HTTP 请求，并将数据作为一组附加到查询字符串中的 URL 的编码参数传递给 web 服务器。

例如，使用 GET 提交登录表单是个坏主意，因为登录细节会显示在地址栏中。

```
GET /login.php?username=user&password=12345 HTTP/1.1
Host: domain.com
```

但是如果我们使用 POST，参数将在 HTTP 请求的主体中传递，而不是在 URL 中。这将发生在浏览器和 web 服务器之间的幕后。

```
POST /login.php HTTP/1.1
Host: domain.com
username=user&password=12345
```

* * *

**GET 缓存**
GET 是用来在你阅读信息时显示在页面上的。浏览器将缓存 GET 请求的结果，如果再次发出相同的 GET 请求，浏览器将显示缓存的结果，而不是重新运行整个请求。

* * *

**REST——“RESTful”客户端服务器架构**

> 例如，HTTP 在动词(或“方法”)、URIs、互联网媒体类型、请求和响应代码等方面拥有非常丰富的词汇。REST 使用 HTTP 协议的这些现有特性，因此允许现有的分层代理和网关组件在网络上执行额外的功能，比如 HTTP 缓存和安全实施。

阅读“表述性状态转移”(REST):[http://en . Wikipedia . org/wiki/representative _ State _ Transfer # RESTful _ example:_ the _ World _ Wide _ Web](http://en.wikipedia.org/wiki/Representational_State_Transfer#RESTful_example:_the_World_Wide_Web)

* * *

**REST——“RESTful”Web 服务(API)**

> 它是一个资源集合，有四个定义的方面:
> web 服务的基础 URI，比如 http://example.com/resources/
> web 服务支持的数据的互联网媒体类型。这通常是 JSON、XML 或 YAML，但也可以是任何其他有效的互联网媒体类型。
> 使用 HTTP 方法的 web 服务支持的一组操作(例如，POST、GET、PUT 或 DELETE)。
> API 必须是超文本驱动的。[11]

[http://en . Wikipedia . org/wiki/representative _ State _ Transfer # RESTful _ web _ services](http://en.wikipedia.org/wiki/Representational_State_Transfer#RESTful_web_services)

## 结论

我希望你清楚地知道什么时候使用 GET，什么时候使用 POST。如果您仍然不确定或者想要检查 AJAX 调用的幕后发生了什么，请使用类似于 [Firebug NET Panel](http://www.jquery4u.com/testing/http-request-net-panel-httpfox-fiddler2/) 的工具来查看您的数据被发送到哪里(比如在头部)请求的类型。除此之外，祝阿贾克斯快乐！

## 分享这篇文章