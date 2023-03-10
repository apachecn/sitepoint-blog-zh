# 如何在 HTML5 中使用 JavaScript 共享 Web Workers

> 原文：<https://www.sitepoint.com/javascript-shared-web-workers-html5/>

我们最近讨论了“专用”类型的 JavaScript web workers。如果您还没有阅读过，我建议您先阅读一下——本文基于相同的概念。

## 简而言之，网络工作者

web worker 是一个单独的 JavaScript 文件，在单独的线程上在后台加载和执行。专门的 web workers 链接到它们的创建者(加载 worker 的脚本)。共享 web workers 允许任意数量的脚本与单个 worker 进行通信。

共享的 web 工作者遵守与他们的专用同行相同的规则:没有 DOM、文档或页面脚本访问，并且对大多数窗口属性具有有限的只读权限。此外，页面脚本只能与来自同一来源/域(somesite.com)的共享 web workers 通信。

目前，Chrome、Safari 和 Opera 都支持共享 web workers。Firefox 4 和 IE9 支持可能会到来，但不要抱太大希望。共享工人可以节省资源，但他们增加了额外的复杂性。预计会出现一些问题，例如，

*   DOM2 事件(addEventListener)处理程序似乎是最可靠的实现。
*   您几乎肯定会遇到特定于浏览器的怪癖，调试也很困难。以下代码可以在最新版本的 Chrome 上运行，但不要认为它可以在 Safari 或 Opera 上运行。

## 创建共享 Web Worker

要创建一个共享 web worker，需要将一个 JavaScript 文件名传递给 SharedWorker 对象的一个新实例:

```
 var worker = new SharedWorker("jsworker.js"); 
```

## 与共享的 Web 工作人员通信

您的任何页面脚本都可以与共享 web worker 通信。与专用的 web workers 不同，您必须通过“port”对象进行通信，并附加一个消息事件处理程序。此外，在使用第一个`postMessage()`之前，必须调用端口的`start()`方法:

**pagescript.js:**

```
 var worker = new SharedWorker("jsworker.js");

worker.port.addEventListener("message", function(e) {
	alert(e.data);
}, false);

worker.port.start();

// post a message to the shared web worker
worker.port.postMessage("Alyssa"); 
```

当 web worker 脚本从脚本接收到第一条消息时，它必须将一个事件处理程序附加到活动端口。在大多数情况下，处理程序会运行自己的`postMessage()`方法向调用代码返回一条消息。最后，还必须执行端口的`start()`方法来启用消息传递:

**jsworker.js:**

```
 var connections = 0; // count active connections

self.addEventListener("connect", function (e) {

	var port = e.ports[0];
	connections++;

	port.addEventListener("message", function (e) {
		port.postMessage("Hello " + e.data + " (port #" + connections + ")");
	}, false);

	port.start();

}, false); 
```

像他们的兄弟姐妹一样，共享网络工作者可以:

*   用`importScripts()`加载更多脚本
*   附加错误处理程序，以及
*   运行`port.close()`方法来阻止特定端口上的进一步通信。

共享 web workers 可能在几年内不会成为一种可行的技术，但是它们为 JavaScript 开发的未来带来了令人兴奋的机会。让我们希望浏览器供应商能提供一些像样的跟踪和调试工具！

## 分享这篇文章