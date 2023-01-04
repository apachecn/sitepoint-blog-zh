# jQuery 方法的本地 JavaScript 等价物:事件、Ajax 和实用程序

> 原文：<https://www.sitepoint.com/jquery-vs-raw-javascript-3-events-ajax/>

这是继 [*之后我的系列文章的最后一部分。你真的需要 jQuery 吗？*](/do-you-really-need-jquery/) 在本文中，我们将看看 jQuery 提供的更高级方法的 JavaScript 等价物…

## 在页面加载时启动脚本

在遥远的过去，开发人员会将脚本放在 HTML `head`中，并使用`window.onload = start;`来启动初始功能。它的工作，但`window.onload`只有在所有的嵌入文件，如图像已经完全下载后才触发；您可能需要等待一段时间来运行代码。

jQuery 提供了一种更有效的方法:

```
$(start)
```

一旦 HTML 已经下载并且 DOM 准备好，就执行 *start()* 函数，但是它不会等待其他资源。在幕后，它使用 jQuery 1.x for IE6/7/8 中带有回退功能的原生 DOMContentLoaded 事件:

```
document.addEventListener("DOMContentLoaded", start);
```

我过去常常为这种无稽之谈而烦恼。这是不必要的:只需在页面底部的结束标签`</body>`之前加载脚本，就可以知道 DOM 已经准备好了。

## 为每一个

jQuery 允许您对数组中的所有项目运行自定义函数。(这包括节点的集合。虽然它们可能以节点列表的形式出现，但 jQuery 会在返回之前将它们复制到一个类似数组的对象中):

```
$("p").each(function(i) {
	console.log("index " + i + ": " + this);
});
```

当您对 jQuery 链应用多个方法时，在内部使用`each`。这个命令将循环所有的`<p>`节点两次:

```
$("p").addClass("newclass").css({color:"#f00"});
```

包括 IE9+在内的大多数浏览器都支持原生的`forEach`。但是，它只适用于数组，因此我们需要通过遍历所有项目或使用数组切片原型来转换节点列表:

```
Array.prototype.slice.call(document.getElementsByTagName("p"));
```

呃。就我个人而言，我更喜欢一个简短的自定义函数，它可以通过一个数组、节点列表或带有回调的对象来传递。它遍历将每个项及其索引传递给该函数的项。此示例在回调第一次返回 false 时自动取消进一步的处理:

```
function Each(obj, fn) {
	if (obj.length) for (var i = 0, ol = obj.length, v = obj[0]; i < ol && fn(v, i) !== false; v = obj[++i]);
	else for (var p in obj) if (fn(obj[p], p) === false) break;
};
```

## 事件

在 jQuery 出现之前，事件处理非常糟糕。IE6/7/8 实现了一个不同的事件模型，甚至那些被认为遵循 W3C 标准的浏览器也有足够多的不一致之处，使得开发变得很尴尬。jQuery 仍然使事件更容易理解:

```
$("#clickme").on("click", function(e) {
	console.log("you clicked " + e.target);
	e.preventDefault();
});
```

但是本地的对等物并不太难:

```
document.getElementById("clickme").addEventListener("click", function(e) {
	console.log("you clicked " + e.target);
	e.preventDefault();
});
```

这在 IE6/7/8 中不起作用，但在 jQuery 2.x 中也不起作用。大多数库都提供了一种使用 oldIE attachEvent 方法注册事件的变通方法。或者，如果在一个节点上只需要一个特定类型的事件，只需应用跨浏览器的 DOM 1“on”方法，例如

```
document.getElementById("clickme").onclick = runSomething;
```

(不要忘记在你的处理函数的顶部包含一行，比如`e = e ? e : window.event;`来处理 oldIE 事件对象)。记住，如果你支持 IE9+，这些都不是必需的。

jQuery 提供了非标准类型，如“focusin”和“live”事件，即未来操作添加到页面的匹配元素也将应用该事件。jQuery 通过将处理程序附加到父元素(如页面主体)并在事件对象出现在 DOM 中时对其进行分析来实现这一点。您可以自己完成这项工作——这样更有效，使用的代码更少，并且是标准的 JavaScript 实践，例如，您不必将悬停事件附加到表格的每一行，而是将它附加到表格，分析对象并做出相应的反应。

如果你更喜欢 jQuery 的语法，为什么不用[雷米夏普的 min.js](https://github.com/remy/min.js) ？它提供了`$`选择器、链式`on`事件、`trigger`功能和`forEach`支持。总重量:缩小版 617 个字符。

## 埃阿斯

你上一次开发需要 XMLHttpRequest、脚本注入、GET 请求、POST 提交、JSON 处理和图像加载的项目是什么时候？jQuery 可以处理很多情况，虽然它很灵活，但是您很少需要所有这些选项。典型的 jQuery Ajax 代码:

```
$.ajax({
	url: "webservice",
	type: "POST",
	data: "a=1&b=2&c=3",
	success: function(d) {
		console.log(d);
	}
}); 
```

本机等效项:

```
var r = new XMLHttpRequest(); 
r.open("POST", "webservice", true);
r.onreadystatechange = function () {
	if (r.readyState != 4 || r.status != 200) return; 
	console.log(r.responseText);
};
r.send("a=1&b=2&c=3"); 
```

还有更多的选项需要考虑——比如超时——所以我建议将本机代码包装在一个易于使用的函数中。但是，您可以针对您的项目对它进行优化，并实现一些更好的 XMLHttpRequest2 特性，比如二进制数据、上传进度和跨源请求，这些都是 jQuery 无法(直接)实现的。

## 应该使用 jQuery 吗？

只有您可以做出这样的决定，但是将 jQuery 作为每个项目的默认起点并不一定有效，也不一定会产生优雅的代码。

jQuery 非常有用，是前端开发重生的主要原因之一。然而，与处理 canvas、SVG 或其他重点任务的专业库不同，jQuery 提供了一组通用工具——您永远不会需要它们。浏览器厂商已经赶上来了，除非你需要支持老歌，否则 jQuery 不再像以前那样是必不可少的资源。在 it 环境中，如果您已经迁移到 jQuery 2.0，您可能不需要 jQuery。

我给你留一个[香草 JS](http://vanilla-js.com/) 的链接；许多顶级网络公司使用的库，具有模块化架构，比所有竞争对手都小。该网站是半开玩笑的，但很好地说明了这一点。

## 分享这篇文章