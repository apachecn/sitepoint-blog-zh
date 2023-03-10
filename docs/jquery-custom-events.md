# JQuery trigger()方法:如何在 JQuery 中创建自定义事件

> 原文：<https://www.sitepoint.com/jquery-custom-events/>

在我的上一篇文章[如何在 JavaScript](/javascript-custom-events) 中创建定制事件中，我们讨论了定制事件和本地 CustomEvent 对象的好处。概括地说，我们可以触发我们自己的命名事件。[演示页面](https://blogs.sitepointstatic.com/examples/tech/custom-events/index.html)提供了一个表单，每当提交有效消息时，该表单就会触发一个“新消息”事件。任何数量的处理程序都可以订阅此事件来执行自己的操作。

我们唯一的问题是:Safari 或 Internet Explorer 目前不支持 CustomEvent。

有很多方法可以解决浏览器兼容性问题。一种解决方案是编写自己的事件封装函数，实现自定义事件功能。幸运的是，不需要:定制事件在几个 JavaScript 库中都得到支持，包括 [jQuery](http://jquery.com/) 。

jQuery 的[。触发方式](http://api.jquery.com/trigger/)是关键。您可以在任何时候用新的类型名和任意数据触发事件，例如

```
 $.event.trigger({
	type: "newMessage",
	message: "Hello World!",
	time: new Date()
}); 
```

处理程序现在可以订阅“新消息”事件，例如

```
 $(document).on("newMessage", newMessageHandler); 
```

## 演示页面

此示例演示了 jQuery 代码:

[**查看 jQuery 自定义事件演示页面**](https://blogs.sitepointstatic.com/examples/tech/custom-events/jquery.html)

标准的事件处理程序在 HTML 表单上寻找提交。该函数获取当前消息，并假设它是有效的，调度一个新的“newMessage”事件。

```
 $("#msgbox").on("submit", SendMessage);

// new message: raise newMessage event
function SendMessage(e) {

	e.preventDefault();
	var msg = $("#msg").val().trim();
	if (msg) {
		$.event.trigger({
			type: "newMessage",
			message: msg,
			time: new Date()
		});
	}

} 
```

处理程序现在可以订阅“新消息”事件。只有存在有效消息时才会引发事件:

```
 // newMessage event subscribers
$(document).on("newMessage", newMessageHandler);

// newMessage event handler
function newMessageHandler(e) {
	LogEvent(
		"Event subscriber on "+e.currentTarget.nodeName+", "
		+e.time.toLocaleString()+": "+e.message
	);
} 
```

消息本身可以从事件对象的消息属性中提取。

## 浏览器兼容性

jQuery 1.x 方法可以在任何浏览器中运行，包括 Internet Explorer 6.0 和更高版本。显然，这是一个巨大的好处，即使代码运行(不明显)比本地客户事件慢。但是，请注意，计划于 2013 年发布的 jQuery 2.0 将不支持 IE6、7 和 8。

jQuery 很流行，但大多数 JavaScript 库中都支持自定义事件，包括 [Prototype](http://prototypejs.org/api/element/fire) 、 [Mootools](http://mootools.net/docs/core/Element/Element.Event) 和 [YUI](http://developer.yahoo.com/yui/examples/event/custom-event.html) 。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [jQuery:新手到忍者:新的踢腿和技巧](https://learnable.com/books/jquery-novice-to-ninja-new-kicks-and-tricks?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)。

对本文的评论已经关闭。有关于 jQuery 的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章