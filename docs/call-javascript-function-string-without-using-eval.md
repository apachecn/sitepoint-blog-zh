# 如何在不使用 eval 的情况下从字符串调用 JavaScript 函数

> 原文：<https://www.sitepoint.com/call-javascript-function-string-without-using-eval/>

`eval`在 JavaScript 中是恶！MDN 评估页面显示:

> **过时**
> 这个功能已经过时了。尽管浏览器仍然支持它，但是不鼓励在新项目中使用它。尽量避免使用。

`eval`执行包含代码的字符串，例如

```
eval("var x = 'Hello from eval!';");
console.log(x);
```

`eval`提出了几个问题:

1.  安全性:您的字符串可以通过第三方脚本或用户输入注入其他命令。
2.  调试:很难调试错误——没有行号或明显的故障点。
3.  优化:JavaScript 解释器不一定要预编译代码，因为它可能会改变。虽然解释器变得越来越高效，但它的运行速度肯定会比本地代码慢。

不幸的是，`eval`非常强大，缺乏经验的开发人员很容易滥用这个命令。

尽管有警告，`eval`仍然工作——甚至在严格模式下——但是你通常可以避免它。过去它主要用于反序列化 JSON 字符串，但是现在我们有了更安全的`JSON.parse`方法。

然而，如果我们在字符串中有一个函数名，例如

```
// function we want to run
var fnstring = "runMe";

function runMe() {
	// do stuff
}
```

我们如何在不使用`eval`的情况下执行`runMe()`函数？我最近在使用 [HTML5 历史 API](/javascript-history-pushstate/) 时就遇到了这种情况；pushState 方法不允许您存储对函数的直接引用，因此您需要将其名称定义为字符串。使用 Web Workers 或任何其他序列化对象的 API 时，您也可能面临类似的挑战。

最简单、最安全的无评估执行解决方案是一系列条件，例如

```
// function we want to run
var fnstring = "runMe";

switch (fnstring) {
	case "functionX": functionX(); break;
	case "functionY": functionY(); break;
	case "functionZ": functionZ(); break;
	case "runMe": runMe(); break;
}
```

这是安全的，但是如果你有几十个可能的函数调用，那么编写起来相当低效和痛苦。

更好的解决方案是使用引用当前窗口和其中所有项目的`window`对象。我们可以检查`fnstring`是否可以作为`window`中的一个对象，如果它是一个函数，就运行它，例如

```
// function we want to run
var fnstring = "runMe";

// find object
var fn = window[fnstring];

// is object a function?
if (typeof fn === "function") fn();
```

如有必要，您可以执行其他检查，以确保函数具有预期的名称。

如果我们要调用的函数有参数——可能存储在数组中——该怎么办？没问题；我们简单地使用`apply`方法:

```
// function name and parameters to pass
var fnstring = "runMe";
var fnparams = [1, 2, 3];

// find object
var fn = window[fnstring];

// is object a function?
if (typeof fn === "function") fn.apply(null, fnparams);
```

所以这是停止使用`eval`的另一个原因。另外，这种解决方案更安全，更不容易出错，更容易调试，通常执行速度更快。希望有帮助。

## 分享这篇文章