# JavaScript“this”和事件处理程序

> 原文：<https://www.sitepoint.com/javascript-this-event-handlers/>

如果有一个原因需要依赖 JavaScript 库，比如 [jQuery](http://jquery.com/) ，那就是事件处理。大多数库将事件抽象成一组统一的对象和处理程序，这些对象和处理程序可以在大多数现代浏览器中工作。但是我们不需要库——我们是铁杆 JavaScript 开发者！

## 事件 101

没有事件和处理程序，我们将永远无法创建漂亮的客户端 web 应用程序。事件允许 JavaScript 检测用户何时执行了某个动作，例如，悬停在元素上、单击链接、滚动页面、调整窗口大小、拖动对象或任何其他活动。

您的 JavaScript 代码可以注册一个在特定事件发生时触发的处理函数。大多数浏览器向包含事件信息的函数传递单个对象，例如，按下了什么键，光标的位置等等。然后就有可能采取某些行动，如动画元素，进行 Ajax 调用，或阻止浏览器的默认行动。

此外，可以设置“这”。您通常可以预期它是触发事件的元素，但情况并非总是如此…

## 内嵌事件

这些是浏览器支持的第一批事件处理程序:

```
 <p><a id="link" href="#" onclick="EventHandler();">click me</a></p>

<script>
function EventHandler() {
	console.log(this);
}
</script> 
```

在这种情况下，我们只是在 onclick 事件发生时运行一个函数，而“this”将成为全局窗口对象。我们可以对我们的内联处理程序做一点小小的修改，这样就可以传递

```
 <p><a id="link" href="#" onclick="return EventHandler(this);">click me</a></p> 
```

请注意，我们还添加了“return”。如果我们的 EventHandler 返回 false，点击事件将被取消。

**important:** Never use inline event handlers!

我以前说过，但它需要重复。内联事件处理程序是有限的、笨重的，并且会增加 HTML 代码的体积。它们导致维护复杂化，因为事件的调用及其处理程序是在不同的地方定义的。最后，脚本标签必须放在 HTML 的顶部而不是底部，因为页面加载时可能会调用事件。

## 传统 DOM0 事件

下面是我们使用传统事件处理的例子:

```
 <p><a id="link" href="#">click me</a></p>

<script>
var link = document.getElementById("link");
link.onclick = EventHandler;

function EventHandler() {
	console.log(this.id);
}
</script> 
```

在 EventHandler()中，每个浏览器都将“this”设置为触发事件的元素——我们的锚标记。它很可靠，但有一个主要缺点:我们只能为每种事件类型分配一个处理程序。

**note:** Drop the parenthesis!

注意不要使用`link.onclick = EventHandler();` — EventHandler 将立即运行，返回值(未定义)将被赋给#link 节点的 onclick 属性。它可能不会引发错误，但是当 click 事件发生时，您的处理程序永远不会被调用。

## 现代 DOM2 事件

最后，我们有现代事件处理，它允许为同一个事件指定多个处理程序。不幸的是，微软和 W3C 对它们的实现有一些不同意见，只有 IE9 支持 addEventListener()。但是，我们可以使用一点对象检测来创建一个跨浏览器的事件附加函数，它可以在所有浏览器中工作:

```
 <p><a id="link" href="#">click me</a></p>

<script>
var link = document.getElementById("link");
AttachEvent(link, "click", EventHandler);

function AttachEvent(element, type, handler) {
	if (element.addEventListener) element.addEventListener(type, handler, false);
	else element.attachEvent("on"+type, handler);
}

function EventHandler(e) {
	console.log(this);
}
</script> 
```

与 DOM0 一样，所有浏览器都将“this”设置为触发事件的元素…只有一个例外。Internet Explorer 8.0 和更低版本只引用事件处理程序，因此“this”始终是全局窗口对象。

幸运的是，我们可以从事件对象中确定目标元素:

```
 function EventHandler(e) {
	e = e || window.event;
	var target = e.target || e.srcElement;
	console.log(target);
} 
```

唷。您想知道为什么 jQuery 如此受欢迎！

## 分享这篇文章