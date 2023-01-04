# JavaScript 与 jQuery HTML 集合

> 原文：<https://www.sitepoint.com/javascript-vs-jquery-html-collections/>

HTMLCollection 对象从`document.getElementsByTagName`、`document.getElementsByName`和`document.getElementsByClassName`方法*(并非所有浏览器都支持)*返回。从表面上看，它们类似于数组，因为它们具有长度属性，元素可以通过[index]访问。然而，它们不是数组；不支持 push()、slice()和 sort()等方法。

考虑下面的 HTML 文档:

```
 <body>
	<p>Paragraph 1</p>
	<p>Paragraph 2</p>
	<p>Paragraph 3</p>
</body> 
```

让我们使用 getElementsByTagName 和一个 jQuery 选择器来获取每个段落节点:

```
 var pCollection = document.getElementsByTagName("p");
var pQuery = $("p");

console.log("pCollection.length: ", pCollection.length);
console.log("pQuery.length: ", pQuery.length); 
```

两者都返回相同的节点，因此集合长度为 3:

```
 pCollection.length: 3
pQuery.length: 3 
```

我们现在将向文档添加另一个段落元素，并再次查看集合:

```
 // add new paragraph
var newp = document.createElement("p");
newp.appendChild(document.createTextNode("Paragraph 4"));
document.body.appendChild(newp);
// 
// display length
console.log("pCollection.length: ", pCollection.length);
console.log("pQuery.length: ", pQuery.length); 
```

结果是:

```
 pCollection.length: 4
pQuery.length: 3 
```

HTMLCollection 对象是 *live* 。每当基础文档发生变化时，它们就会自动更新。jQuery 和大多数其他 JavaScript 库使用 document.getElementsByTagName()之类的方法，但是将结果节点复制到一个真实的数组中。因此，这是对当时文档状态的查询:它从不更新。

这两种方法各有利弊。例如，以下代码会导致无限循环，因为 HTMLCollection 长度会随着添加

元素而增加:

```
 var pCollection = document.getElementsByTagName("p");
for (var i = 0; i < pCollection.length; i++) {
	document.body.appendChild(pCollection[i].cloneNode(true));
} 
```

也就是说，在某些情况下，更快的本地动态 HTMLCollection 比静态的 jQuery 节点集合或重复进行相同的选择更有用。幸运的是，当我们想要操作 jQuery 时，可以将任何集合传递给它，例如

```
 var pCollection = document.getElementsByTagName("p");
// ... add nodes, do work, etc ...
$(pCollection).addClass("myclass"); 
```

jQuery 和其他库可以减少开发工作量，但总是检查是否有可能用普通的旧 JavaScript 编写更高效的代码，而不会导致额外的文件请求和处理开销。

## 分享这篇文章