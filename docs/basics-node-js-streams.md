# Node.js 流的基础知识

> 原文：<https://www.sitepoint.com/basics-node-js-streams/>

Node.js 本质上是异步和事件驱动的。因此，它非常擅长处理 I/O 绑定的任务。如果您正在开发一个执行 I/O 操作的应用程序，您可以利用 Node.js 中可用的流。因此，让我们详细探索流，并了解它们如何简化 I/O。

## 什么是溪流

流是 unix 管道，让您可以轻松地从源读取数据，并通过管道将其传输到目的地。简单地说，流就是一个`EventEmitter`并实现一些特殊的方法。根据实现的方法，流可以是可读的、可写的或双工的(既可读又可写)。可读流允许您从源读取数据，而可写流允许您将数据写入目标。

如果您已经使用过 Node.js，您可能会遇到流。例如，在基于 Node.js 的 HTTP 服务器中，`request`是可读流，`response`是可写流。你可能使用过`fs`模块，它可以让你处理可读和可写的文件流。

现在你知道了基础知识，让我们了解不同类型的流。在本文中，我们将讨论可读和可写的流。双工流超出了本文的范围。

## 可读流

可读流允许您从源中读取数据。来源可以是任何东西。它可以是文件系统中的一个简单文件，内存中的一个缓冲区，甚至是另一个流。由于流是`EventEmitters`，它们在不同的点发出几个事件。我们将使用这些事件来处理流。

### 从流中读取

从流中读取数据的最佳方式是侦听`data`事件并附加一个回调。当一块数据可用时，可读流发出一个`data`事件，您的回调执行。看看下面的片段:

```
var fs = require('fs');
var readableStream = fs.createReadStream('file.txt');
var data = '';

readableStream.on('data', function(chunk) {
    data+=chunk;
});

readableStream.on('end', function() {
    console.log(data);
});
```

函数调用`fs.createReadStream()`给你一个可读的流。最初，流处于静态。一旦你监听`data`事件并附加一个回调，它就开始流动。之后，大块数据被读取并传递给回调函数。流实现者决定发出`data`事件的频率。例如，一旦读取了几 KB 的数据，HTTP 请求可能会发出一个`data`事件。当你从一个文件中读取数据时，你可以决定一旦一行被读取就发出`data`事件。

当没有更多的数据要读取时(到达结尾)，流发出一个`end`事件。在上面的代码片段中，我们监听这个事件，以便在到达终点时得到通知。

还有另一种从 stream 读取的方法。您只需要在流实例上重复调用`read()`，直到每个数据块都被读取。

```
var fs = require('fs');
var readableStream = fs.createReadStream('file.txt');
var data = '';
var chunk;

readableStream.on('readable', function() {
    while ((chunk=readableStream.read()) != null) {
        data += chunk;
    }
});

readableStream.on('end', function() {
    console.log(data)
});
```

`read()`函数从内部缓冲区读取一些数据并返回。当没有要读取的内容时，它返回`null`。因此，在 while 循环中，我们检查`null`并终止循环。请注意，当可以从流中读取数据块时，会发出`readable`事件。

### 设置编码

默认情况下，从流中读取的数据是一个`Buffer`对象。如果你正在读字符串，这可能不适合你。因此，您可以通过调用`Readable.setEncoding()`在流上设置编码，如下所示。

```
var fs = require('fs');
var readableStream = fs.createReadStream('file.txt');
var data = '';

readableStream.setEncoding('utf8');

readableStream.on('data', function(chunk) {
    data+=chunk;
});

readableStream.on('end', function() {
    console.log(data);
});
```

在上面的代码片段中，我们将编码设置为`utf8`。因此，数据被解释为`utf8`，并作为字符串传递给回调函数。

### 平静的

管道是一种很好的机制，在这种机制中，您可以从源读取数据并写入目标，而无需自己管理流。看看下面的片段:

