# 集合不是数组

> 原文：<https://www.sitepoint.com/a-collection-is-not-an-array/>

我偶尔会对不能像数组一样操作一组 <abbr title="Document Object Model">DOM</abbr> 元素(更正式的称呼是`NodeList`)感到恼火，因为它不是数组。然而，它看起来确实像一个，认为它是一个是 JavaScript 新手经常犯的错误，对于我们即将到来的 JavaScript 参考，我觉得有必要为每一个单独的*<abbr title="Document Object Model">DOM</abbr>对象(即或返回集合)指出这一点。*

 *您可以像数组一样遍历集合:

```
for(var i=0; i<collection.length; i++)
{
	//whatever
}
```

但是你不能用`push()`、`splice()`或`reverse()`这样的`Array`方法来操纵它。

除非你可以，如果你进行下一步，把它转换成一个数组。这实际上是微不足道的:

```
function collectionToArray(collection)
{
	var ary = [];
	for(var i=0, len = collection.length; i < len; i++)
	{
		ary.push(collection[i]);
	}
	return ary;
}
```

上面的代码是完全跨浏览器的，调用时使用原始集合作为参数:

```
var elements = collectionToArray(document.getElementsByTagName('*'));
```

然而，如果您只需要处理支持原生对象原型的浏览器(Opera、Firefox 和 Safari 3)，那么您可以简单地创建一个`NodeList`的`toArray()`方法:

```
NodeList.prototype.toArray = function()
{
	var ary = [];
	for(var i=0, len = this.length; i < len; i++)
	{
		ary.push(this[i]);
	}
	return ary;
};
```

然后可以调用它作为单个集合的方法:

```
var elements = document.getElementsByTagName('*').toArray();
```

这种转换有一个明显的缺点(不管它是如何完成的)，那就是得到的数组将不再是一个`NodeList`。显而易见，是的，但相关，因为它有两个含义:

*   它将**失去它从`NodeList`** 继承的属性和方法。然而`NodeList`只有一个属性(它的`length`，也可用于数组)和一个方法(方法`item()`，它通常是冗余的，因为成员仍然可以用方括号符号来引用)。所以这个损失一点也不重要
*   它将**不再是一个现场集合**。一个`NodeList`是一个对对象集合的*引用*，如果该集合发生变化(例如，元素被添加或删除),`NodeList`将自动更新以反映这种变化；相反，我们的数组是集合在某个时间点的静态快照，因此不会随着 <abbr title="Document Object Model">DOM</abbr> 的变化而更新。根据您的应用，这一点可能很重要。* 

## *分享这篇文章*