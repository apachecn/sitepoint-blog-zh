# Node.js 事件循环:概念和代码开发人员指南

> 原文：<https://www.sitepoint.com/node-js-event-loop-guide/>

任何编程语言中的异步都是困难的。像并发性、并行性和死锁这样的概念甚至让最有经验的工程师也不寒而栗。异步执行的代码是不可预测的，当有 bug 时很难跟踪。这个问题是不可避免的，因为现代计算有多个核心。CPU 的每个内核都有一个热量限制，没有什么比这更快了。这给开发人员带来了压力，他们需要编写有效的代码来利用硬件。

JavaScript 是单线程的，但是这限制了 Node 利用现代架构吗？由于其固有的复杂性，最大的挑战之一是处理多线程。启动新线程和管理线程之间的上下文切换是非常昂贵的。操作系统和程序员都必须做大量的工作来交付一个有许多边缘情况的解决方案。在本文中，我将向您展示 Node 如何通过事件循环来处理这种困境。我将探索 Node.js [事件循环](https://www.sitepoint.com/useful-built-in-node-js-apis/)的每个部分，并演示它是如何工作的。Node 的“杀手级应用”特性之一就是这个循环，因为它以一种全新的方式解决了一个难题。

## 什么是事件循环？

事件循环是一个单线程、非阻塞、异步并发循环。对于没有计算机科学学位的人来说，想象一个进行数据库查询的 web 请求。单线程一次只能做一件事。它不再等待数据库响应，而是继续选取队列中的其他任务。在事件循环中，主循环展开调用堆栈，不等待回调。因为循环不会阻塞，所以可以自由地同时处理多个 web 请求。多个请求可以同时排队，这使得它是并发的。循环并不等待从一个请求开始的所有事情都完成，而是在回调到来时无阻塞地进行。

循环本身是半无限的，这意味着如果调用堆栈或回调队列为空，它可以退出循环。可以把调用栈想象成在循环轮询更多工作之前展开的同步代码，就像`console.log`一样。Node 在后台使用 libuv 来轮询操作系统，以获得来自传入连接的回调。

您可能想知道，为什么事件循环在单线程中执行？对于每个连接所需的数据，线程占用的内存相对较多。线程是不断旋转的操作系统资源，这不会扩展到成千上万的活动连接。

多线程通常也会使故事变得复杂。如果回调返回数据，它必须将上下文封送回执行线程。线程之间的上下文切换很慢，因为它必须像调用堆栈或局部变量一样同步当前状态。当多个线程共享资源时，事件循环会粉碎错误，因为它是单线程的。单线程循环减少了线程安全边缘情况，可以更快地进行上下文切换。这才是循环背后真正的天才。它有效地利用了连接和线程，同时保持了可伸缩性。

够理论；是时候看看这在代码中是什么样子了。随意跟随 REPL 或者[下载源代码](https://github.com/beautifulcoder/event-loop-playground)。

## 半无限循环

事件循环必须回答的最大问题是该循环是否是活的。如果是这样，它会计算出在回调队列上要等待多长时间。在每次迭代中，循环都会展开调用堆栈，然后进行轮询。

下面是一个阻塞主循环的示例:

```
setTimeout(
  () => console.log('Hi from the callback queue'),
  5000); // Keep the loop alive for this long

const stopTime = Date.now() + 2000;
while (Date.now() < stopTime) {} // Block the main loop 
```

如果运行这段代码，请注意循环被阻塞了两秒钟。但是该循环保持活动状态，直到回调在五秒钟后执行。一旦主循环解除阻塞，轮询机制就会计算出它等待回调的时间。当调用堆栈展开并且不再有回调时，该循环终止。

## 回调队列

现在，当我阻塞主循环然后安排回调时会发生什么？一旦循环被阻塞，它不会将更多的回调放到队列中:

```
const stopTime = Date.now() + 2000;
while (Date.now() < stopTime) {} // Block the main loop

// This takes 7 secs to execute
setTimeout(() => console.log('Ran callback A'), 5000); 
```

这一次，循环保持活动状态 7 秒钟。事件循环的简单性是愚蠢的。它无法知道将来会有什么排队。在真实的系统中，传入的回调被排队并执行，因为主循环可以自由轮询。当事件循环被解除阻塞时，它将依次经历几个阶段*。因此，要想在关于循环的面试中胜出，就要避免“事件发射器”或“反应模式”这样的花哨术语。这是一个简单的单线程循环，并发的，非阻塞的。*

 *## 带有 async/await 的事件循环

为了避免阻塞主循环，一个想法是将同步 I/O 包装在 async/await 周围:

```
const fs = require('fs');
const readFileSync = async (path) => await fs.readFileSync(path);

readFileSync('readme.md').then((data) => console.log(data));
console.log('The event loop continues without blocking...'); 
```

在`await`之后的任何东西都来自回调队列。代码读起来像同步阻塞代码，但它不阻塞。注意 async/await 使`readFileSync` *成为可用的*，这使它脱离了主循环。把在`await`之后发生的任何事情都看作是通过回调实现的非阻塞。

完全公开:以上代码仅用于演示目的。在实际代码中，我推荐使用`fs.readFile`，它触发一个回调，可以包装在一个承诺周围。总体意图仍然有效，因为这将阻塞 I/O 从主循环中移除。

## 更进一步

如果我告诉你事件循环不仅仅是调用堆栈和回调队列，会怎么样？如果事件循环不仅仅是一个循环，而是多个循环呢？如果它可以有多个线程呢？

现在，我想带您深入了解一下节点内部的情况。

## 事件循环阶段

这些是事件循环阶段:

![Event Loop Phases](img/6cfe143616f46db5a0668fe6727deb52.png)

图片来源: [libuv 文档](http://docs.libuv.org/en/v1.x/_images/loop_iteration.png)

1.  时间戳被更新。事件循环在循环开始时缓存当前时间，以避免频繁的与时间相关的系统调用。这些系统调用是 libuv 内部的。
2.  回路是活的吗？如果循环有活动句柄、活动请求或关闭句柄，则它是活动的。如图所示，队列中挂起的回调使循环保持活动状态。
3.  到期定时器执行。这是`setTimeout`或`setInterval`回调运行的地方。循环检查缓存的 *now* 是否有过期执行的活动回调。
4.  队列中挂起的回调执行。如果前一次迭代推迟了任何回调，这些回调将在此时运行。轮询通常会立即运行 I/O 回调，但也有例外。这一步处理来自前一次迭代的任何落后者。
5.  空闲处理程序执行——大部分是由于糟糕的命名，因为这些处理程序在每次迭代中都运行，并且是 libuv 内部的。
6.  为循环迭代中的`setImmediate`回调执行准备句柄。这些句柄在 I/O 循环阻塞之前运行，并为此回调类型准备队列。
7.  计算轮询超时。循环必须知道它为 I/O 阻塞了多长时间，这是它计算超时的方法:
    *   如果循环即将退出，超时值为 0。
    *   如果没有活动的句柄或请求，超时值为 0。
    *   如果有空闲句柄，超时值为 0。
    *   如果队列中有任何挂起的句柄，超时值为 0。
    *   如果有任何关闭句柄，超时为 0。
    *   如果以上都不是，超时设置为最接近的定时器，或者如果没有活动的定时器， *infinity* 。
8.  循环阻塞 I/O，持续时间与前一阶段相同。队列中与 I/O 相关的回调在此时执行。
9.  检查句柄回调执行。这个阶段是`setImmediate`运行的地方，它对应于准备句柄。任何在 I/O 回调执行中排队的`setImmediate`回调都在这里运行。
10.  关闭回调执行。这些是从关闭的连接中释放的活动句柄。
11.  迭代结束。

您可能想知道为什么轮询 I/O 阻塞，而它应该是非阻塞的？只有当队列中没有挂起的回调并且调用堆栈为空时，循环才会阻塞。例如，在 Node 中，最近的计时器可以通过`setTimeout`设置。如果设置为无穷大，循环将等待更多工作的传入连接。这是一个半无限循环，因为当没有其他事情可做并且有活动连接时，轮询会使循环保持活动状态。

下面是这个超时计算的 Unix 版本的全部 C 荣耀:

```
int uv_backend_timeout(const uv_loop_t* loop) {
  if (loop->stop_flag != 0)
    return 0;

  if (!uv__has_active_handles(loop) && !uv__has_active_reqs(loop))
    return 0;

  if (!QUEUE_EMPTY(&loop->idle_handles))
    return 0;

  if (!QUEUE_EMPTY(&loop->pending_queue))
    return 0;

  if (loop->closing_handles)
    return 0;

  return uv__next_timeout(loop);
} 
```

你可能不太熟悉 C 语言，但它读起来像英语，做的和第七阶段完全一样。

## 分阶段的演示

要用普通 JavaScript 显示每个阶段:

```
// 1\. Loop begins, timestamps are updated
const http = require('http');

// 2\. The loop remains alive if there's code in the call stack to unwind
// 8\. Poll for I/O and execute this callback from incoming connections
const server = http.createServer((req, res) => {
  // Network I/O callback executes immediately after poll
  res.end();
});

// Keep the loop alive if there is an open connection
// 7\. If there's nothing left to do, calculate timeout
server.listen(8000);

const options = {
  // Avoid a DNS lookup to stay out of the thread pool
  hostname: '127.0.0.1',
  port: 8000
};

const sendHttpRequest = () => {
  // Network I/O callbacks run in phase 8
  // File I/O callbacks run in phase 4
  const req = http.request(options, () => {
    console.log('Response received from the server');

    // 9\. Execute check handle callback
    setImmediate(() =>
      // 10\. Close callback executes
       server.close(() =>
        // The End. SPOILER ALERT! The Loop dies at the end.
        console.log('Closing the server')));
  });
  req.end();
};

// 3\. Timer runs in 8 secs, meanwhile the loop is staying alive
// The timeout calculated before polling keeps it alive
setTimeout(() => sendHttpRequest(), 8000);

// 11\. Iteration ends 
```

因为文件 I/O 回调在第四阶段和第九阶段之前运行，所以预计`setImmediate()`会首先触发:

```
fs.readFile('readme.md', () => {
  setTimeout(() => console.log('File I/O callback via setTimeout()'), 0);
  // This callback executes first
  setImmediate(() => console.log('File I/O callback via setImmediate()'));
}); 
```

没有 DNS 查找的网络 I/O 比文件 I/O 开销低，因为它在主事件循环中执行。相反，文件 I/O 通过线程池排队。DNS 查找也使用线程池，所以这使得网络 I/O 和文件 I/O 一样昂贵。

## 线程池

节点内部有两个主要部分:V8 JavaScript 引擎和 libuv。文件 I/O、DNS 查找和网络 I/O 通过 libuv 发生。

这是整体架构:

![Thread Pool Design Overview](img/c7f4da6410135f24a3d7bf9431ab7e46.png)

图片来源: [libuv 文档](http://docs.libuv.org/en/v1.x/_images/architecture.png)

对于网络 I/O，事件循环在主线程内部轮询。这个线程不是线程安全的，因为它不与另一个线程进行上下文切换。文件 I/O 和 DNS 查找是特定于平台的，所以方法是在一个线程池中运行它们。一种想法是自己进行 DNS 查找以远离线程池，如上面的代码所示。例如，输入一个 IP 地址而不是`localhost`，将查找从地址池中取出。线程池的可用线程数量有限，可以通过环境变量`UV_THREADPOOL_SIZE`来设置。默认的线程池大小大约是 4。

V8 在一个单独的循环中执行，耗尽调用堆栈，然后将控制权交还给事件循环。V8 可以在自己的循环之外使用多线程进行垃圾收集。可以把 V8 想象成接收原始 JavaScript 并在硬件上运行的引擎。

对于普通程序员来说，JavaScript 仍然是单线程的，因为没有线程安全。V8 和 libuv 的内部机制旋转它们自己独立的线程来满足它们自己的需求。

如果节点中存在吞吐量问题，从主事件循环开始。检查应用程序完成一次迭代需要多长时间。应该不会超过一百毫秒。然后，检查线程池是否饥饿，以及哪些线程可以被逐出线程池。还可以通过环境变量增加池的大小。最后一步是在 V8 中对同步执行的 JavaScript 代码进行微基准测试。

## 包扎

当回调排队时，事件循环继续遍历每个阶段。但是，在每个阶段都有一种方法来排队另一种类型的回调。

### `process.nextTick()`对`setImmediate()`

在每个阶段结束时，循环执行`process.nextTick()`回调。注意，这个回调类型不是事件循环的一部分，因为它在每个阶段结束时运行。`setImmediate()`回调是整个事件循环的一部分，所以它不像名字暗示的那样直接。因为`process.nextTick()`需要对事件循环有深入的了解，我建议一般情况下使用`setImmediate()`。

你可能需要`process.nextTick()`有几个原因:

1.  在循环继续之前，允许网络 I/O 处理错误、清理或重试请求。
2.  在调用堆栈展开之后，循环继续之前，可能有必要运行回调。

比方说，一个事件发射器想要在它自己的构造函数中触发一个事件。在调用事件之前，调用堆栈必须先展开。

```
const EventEmitter = require('events');

class ImpatientEmitter extends EventEmitter {
  constructor() {
    super();

    // Fire this at the end of the phase with an unwound call stack
    process.nextTick(() => this.emit('event'));
  }
}

const emitter = new ImpatientEmitter();
emitter.on('event', () => console.log('An impatient event occurred!')); 
```

允许调用堆栈展开可以防止类似`RangeError: Maximum call stack size exceeded`的错误。一个问题是确保`process.nextTick()`不会阻塞事件循环。在同一个阶段中，递归回调调用的阻塞可能会有问题。

## 结论

事件循环的最终复杂性是简单的。它需要一个像异步、线程安全和并发这样的难题。它剔除无用或不需要的内容，并以最有效的方式最大化吞吐量。正因为如此，节点程序员花更少的时间来追踪异步错误，而花更多的时间来交付新特性。

## 分享这篇文章*