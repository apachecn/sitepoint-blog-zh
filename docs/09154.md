# 如何将 XML 转换成类似 JSON 的 JavaScript 对象

> 原文：<https://www.sitepoint.com/how-to-convert-xml-to-a-javascript-object/>

在我的文章[如何在 PHP 中创建一个 XML 到 JSON 的代理服务器](https://www.sitepoint.com/php-xml-to-json-proxy/)中，我们创建了一个将 XML 消息翻译成 Ajax-ready JSON 的系统。如果您运行的是 PHP 或其他合适的服务器端进程，那就太好了。但是如果你只限于 JavaScript 呢？

从 XML 文档中随机访问数据并不好玩。您可以使用 DOM 或 XPath 方法，但它们不像本机(JSON 生成的)JavaScript 对象属性(如`myobj.list[0].property1`)那样简单。如果您经常从同一个 XML 文档中访问数据，首先将它转换成 JavaScript 对象可能比较实用。准备好写一些代码了吗？…

## XML2jsobj 函数

我们将编写一个函数，递归地分析 XML 文档的 DOM 树的每个节点，并返回一个 JavaScript 对象。向该函数传递一个起始节点(通常是根 documentElement)并返回一个对象(内部命名的数据):

```
 function XML2jsobj(node) {

	var	data = {}; 
```

我们现在将在 XML2jsobj 中定义一个 Add()函数。这将向数据对象追加一个名称/值对，例如 data[name] = value。但是，如果该名称已经存在，它必须将 data[name]转换为数组，以便可以应用两个或更多的值:

```
 // append a value
	function Add(name, value) {
		if (data[name]) {
			if (data[name].constructor != Array) {
				data[name] = [data[name]];
			}
			data[name][data[name].length] = value;
		}
		else {
			data[name] = value;
		}
	}; 
```

我们现在需要一个循环来检查 XML 节点的属性(例如<node attrib1="1" attrib2="2">)并使用 Add()函数将它们附加到数据对象:</node>

```
 // element attributes
	var c, cn;
	for (c = 0; cn = node.attributes `；c++){
Add(cn . name，cn . value)；
-` 

下一个循环检查所有子节点。注释和空白被忽略，但是，如果一个子元素包含一个文本数据项，则使用 Add()将它追加到数据对象。如果这个孩子有自己的孩子，我们递归地调用 XML2jsobj 来生成对象:

```
 // child elements
	for (c = 0; cn = node.childNodes `；c++){
if(cn . nodetype = = 1){
if(cn . child nodes . length = = 1&&cn . first child . nodetype = = 3){
//text value
Add(cn . nodename，cn . first child . nodevalue)；
}
else {
//子对象
Add(cn . nodename，XML 2 jsobj(cn))；
}
}
}` 

最后，我们将数据对象返回给我们的调用函数:

```
 return data;

} 
```

## 转换 XML

我们的 Ajax 调用可以从 web 服务中检索 XML:

```
 // example XML feed
var url = "example.xml";

// AJAX request
var xhr = (window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHTTP"));
xhr.onreadystatechange = XHRhandler;
xhr.open("GET", url, true);
xhr.send(null); 
```

我们的 xmlhttprequestonreadystatechange 处理程序接收 XML 数据并将其转换为 JavaScript 对象:

```
 // handle response
function XHRhandler() {

	if (xhr.readyState == 4) {

		var obj = XML2jsobj(xhr.responseXML.documentElement);

		// do something with our returned data...
		console.log(obj);

		xhr = null;

	}

} 
```

因此，如果 example.xml 返回以下 xml 数据:

```
 <?xml version="1.0"?>
<statuses>
	<status id="one">
		<id>1</id>
		<text>Hello!</text>
	</status>
</statuses> 
```

XML 2 jsobj(xhr . response XML . documentelement)将返回以下对象:

```
 {
	status: {
		id: ["one", 1],
		text: "Hello!"
	}
} 
```

因此，您可以使用 obj.status.text 来检索“Hello！”文字。

## 买家当心！

关于 XML2jsobj 的几点注意事项:

*   XML 属性和子元素之间没有区别——如果它们具有相同的名称，则返回一个包含索引为 0 的属性的项目数组。
*   XML2jsobj 应该只在实用的时候使用。如果只检索一两个 XML 节点值，用 DOM 或 XPath 方法访问它们会更快。
*   该代码是跨浏览器兼容的(包括 IE6 ),可以快速处理大型 XML 文档。也就是说，它可能不适合每种情况。它可能不应该优先用于从服务器返回 JSON。

## 抓住密码

请 [**查看演示页面**](https://blogs.sitepointstatic.com/examples/tech/xml2json/index.html) 或[下载自己项目的代码和示例](https://blogs.sitepointstatic.com/examples/tech/xml2json/xml2jsobj.zip)。

我希望你会觉得它有用——如果能减轻一些 XML 问题，请告诉我！

## 分享这篇文章 
``` 
```