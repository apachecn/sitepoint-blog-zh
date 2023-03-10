# getElementsByTagName (W3C DOM 核心方法)

> 原文：<https://www.sitepoint.com/getelementsbytagname-w3c-dom-core-method/>

## 返回

[节点列表](https://reference.sitepoint.com/javascript/NodeList "NodeList")

## 例子

```
var paragraphs = document.getElementByTagName('p');
```

上面的例子获取了对当前文档中的`p`元素集合的引用，并将其保存到变量`paragraphs`中。返回的集合是 <dfn class="firstterm">live</dfn> ，这意味着对它表示的 HTML 的更改会立即反映在集合中，而不必再次检索它。

此方法也可以在上下文中使用，以获取对特定元素内部的元素的引用，例如:

```
var items = list.getElementByTagName('li');
```

因此，如果那个例子中的`list`是一个`ul`元素，那么`items`变量将引用列表中所有`li`元素的集合。

元素按照它们在文档中出现的线性顺序进行检索，即。它们对应于 DOM 的展平视图。因此，如果在前面的例子中，列表对应于这个 HTML:

```
<ul>
  <li>Cheeses
    <ul>
      <li>Edam</li>
      <li>Gouda</li>
      <li>Cheddar</li>
    </ul>
  </li>
  <li>Hams
    <ul>
      <li>Prosciutto</li>
      <li>Parma</li>
      <li>Salami</li>
    </ul>
  </li>
</ul>
```

返回的集合将按以下顺序排列:

1.  奶酪
2.  `[1]` Edam
3.  `[2]`豪达
4.  切达干酪
5.  `[4]`火腿
6.  火腿
7.  `[6]` Parma
8.  意大利腊肠

## 争论

**名称** `([DOMString](https://reference.sitepoint.com/javascript/domcore#domcore__DOMString))` **需要**
元素的`[tagName](https://reference.sitepoint.com/javascript/Element/tagName "tagName")`来匹配。值*匹配所有元素。

## 描述

获取具有给定标记名的所有元素的有序列表，这些元素是该文档或元素的后代，按照它们在 DOM 树中出现的线性顺序排列。

返回的集合是一个`NodeList` —一个有序的节点集合，从零开始进行数字索引。如果没有匹配的元素，那么它就是一个没有成员的集合。

> ## 集合不是数组
> 
> 尽管一个集合看起来像一个数组，但它不是一个数组——尽管您可以像数组一样遍历它并引用它的成员，但您不能对它使用像 push 或 pop 这样的数组方法。

## 返回值

包含所有匹配元素的节点列表；如果没有找到元素，这将是一个没有成员的列表。

## 分享这篇文章