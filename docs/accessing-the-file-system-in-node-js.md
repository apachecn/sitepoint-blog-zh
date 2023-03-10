# 在 Node.js 中访问文件系统

> 原文：<https://www.sitepoint.com/accessing-the-file-system-in-node-js/>

多年来，JavaScript 对文件系统的访问非常有限。当然，JavaScript 的大部分时间都存在于浏览器中。对于 web 脚本语言来说，访问文件系统被认为是一个主要的安全风险。前端开发人员被迫使用 cookies、 [web 存储、](https://www.sitepoint.com/an-overview-of-the-web-storage-api/ "An Overview of the Web Storage API - SitePoint")、 [ActiveX、](http://msdn.microsoft.com/en-us/library/aa242706(v=vs.60).aspx "FileSystemObject Object - MSDN")、Flash 和其他技术。HTML5 带来了[文件系统 API](http://www.html5rocks.com/en/tutorials/file/filesystem/ "Exploring the FileSystem APIs - HTML5 Rocks") ，但是在 Chrome 之外，它仍然[不受支持。随着 Node.js 的出现，JavaScript 开始成为合法的服务器端语言。在服务器上，文件系统访问是经常发生的，这使得 API 的思想变得不那么重要。
](http://caniuse.com/filesystem "Can I use Filesystem & FileWriter API")

## 文件系统模块

默认情况下，Node.js 安装附带文件系统模块`[fs](http://nodejs.org/api/fs.html "File System Node.js")`。在很大程度上，`fs`只是为标准文件操作提供了一个包装器。以下示例使用文件系统模块将文件内容读入内存。首先，在第 1 行导入文件系统模块。在第 3 行，`exists()`函数判断文件“foo.txt”是否存在。`exists()`回调函数采用一个布尔参数来指定文件是否存在。从那里，`stat()`函数被用来确定文件的字节长度。这些信息很重要，因此我们知道要读取多少数据。

接下来，我们使用`open()`打开文件。`r`参数表示文件正被打开以供读取。`open()`回调函数提供了一个[文件描述符](http://en.wikipedia.org/wiki/File_descriptor "File descriptor - Wikipedia")、`fd`用于访问新打开的文件。在回调函数中，我们定义了一个缓冲区来保存文件的内容。注意，缓冲区被初始化为文件的大小，它存储在`stats.size`中。接下来，使用`read()`功能将文件读入缓冲区。缓冲区现在包含从文件中读取的原始数据。为了显示数据，我们必须首先将其转换为 UTF-8 编码的字符串。最后，文件内容被打印到控制台，文件被关闭。

```
var fs = require("fs");
var fileName = "foo.txt";

fs.exists(fileName, function(exists) {
  if (exists) {
    fs.stat(fileName, function(error, stats) {
      fs.open(fileName, "r", function(error, fd) {
        var buffer = new Buffer(stats.size);

        fs.read(fd, buffer, 0, buffer.length, null, function(error, bytesRead, buffer) {
          var data = buffer.toString("utf8", 0, buffer.length);

          console.log(data);
          fs.close(fd);
        });
      });
    });
  }
});
```

### 同步功能

浏览文档时，您可能会注意到许多函数都以`Sync`结尾。这些表示同步函数——在 Node.js 的回调驱动世界中有点罕见。例如，用 Node.js 编写的简单批处理脚本可能不需要担心性能最大化。同步功能对于在程序初始化期间加载某些文件也很有用。然而，在主动服务器应用程序中，同步功能可能会因停止节点的单线程执行而严重影响性能。

以下示例显示了如何将文件同步和异步读入内存。至少可以说，我们前面的读取文件的例子有点复杂。这一次，我们的示例使用`readFile()`函数在一次函数调用中读取整个文件。`readFile()`的前两个参数是文件名及其编码。按照 Node.js 约定，最后一个参数是回调函数。回调函数的参数提供错误信息和文件内容。

```
var fs = require("fs");

fs.readFile("foo.txt", "utf8", function(error, data) {
  console.log(data);
});
```

下面的例子使用`readFileSync()`同步执行相同的任务。同步代码可读性稍强，但不提供与异步代码相同的可伸缩性。

```
var fs = require("fs");
var data = fs.readFileSync("foo.txt", "utf8");

console.log(data);
```

### 观看文件

文件系统模块允许程序监视对特定文件的修改。这在像 [nodemon](https://github.com/remy/nodemon "remy/nodemon") 这样的程序中非常有用，当程序的源代码被修改时，它会自动重启程序。以下示例监视名为“foo.txt”的文件。当文件被修改时，事件的类型被打印到控制台。

```
var fs = require("fs");
var fileName = "foo.txt";

fs.watch(fileName, {
  persistent: true
}, function(event, filename) {
  console.log(event + " event occurred on " + filename);
});
```

`watch()`函数有三个参数。第一个参数是要监视的文件的名称。第二个参数是可选的，提供配置设置。如果存在，第二个参数应该是一个包含名为`persistent`的布尔值的对象。如果`true`，`persistent`阻止程序终止。如果省略第二个参数，则默认为`true`。最后一个参数是修改目标文件时触发的回调。回调传递事件类型(更改、重命名等)。)和文件的名称。值得注意的是`watch()`依赖于底层的操作系统，可能无法在每个系统上工作。如果`watch()`不可用，较慢的`watchFile()`可以作为备用。

## 结论

本文在很高的层次上介绍了 Node.js 文件系统模块。该模块包含 50 多个不同的函数，这对于一篇文章来说显然太多了。例如，本文只涉及了读取文件，而完全忽略了写入文件。我鼓励您浏览模块文档以获得更深入的理解。请记住，使用同步函数时要非常小心！

## 分享这篇文章