# Node.js 中的网页抓取

> 原文：<https://www.sitepoint.com/web-scraping-in-node-js/>

网页抓取器是一种软件，它以编程方式访问网页并从中提取数据。由于内容重复的问题，网络抓取是一个有点争议的话题。相反，大多数网站所有者更喜欢公开可用的 API 访问他们的数据。不幸的是，许多网站提供的 API 乏善可陈，或者根本没有。这迫使许多开发者转向网络抓取。本文将教你如何在 Node.js.
中实现自己的 web scraper

网络抓取的第一步是从远程服务器下载源代码。在“[在 Node.js](https://www.sitepoint.com/making-http-requests-in-node-js/ "Making HTTP Requests in Node.js") 中发出 HTTP 请求”中，读者学习了如何使用`[request](https://github.com/mikeal/request "mikeal/request - GitHub")`模块下载页面。下面的例子提供了在 Node.js 中进行`GET`请求的快速复习。

```
var request = require("request");

request({
uri: "http://www.sitepoint.com",
}, function(error, response, body) {
console.log(body);
});
```

web 抓取的第二步，也是更困难的一步，是从下载的源代码中提取数据。在客户端，使用[选择器 API](http://cjihrig.com/blog/javascripts-selectors-api/ "JavaScript’s Selectors API") ，或者像 [jQuery](http://jquery.com/ "jQuery: The Write Less, Do More, JavaScript Library") 这样的库，这将是一个微不足道的任务。不幸的是，这些解决方案依赖于 DOM 可用于查询的假设。遗憾的是，Node.js 没有提供 DOM。是吗？

## 啦啦队模块

虽然 Node.js 没有提供内置的 DOM，但是有几个模块可以从一串 HTML 源代码中构造一个 DOM。两个流行的 DOM 模块是 [`cheerio`](https://github.com/MatthewMueller/cheerio "MatthewMueller/cheerio - GitHub") 和 [`jsdom`](https://github.com/tmpvar/jsdom "tmpvar/jsdom - GitHub") 。本文主要关注`cheerio`，可以使用下面的命令安装它。

```
npm install cheerio

```

`cheerio`模块实现了 jQuery 的一个子集，这意味着许多开发人员将能够很快掌握它。事实上，`cheerio`与 jQuery 非常相似，以至于你很容易发现自己在尝试使用没有在`cheerio`中实现的 jQuery 函数。

下面的例子展示了如何使用`cheerio`来解析 HTML 字符串。第一行将`cheerio`导入到程序中。`html`变量保存要解析的 HTML 片段。在第 3 行，使用`cheerio`解析 HTML。结果被赋给`$`变量。选择美元符号是因为它通常在 jQuery 中使用。第 4 行使用 CSS 样式选择器选择了`<ul>`元素。最后，使用`html()`方法打印列表的内部 HTML。

```
var cheerio = require("cheerio");
var html = "<ul><li>foo</li><li>bar</li></ul>";
var $ = cheerio.load(html);
var list = $("ul");

console.log(list.html());
```

### 限制

正在积极开发中，并且一直在变得更好。然而，它仍然有许多限制。`cheerio`最令人沮丧的方面是 HTML 解析器。HTML 解析是一个很难的问题，并且存在大量包含不良 HTML 的页面。虽然`cheerio`在这些页面上不会崩溃，但你可能会发现自己无法选择元素。这使得很难确定是选择器还是页面本身有问题。

## 抓取 JSPro

下面的例子结合了`request`和`cheerio`来构建一个完整的 web scraper。示例 scraper 提取 JSPro 主页上所有文章的标题和 URL。前两行将所需模块导入到示例中。第 3 行到第 5 行下载了 JSPro 主页的源代码。然后将源代码传递给`cheerio`进行解析。

```
var request = require("request");
var cheerio = require("cheerio");

request({
uri: "http://www.sitepoint.com",
}, function(error, response, body) {
var $ = cheerio.load(body);

$(".entry-title > a").each(function() {
var link = $(this);
var text = link.text();
var href = link.attr("href");

console.log(text + " -> " + href);
});
});
```

如果您查看 JSPro 源代码，您会注意到每篇文章的标题都是包含在类`entry-title`的`<h1>`元素中的一个链接。第 7 行的选择器选择所有的文章链接。然后使用`each()`函数来循环所有的文章。最后，文章标题和 URL 分别取自链接的文本和`href`属性。

## 结论

这篇文章向你展示了如何在 Node.js 中创建一个简单的网页抓取程序。还有其他技术，比如使用无头浏览器，这些技术更强大，但可能会牺牲简单性和/或速度。请关注即将发表的一篇关于无头浏览器的文章。

## 分享这篇文章