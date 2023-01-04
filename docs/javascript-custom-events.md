# 如何用 JavaScript 创建自定义事件

> 原文：<https://www.sitepoint.com/javascript-custom-events/>

JavaScript 事件处理是所有客户端应用程序的基础。当一个事件发生在一个目标元素上时，例如按钮点击、鼠标移动、表单提交等，一个处理函数被执行。事件对象被传递给处理程序，该处理程序提供各种属性和许多方法来防止默认操作。

一个缺点是事件与 DOM 元素有着千丝万缕的联系。考虑一个接受用户消息的简单表单:

```
 <form id="msgbox" action="#" method="get">
<label for="msg">your message</label>
<input id="msg" value="" />
<button>SEND</button>
</form> 
```

我们可以编写一个处理程序，在提交表单时将消息回显到屏幕上，例如

```
 document.getElementById("msgbox").addEventListener("submit", function(e) {
	e.preventDefault();
	var msg = e.currentTarget.getElementById("msg").value.trim();
	if (msg) {
		alert(msg);
	}
}, false); 
```

如果我们还想以 tweet 的形式发送消息，将它存储在服务器上，或者执行其他操作，会怎么样？对于现有的事件委派方法，我们有两种选择:

1.  向我们现有的处理程序添加更多的代码。
    这是不灵活的，因为我们每次添加、更改或删除功能时都需要更新和测试我们的处理函数。发布的消息可能有几十种用法，我们试图在同一个代码块中应用它们。
2.  **为每次使用创建进一步的事件处理程序。**
    这会产生更优雅的代码，但会导致维护问题。首先，每个函数必须执行相似的操作来提取和验证消息。如果我们需要改变我们的形式呢？简单地重命名 ID 将需要我们为每个订户更改事件处理代码。

如果每当发布有效消息时，我们可以简单地引发一个自定义的“newMessage”事件，这不是很好吗？如果我们能够简单地监控文档或`body`标签，而不是引用特定的`form`节点，那就更好了。这正是自定义事件允许我们做的。

引发自定义事件很简单；我们将名称、详细信息和选项传递给新的 CustomEvent 对象:

```
 var event = new CustomEvent(
	"newMessage", 
	{
		detail: {
			message: "Hello World!",
			time: new Date(),
		},
		bubbles: true,
		cancelable: true
	}
); 
```

在本例中，“newMessage”是自定义事件类型。第二个参数是具有三个属性的对象:

*   **detail** :提供事件定制信息的子对象。在本例中，我们添加了消息和时间。
*   **bubbles** :如果为 true，事件将冒泡到触发事件的元素的祖先。
*   **cancelable** :如果为 true，则可以使用事件对象的 stopPropagation()方法取消事件。

现在，我们需要在特定元素上调度这个事件，例如

```
 document.getElementById("msgbox").dispatchEvent(event); 
```

任何数量的处理程序都可以使用如下代码订阅该事件:

```
 document.addEventListener("newMessage", newMessageHandler, false); 
```

## 演示页面

这个例子演示了这种技术:

[**查看自定义事件演示页面**](https://blogs.sitepointstatic.com/examples/tech/custom-events/index.html)

一个标准的事件处理程序在上面的 HTML 表单中寻找提交。该函数获取当前消息，并假设它是有效的，调度一个新的“newMessage”事件。

```
 var msgbox = document.getElementById("msgbox");
msgbox.addEventListener("submit", SendMessage, false);

// new message: raise newMessage event
function SendMessage(e) {

	e.preventDefault();
	var msg = document.getElementById("msg").value.trim();

	if (msg && window.CustomEvent) {
		var event = new CustomEvent("newMessage", {
			detail: {
				message: msg,
				time: new Date(),
			},
			bubbles: true,
			cancelable: true
		});

		e.currentTarget.dispatchEvent(event);
	}

} 
```

处理程序现在可以订阅“新消息”事件。只有当消息有效时才会引发事件，因为 bubbles 设置为 true，所以事件可以应用于表单或它的任何祖先，比如根文档

```
 // listen for newMessage event
document.addEventListener("newMessage", newMessageHandler, false);

// newMessage event handler
function newMessageHandler(e) {
	LogEvent(
		"Event subscriber on "+e.currentTarget.nodeName+", "
		+e.detail.time.toLocaleString()+": "+e.detail.message
	);
} 
```

消息本身可以从事件对象的 detail.message 属性中提取。

## 浏览器兼容性

在撰写本文时，Chrome、Firefox 和 Opera 都支持 CustomEvent 对象。它在 Safari 的夜间版本中可用，所以它很可能很快就会出现在那个浏览器中。

IE9 和更低版本不支持对象。幸运的是，有几个 JavaScript 库支持自定义事件委托，所以请继续关注 SitePoint，它将很快提供跨浏览器解决方案。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start JavaScript](https://learnable.com/books/jump-start-javascript?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink%22) 。

对本文的评论已经关闭。有关于 JavaScript 的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章