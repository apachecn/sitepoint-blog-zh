# 如何为 JavaScript 编写无 Cookie 的会话库

> 原文：<https://www.sitepoint.com/javascript-session-variable-library/>

![the (JavaScript) Cookie Monster](img/bb268678e62a59063715893682a341f1.png)在我之前的文章[JavaScript](https://www.sitepoint.com/cookieless-javascript-session-variables/)中的无 Cookie 会话变量中，我们发现了如何将 JavaScript 会话数据保存到 window.name 属性中。今天，我们创建了一个 JavaScript 库来利用这一特性。

[**查看 JavaScript 会话库演示页面……**](https://blogs.sitepointstatic.com/examples/tech/js-session/index.html)

该代码提供了三种主要方法:

*   **Session.set( *名称*，*对象* )** —存储已命名的会话值/对象
*   **session . get(*name*)**—检索命名的会话值/对象
*   **Session.clear()** —重置所有会话数据

另一个 **Session.dump()** 方法返回所有 JSON 编码的会话数据。这通常仅用于调试目的。

JavaScript 代码刚好在结束的`body`标签之前加载。首先，我们加载 JSON 库:

```
 <script type="text/javascript" src="json-serialization.js"></script> 
```

JSON 库提供了我们的会话库所需的跨浏览器序列化方法。更多信息请参考 JavaScript 中的[跨浏览器 JSON 序列化。](https://www.sitepoint.com/javascript-json-serialization/)

接下来加载 [session.js](https://blogs.sitepointstatic.com/examples/tech/js-session/session.js) 文件。这是可以在任何系统中实现的独立代码——它不依赖于 jQuery 或任何其他 JavaScript 库。通过代码工作:

```
 if (JSON && JSON.stringify && JSON.parse) var Session = Session || (function() {

	// window object
	var win = window.top || window;

	// session store
	var store = (win.name ? JSON.parse(win.name) : {}); 
```

第一行定义了我们的会话模块。但是，只有当 JSON 库已经加载并且没有其他冲突的会话变量或函数时，才会定义它。

第二行将 win 设置为“window.top”。如果不可用，它将被设置为“window”(可能是因为浏览器不支持框架)。

接下来，我们定义一个“store”对象来保存所有的会话数据。对 window.name 属性中现有的 JSON 编码的数据进行解析，但是如果该属性不可用，则将“store”设置为空对象。

```
 // save store on page unload
	function Save() {
		win.name = JSON.stringify(store);
	};

	// page unload event
	if (window.addEventListener) window.addEventListener("unload", Save, false);
	else if (window.attachEvent) window.attachEvent("onunload", Save);
	else window.onunload = Save; 
```

私有的 Save()函数将序列化的“store”对象字符串分配给窗口。名称属性。下面三行定义了一个跨浏览器事件，该事件在页面卸载时调用 Save 函数。因此，您的页面可以根据需要修改“store ”,但是序列化和保存的繁重工作只能在最后一刻进行。

```
 // public methods
	return {

		// set a session variable
		set: function(name, value) {
			store[name] = value;
		},

		// get a session value
		get: function(name) {
			return (store[name] ? store[name] : undefined);
		},

		// clear session
		clear: function() { store = {}; },

		// dump session data
		dump: function() { return JSON.stringify(store); }

	};

 })(); 
```

最后，我们有四个公共的 set、get、clear 和 dump 函数，它们相应地处理存储对象。如果找不到会话名，Session.get()方法将返回一个 JavaScript 'undefined '值。

我希望这些代码对您有用。请随意在您自己的项目中使用它。

有用的资源:

*   [**JavaScript 会话变量演示页面**](https://blogs.sitepointstatic.com/examples/tech/js-session/index.html)
*   [完整的 JavaScript session.js 代码](https://blogs.sitepointstatic.com/examples/tech/js-session/session.js)
*   [下载 ZIP 文件中的完整代码](https://blogs.sitepointstatic.com/examples/tech/js-session/js-session.zip)

另请参见:

*   [JavaScript 中的无 Cookie 会话变量](https://www.sitepoint.com/cookieless-javascript-session-variables/)
*   [JavaScript 跨浏览器 JSON 序列化](https://www.sitepoint.com/javascript-json-serialization/)
*   [如何开发一个 jQuery 插件](https://www.sitepoint.com/how-to-develop-a-jquery-plugin/)
*   [如何构建自动扩展的 Textarea jQuery 插件](https://www.sitepoint.com/build-auto-expanding-textarea-1/)

## 分享这篇文章