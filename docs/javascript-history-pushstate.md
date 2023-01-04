# 如何在复杂的 HTML5 和 JavaScript 应用程序中修改浏览器历史

> 原文：<https://www.sitepoint.com/javascript-history-pushstate/>

你不喜欢时髦的标题吗？！

考虑一个复杂的应用程序，如 webmail 客户端。本质上，它是一个运行在单个 HTML 页面上的复杂 JavaScript 程序。用户加载 URL，并显示一个电子邮件列表。他们点击一个标题，使用 Ajax 检索电子邮件内容并显示出来。他们现在想返回电子邮件列表；他们是做什么的？…

…单击浏览器的“后退”按钮。

砰。应用程序关闭并返回到他们在访问应用程序之前正在查看的页面。或者，如果它是一个新的浏览器标签，后退按钮被禁用，不能被点击。

所以我们有一个问题。我们的网络邮件客户端不支持大多数用户理解的浏览器控件。有解决办法。有些涉及到改变 URL 中的散列标记(#name)，这样就可以保留状态。它并不完美，但可以在所有浏览器中运行。

幸运的是，HTML5 `history.pushState`和`history.replaceState`方法结合`window.onpopstate`事件已经解决了这个问题。

[**试试 history.pushState()演示页面……**](https://blogs.sitepointstatic.com/examples/tech/history-pushstate/index.html)

这项技术非常简单:

1.  当状态改变时，例如用户打开电子邮件，history.pushState()被传递状态信息并被执行。这将启用后退按钮，但重要的是，不会将用户从页面上移开。
2.  您可以根据需要多次运行 history.pushState()，或者使用 history.replaceState()修改当前状态。
3.  当用户单击后退(或前进)时，将触发 window.onpopstate 事件。处理函数可以检索相关的状态并显示适当的屏幕。

坏处呢？在 v10 到来之前，忘记 IE 兼容性。如果需要支持 IE9 及以下版本，有很多垫片包括 [History.js](https://github.com/balupton/history.js) 和 [HTML5-History-API](https://github.com/devote/HTML5-History-API) 。

让我们写一些代码。假设您刚刚显示了一个 Ajax 请求的结果:

```
 // Ajax request
...
// display result
...

// modify history
history.pushState(obj, title, url); 
```

其中:

*   **obj** 是任何 JavaScript 对象。您可以使用它来保留状态信息，例如{ "view": "EMAILCONTENT "，" item ":123 }；
*   **标题**是可选标题
*   **url** 是可选的 url。URL 可以是任何东西——浏览器不会跳转到那个页面，但是如果用户重新加载页面或者重启浏览器的话,*可以*跳转到那个页面。在大多数情况下，您会希望使用参数或散列名称，例如？view = email content&item = 123；您的应用程序可以在启动时分析这些值，然后返回到同一个地方。

history.replaceState()具有相同的参数，仅当您想要用新状态替换当前状态时使用。

现在您需要一个处理函数，它在单击浏览器的后退或前进按钮后触发窗口 popstate 事件时运行:

```
 window.addEventListener("popstate", function(e) {

	// URL location
	var location = document.location;

	// state
	var state = e.state;

	// return to last state
	if (state.view == "EMAILCONTENT") {
		...
	}

}); 
```

可以用`document.location`来确定 URL 的位置(`document.location.search`和`document.location.hash`分别返回参数和哈希名)。

由 pushState()或 replaceState()设置的历史状态对象是从事件对象的`state`属性中获取的。您可以使用这些信息来显示适当的屏幕。

[**试试 history.pushState()演示页面……**](https://blogs.sitepointstatic.com/examples/tech/history-pushstate/index.html)

点击几次 **history.pushState** 按钮，然后返回查看日志中发生了什么。

非常有用。您在 web 应用程序中遇到过后退和前进按钮问题吗？

## 分享这篇文章