# 如何用 JavaScript 处理大量数据

> 原文：<https://www.sitepoint.com/javascript-large-data-processing/>

在我之前的帖子中，我们研究了 [JavaScript 执行和浏览器限制](https://www.sitepoint.com/javascript-execution-browser-limits/)以及一种使用[基于定时器的伪线程](https://www.sitepoint.com/javascript-timer-pseudo-threading)解决“无响应脚本”警报的方法。今天，我们将探讨在浏览器中处理大量数据的方法。

几年前，开发人员绝不会考虑复杂的服务器端处理的替代方案。这种观念已经改变，许多 Ajax 应用程序在客户机和服务器之间发送大量数据。此外，代码可能会更新 DOM，这是一个特别耗时的浏览器过程。但是，试图一次性分析这些信息可能会导致应用程序无响应并引发脚本警报。

JavaScript 计时器可以通过将一个长的数据分析过程分成较短的块来帮助防止浏览器锁定问题。这是我们的 JavaScript 函数的开始:

```
 function ProcessArray(data, handler, callback) { 
```

`ProcessArray()`函数接受三个参数:

1.  **数据:**要处理的项目数组
2.  **处理器:**处理单个数据项的功能
3.  **回调:**所有处理完成时调用的可选函数。

接下来，我们将定义配置变量:

```
 var maxtime = 100;		// chunk processing time
  var delay = 20;		// delay between processes
  var queue = data.concat();	// clone original array 
```

`maxtime`指定每个处理块允许的最大毫秒数。`delay`是处理组块之间的时间，以毫秒为单位。最后，`queue`是克隆原始数据数组——这并不是在所有情况下都是必要的，但是，由于数组是通过引用传递的，我们将丢弃每一项，所以这是最安全的选择。

我们现在可以使用一个`setTimeout`来开始处理:

```
 setTimeout(function() {

    var endtime = +new Date() + maxtime;

    do {
      handler(queue.shift());
    } while (queue.length > 0 && endtime > +new Date()); 
```

首先，计算一个`endtime`——这是处理必须停止的未来时间。do…while 循环依次处理排队的项目，直到每个项目都完成或到达`endtime`为止。

**note:** Why use `do…while?`

JavaScript 支持 while 循环和 do…while 循环。不同之处在于`do…while`循环保证至少执行一次迭代。如果我们使用标准的`while`循环，开发者可以设置一个低的或者负的`maxtime`，数组处理将永远不会开始或者完成。

最后，我们确定是否需要处理更多的项目，如果需要，在短暂的延迟后调用我们的处理函数:

```
 if (queue.length > 0) {
      setTimeout(arguments.callee, delay);
    }
    else {
      if (callback) callback();
    }

  }, delay);
}
// end of ProcessArray function 
```

一旦处理完每个项目，回调函数就会执行。

我们可以用一个小的测试用例来测试`ProcessArray()`:

```
// process an individual data item
function Process(dataitem) {
  console.log(dataitem);
}

// processing is complete
function Done() {
  console.log("Done");
}

// test data
var data = [];
for (var i = 0; i < 500; i++) data[i] = i;

// process all items
ProcessArray(data, Process, Done); 
```

该代码可以在包括 IE6+在内的所有浏览器中运行。这是一个可行的跨浏览器解决方案，但是 HTML5 提供了一个更好的解决方案！在我的下一篇文章中，我们将讨论网络工作者…

## 分享这篇文章