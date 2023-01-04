# 使用 mod_gzip 和 Apache 压缩 Web 输出

> 原文：<https://www.sitepoint.com/web-output-mod-gzip-apache/>

**网页压缩并不是一项新技术，但最近它在 it 管理员和经理的心目中获得了更高的认可，因为它能产生快速的投资回报。大多数主要的 Web 服务器平台都有压缩扩展，但是在本文中，我将重点介绍开源的 Apache 和`mod_gzip`解决方案。**

##### GZIP 编码基础知识

GZIP 编码文档背后的想法非常简单。获取一个要传输到网络客户端的文件，并发送数据的压缩版本，而不是原始文件。根据文件的大小，压缩版本可以是原始文件大小的 50%到 20%。

在 Apache 中，这可以通过使用内容协商来实现，内容协商需要生成两套独立的 HTML 文件:一套用于能够处理 GZIP 编码的客户端，另一套用于不能处理的客户端。这个解决方案将 gzip 编码的文件发送给理解它们的客户端，但是不允许压缩动态生成的页面。

##### 更优雅的解决方案

更好的解决方案是使用`mod_gzip`，这是 Apache 可用的许多附加模块之一。我认为它是设计高性能 Web 服务器时被忽视的瑰宝之一。使用这个模块，配置的文件类型在被 Apache 的其他模块处理之后，在被发送到客户机之前，将使用 GZIP 编码进行压缩。生成的压缩数据减少了传输到客户机的字节数，而原始未压缩文档的结构或内容没有任何损失。

```
mod_gzip can be compiled into Apache as either a static or dynamic module -- I've chosen to compile it as a dynamic module in my own server. The advantage of using mod_gzip is that this method doesn't require anything to be done on the client side in order to make it work. As for the server side, all the server or site administrator has to do is:

```

*   编译模块，
*   编辑添加到 httpd.conf 文件中的适当配置指令，
*   启用 httpd.conf 文件中的模块，并
*   重新启动服务器。

不到 10 分钟，你就可以使用 GZIP 编码提供 HTML 文件。

##### 它是如何工作的

当从客户机收到请求时，Apache 通过注意客户机是否发送了“Accept-Encoding”HTTP 请求头来确定是否应该调用`mod_gzip`。如果客户端发送文件头(如下所示)，`mod_gzip`会在将所有配置的文件类型发送到客户端时压缩它们的输出。

```
Accept-encoding: gzip
```

这个客户机头向 Apache 声明，客户机将理解 GZIP 编码的文件。`mod_gzip`然后处理传出内容，并包括以下服务器响应头。

```
Content-Type: text/html 

Content-Encoding: gzip
```

这些服务器响应头声明从服务器返回的内容是 GZIP 编码的，但是当客户端应用程序扩展内容时，它应该被视为标准的 HTML 文件。这不仅适用于静态 HTML 文件，还适用于包含动态元素的页面，例如由服务器端 Includes (SSI)、PHP 和其他动态页面生成方法生成的页面。您还可以使用它来压缩级联样式表(CSS)和纯文本文件。我的 httpd.conf 文件为`mod_gzip`设置了以下配置:

```
mod_gzip_item_exclude         file       .js$ 

mod_gzip_item_exclude         mime       ^text/css$ 

mod_gzip_item_include         file       .html$ 

mod_gzip_item_include         file       .shtml$ 

mod_gzip_item_include         file       .php$ 

mod_gzip_item_include         mime       ^text/html$ 

mod_gzip_item_include         file       .txt$ 

mod_gzip_item_include         mime       ^text/plain$ 

mod_gzip_item_include         file       .css$ 

mod_gzip_item_include         mime       ^text/css$
```

我在压缩其他文件格式方面取得了有限的成功，主要是因为微软的 Internet Explorer 似乎在检查“内容编码”标题消息之前先检查“内容类型”标题消息。因此，假设您使用以下`mod_gzip`指令将您的服务器配置为对 PDF 文件进行 GZIP 编码:

```
mod_gzip_item_include         file       .pdf$ 

mod_gzip_item_include         mime       ^application/pdf$
```

这将在 Mozilla 和 Opera 中完美地工作，因为这些应用程序在将 GZIP 编码的内容传递给 PDF 阅读器之前对其进行解码(大多数人使用 Adobe Acrobat Reader)。

然而，Internet Explorer 只是将 GZIP 编码的内容直接传递给 PDF 阅读器。一旦这个问题在 MSIE 代码中得到纠正，你可能会看到更多的网络服务器提供更广泛的 GZIP 编码的内容。

##### 带宽节省

如您所见，GZIP 编码的文档可以节省大量带宽:

```
http://www.pierzchala.com/bio.html 

Uncompressed File Size:  3122 bytes 

http://www.pierzchala.com/bio.html 

Compressed File Size:  1578 bytes 

http://www.pierzchala.com/compress/homepage2.html   

Uncompressed File Size:  56279 bytes 

http://www.pierzchala.com/compress/homepage2.html   

Compressed File Size:  16286 bytes
```

作为一名服务器管理员，您可能会担心`mod_gzip`会给系统带来沉重的负担，因为它们会动态压缩文件。我想指出的是，这似乎与 [Slashdot](http://slashdot.org/) 的管理员无关，这是互联网上最繁忙的网络服务器之一，他们在非常高流量的环境中使用`mod_gzip`。

`mod_gzip` [项目页面位于 SourceForge](http://sourceforge.net/projects/mod-gzip/) 。你自己试试吧。

## 分享这篇文章