# Node.js 流简介

> 原文：<https://www.sitepoint.com/introduction-to-streams/>

可扩展性。大数据。实时。这些是 web 应用程序在现代万维网中必须面对的一些挑战。这就是 Node.js 及其非阻塞 I/O 模型发挥作用的地方。本文将向您介绍 Node 最强大的数据密集型计算 API 之一， *streams* 。


## 为什么使用流？

让我们考虑下面的例子:

```
var http = require('http')
   , fs = require('fs')
   ;

var server = http.createServer(function (req, res) {
  fs.readFile(__dirname + '/data.txt', function (err, data) {
    res.end(data);
  });
});

server.listen(8000);
```

这段代码运行良好。它没有任何问题，除了 Node.js
在将数据发送回客户端之前缓冲了 *data.txt* 的全部内容。随着客户端请求的增加，您的应用程序可能会开始消耗大量内存。此外，客户端需要等待服务器应用程序读取整个文件，从而导致延迟增加。

让我们来看另一个例子:

```
var http = require('http')
  , fs = require('fs')
  ;

var server = http.createServer(function (req, res) {
  var stream = fs.createReadStream(__dirname + '/data.txt');
  stream.pipe(res);
});
server.listen(8000);
```

这里，为了克服可伸缩性问题，我们使用 streams API。使用流对象可确保从磁盘读取数据时， *data.txt* 一次一个块地发送给客户端，无需服务器缓冲和客户端等待时间。

## 什么是流？

流可以定义为连续的数据流，当数据进入(或流出)时，可以对其进行异步操作。在 Node.js 中，流可以是可读或可写的。一个[可读流](http://nodejs.org/api/stream.html#stream_readable_stream)是一个 [`EventEmitter`](http://nodejs.org/api/events.html) 对象，它在每次接收到一大块数据时发出`data`事件。在我们前面的例子中，一个可读的流已经被用来通过管道把一个文件的内容传送到 HTTP 客户端。当流到达我们文件的末尾时，它发出一个`end`事件，表明不会再有`data`事件发生。此外，可读流可以暂停和恢复。

另一方面，可写流接受数据流。这种类型的流也继承自`EventEmitter`对象，并实现了两个方法:`write()`和`end()`。第一种方法将数据写入缓冲区，如果数据已被正确刷新，则返回`true`，如果缓冲区已满，则返回`false`(在这种情况下，数据将稍后发送)。`end()`方法简单地表明流已经结束。

## 您的第一个流应用程序

让我们仔细看看流。为此，我们将构建一个简单的文件上传应用程序。首先，我们需要构建一个使用可读流读取文件并将数据传输到特定目的地的客户端。在管道的另一端，我们将实现一个使用可写流保存上传数据的服务器。

先说客户。我们从导入 HTTP 和文件系统模块开始。

```
var http = require('http')
  , fs = require('fs');
```

然后，我们定义我们的 HTTP 请求。

```
var options = {
  host: 'localhost'
  , port: 8000
  , path: '/'
  , method: 'POST'
};
var req = http.request(options, function(res) {
  console.log(res.statusCode);
});
```

现在我们有了我们的`request`，我们创建一个可读的流来读取文件并将内容通过管道传输到`request`对象。

```
var readStream = fs.ReadStream(__dirname + &quot;/in.txt&quot;);
readStream.pipe(req);
```

一旦流读取完所有数据，我们就关闭与服务器的连接，调用我们的`request`的`end()`方法。

```
readStream.on('close', function () {
  req.end();
  console.log(&quot;I finished.&quot;);
});
```

### 服务器

正如我们对客户端所做的那样，我们从导入 Node.js 模块开始。然后，我们创建一个新的可写流，将数据保存到一个文本文件中。

```
var http = require('http')
  , fs = require('fs');

var writeStream = fs.createWriteStream(__dirname + &quot;/out.txt&quot;);
```

为了让我们的客户端应用程序上传文件，我们必须创建一个新的 web 服务器对象。当数据来自`request`对象时，服务器调用我们的流并将缓冲区刷新到输出文件。

```
var server = http.createServer(function (req, res) {
  req.on('data', function (data) {
    writeStream.write(data);
  });
  req.on('end', function() {
    writeStream.end();
    res.statusCode = 200;
    res.end(&quot;OK&quot;);
  });
});
server.listen(8000);
```

请注意，`createServer()`返回的`req`和`res`对象分别是可读流和可写流。我们可以监听`data`事件，并在所有处理结束后将结果返回给客户端。

## 结论

本文介绍了 Node.js 最强大的工具之一 streams API。在接下来的几周里，我们将深入流的世界，探索 Node.js 内置的所有不同类型，以及第三方流。

## 分享这篇文章