# 在 Node.js 中创建 HTTP 服务器

> 原文：<https://www.sitepoint.com/creating-a-http-server-in-node-js/>

在我的上一篇文章中，我介绍了最基本的 Node.js 程序。虽然 Hello World 程序不错，但 Node.js 更广为人知的是创建高度可伸缩的服务器应用程序。本文介绍一个构建在 Node.js 之上的简单 HTTP 服务器

## 运行服务器

首先创建一个名为“web_server.js”的新文件。将以下代码插入文件并保存。

```
var http = require("http");
var server = http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/html"});
  response.write("<!DOCTYPE "html">");
  response.write("<html>");
  response.write("<head>");
  response.write("<title>Hello World Page</title>");
  response.write("</head>");
  response.write("<body>");
  response.write("Hello World!");
  response.write("</body>");
  response.write("</html>");
  response.end();
});

server.listen(80);
console.log("Server is listening");
```

要启动服务器，请键入如下所示的命令。如果一切正常，您将看到服务器正在监听的消息。注意，示例服务器试图绑定到端口 80，即标准的 HTTP 端口。如果这个端口已经被使用，或者在您的机器上受到限制，您将遇到一个错误。

```
node web_server.js

```

下一步是使用 web 浏览器连接到服务器。启动您选择的浏览器，并将其定向到以下链接之一。在网络术语中，`localhost`(其 IP 地址为 127.0.0.1)指的是您当前使用的机器。你的浏览器应该说“你好，世界！”。

```
http://localhost
http://127.0.0.1

```

## 服务器如何工作

现在服务器已经启动并运行了，是时候分析代码了。首先要注意的是第 1 行对`require()`的调用。Node.js 为大型开发者社区提供了一个简单的模块系统。Node.js 程序可以使用`require()`方法加载单个模块。虽然必须下载许多模块，但是 Node.js 安装中包含了一些模块，比如`[http](http://nodejs.org/api/http.html "HTTP Node.js")`。

在第 2 行，HTTP 服务器是使用`http`模块的`createServer()`方法创建的。像大多数 Node.js 函数一样，`createServer()`接受一个回调函数作为参数。每当服务器接收到一个新的请求时，就会执行这个回调函数。

回调函数有两个参数，`request`和`response`。`request`对象包含关于客户端请求的信息，比如 URL、HTTP 头等等。类似地，`response`对象用于将数据返回给客户端。

回调函数通过调用`response.writeHead()`方法开始。这个方法将一个 [HTTP 状态码](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html "HTTP/1.1: Status Code Definitions")和一组[响应头](http://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Responses "List of HTTP header fields - Wikipedia")发送回客户端。状态代码用于指示请求的结果。比如大家之前都遇到过 404 错误，表示找不到某个页面。示例服务器返回代码 200，表示成功。

除了状态代码，服务器还返回许多 HTTP 头，这些头定义了响应的参数。如果不指定头，Node.js 将隐式地为您发送它们。示例服务器只指定了`Content-Type`头。这个特殊的头定义了响应的 [MIME 类型](http://en.wikipedia.org/wiki/MIME_type "Internet media type - Wikipedia")。在 HTML 响应的情况下，MIME 类型是“文本/html”。

接下来，服务器执行几次对`response.write()`的调用。这些调用用于编写 HTML 页面。默认情况下，使用 UTF-8 字符编码。从技术上讲，所有这些调用都可以合并成一个调用来提高性能。然而，对于这样一个微不足道的例子，为了代码的可读性，牺牲了性能。

编写完 HTML 页面后，调用`response.end()`方法。通过调用这个方法，我们告诉服务器已经发送了响应头和响应体，并且已经完成了请求。示例服务器不带参数调用`end()`。然而，`end()`也可以像`write()`一样被调用，假设只需要一个调用。

对第 15 行上的`listen()`的调用导致服务器绑定到一个端口并监听传入的连接。计算机有成千上万个端口，这些端口充当通信端点。为了连接到服务器，客户端必须确切地知道服务器正在监听哪个端口。端口由端口号标识，HTTP 服务器通常监听端口 80。

## 结论

本文展示了一个非常基本的 HTTP 服务器。在当前状态下，服务器只能返回一个 HTML 页面。在接下来的几周里，我们将深入 Node.js 的世界，探索更多的特性，比如从文件系统中读取 web 页面和加入 HTTP 认证。

 **如果你喜欢这篇文章，你会想要了解 SitePoint 最新系列的印刷和电子书。第一个标题是 Don Nguyen 的 Node.js 请访问[网站了解更多信息！](https://www.sitepoint.com/premium/library)** 

## **分享这篇文章**