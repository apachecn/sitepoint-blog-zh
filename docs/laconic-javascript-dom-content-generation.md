# Laconic:一种从 JavaScript 生成 DOM 内容的新方法

> 原文：<https://www.sitepoint.com/laconic-javascript-dom-content-generation/>

可以使用 innerHTML 或 outerHTML 将内容插入到页面中…

```
 var container = document.getElementById("container");
container.innerHTML = "<p>Here's some new <strong>DOM</strong> content.</p>"; 
```

*(注意 jQuery 也使用 innerHTML 进行 DOM 内容操作)。*

它又快又简单，但也不是没有问题:

1.  可能会插入无效的 HTML 字符串，这使得很难发现和调试错误。
2.  如果您尝试复杂的活动，例如使用或修改结果 DOM 内容中的节点，可能会遇到浏览器问题。
3.  尽管 innerHTML 得到了很好的支持，但它是一项专有技术，不是 W3C DOM 的一部分。这违背了神的标准。

所以让我们看看基于 DOM 的替代方案:

```
 var newpara = document.createElement("p");
var newstrong = document.createElement("strong");
newstrong.appendChild(document.createTextNode("DOM"));
newpara.appendChild(document.createTextNode("Here's some new "));
newpara.appendChild(newstrong);
newpara.appendChild(document.createTextNode(" content."));

var container = document.getElementById("container");
container.appendChild(newpara); 
```

恶心。它要长三倍，执行起来更慢，而且仍然容易出现人为错误。

几年前，我自己设计了一个解决方案， [BetterInnerHTML](http://www.optimalworks.net/resources/betterinnerhtml/) ，它加载一个 HTML 字符串作为 XML，递归这个结构并将适当的节点插入 DOM。它工作了，但是我从来没有对性能或 HTML 实体这样的问题完全满意过。

幸运的是，Joe Stelmach 设计了一个替代方案。这是一个名为 [Laconic](https://github.com/joestelmach/laconic) 的小型独立实用程序，它使用逻辑 JavaScript 符号直接映射到 HTML，例如

```
 $.el.p(
	"Here's some new ",
	$.el.strong("DOM"),
	" content."
).appendTo(document.getElementById("container")); 
```

使用对象文字符号支持属性:

```
 // produce <div class="example"><div>Content</div></div>
$.el.div(
	{ "class": "example"},
	$.el.div("Content")
); 
```

我喜欢。虽然 innerHTML 仍然是快速生成脏 DOM 内容的最佳选择，但在无法按预期工作的奇怪情况下，Laconic 会很有用。

有关更多信息和下载，请参考:

*   [GitHub 上的简洁](https://github.com/joestelmach/laconic)
*   [简洁的示例页面](http://joestelmach.github.com/laconic/)

## 分享这篇文章