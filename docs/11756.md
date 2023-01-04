# JavaScript 中的跨浏览器 JSON 序列化

> 原文：<https://www.sitepoint.com/javascript-json-serialization/>

在本文中，我们将研究对象序列化的好处，当前的浏览器实现，并开发一些代码来帮助你的基于 Ajax 的项目。

假设我们有一个使用文字符号定义的相当复杂的 JavaScript 对象:

```
 var obj1 = {
	b1: true,
	s1: "text string",
	n1: 12345,
	n2: null,
	n3: undefined,
	a1: [ 1,1,2,3,5,8, [13, 21, 34] ],
	o1: {
		a: [3, 2, 1],
		b: {
			c: 42,
			d: [ 3.14, 1.618 ]
		}
	}
}; 
```

我们可以通过多种方式访问任何对象属性:

```
 obj1.s1;				// returns "text string"
obj1["n1"];				// returns 12345
obj1.a1[6][1];			// returns 21
obj1["o1"]["b"]["c"];	// returns 42 
```

这个对象也可以传递给 JavaScript 函数和方法，而不是指定单独的参数。有用的东西。

但是，如果我们需要将这个对象存储在一个 cookie 中呢？如果我们需要通过 Ajax 请求将对象传递给 web 服务呢？如果 web 服务想要返回对象的修改版本，该怎么办？答案是序列化:

*   **序列化**就是把任何对象变成字符串的过程。
*   **反序列化**将字符串转换回原生对象。

也许我们可以在 JavaScript 中使用的最好的字符串表示法是 JSON——JavaScript 对象表示法。JSON 是一种轻量级的数据交换格式，灵感来自于如上所示的 JavaScript 对象文字符号。PHP 和许多其他服务器端语言都支持 JSON(参考[json.org](http://www.json.org/))。

JavaScript 中有两种 JSON 方法:

1.  **JSON . stringify(*obj*)**—将 JavaScript 对象转换为 JSON 字符串
2.  **JSON . parse(*str*)**—将 JSON 字符串转换回 JavaScript 对象

不幸的是，很少有浏览器提供这些方法。迄今为止，只有 Firefox 3.5、Internet Explorer 8.0 和 Chrome 3 beta 提供原生支持。一些 JavaScript 库提供了自己的 JSON 工具(如 [YUI](http://developer.yahoo.com/yui/json/) )，但许多没有(包括 [jQuery](http://jquery.com/) )。

然而，并没有失去一切——JavaScript 很灵活，只要浏览器需要，我们就可以实现 JSON stringify 和 parse 方法。

在代码的顶部，我们将创建一个 JSON 变量，该变量指向本机 JSON 对象或一个空对象(如果该对象不可用):

```
 var JSON = JSON || {}; 
```

JSON.stringify 代码稍微复杂一些:

```
 // implement JSON.stringify serialization
JSON.stringify = JSON.stringify || function (obj) {

	var t = typeof (obj);
	if (t != "object" || obj === null) {

		// simple data type
		if (t == "string") obj = '"'+obj+'"';
		return String(obj);

	}
	else {

		// recurse array or object
		var n, v, json = [], arr = (obj && obj.constructor == Array);

		for (n in obj) {
			v = obj[n]; t = typeof(v);

			if (t == "string") v = '"'+v+'"';
			else if (t == "object" && v !== null) v = JSON.stringify(v);

			json.push((arr ? "" : '"' + n + '":') + String(v));
		}

		return (arr ? "[" : "{") + String(json) + (arr ? "]" : "}");
	}
}; 
```

如果 JSON.stringify 不可用，我们定义一个新函数，它接受单个 obj 参数。该参数可以是单个值、数组或复杂对象，如上面的 obj1。

该代码检查对象类型。单个值会立即返回，只有字符串会被修改，并在值的两边加上引号。

如果传递了数组或对象，代码将遍历每个属性:

1.  字符串值添加了引号。
2.  子数组或对象被递归传递给 JSON.stringify 函数。
3.  结果值作为“name : value”字符串添加到 json[]数组的末尾，或者只是数组项的单个值。
4.  最后，json 数组被转换成逗号分隔的列表，并根据需要在 array []或 object {}括号内返回。

如果您感到头疼，您会很高兴地知道 JSON.parse 代码要简单得多:

```
 // implement JSON.parse de-serialization
JSON.parse = JSON.parse || function (str) {
	if (str === "") str = '""';
	eval("var p=" + str + ";");
	return p;
}; 
```

这将使用 eval()将 JSON 字符串转换为对象。

在您匆忙在所有项目中实现 JSON 序列化函数之前，有几个问题需要注意:

*   这段代码有意保持简短。它在大多数情况下都可以工作，但是与本机 JSON.stringify 和 JSON.parse 方法有细微的区别。
*   并不是每个 JavaScript 对象都受支持。例如，Date()将返回一个空对象，而本地 JSON 方法将把它编码成一个日期/时间字符串。
*   代码将序列化函数，例如 var obj 1 = { my func:function(x){ } }；而原生 JSON 方法不会。
*   非常大的对象会抛出递归错误。
*   在 JSON.parse 中使用 eval()本身就有风险。如果您调用自己的 web 服务，这不会有问题，但是调用第三方应用程序可能会意外或故意破坏您的页面并导致安全问题。如果有必要的话，可以从[json.org](http://www.json.org/)获得一个更安全(但是更长和更慢)的 JavaScript 解析器。

我希望这些代码对您有用。请随意在您自己的项目中使用它。

资源文件:

*   [**JSON 序列化演示页面**](https://blogs.sitepointstatic.com/examples/tech/json-serialization/index.html)
*   [完整的 JavaScript 代码(json-serialization.js)](https://blogs.sitepointstatic.com/examples/tech/json-serialization/json-serialization.js)
*   [下载 ZIP 文件中的完整代码](https://blogs.sitepointstatic.com/examples/tech/json-serialization/json-serialization.zip)

相关阅读:

*   [如何开发一个 jQuery 插件](https://www.sitepoint.com/how-to-develop-a-jquery-plugin/)
*   [如何构建自动扩展的 Textarea jQuery 插件](https://www.sitepoint.com/build-auto-expanding-textarea-1/)

*即将推出:JSON 序列化的一个有用应用…*

## 分享这篇文章