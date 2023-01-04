# HTML5 Web Workers 的 JavaScript 线程

> 原文：<https://www.sitepoint.com/javascript-threading-html5-web-workers/>

忘记转换、原生视频、语义标签和所有其他无聊的 HTML5 废话；web workers 是自 JavaScript 以来浏览器中最好的特性！Web workers 最终允许开发人员在单独的线程中运行正在进行的流程。

线程听起来很复杂，一些开发语言让它变得棘手，但是你会很高兴听到 JavaScript 的实现很好，W3C 工作草案也很稳定。Web workers 提供了巨大的客户端性能增益，但是在我们开始之前有一些事情需要注意…

## Web Worker 限制

Web 工作者独立于浏览器 UI 线程运行，因此他们无法访问 JavaScript 开发人员了解和喜爱的许多特性。主要的限制是 web 工作者不能访问 DOM 他们不能阅读或修改 HTML 文档。此外，您不能在页面中访问全局变量或 JavaScript 函数。最后，对某些对象的访问是受限的，例如 window.location 属性是只读的。

然而，web workers 可以使用标准的 JavaScript 数据类型，处理 XMLHttpRequest (Ajax)调用，使用计时器，甚至导入其他 worker。它们非常适合耗时的任务，如分析大块数据、游戏人工智能逻辑、[光线追踪](http://nerget.com/rayjs-mt/rayjs.html)等。

## Web Worker 浏览器支持

在撰写本文时，Firefox、Chrome、Safari 和 Opera 的所有最新版本都在一定程度上支持 web workers。猜猜哪个浏览器不见了？

不出所料，任何版本的 Internet Explorer 都没有实现 web workers。甚至 IE9 也不提供支持，但我怀疑/希望它会在最终版本中实现。在那之前，你有三个选择:

*   再过一两年，别再想网络工作者了。
*   接受你的应用程序会在 IE 中崩溃。
*   实现你自己的 web worker shim，它回退到基于定时器的[伪线程](https://www.sitepoint.com/javascript-timer-pseudo-threading)或[数组处理](https://www.sitepoint.com/javascript-large-data-processing)。这在所有应用中可能是不可能的或不可取的。

## 什么是网络工作者？

web worker 是一个在后台加载和执行的 JavaScript 文件。有两种类型:

*   **专用工人**:这些工人链接到他们的创建者(加载工人的脚本)。
*   **共享工人**:允许来自同一个域(somesite.com)的任何脚本与工人通信。

今天，我们看到的是敬业的网络工作者…

## 创建专门的网络工作者

要创建一个专用的 web worker，需要将一个 JavaScript 文件名传递给 worker 对象的一个新实例:

```
 var worker = new Worker("thread1.js"); 
```

## 与专门的网络工作者交流

由于 web worker 不能访问 DOM 或执行页面脚本中的函数，所以所有通信都通过事件接口来处理。网页脚本通过`postMessage()`方法传递一个数据参数，并通过`onmessage`事件处理程序接收一个数据参数

**pagescript.js:**

```
 var worker = new Worker("thread1.js");

// receive messages from web worker
worker.onmessage = function(e) {
	alert(e.data);
};

// send message to web worker
worker.postMessage("Jennifer"); 
```

web worker 脚本通过自己的`onmessage`事件处理程序和`postMessage()`方法相应地接收和发送数据:

**thread1.js:**

```
 self.onmessage = function(e) {
	self.postMessage("Hello " + e.data);
}; 
```

消息数据可以是字符串、数字、布尔值、数组、对象、空值或未定义值。在通信过程中，数据总是按值传递，先序列化，再反序列化。

## 处理专用 Web Worker 错误

Web worker 代码不太可能完美，页面脚本传递的数据可能会导致逻辑错误。幸运的是，可以使用 onerror 事件处理程序捕获错误。向处理程序事件传递一个具有 3 个属性的对象:

*   **文件名**:导致错误的脚本的名称；
*   **行号**:发生错误的行号；和
*   **消息**:错误描述。

**pagescript.js:**

```
 worker.onerror = function(e) {
	alert("Error in file: "+e.filename+"nline: "+e.lineno+"nDescription: "+e.message);
}; 
```

## 加载更多 JavaScript 文件

可以使用`importScripts()`在 web worker 中加载一个或多个额外的 JavaScript 库，例如

```
 importScripts("lib1.js", "lib2.js", "lib3.js"); 
```

或者，您可以加载更多的 web workers …但是，我建议保持简单，直到浏览器赶上您的线程野心！

## 停止专用 Web Worker

web worker 线程可以使用`close()`方法停止，例如

**thread1.js:**

```
 self.onmessage = function(e) {
	if (e.data == "STOP!") self.close();
}; 
```

这将丢弃任何等待处理的任务，并防止进一步的事件排队。

关于敬业的网络工作者，你只需要知道这些。在我的下一篇文章中，我们将讨论共享 web workers——一种更复杂的野兽！

## 分享这篇文章