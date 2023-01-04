# 基于 JavaScript 定时器的伪线程

> 原文：<https://www.sitepoint.com/javascript-timer-pseudo-threading/>

在我之前的文章 [JavaScript 执行和浏览器限制](https://www.sitepoint.com/javascript-execution-browser-limits/)中，我描述了排名前 5 的浏览器如何确定 JavaScript 代码何时运行了太长时间并抛出“无响应脚本”错误。改变浏览器的行为是不切实际的，也不可能总是将处理任务卸载给服务器。幸运的是，计时器可以帮助我们在不锁定浏览器的情况下执行长时间运行的任务。

## 什么是计时器？

JavaScript 代码(如单个函数)可以设置为在特定时间段过后运行:

*   **setTimeout(function，msec[，arg1 … argN])**
    在 msec 毫秒过去后运行指定的函数。参数(如果有)被传递给该函数。
*   **setInterval(function，msec[，arg1 … argN])**
    类似于 setTimeout，除了函数每隔 msec 毫秒被无限调用一次。

另外两个功能`clearTimeout()`和`clearInterval()`将取消定时器操作，例如

```
 var timerID = setTimeout(myfunction, 500);
clearTimeout(timerID); // myfunction() will never be called 
```

注意:

1.  setTimeout 和 setInterval 被传递一个对函数的引用(没有括号)。代码`setTimeout(myfunction(), 500);`将立即运行 myfunction()。
2.  毫秒计时很少是精确的。它们只指定当浏览器在一段特定的时间后变得空闲时，应该运行一个函数。
3.  不要依赖于以特定顺序运行的函数，例如`setTimeout(f1, 50); setTimeout(f2, 50);` — f2()可以先执行。

## 基于计时器的执行

定时代码不会立即运行，因此浏览器处理线程被释放以执行其他任务。因此，我们可以将长流程分割成较短的块。

举个简单的例子，假设我们想按顺序运行函数 f1()、f2()和 f3():

```
 function ProcessThread(func) {
	var ms = 20;
	setTimeout(function() {
		func.shift()();
		if (func) {
			setTimeout(arguments.callee, ms);
		}
	}, ms);
}

ProcessThread([f1, f2, f3]); 
```

**note:** func.shift()() ?!

这需要一点进一步的解释；func.shift()从数组中移除第一项并返回它。这将引用一个函数，所以我们添加括号来执行它。

该语句的功能与`var f = func.shift(); f();`相同

ProcessThread 运行传递的数组中的所有函数，但在每个函数之间等待 20 毫秒。可以按顺序执行任意数量的函数…假设没有单个函数抛出“脚本无响应”错误。

然而，最耗时的代码可能是处理大型数据数组。在我的下一篇文章中，我们将编写更健壮的代码来处理这种类型的操作。

## 分享这篇文章