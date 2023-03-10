# Ajax 和 Web 服务数据格式第 2 部分:JSON 和 JSONP

> 原文：<https://www.sitepoint.com/ajax-data-formats-json-jsonp/>

这是讨论 Ajax web 服务数据格式系列的第二篇文章。在第 1 部分中，我们研究了 XML、SOAP 和 HTML。今天，我们仔细看看 JSON 和 JSONP。

## JSON

JavaScript 对象符号是由 JavaScript 大师道格拉斯·克洛克福特设计的。本质上，它是一种使用 JavaScript 对象和数组字面语法编写的轻量级数据交换格式。

我们可以将第一篇文章中的 XML books 示例转换成类似的 JSON 格式:

```
 [
	{
		title: "The Principles of Beautiful Web Design, 2nd Edition",
		url: "https://www.sitepoint.com/books/design2/",
		author: "Jason Beaird",
		publisher: "SitePoint",
		price: {
			currency: "USD",
			amount: 39.95
		}
	},
	{
		title: "jQuery: Novice to Ninja",
		url: "https://www.sitepoint.com/books/jquery1/",
		author: "JEarle Castledine & Craig Sharkie",
		publisher: "SitePoint",
		price: {
			currency: "USD",
			amount: 29.95
		}
	},
	{
		title: "Build Your Own Database Driven Website",
		url: "https://www.sitepoint.com/books/phpmysql4/",
		author: "Kevin Yank",
		publisher: "SitePoint",
		price: {
			currency: "USD",
			amount: 39.95
		}
	}
] 
```

这是一个书籍数组，表示为具有标题、url、作者、出版商和价格属性的对象。Price 是具有货币和数字金额属性的子对象。

用 JavaScript 解析一串 JSON 数据很容易。理想情况下，你可以使用浏览器的原生 JSON.parse( *json-string* )方法或者像道格拉斯·克洛克福特的 [JSON-js](https://github.com/douglascrockford/JSON-js) 这样的库。即使这些都不可用，您也可以使用 JavaScript 的本机 [eval()](http://en.wikipedia.org/wiki/Eval#JavaScript) 函数。无需编写解析代码，检索数据也很容易:

```
 var json = xhr.responseText;
var book = JSON.parse(json);
alert(book[0].title); // first book title
alert(book[1].url); // second book URL 
```

JSON 的优点是:

*   一种比 XML 更小、更简洁的格式。
*   JSON 通常是可读的，尽管最好使用制表符和回车来分隔数据。与 XML 不同，浏览器通常不会以可读的格式显示 JSON，尽管查看插件可用于 [Firefox](https://addons.mozilla.org/en-US/firefox/addon/jsonview/) 、 [Chrome](https://chrome.google.com/extensions/detail/chklaanhfefbnpoihckbnefhakgolnmc) 和 Opera。
*   在 JavaScript 中解析和使用 JSON 数据很容易。
*   JSON 支持在其他语言中提供，比如 [PHP](http://php.net/manual/en/book.json.php) 。

有几个缺点:

*   在服务器端语言中，JSON 的支持比 XML 少，尽管许多第三方库可以从[JSON.org 网站](http://www.json.org/)获得。
*   如果您依赖 eval()来解析 JSON 字符串，就会出现安全问题——负载可能包含恶意脚本。

然而，JSON 的好处大于风险。这是一种理想的 Ajax 数据格式，应该是您的首选。

## JSONP(或 JSON-P)

如果您使用 XMLHttpRequest 调用 JSON web 服务，响应将作为字符串返回，可以使用 JSON.parse()或 eval()解析该字符串。但是，您也可以创建使用脚本注入技术的 Ajax 组件，即在 DOM 中添加一个`script`标签来加载远程代码:

```
 var script = document.createElement("script");
script.src = "http://webservice.com/?a=1&b=2";
document.getElementsByTagName("head")[0].appendChild(script); 
```

与 XMLHttpRequest 不同，脚本注入可以从不同域上的远程服务器检索数据。这使得该技术成为流量分析器、书签、小工具和广告系统的理想选择。

不幸的是，任何返回的 JSON 数据都会立即作为原生 JavaScript 代码执行。它没有被赋值给变量，所以永远丢失了。我们可以通过将回调函数的名称传递给我们的 web 服务来解决这个问题:

```
 var script = document.createElement("script");
script.src = "http://webservice.com/?a=1&b=2&callback=MyDataHandler";
document.getElementsByTagName("head")[0].appendChild(script); 
```

web 服务返回包装在回调函数调用中的 JSON 数据。这是 JSONP，或“带填充的 JSON”，例如

```
 MyDataHandler([
	{
		title: "The Principles of Beautiful Web Design, 2nd Edition",
		url: "https://www.sitepoint.com/books/design2/",
		author: "Jason Beaird",
		publisher: "SitePoint",
		price: {
			currency: "USD",
			amount: 39.95
		}
	}
	...
]); 
```

下载一完成，JSON 对象就被解析并传递给 MyDataHandler()函数。

JSON 和 JSONP 已经成为流行的 Ajax 数据格式。然而，还可以进一步减少数据量— [我们将在本系列的最后一篇文章](https://www.sitepoint.com/ajax-data-formats-custom/)中讨论这些选项。

## 分享这篇文章