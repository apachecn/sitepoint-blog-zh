# 如何创建 Node.js 集群来加速应用程序

> 原文：<https://www.sitepoint.com/how-to-create-a-node-js-cluster-for-speeding-up-your-apps/>

[Node.js](https://nodejs.org/) 作为服务器端运行时环境越来越受欢迎，尤其是对于高流量的网站，正如[统计数据显示](http://w3techs.com/technologies/details/ws-nodejs/all/all)。此外，几个框架的可用性使它成为快速原型开发的良好环境。Node.js 有一个事件驱动的架构，利用一个非阻塞的 I/O API，允许异步处理请求。

Node.js 的一个重要但不太突出的特性是它的可伸缩性。事实上，这也是为什么[一些流量巨大的大公司正在将 Node.js 整合到他们的平台](https://github.com/joyent/node/wiki/Projects,-Applications,-and-Companies-Using-Node)(例如微软、雅虎、优步和沃尔玛)中，甚至将他们的服务器端运营完全转移到 Node.js(例如 PayPal、易贝和 Groupon)的主要原因。

每个 Node.js 进程都在一个线程中运行，默认情况下，它在 32 位系统上的内存限制是 512MB，在 64 位系统上是 1GB。虽然内存限制在 32 位系统上可以达到约 1GB，在 64 位系统上可以达到约 1.7 GB，但内存和处理能力仍然会成为各种进程的瓶颈。

Node.js 为扩展应用程序提供的优雅解决方案是将单个流程拆分成多个流程，用 Node.js 的术语来说就是 *workers* 。这可以通过集群模块来实现。集群模块允许您创建子进程(workers)，这些子进程与主节点进程(master)共享所有服务器端口。

在本文中，您将看到如何创建 Node.js 集群来加速您的应用程序。

### Node.js 集群模块:它是什么以及如何工作

集群是在父节点进程下运行的相似工作线程的池。使用`child_processes`模块的`fork()`方法生成工人。这意味着工作人员可以共享服务器句柄，并使用 IPC(进程间通信)与父节点进程通信。

主流程负责启动工人并控制他们。您可以在主流程中创建任意数量的工作线程。此外，请记住，默认情况下，传入的连接是以循环方式在工作线程之间分配的(Windows 除外)。实际上，还有另一种方法来分配传入的连接，我不会在这里讨论，它将分配交给操作系统(Windows 中的默认设置)。 [Node.js 文档](https://nodejs.org/api/cluster.html#cluster_cluster_schedulingpolicy)建议使用默认的循环方式作为调度策略。

虽然使用集群模块在理论上听起来很复杂，但实现起来非常简单。要开始使用它，必须将它包含在 Node.js 应用程序中:

```
var cluster = require('cluster);
```

一个集群模块多次执行同一个 Node.js 进程。因此，您需要做的第一件事是确定代码的哪一部分用于主流程，哪一部分用于工人流程。集群模块允许您按如下方式识别主流程:

```
if(cluster.isMaster) { ... }
```

主流程是您启动的流程，它反过来会初始化工作进程。为了在主进程中启动一个工作进程，我们将使用`fork()`方法:

```
cluster.fork();
```

该方法返回一个 worker 对象，该对象包含一些关于分叉的 worker 的方法和属性。我们将在下一节看到一些例子。

一个集群模块包含几个事件。与工人开始和结束时刻相关的两个常见事件是`online`和`exit`事件。`online`在工作者分叉并发送在线消息时发出。`exit`在工作进程死亡时发出。稍后，我们将看到如何使用这两个事件来控制工人的寿命。

现在，让我们把目前为止看到的所有东西放在一起，展示一个完整的工作示例。

## 例子

本节有两个例子。第一个是一个简单的应用程序，展示了如何在 Node.js 应用程序中使用集群模块。第二个是利用 Node.js 集群模块的 Express 服务器，这是我通常在大型项目中使用的产品代码的一部分。[这两个例子都可以从 GitHub](https://github.com/sitepoint-editors/node-cluster-tutorial) 下载。

### 如何在 Node.js 应用程序中使用集群模块

在第一个例子中，我们设置了一个简单的服务器，用一条包含处理请求的工作进程 ID 的消息来响应所有传入的请求。主进程派生出四个工作线程。在其中的每一个中，我们开始监听端口 8000 来接收请求。

实现我刚才描述的代码如下所示:

```
var cluster = require('cluster');
var http = require('http');
var numCPUs = 4;

if (cluster.isMaster) {
    for (var i = 0; i < numCPUs; i++) {
        cluster.fork();
    }
} else {
    http.createServer(function(req, res) {
        res.writeHead(200);
        res.end('process ' + process.pid + ' says hello!');
    }).listen(8000);
}
```

您可以在您的机器上测试这个服务器，方法是启动它(运行命令`node simple.js`)并访问 URL[http://127 . 0 . 0 . 1:8000/](http://127.0.0.1:8000/)。当接收到请求时，它们被一次一个地分配给每个工作者。如果有工作线程可用，它会立即开始处理请求；否则，它将被添加到队列中。

上面的例子中有几点效率不是很高。例如，想象一下如果一个工人因为某种原因去世了。在这种情况下，您失去了一个工作进程，如果同样的情况再次发生，您将最终得到一个没有工作进程来处理传入请求的主进程。另一个问题与工人数量有关。在您部署应用的系统中有不同数量的核心/线程。在上面提到的例子中，为了使用所有的系统资源，您必须手动检查每个部署服务器的规格，找出有多少线程可用，并在您的代码中更新它。在下一个例子中，我们将看到如何通过 Express 服务器使代码更有效。

### 如何开发高度可伸缩的 Express 服务器

Express 是 Node.js 最流行的 web 应用程序框架之一(如果不是最流行的话)。在 SitePoint 上，我们已经讨论过几次了。如果你有兴趣了解更多，我建议你阅读文章[用 Express 4 创建 RESTful APIs】和](https://www.sitepoint.com/creating-restful-apis-express-4/)[构建 Node.js 支持的聊天室 Web 应用:Express 和 Azure](https://www.sitepoint.com/build-node-js-powered-chatroom-web-app-expess-azure/) 。

这个[第二个例子](https://github.com/sitepoint-editors/node-cluster-tutorial/blob/master/express.js)展示了我们如何开发一个高度可伸缩的 Express 服务器。它还演示了如何迁移单个 process server，以利用具有少量代码行的集群模块。

```
var cluster = require('cluster');

if(cluster.isMaster) {
    var numWorkers = require('os').cpus().length;

    console.log('Master cluster setting up ' + numWorkers + ' workers...');

    for(var i = 0; i < numWorkers; i++) {
        cluster.fork();
    }

    cluster.on('online', function(worker) {
        console.log('Worker ' + worker.process.pid + ' is online');
    });

    cluster.on('exit', function(worker, code, signal) {
        console.log('Worker ' + worker.process.pid + ' died with code: ' + code + ', and signal: ' + signal);
        console.log('Starting a new worker');
        cluster.fork();
    });
} else {
    var app = require('express')();
    app.all('/*', function(req, res) {res.send('process ' + process.pid + ' says hello!').end();})

    var server = app.listen(8000, function() {
        console.log('Process ' + process.pid + ' is listening to all incoming requests');
    });
}
```

这个例子的第一个附加内容是使用 Node.js `os`模块获取 CPU 内核的数量。`os`模块包含一个`cpus()`函数，它返回一个 CPU 内核数组。使用这种方法，我们可以根据服务器规格动态确定要分叉的工作线程数量，以最大化利用率。

第二点也是更重要的一点是处理工人的死亡。当一个工人死亡时，集群模块发出一个`exit`事件。可以通过监听事件并在事件发出时执行回调函数来处理它。你可以通过写一个类似于`cluster.on('exit', callback);`的语句来实现。在回调中，我们派生一个新的 worker，以便保持预期的 worker 数量。这允许我们保持应用程序运行，即使有一些未处理的异常。

在这个例子中，我还为一个`online`事件设置了一个监听器，每当一个 worker 分叉并准备好接收传入的请求时，就会发出这个监听器。这可用于日志记录或其他操作。

### 性能比较

有几个工具可以对 API 进行基准测试，但是这里我使用 Apache 基准测试工具来分析使用集群模块如何影响应用程序的性能。

为了设置测试，[我开发了一个 Express 服务器](https://github.com/sitepoint-editors/node-cluster-tutorial/blob/master/express.js)，它有一个路由和一个路由回调。在回调中，执行一个虚拟操作，然后返回一条短消息。有两个版本的服务器:一个没有工作进程，所有的事情都发生在主进程中，另一个有 8 个工作进程(因为我的机器有 8 个内核)。下表显示了合并集群模块如何增加每秒处理的请求数。

| 并发连接 | one | Two | four | eight | Sixteen |
| --- | --- | --- | --- | --- | --- |
| **单流程** | Six hundred and fifty-four | Seven hundred and eleven | Seven hundred and eighty-three | Seven hundred and seventy-six | Seven hundred and fifty-four |
| **8 名工人** | Five hundred and ninety-four | One thousand one hundred and ninety-eight | Two thousand one hundred and ten | Three thousand and ten | Three thousand and twenty-four |

(每秒处理的请求数)

## 高级操作

虽然使用集群模块相对简单，但是还可以使用 workers 执行其他操作。例如，您可以实现(几乎！)使用集群模块使应用程序的停机时间为零。我们稍后将看到如何执行这些操作。

### 主人和工人之间的交流

有时，您可能需要从主服务器向工作服务器发送消息，以分配任务或执行其他操作。作为回报，工人可能需要通知主人任务已经完成。为了监听消息，应该在主进程和工作进程中都设置一个`message`事件的事件监听器:

```
worker.on('message', function(message) {
    console.log(message);
});
```

`worker`对象是由`fork()`方法返回的引用。要在工作线程中监听来自主线程的消息:

```
process.on('message', function(message) {
    console.log(message);
});
```

消息可以是字符串或 JSON 对象。要从主服务器向特定的工作服务器发送消息，您可以编写如下所示的代码:

```
worker.send('hello from the master');
```

类似地，要从一个工人向主人发送消息，您可以写:

```
process.send('hello from worker with id: ' + process.pid);
```

在 Node.js 中，消息是通用的，没有特定的类型。因此，将消息作为 JSON 对象发送是一种很好的做法，其中包含一些关于消息类型、发送者和内容本身的信息。例如:

```
worker.send({
    type: 'task 1',
    from: 'master',
    data: {
        // the data that you want to transfer
    }
});
```

这里需要注意的重要一点是消息事件回调是异步处理的。没有明确的执行顺序。[你可以在 GitHub](https://github.com/sitepoint-editors/node-cluster-tutorial/blob/master/communication.js) 上找到一个完整的工长与工人交流的例子。

### 零停机时间

使用工人可以实现的一个重要结果是(几乎)零停机时间服务器。在主进程中，您可以在对应用程序进行更改之后，一次终止和重新启动一个工作进程。这允许您在加载新版本的同时运行旧版本。

为了能够在运行时重启应用程序，您必须记住两点。首先，主进程一直运行，只有工作进程被终止并重新启动。因此，保持您的主进程简短并且只负责管理工人是很重要的。

其次，您需要以某种方式通知主进程它需要重新启动工作进程。有几种方法可以做到这一点，包括用户输入或观察文件的变化。后者效率更高，但是您需要在主进程中确定要监视的文件。

对于重启你的工人，我的建议是先尝试安全关闭；然后，如果他们没有安全地终止，强迫杀死他们。您可以通过向工作人员发送一条`shutdown`消息来执行前者，如下所示:

```
workers[wid].send({type: 'shutdown', from: 'master'});
```

并在工作消息事件处理程序中启动安全关闭:

```
process.on('message', function(message) {
    if(message.type === 'shutdown') {
        process.exit(0);
    }
});
```

要对所有工作线程执行此操作，您可以使用集群模块的`workers`属性，该属性保存对所有正在运行的工作线程的引用。我们还可以将所有任务包装在主进程中的一个函数中，当我们想要重新启动所有工作进程时，就可以调用这个函数。

```
function restartWorkers() {
    var wid, workerIds = [];

    for(wid in cluster.workers) {
        workerIds.push(wid);
    }

    workerIds.forEach(function(wid) {
        cluster.workers[wid].send({
            text: 'shutdown',
            from: 'master'
        });

        setTimeout(function() {
            if(cluster.workers[wid]) {
                cluster.workers[wid].kill('SIGKILL');
            }
        }, 5000);
    });
};
```

我们可以从集群模块中的`workers`对象获得所有正在运行的工作者的 ID。该对象保存对所有正在运行的工作线程的引用，并在工作线程终止和重新启动时动态更新。首先，我们将所有正在运行的工作者的 ID 存储在一个`workerIds`数组中。这样，我们可以避免重新启动新分叉的工人。

然后，我们要求每个工人安全关机。如果 5 秒钟后工作线程仍然在运行，并且它仍然存在于`workers`对象中，那么我们调用工作线程上的`kill`函数来强制它关闭。[你可以在 GitHub](https://github.com/sitepoint-editors/node-cluster-tutorial/blob/master/zero_downtime.js) 上找到一个实用的例子。

## 结论

Node.js 应用程序可以使用集群模块进行并行化，以便更有效地使用系统。使用几行代码就可以同时运行多个进程，这使得迁移相对容易，因为 Node.js 处理了困难的部分。

正如我在性能比较中所展示的，通过以更有效的方式利用系统资源，应用程序性能有可能得到显著的提高。除了性能，您还可以通过在应用程序运行时重新启动工作线程来增加应用程序的可靠性和正常运行时间。

也就是说，当考虑在应用程序中使用集群模块时，您需要小心。建议群集模块主要用于 web 服务器。在其他情况下，您需要仔细研究如何在工人之间分配任务，以及如何在工人和主人之间有效地交流进展。即使对于 web 服务器，在对应用程序进行任何更改之前，也要确保单个 Node.js 进程是一个瓶颈(内存或 CPU ),因为您的更改可能会引入错误。

最后一件事，Node.js 网站有一个很棒的集群模块的[文档。所以，一定要去看看！](https://nodejs.org/api/cluster.html "Cluster module documentation")

## 分享这篇文章