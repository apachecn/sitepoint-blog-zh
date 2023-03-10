# childNodes (W3C DOM 核心属性)

> 原文：<https://www.sitepoint.com/childnodes-w3c-dom-core-property/>

## 例子

```
var kids = node.childNodes;
```

在上面的例子中，`kids`将是`node`的所有直接子节点的集合。如果`node`没有子节点，那么`kids`将是一个空集合(长度为零)。返回的集合是 <dfn class="firstterm">live</dfn> ，这意味着对它表示的 HTML 的更改会立即反映在集合中，而不必再次检索它。

所以如果我们说`node`实际上是一个 HTML `ul`元素，就像这样:

```
<ul>
  <li>Mostly set in 1955 <em>(Part 1)</em></li>
  <li>Mostly set in 2015 <em>(Part 2)</em></li>
  <li>Mostly set in 1885 <em>(Part 3)</em></li>
</ul>
```

这些`li`元素中的每一个都是`ul`的子节点，并将被包含在它的`childNodes`集合中，编号为`0`到`2`。`em`元素将不在该集合中，因为它们不是列表的直接子节点(它们是<dfn class="firstterm">的后代</dfn>，而不是子节点)。

**Note:** This example doesn’t take account of whitespace

实际上这个例子是理想化的，在一些浏览器中，`childNodes`集合中可能有额外的节点；这是因为一些浏览器将中间的空白算作文本节点，因此会将结构节点之间的每一个*空白视为一个文本节点。*

有关此行为的更多信息，请参见 [DOM Core](https://reference.sitepoint.com/javascript/domcore) 。

## 描述

childNodes 集合是此节点的所有直接子节点的有序列表；如果没有子节点，则该集合为空(长度为零)。childNodes 集合是一个[节点列表](https://reference.sitepoint.com/javascript/NodeList)，其中的项目按数字索引，并按源代码顺序显示。

与所有节点列表一样，childNodes 是一个动态集合，这意味着对它所代表的集合的更改会立即反映在节点列表中(而不是静态快照)。

元素的属性不被视为子节点，因此不会出现在 child nodes 集合 2 中

此集合是只读的。

> ### 集合不是数组
> 
> 尽管一个集合看起来像一个数组，但它不是一个数组——尽管您可以像数组一样遍历它并引用它的成员，但您不能对它使用像 push 或 pop 这样的数组方法。

## 分享这篇文章