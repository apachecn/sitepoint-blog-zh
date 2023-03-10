# 在 Node.js 中发出 HTTP 请求

> 原文：<https://www.sitepoint.com/making-http-requests-in-node-js/>

在以前的文章中，我主要关注于[创建 Node.js 服务器](https://www.sitepoint.com/build-a-simple-web-server-with-node-js/ "Creating a HTTP Server in Node.js")来处理 HTTP 请求。本文通过向您展示如何从 Node.js 应用程序发出 HTTP 请求，反过来看待这个问题。你可能会问自己为什么要这么做。我马上想到了两个应用程序——[网络抓取](http://en.wikipedia.org/wiki/Web_scraping "Web scraping - Wikipedia")和[代理](http://en.wikipedia.org/wiki/Proxy_server "Proxy server - Wikipedia")。抓取器是下载网页并通过程序从网页中提取信息的软件。代理服务器充当中介，将客户端请求转发到其他服务器并返回响应。

## 请求模块

在 Node.js 中创建 HTTP 请求最简单的方法是使用 [`request`](https://github.com/mikeal/request "mikeal/request") 模块。由 [Mikeal Rogers](https://github.com/mikeal "Mikeal Rogers") 编写的`request`允许你发出所有类型的 HTTP 请求，包括`GET`、`POST`、`PUT`和`DELETE`。它的灵活性使得`request`模块非常适合与[RESTful API](http://en.wikipedia.org/wiki/Representational_state_transfer "Representational state transfer - Wikipedia")交互。您可以使用下面的 [npm](https://npmjs.org/ "npm") 命令安装`request`。

```
npm install request

```

安装完`request`之后，创建一个新的 Node.js 文件，包含如下所示的代码。示例的第一行用于将`request`导入到程序中。发出 HTTP 请求就像调用`request()`函数一样简单。在这个例子中，我们请求下载 JSPro 主页。`request()`的第二个参数当然是回调函数。

```
var request = require("request");

request("http://www.sitepoint.com", function(error, response, body) {
  console.log(body);
});
```

`request()`回调函数有三个参数——`error`、`response`和`body`。顾名思义，`error`包含了关于 HTTP 请求过程中发生的任何问题的信息。`response`参数是一个包含从调用返回的各种数据的对象，包括状态代码、标题等等。第三个回调参数`body`是响应的主体。在前面的例子中，`body`包含了 JSPro 主页的源代码。

`request`模块也可以与[文件系统模块](https://www.sitepoint.com/accessing-the-file-system-in-node-js/ "Accessing the File System in Node.js")结合使用，以流式传输文件的 HTTP 请求。下面的例子再次获取 JSPro。但是，它没有在控制台中显示源代码，而是被写入一个名为“jspro.htm”的文件中。

```
var request = require("request");
var fs = require("fs");

request("http://www.sitepoint.com").pipe(fs.createWriteStream("jspro.htm"));
```

### 参数化请求

前面的例子已经为 URL 创建了简单的`GET`请求。然而，我们可以通过将一个配置对象作为第一个参数传递给`request()`来修改 HTTP 请求。以下示例使用更强大的语法再次下载 JSPro。请注意众多的配置参数。`uri`和`method`参数告诉`request`向 JSPro 发送一个`GET`请求。

```
var request = require("request");

request({
  uri: "http://www.sitepoint.com",
  method: "GET",
  timeout: 10000,
  followRedirect: true,
  maxRedirects: 10
}, function(error, response, body) {
  console.log(body);
});
```

`timeout`参数告诉`request`在放弃之前等待 10，000 毫秒(10 秒)的响应。最后两个参数处理以下重定向。许多页面在用户不知情的情况下在幕后执行重定向。`followRedirect`和`maxRedirects`参数指示`request`跟踪多达 10 个 3 *xx* HTTP 重定向。另外，请注意`request()`支持这里没有显示的许多其他参数。

### 提交表单数据

如前所述，`request`支持的不仅仅是`GET`请求。一个非常常见的操作是使用`POST`请求提交表单数据。下面的例子使用`request()`的`form`参数发送用户名作为`POST`的一部分。然后，PHP 页面通过用名字问候用户来响应。

```
var request = require("request");

request({
  uri: "http://www.cjihrig.com/development/php/hello_form.php",
  method: "POST",
  form: {
    name: "Bob"
  }
}, function(error, response, body) {
  console.log(body);
});
```

### 处理饼干

许多网站使用 [cookies](https://www.sitepoint.com/how-to-deal-with-cookies-in-javascript/ "How to Deal with Cookies in JavaScript") 跟踪各种用户交互。Cookies 是在每个 HTTP 事务中在客户机和服务器之间来回传递的小块数据。为了方便使用 cookies，`request`提供了`jar()`和`cookie()`方法。使用`cookie()`方法创建单独的 cookies。在以下示例的第 3 行，创建了一个指定用户名的 cookie。然后将 cookie 添加到第 2 行创建的 cookie jar 中。当发出 HTTP 请求时，使用`jar`参数将 cookie jar 发送到服务器。

```
var request = require("request");
var jar = request.jar();
var cookie = request.cookie("name=John");

jar.add(cookie);
request({
  uri: "http://www.cjihrig.com/development/php/hello_cookies.php",
  method: "GET",
  jar: jar
}, function(error, response, body) {
  console.log(body);
});
```

当收到响应时，用户将被问候为 John，这个名字存储在 cookie 中。cookie jar 也会随着服务器所做的任何更改而更新。如果您在请求前后打印出 cookie jar，您会看到服务器将 cookie 的值从`"John"`更改为`"Stranger"`。同一个 cookie jar 可以被传递给连续的 HTTP 请求，以便模拟真实用户与站点的交互。

## 结论

本文介绍了简单而强大的`request`模块。由于这篇文章并不全面，我建议至少浏览一下文档，看看`request`还能做什么。请继续关注下一篇文章，这篇文章将更深入地介绍使用`request`进行网络抓取。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start JavaScript](https://learnable.com/books/jump-start-javascript?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。
*对这篇文章的评论是封闭的。有关于 JavaScript 的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？*

## 分享这篇文章