# 从 DOM 中删除无用的节点

> 原文：<https://www.sitepoint.com/removing-useless-nodes-from-the-dom/>

作为这个系列的第三篇关于简短函数的文章，我将向您展示一个我认为不可或缺的简单函数，在使用 <abbr title="HyperText Markup Language">HTML</abbr> <abbr title="Document Object Model">DOM</abbr> 时。这个函数叫做`clean()`，它的目的是移除注释和只有空白的文本节点。

该函数将单个元素引用作为其参数，并从其中移除所有不需要的节点。该函数直接对相关元素进行操作，因为 JavaScript 中的对象是通过引用传递的，这意味着该函数接收的是对原始对象的引用，而不是它的副本。下面是`clean()`函数的代码:

```
function clean(node)
{
  for(var n = 0; n < node.childNodes.length; n ++)
  {
    var child = node.childNodes[n];
    if
    (
      child.nodeType === 8 
      || 
      (child.nodeType === 3 && !/\S/.test(child.nodeValue))
    )
    {
      node.removeChild(child);
      n --;
    }
    else if(child.nodeType === 1)
    {
      clean(child);
    }
  }
}
```

因此，要从`<body>`元素中清除那些不需要的节点，您只需这样做:

```
clean(document.body);
```

或者，要清理整个文档，您可以这样做:

```
clean(document);
```

虽然通常的引用是一个`Element`节点，但是它也可以是另一种包含元素的节点，比如一个`#document`。该函数也不局限于处理 HTML，可以处理任何其他类型的 <abbr title="eXtensible Markup Language">XML</abbr> DOM。

## 为什么要清理大教堂

当在 JavaScript 中使用 DOM 时，我们使用标准属性如`firstChild`和`nextSibling`来获取相对节点引用。不幸的是，当 DOM 中存在空白时，情况会变得复杂，如下例所示。

```
<div>
  <h2>Shopping list</h2>
  <ul>
    <li>Washing-up liquid</li>
    <li>Zinc nails</li>
    <li>Hydrochloric acid</li>
  </ul>
</div>
```

对于大多数现代浏览器(除了 <abbr title="Internet Explorer 8">IE8</abbr> 和更早的版本)，前面的 HTML 代码会产生下面的 DOM 结构。

```
DIV
#text ("\n\t")
+ H2
| + #text ("Shopping list")
+ #text ("\n\t")
+ UL
| + #text ("\n\t\t")
| + LI
| | + #text ("Washing-up liquid")
| + #text ("\n\t\t")
| + LI
| | + #text ("Zinc nails")
| + #text ("\n\t\t")
| + LI
| | + #text ("Hydrochloric acid")
| + #text ("\n\t")
+ #text ("\n")
```

树中的换行符和制表符显示为空白`#text`节点。因此，举例来说，如果我们从引用`<h2>`元素开始，那么`h2.nextSibling`将*而不是*引用`<ul>`元素。相反，它会引用它前面的空白`#text`节点(换行符和制表符)。或者，如果我们从引用`<ul>`元素开始，那么`ul.firstChild`将不是第一个`<li>`，而是它前面的空格。

HTML 注释也是节点，大多数浏览器也将它们保存在 DOM 中——这是应该的，因为不是由浏览器来决定哪些节点重要，哪些不重要。但是脚本实际上很少*想要*注释中的数据。注释(和中间的空格)更有可能是不需要的“垃圾”节点。

有几种方法可以处理这些节点。例如，通过迭代它们:

```
var ul = h2.nextSibling;
while(ul.nodeType !== 1)
{
  ul = ul.nextSibling;
}
```

最简单、最实用的方法是简单地删除它们。这就是`clean()`函数所做的——有效地规范化元素的子树，创建一个符合我们实际使用的模型，并且在不同浏览器之间是相同的。

一旦原始示例中的`<div>`元素被清除，那么`h2.nextSibling`和`ul.firstChild`引用将指向预期的元素。清理后的 DOM 如下所示。

```
SECTION
+ H2
| + #text ("Shopping list")
+ UL
| + LI
| | + #text ("Washing-up liquid")
| + LI
| | + #text ("Zinc nails")
| + LI
| | + #text ("Hydrochloric acid")
```

## 该功能如何工作

`clean()`函数是递归的——一个调用自身的函数。递归是一个非常强大的特性，这意味着该函数可以清理任何大小和深度的子树。递归行为的关键是`if`语句的最终条件，下面将重复。

```
else if(child.nodeType === 1)
{
  clean(child);
}
```

因此，元素的每个子元素都被传递给`clean()`。然后，该子节点的子节点被传递给`clean()`。这一直持续到所有后代都被清除。

在`clean()`的每个调用中，该函数遍历元素的`childNodes`集合，删除任何值为空白的`#comment`节点(其`nodeType`为 8)或`#text`节点(其`nodeType`为 3)。正则表达式实际上是一个逆向测试，寻找那些*不包含非空白字符的节点。*

当然，这个函数不会删除*所有的*空格。任何作为包含非空白文本的`#text`节点一部分的空白都会被保留。因此，唯一受影响的`#text`节点是那些只有空白的节点。

注意迭代器每次都要查询`childeNodes.length`，而不是提前保存`length`，这样通常效率更高。我们之所以这样做，是因为我们正在*删除*节点，这显然会改变集合的长度。

## 分享这篇文章