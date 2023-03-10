# 在 Ruby: Node.js 之外寻找

> 原文：<https://www.sitepoint.com/looking-outside-ruby-node-js/>

在这个,*关注 Ruby* 系列的最新文章中，我们将看看服务器端的 JavaScript 和 Node。

## 什么是节点？

Node 是一组允许 JavaScript 在浏览器之外运行的库，这里讨论的 JavaScript 是 Chrome 的 V8 引擎。它的主要目标是使构建网络客户端和服务器变得简单和容易。Node 由 Ryan Dahl 于 2009 年启动，目前该项目由他的雇主 Joyent 赞助。

## 为什么要用？

简而言之:速度和可扩展性。作为 Node 核心的 V8 JavaScript 引擎以其速度著称，Node 使用的“事件 I/O”方法使其具有可伸缩性，能够轻松处理许多并发连接。

## 事件输入输出

Node 采用了事件循环架构，其设计部分受到了 [Ruby 的事件机器](http://rubyeventmachine.com/)的影响。如果你不熟悉事件循环，它们在维基百科上被[定义为:](http://en.wikipedia.org/wiki/Event_loop)

> 在程序中等待和发送事件或消息的一种编程结构。它通过轮询一些内部或外部的“事件提供者”来工作，事件提供者通常会阻塞，直到事件到达，然后调用相关的事件处理程序(“调度事件”)…。事件循环几乎总是与消息发起者异步操作。

基本上，节点不断地检查事件的发生，无论是接收 HTTP 请求还是访问磁盘上的文件。当一个给定的事件触发时，它的事件处理回调函数被执行。

这种类型的事件处理在日常生活中经常发生。有多少次你给别人打电话，不得不留言让他们给你回电话？在你等待他们回电的时候，你可以自由地做其他事情。

Ryan Dahl 2009 年在 JSConf.eu 的演讲中使用的[幻灯片中的一个例子是关于数据库访问的:](http://s3.amazonaws.com/four.livejournal/20091117/jsconf.pdf)

```
var result = db.query("select * from T");
// use result
```

接下来要问的相关问题是:当你的软件查询数据库时，它在做什么？答案是:通常没什么。在事件循环中实现的相同代码可能如下所示:

```
db.query("select...", function(result) {
    // use result
});
```

程序可以直接返回到事件循环，而不必等待数据库查询完成。当*完成*时，事件循环将分派回调函数。

Node 在单线程中运行，就像 JavaScript 在浏览器的单线程中运行一样。因此，您应该努力确保节点代码中的所有 I/O 都是非阻塞的——您不希望通过进行一些 CPU 密集型计算或同步 I/O 来阻塞事件循环。幸运的是，Node 附带了一组核心模块，它们提供了一个非阻塞接口来访问文件和网络资源等内容。

## 装置

在 OS X 和 Linux 上可以找到节点源代码，在 Windows 上也可以下载 node.exe。您可以通过包管理器安装节点；在撰写本文时，OS X 已经有了 Debian、Ubuntu、openSUSE、Arch Linux、homebrew 和 macports 的软件包，Windows 上有了 [chocolatey](https://github.com/chocolatey/chocolatey/wiki) 。

如果您想在 OS X 或 Linux 上自己构建节点，您需要启动一个命令行并键入以下内容:

```
curl -O http://nodejs.org/dist/node-v0.4.12.tar.gz
tar xzvf node-v0.4.12.tar.gz
cd node-v0.4.12.tar.gz
./configure --prefix=/usr/local/node
make
make install
```

**NB** 在这里，我们将 Node 安装到 [`/usr/local`](http://hivelogic.com/articles/using_usr_local/) 中，但是，如果你喜欢从源代码构建包，你可以随意修改。

一旦安装了 Node，设置您的`$NODE_PATH`环境变量，以便 Node 知道在哪里寻找模块:

```
echo 'export NODE_PATH=/opt/node:/opt/node/lib/node_modules' >> ~/.bashrc # ~/.bash_profile or ~/.profile on some systems
```

如果`/usr/local/node/bin`不在您的`$PATH`中，您可以这样添加:

```
echo 'export PATH=$PATH:/opt/node/bin' >> ~/.bashrc # ~/.bash_profile or ~/.profile on some systems
```

然后键入以下内容以重新加载新的`$PATH`:

```
. ~/.bashrc # ~/.bash_profile or ~/.profile on some systems
```

## 编写代码

让我们从规范的“hello world”程序开始，这次是以 web 服务器的形式:

```
var http = require('http');

http.createServer(function(req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello, World!');
}).listen(4567, '127.0.0.1');
console.log('Server running at http://127.0.0.1:4567');
```

您可以将这段代码保存在一个类似于`server.js`的文件中，然后在终端中键入`node server.js`来运行它，或者在命令提示符下键入`node`以交互模式启动 node 相当于 Ruby 的`irb`——然后将代码复制并粘贴到终端中。在这段代码中，我们:

1.  使用`require`函数导入 http 模块
2.  调用 http 模块的`createServer`函数，并传递给它一个回调函数；每当节点接收到一个新的连接时，这个函数将被触发
3.  告诉节点我们希望服务器在哪个端口和 IP 地址上运行/监听
4.  将消息记录到控制台，以便我们知道我们的服务器已经启动并正在运行

还记得我们之前提到的节点的可伸缩性吗？

> “hello world”web 服务器…可以同时处理许多客户端连接。Node 告诉操作系统(通过 epoll、kqueue、/dev/poll 或 select)当建立新连接时应该通知它，然后它进入睡眠状态。如果有新的连接，它就会执行回调。每个连接只是一个小的堆分配。
> *—来自【http://nodejs.org/#about】T2*

## 发出和处理您自己的事件

无论您使用多少 Node 的核心模块，总有一天您需要发出和处理您自己的或者其他人的定制事件。Node 附带了一个 EventEmitter 类来帮助我们做到这一点。我们感兴趣的两个主要方法是`on`和`emit`。当连接和处理我们的自定义事件时，我们可以从 EventEmitter 继承，将这些方法附加到我们的代码中。让我们看一个简单的服务器来说明这一点:

```
var util = require('util'),
        EventEmitter = require('events').EventEmitter;

var Server = function() {
    console.log('init');
};

util.inherits(Server, EventEmitter);
var s = new Server();

s.on('error', function() {
    console.log('error...');
});

...

s.emit('error');
```

在本例中:

1.  我们使用`util`模块的`inherits`方法让我们的服务器类继承 EventEmitter
2.  我们通过调用现在继承的`on`方法为“错误”事件设置了一个回调函数
3.  在我们的服务器实例上调用`emit`将会触发错误回调函数

## 节点模块

节点使用 CommonJS 模块系统。CommonJS 的目标是在服务器上为 JavaScript 提供一个通用的 API，无论 JavaScript 解释器是什么都可以使用，所以在 Node 上运行的相同代码也可以在 Rhino 上不加修改地运行。CommonJS 还定义了如何使用`require`加载模块，以及在编写自己的模块时如何利用`exports`对象。

如果您查看前面的示例代码，您会发现我们使用了`require`来导入一些核心模块。CommonJS 规定，每个模块都可以访问`require`函数，并且`require`应该返回模块的导出 API(稍后将详细介绍)。当你给`require`一个模块的名字时，你可以给它一个“顶级”模块或者“相对”模块的名字，即模块*标识符*以`.`或者`..`开始。

“导出的 API”是什么意思？好吧，CommonJS 还声明每个模块都将拥有对`exports`对象的访问权，你通过显式地向它添加函数和变量来定义你的模块的 API 任何*没有*添加到`exports`的函数和变量对于模块来说都是私有的。

我们可以通过编写自己的模块`jedimindtrick`来说明这一点。因为 Node 使用每个模块一个文件的方法，所以将下面的代码保存在一个名为`jedimindtrick.js`的文件中:

```
// jedimindtrick.js
var droid1 = 'R2-D2',
    droid2 = 'C-3PO';

exports.mindTrick = function(d1, d2) {
    if (droid1 === d1 &amp;&amp; droid2 === d2) {
        console.log("These aren't the droids you're looking for");
    }
}

moveAlong = function() {
    console.log("Move along...move along");
}
```

然后，在命令行中，改变目录，这样你就在刚才保存`jedimindtrick.js`的同一个文件夹中，键入`node`并点击`<Enter>`来启动节点的交互模式，并键入以下内容:

```
var jedi = require('./jedimindtrick');
jedi.mindTrick('R2-D2', 'C-3PO');
jedi.moveAlong();
```

在这段代码中，我们将模块的相对路径传递给`require`，调用导出的函数`mindTrick`，然后尝试调用`moveAlong`函数。但是，由于这没有添加到我们模块中的`exports`对象，我们将导致一个`TypeError: Object [object Object] has no method 'moveAlong'`。

## 主办；主持

最后，简单说说主机。Node wiki 有一个[主机提供商](https://github.com/joyent/node/wiki/Node-Hosting)的列表。对 Ruby 程序员来说，好消息是 Heroku 提供了[节点托管。](http://devcenter.heroku.com/articles/node-js)

嗯，这是对 Node 的快速浏览。我们已经了解了什么是 Node 以及它提供的潜在优势，如何安装它，浏览了一些简短的代码示例，快速浏览了节点模块以及如何编写自己的模块。这绝不是一本全面的指南，但是如果您还没有看过 Node，希望它能鼓励您去看看。

## 进一步阅读

*   官方节点地点:[http://nodejs.org/](http://nodejs.org/)
*   GitHub 上的节点(包括节点 wiki):[https://github.com/joyent/node](https://github.com/joyent/node)
*   NPM–节点程序包管理器:[http://npmjs.org/](http://npmjs.org/)

## 分享这篇文章