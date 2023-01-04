# 如何用 JavaScript 创建一次性事件

> 原文：<https://www.sitepoint.com/create-one-time-events-javascript/>

有时一个事件只需要在你的页面中调用一次。例如，单击缩略图加载并播放视频文件，或者单击“更多”图标通过 Ajax 检索并显示额外内容。但是，您可能已经定义了一个事件处理程序，每次该操作发生时都会调用它。充其量，它有点低效，浏览器保留了不必要的资源。最坏的情况是，您的处理程序可能会做一些意想不到的事情，或者重新加载已经可用的数据。

幸运的是，用 JavaScript 创建一次性事件处理程序相对容易。流程:

1.  一个处理程序被分配给一个事件，例如单击一个元素。
2.  当单击元素时，处理程序运行。
3.  处理程序被删除。进一步单击该元素将不再调用该函数。

## jQuery

我们先来看最简单的解决方案。如果您使用 jQuery，有一个鲜为人知的`one()`事件绑定方法，它实现一次性事件。

```
$("#myelement").one( "click", function() { alert("You'll only see this once!"); } );
```

它的用法与其他 jQuery 事件方法相同。更多信息，请参考[api.jquery.com/one/](http://api.jquery.com/one/)。

## 自动移除处理程序

如果您使用的是原始 JavaScript，任何处理函数都可以使用一行代码删除自己:

```
document.getElementById("myelement").addEventListener("click", handler);

// handler function
function handler(e) {
	// remove this handler
	e.target.removeEventListener(e.type, arguments.callee);

	alert("You'll only see this once!");
}
```

假设您的处理程序事件参数名为“e ”,该行:

```
e.target.removeEventListener(e.type, arguments.callee);
```

将在第一次调用处理程序时移除它。您为处理程序使用什么事件类型或名称并不重要，它甚至可以是一个内嵌的匿名函数。

注意，我使用的是标准的事件调用，在 IE8 及以下版本中无法使用。旧类型需要调用`detachEvent`，类型需要“on”前缀，例如“onclick”。但是，如果你支持老歌，你可能会使用 jQuery 或你自己的自定义事件处理程序。

如果您需要一定的灵活性，自移除处理程序可能是最好的选择，例如，您只想在不同的条件下(例如，两次或更多次点击)取消挂钩某些事件类型或移除处理程序。

## 一次性事件创建功能

像我一样，您可能太懒或太健忘，没有给每个处理函数添加一个事件移除行。让我们创建一个`onetime`函数来为我们完成这项艰巨的工作:

```
// create a one-time event
function onetime(node, type, callback) {

	// create event
	node.addEventListener(type, function(e) {
		// remove event
		e.target.removeEventListener(e.type, arguments.callee);
		// call handler
		return callback(e);
	});

}
```

我们现在可以在需要一次性事件时使用该函数:

```
// one-time event
onetime(document.getElementById("myelement"), "click", handler);

// handler function
function handler(e) {
	alert("You'll only see this once!");
}
```

虽然您不需要每个页面都有一次性事件，但是发现 JavaScript 开发人员有几个选项是很好的。

## 分享这篇文章