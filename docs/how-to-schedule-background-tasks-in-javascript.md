# 如何在 JavaScript 中调度后台任务

> 原文：<https://www.sitepoint.com/how-to-schedule-background-tasks-in-javascript/>

如果你不记得 JavaScript 的其他内容，请不要忘记:*它阻塞了*。

想象一个神奇的处理精灵让你的浏览器工作。所有的事情都由这个小精灵来处理，无论是渲染 HTML、响应菜单命令、在屏幕上绘画、处理鼠标点击还是运行 JavaScript 函数。像我们大多数人一样，小精灵一次只能做一件事。如果我们向 pixie 抛出许多任务，它们会被添加到一个大的待办事项列表中，并按顺序处理。

当 pixie 遇到一个`script`标签或者必须运行一个 JavaScript 函数时，其他的一切都会停止。在可以处理进一步的事件或渲染之前，立即下载(如果需要)并运行代码。这是必要的，因为您的脚本可以做任何事情:加载更多的代码，删除每个 DOM 元素，重定向到另一个 URL 等。即使有两个或更多的 pixies，当第一个处理你的代码时，其他的也需要停止工作。那是阻挡。这就是为什么长时间运行的脚本会导致浏览器反应迟钝的原因。

您通常希望 JavaScript 尽快运行，因为代码会初始化小部件和事件处理程序。然而，有一些不太重要的后台任务不会直接影响用户体验，例如

*   记录分析数据
*   向社交网络发送数据(或添加 57 个“分享”按钮)
*   预取内容
*   预处理或预呈现 HTML

这些不是时间关键的，但是为了让页面保持响应，它们不应该在用户滚动或与内容交互时运行。

一种选择是使用 [Web Workers](https://www.sitepoint.com/javascript-threading-html5-web-workers/) ，它可以在一个单独的线程中同时运行代码。对于预取和处理来说，这是一个很好的选择，但是不允许直接访问或更新 DOM。您可以在自己的脚本中避免这种情况，但您不能保证在第三方脚本(如 Google Analytics)中永远不需要这种情况。

另一种可能是`setTimeout`，例如`setTimeout(doSomething, 1);`。一旦其他立即执行的任务完成，浏览器将执行`doSomething()`功能。实际上，它被放在了待办事项列表的底部。不幸的是，无论处理需求如何，该函数都会被调用。

## requestIdleCallback

[requestIdleCallback](https://www.w3.org/TR/requestidlecallback/) 是一个新的 API，用于在浏览器休息的时候安排非必要的后台任务。这让人想起了 [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) ，它在下一次重画之前调用一个函数来更新动画。你可以在这里阅读更多关于`requestAnimationFrame`的内容:[使用 requestAnimationFrame 的简单动画](https://www.sitepoint.com/simple-animations-using-requestanimationframe/)

我们可以这样检测`requestIdleCallback`是否被支持:

```
if ('requestIdleCallback' in window) {
  // requestIdleCallback supported
  requestIdleCallback(backgroundTask);
}
else {
  // no support - do something else
  setTimeout(backgroundTask1, 1);
  setTimeout(backgroundTask2, 1);
  setTimeout(backgroundTask3, 1);
}
```

您还可以指定一个带有超时(以毫秒为单位)的 options 对象参数，例如

```
requestIdleCallback(backgroundTask, { timeout: 3000; });
```

这确保了无论浏览器是否空闲，您的函数都在前三秒内被调用。

`requestIdleCallback`只调用一次函数，并传递一个具有以下属性的`deadline`对象:

*   `didTimeout` —如果触发了可选超时，则设置为真
*   `timeRemaining()` —返回执行任务剩余的毫秒数的函数

`timeRemaining()`将为您的任务分配不超过 50 毫秒的运行时间。它不会停止超过这个限制的任务，但是，最好，您应该再次调用`requestIdleCallback`来调度进一步的处理。

让我们创建一个简单的例子，它按顺序执行几个任务。任务作为函数引用存储在数组中:

```
// array of functions to run
var task = [
	background1,
	background2,
	background3
];

if ('requestIdleCallback' in window) {
  // requestIdleCallback supported
  requestIdleCallback(backgroundTask);
}
else {
  // no support - run all tasks soon
  while (task.length) {
  	setTimeout(task.shift(), 1);
  }
}

// requestIdleCallback callback function
function backgroundTask(deadline) {

  // run next task if possible
  while (deadline.timeRemaining() > 0 && task.length > 0) {
  	task.shift()();
  }

  // schedule further tasks if necessary
  if (task.length > 0) {
    requestIdleCallback(backgroundTask);
  }
}
```

## requestIdleCallback 中有什么不应该做的事情吗？

正如 Paul Lewis 在他关于主题的[博客文章中指出的，你在 requestIdleCallback 中所做的工作应该是小块的。它不适合执行时间不可预测的任何事情(比如操作 DOM，使用 requestAnimationFrame 回调会做得更好)。您还应该警惕解决(或拒绝)承诺，因为回调将在空闲回调完成后立即执行，即使没有剩余时间。](https://developers.google.com/web/updates/2015/08/using-requestidlecallback?hl=en)

## requestIdleCallback 浏览器支持

`requestIdleCallback`是一个实验性的特性，规范还在不断变化，所以当你遇到 API 变化时不要惊讶。它在 Chrome 47 中得到支持 Chrome 47 应该会在 2015 年底之前上市。歌剧也即将获得这一特征。微软和 Mozilla 都在考虑 API，这听起来很有希望。和往常一样，苹果没有任何消息。如果你今天想试一试，你最好的选择是使用 [Chrome Canary](https://www.google.com/chrome/browser/canary.html) (一个更新的 Chrome 版本，没有经过很好的测试，但是有最新的闪亮的东西)。

Paul Lewis(上面提到过)创建了一个简单的 [requestIdleCallback shim](https://gist.github.com/paullewis/55efe5d6f05434a96c36) 。这实现了所描述的 API，但是它不是一个可以模拟浏览器空闲检测行为的 polyfill。它像上面的例子一样求助于使用`setTimeout`,但是如果你想使用没有对象检测和代码分叉的 API，这是一个很好的选择。

虽然目前的支持是有限的，但`requestIdleCallback`可能是一个有趣的工具，可以帮助您最大限度地提高网页性能。但是你怎么看？我很高兴在下面的评论区听到你的想法。

## 分享这篇文章