```
var fs = require('fs');
var readableStream = fs.createReadStream('file1.txt');
var writableStream = fs.createWriteStream('file2.txt');

readableStream.pipe(writableStream);
```

上面的代码片段利用`pipe()`函数将`file1`的内容写入`file2`。由于`pipe()`为您管理数据流，您不必担心数据流的快慢。这使得`pipe()`成为一个读写数据的好工具。您还应该注意到，`pipe()`返回目标流。因此，您可以很容易地利用这一点将多个流链接在一起。让我们看看如何！

### 链接

假设您有一个归档文件，并希望将其解压缩。有许多方法可以实现这一点。但是最简单和最干净的方法是使用管道和链条。看看下面的片段:

```
var fs = require('fs');
var zlib = require('zlib');

fs.createReadStream('input.txt.gz')
  .pipe(zlib.createGunzip())
  .pipe(fs.createWriteStream('output.txt'));
```

首先，我们从文件`input.txt.gz`创建一个简单可读的流。接下来，我们将这个流通过管道传输到另一个流`zlib.createGunzip()`中，以解压缩内容。最后，由于流可以链接，我们添加了一个可写的流，以便将未压缩的内容写入文件。

### 其他方法

我们讨论了可读流中的一些重要概念。以下是您需要了解的更多流方法:

1.  `Readable.pause()`–此方法暂停流。如果流已经在流动，它将不再发出`data`事件。数据将保存在缓冲区中。如果你在一个静态的(非流动的)流上调用这个函数，流开始流动，但是`data`事件不会被发出。
2.  `Readable.resume()`–恢复暂停的数据流。
3.  `readable.unpipe()`–这将从管道目标中删除目标流。如果传递了参数，它会阻止可读流通过管道进入特定的目标流。否则，所有目标流都将被删除。

## 可写流

可写流允许您将数据写入目标。像可读的流一样，这些也是`EventEmitters`并且在不同的点发出不同的事件。让我们看看可写流中可用的各种方法和事件。

### 写入流

要将数据写入可写流，您需要在流实例上调用`write()`。下面的代码片段演示了这种技术。

```
var fs = require('fs');
var readableStream = fs.createReadStream('file1.txt');
var writableStream = fs.createWriteStream('file2.txt');

readableStream.setEncoding('utf8');

readableStream.on('data', function(chunk) {
    writableStream.write(chunk);
});
```

上面的代码很简单。它只是从输入流中读取数据块，并使用`write()`写入目的地。此函数返回一个布尔值，表明操作是否成功。如果`true`，则写入成功，您可以继续写入更多数据。如果`false`被返回，这意味着出现了错误，此时你不能写任何东西。可写流将通过发出一个`drain`事件让您知道何时可以开始写入更多数据。

### 数据结尾

当你没有更多的数据要写时，你可以简单地调用`end()`来通知流你已经写完了。假设`res`是一个 HTTP 响应对象，您通常通过以下方式将响应发送到浏览器:

```
res.write('Some Data!!');
res.end('Ended.');
```

当调用`end()`并且刷新了每个数据块时，流会发出一个`finish`事件。请注意，在调用`end()`之后，您不能向流中写入内容。例如，以下内容将导致错误。

```
res.write('Some Data!!');
res.end();
res.write('Trying to write again'); //Error!
```

以下是与可写流相关的一些重要的`events`:

1.  `error`–发出表示写入/管道时发生错误。
2.  `pipe`–当可读流通过管道传输到可写流时，该事件由可写流发出。
3.  `unpipe`–在对可读流调用 unpipe 并阻止它通过管道进入目标流时发出。

## 结论

这都是关于流的基础知识。流、管道和链接是 Node.js 中最核心和最强大的特性。如果负责任地使用，流确实可以帮助您编写整洁和高性能的代码来执行 I/O。

你喜欢这篇文章吗？请通过评论让我们知道你的想法。

## 分享这篇文章