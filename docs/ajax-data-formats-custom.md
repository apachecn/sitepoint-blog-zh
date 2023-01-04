# Ajax 和 Web 服务数据格式第 3 部分:自定义响应

> 原文：<https://www.sitepoint.com/ajax-data-formats-custom/>

这是讨论 Ajax web 服务数据格式系列的最后一篇文章。第 1 部分研究了 XML、SOAP 和 HTML，第 2 部分讨论了 T2 JSON 和 JSONP。今天，我们来看看自定义数据格式。

最有效的数据传输格式使用最少数量的结构元素来界定数据。考虑一下我们在 XML 和 JSON 中的书籍数据。我们总是期望每本书有六项数据，所以我们可以简单地使用这样的格式:

```
 The Principles of Beautiful Web Design, 2nd Edition;https://www.sitepoint.com/books/design2/;Jason Beaird;SitePoint;39.95;USD
jQuery: Novice to Ninja;https://www.sitepoint.com/books/jquery1/;JEarle Castledine & Craig Sharkie;SitePoint;29.95;USD
Build Your Own Database Driven Website;https://www.sitepoint.com/books/phpmysql4/;Kevin Yank;SitePoint;39.95;USD 
```

本质上，我们的数据类似于逗号分隔的列表。我们使用回车来分隔书籍，使用分号来分隔单个数据项(不能使用逗号，因为它们出现在书名中，也可能出现在数值中)。选择正确的数据分隔符是我们必须做出的最重要的决定。

与 JSON 不同，我们必须从返回的字符串中提取和解析数据，但这可以使用 JavaScript 的 string split()方法快速轻松地实现。下面的代码将把上面的数据转换成相同的 JSON 格式:

```
 // convert custom data to an array of JavaScript objects
function ParseBookData(ajaxdata) {
	var book = [], bookData = ajaxdata.split("n"), bookItem;
	for (var b=0, bl=bookData.length; b < bl; b++) {
		bookItem = bookData[b].split(";");
		book[b] = {
			title: bookItem[0],
			url: bookItem[1],
			author: bookItem[2],
			publisher: bookItem[3],
			price: {
				amount: parseFloat(bookItem[4]),
				currency: bookItem[5]
			}
		};

	}
	return book;
}

var book = ParseBookData(xhr.responseText);
alert(book[0].title); // first book title
alert(book[1].url); // second book URL 
```

JavaScript 可以非常快速地处理数据——即使返回了数千本书。在大多数情况下，您会发现下载和解析数据的总时间比同等的基于 JSON 的 Ajax 代码要少。

自定义数据格式有几个优点:

*   这是最轻量级的格式，将导致更快的 Ajax 响应。
*   任何服务器端语言都可以快速生成数据，而无需额外的格式库。
*   创建恶意负载并不容易。

但是注意缺点:

*   如果对象属性的数量不一致，这可能不是可行的解决方案。例如，假设我们的书有可选的 PDF 网址或多种货币的价格。我们可能需要更多的分隔符，我们的解析将变得更加困难。
*   您的 web 服务在范围上可能比返回 XML 或 JSON 的服务更受限制。如果您正在为自己的应用程序编写服务，这不是一个问题，但是如果您打算为第三方发布数据，这是一个需要考虑的问题。
*   必须为每种类型的数据响应编写自定义解析器。
*   数据不一定是人类可读的。
*   您必须绝对确定原始数据中没有出现分隔符。您可以检查它们是否存在，或者考虑在 ASCII 字符集的开头使用分隔符。

我希望这一系列文章已经为您提供了许多 Ajax 数据格式化选项供您考虑。你用过其他数据格式吗？

## 分享这篇文章