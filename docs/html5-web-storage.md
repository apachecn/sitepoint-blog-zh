# HTML5 网络存储

> 原文：<https://www.sitepoint.com/html5-web-storage/>

某些技术专家偏爱本地应用程序的原因之一是它们能够在客户机上本地存储数据。HTML5 取得了突破，允许网络应用程序在浏览器中存储数据。

虽然 cookies 可以用来在浏览器中存储数据，但它也有一些缺点 HTML5 Web 存储的发明解决了这些缺点。

## 饼干的缺点

*   网络浏览器中 cookies 的存储限制在 4KB 左右。
*   Cookies 随每个 HTTP 请求一起发送，从而降低了 web 应用程序的性能。

## 什么是 HTML5 Web 存储？

这是一个简单的客户端数据库，允许用户以键/值对的形式保存数据。

它有一个相当简单的 API 来检索/写入数据到本地存储。每个域最多可以存储 10MB 的数据。与 cookies 不同，存储的数据并不包含在每个 HTTP 请求中。

IE7 和旧版本不支持 Web 存储。所有其他浏览器，如 Chrome、Opera、Firefox、Safari 和 IE8+都支持网络存储。

## Web 存储的类型

**本地存储:**存储没有截止日期的数据。即使关闭或重新打开浏览器/浏览选项卡，数据仍然可用。

**会话存储:**存储一个会话的数据。一旦用户关闭浏览器，保存的数据将被清除。

在这两种情况下，请注意，web 存储数据在不同的浏览器之间不可用。

## 例子

让我们直接看一个例子来更好地理解 HTML5 Web 存储。

我们将创建一个简单的用户设置网页，允许我们改变页面的背景颜色和标题字体大小。

```
</pre>
<section><form onsubmit="javascript:setSettings()"><label>Select your BG color: </label>
 <input id="favcolor" type="color" value="#ffffff" />

 <label>Select Font Size: </label>
 <input id="fontwt" type="number" max="14" min="10" value="13" />

 <input type="submit" value="Save" />
 <input onclick="clearSettings()" type="reset" value="Clear" /></form></section>
<pre>
```

从表单的`onsubmit` 事件调用的`setSettings`函数将允许我们将用户选择的数据保存到本地存储中。

在我们使用 HTML5 Web 存储来存储数据之前，我们应该检查浏览器是否支持 HTML5 存储。

我们可以使用默认的 API 或 Modernizr 来检查浏览器的兼容性。

```
function setSettings() {
	if ('localStorage' in window && window['localStorage'] !== null) {
				//use localStorage object to store data
	} else {
		alert('Cannot store user preferences as your browser do not support local storage');
	}
}
```

Modernizr 是一个 JavaScript 库，帮助我们检测浏览器对 HTML5 和 CSS 特性的支持。下载 Modernizr 的最新版本，并将库包含在 script 元素中。

```
<script type="text/javascript" src="modernizr.min.js"></script>

if (Modernizr.localstorage) {
 //use localStorage object to store data
} else {
 alert('Cannot store user preferences as your browser do not support local storage');
}
```

`setItem(‘key’,’value’)`允许我们将数据写入本地存储器。

如果存储限制超过 5MB，将抛出异常。因此，在保存数据时，最好将 try/catch 块添加到存储代码中。

```
function setSettings() {
if ('localStorage' in window && window['localStorage'] !== null) {
	try {
		var favcolor = document.getElementById('favcolor').value;
		var fontwt = document.getElementById('fontwt').value;
		localStorage.setItem('bgcolor', favcolor);
		localStorage.fontweight = fontwt;
	} catch (e) {
		if (e == QUOTA_EXCEEDED_ERR) {
			alert('Quota exceeded!');
		}
	}
	} else {
		alert('Cannot store user preferences as your browser do not support local storage');
	}
}
```

我们可以通过使用浏览器附带的开发工具来检查数据是否存储在本地存储中。例如，在 Chrome 中，右键单击浏览器并选择 Inspect Element。选择资源选项卡，然后单击本地存储项目。我们可以看到用户选择的数据以键/值对的形式存储。

可以用用户设置的背景和字体大小值重新加载网页。`getItem(‘Key’)`帮助检索存储在数据库中的数据。

```
function applySetting() {
	if (localStorage.length != 0) {
	document.body.style.backgroundColor = localStorage.getItem('bgcolor');
	document.body.style.fontSize = localStorage.fontweight + 'px';
	document.getElementById('favcolor').value = localStorage.bgcolor;
	document.getElementById('fontwt').value = localStorage.fontweight;
	} else {
	document.body.style.backgroundColor = '#FFFFFF';
	document.body.style.fontSize = '13px'
	document.getElementById('favcolor').value = '#FFFFFF';
	document.getElementById('fontwt').value = '13';
	}
}
```

`length` 函数检索存储区中值的总数。

在 body 标签的`onload` 事件期间，可以调用上述函数，如下所示

`<body onload="applySetting()">`

使用`clear()`功能或`removeItem(‘key’)`功能可以清除本地存储区。在我们的例子中，下面的函数在清除按钮的`click` 事件中被调用。

```
function clearSettings() {
		localStorage.removeItem("bgcolor");
		localStorage.removeItem("fontweight");
		document.body.style.backgroundColor = '#FFFFFF';
		document.body.style.fontSize = '13px'
		document.getElementById('favcolor').value = '#FFFFFF';
		document.getElementById('fontwt').value = '13';

}
```

## 存储事件

当我们从 web 存储器中设置或删除数据时，将在`window` 对象上触发一个存储事件。如果需要，我们可以向事件添加侦听器并处理存储更改。

```
window.addEventListener('storage', storageEventHandler, false);
	function storageEventHandler(event) {
			applySetting();
	}
```

事件对象具有以下属性

*   密钥–已更改的属性
*   新值–新设置的值
*   旧值–先前存储的值
*   url–事件起源的完整 URL 路径
*   storage area–local storage 或 sessionStorage 对象

请记住，该事件仅在其他窗口上触发(而不是在触发该事件的窗口上),如果数据没有变化，则该事件不会被触发。

相同的 API 方法也适用于会话存储，只是这些方法应该在`sessionStorage` 对象上执行。

## 结论

因此，现在您可以开始使用网络存储来存储用户偏好、用户信息、会话信息等。你也可以尝试创建可以完全离线使用的应用程序，当用户再次在线时，离线期间存储的数据可以作为批量更新发送回服务器。

## 分享这篇文章