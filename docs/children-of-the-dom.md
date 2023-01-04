# 大教堂的孩子们

> 原文：<https://www.sitepoint.com/children-of-the-dom/>

在 <abbr title="Document Object Model">DOM</abbr> 中的紧密节点关系总是有问题的，因为大多数对 <abbr title="Document Object Model">DOM</abbr> **的解释都包含空白文本节点**，这是脚本通常不关心的。

当然，应该包括它们，因为不是由实现来决定这个或那个节点是否重要。尽管如此，空白文本节点通常并不重要，它们只是碍事，使像`firstChild`和`nextSibling`这样简单的关系变得复杂。

下面是一个简单的标记示例来演示:

```
<ul>
  <li>list-item 1</li>
  <li>list-item 2</li>
  <li>list-item 3</li>
</ul>
```

所以那个`<ul>`元素的`firstChild`是*而不是第一个`<li>`元素的*，它是`<ul>`和`<li>`标签之间的空白(即换行符和制表符)。同样，第一个列表项的`nextSibling`不是第二个列表项，而是中间的空白文本节点。

## 经典解决方案

这并不新鲜，传统上我们有三种基本的处理方式。第一种是**像这样使用基于集合的引用**:

```
var item = list.getElementsByTagName('li')[0];
```

第二种方法是**遍历不需要的节点**，使用`nodeType`测试来确定何时得到我们想要的节点:

```
var item = list.firstChild;
while(item.nodeType != 1)
{
  item = item.nextSibling;
}
```

第三种也是最强力的解决方案是简单地**完全删除不需要的节点**，使用类似这样的递归函数(也删除注释节点):

```
function clean(element)
{
  for(var x = 0; x < element.childNodes.length; x ++)
  {
    var child = element.childNodes[x];
    if(child.nodeType == 8 
      || (child.nodeType == 3 && !/S/.test(child.nodeValue)))
    {
      element.removeChild(element.childNodes[x --]);
    }
    if(child.nodeType == 1)
    {
      clean(child);
    }
  }
}
```

## 元素遍历解决方案

这些解决方案都可以工作，但是有一种更简单、更容易的方法来获得我们想要的元素引用，使用在 [DOM3 元素遍历](https://www.w3.org/TR/ElementTraversal/)中定义的一组鲜为人知的引用。

元素遍历规范定义了四个新的引用，其中只有*与元素节点相关，实际上忽略了所有其他类型:*

*   `firstElementChild`
*   `lastElementChild`
*   `nextElementSibling`
*   `previousElementSibling`

所以现在我们可以用更简单的方式获得这些列表项引用，中间有多少空白文本节点(或其他任何东西)都没有关系:

```
var item = list.firstElementChild;
var item2 = item.nextElementSibling;
```

该规范还定义了一个`childElementCount`属性，当忽略所有非元素节点时，该属性相当于`childNodes.length`。

## 现实世界的解决方案？

那么我们能依赖这些属性吗，它们能在我们编码的浏览器中工作吗？答案多半是*“是”*。旧版本的 <abbr title="Internet Explorer">IE</abbr> 是常见的故事，但对于 <abbr title="Internet Explorer 9">IE9</abbr> 或更高版本，或任何其他主流浏览器的任何合理的最新版本，我们发现所有这些属性都是受支持的，而且已经支持了相当长一段时间。

PPK 的 [DOM 兼容表](http://www.quirksmode.org/dom/w3c_traversal.html "W3C DOM Compatibility — Traversal (quirksmode.org)")给了我们真相，并表明我们根本不需要担心缺乏浏览器支持——除非我们必须支持 <abbr title="Internet Explorer 8">IE8</abbr> 。

所以我想这是其中之一，就像过去的选择器查询一样——如果旧浏览器是一个问题，那么库可以提供后备，或者你可以继续使用我们一直依赖的传统解决方案。但是如果你足够幸运，不必考虑那些旧的浏览器，那么元素遍历属性肯定会使生活变得更容易。

我还可以指出，早期版本的 <abbr title="Internet Explorer">IE</abbr> 对 <abbr title="Document Object Model">DOM</abbr> 有不同的看法——与所有其他浏览器不同，它们*不包含空白文本节点。所以在紧要关头，你总是可以这样做:*

```
function firstChild(element)
{
  //using pre-defined browser variable
  if(isie)
  {
    return element.firstChild;
  }
  return element.firstElementChild;
}
```

浏览器测试适合于此，而不是简单地测试是否定义了`firstElementChild`，因为缺乏对该属性的支持并不一定意味着实现中不包含空白。这种差异是<abbr title="Internet Explorer">即</abbr>所独有的，所以我们必须测试<abbr title="Internet Explorer">即</abbr>。

## 常识王国

对我来说，这些元素遍历属性在 W3C T2 规范中是轻而易举的常识——在标准中认可了大多数人对 DOM T4 的实际看法。他们肯定比 [DOM2 Traversal](https://www.w3.org/TR/DOM-Level-2-Traversal-Range/traversal.html) 更容易接近(这里有人使用`TreeWalker`？不，我不这么认为！). <abbr title="Document Object Model Level 2">DOM</abbr> 遍历试图解决的根本问题是，实现无法知道脚本将关注哪种类型的节点，然而它试图通过继续平等对待所有类型的节点来解决这个问题。

但是所有的节点并不相等——是元素起作用——元素遍历规范将它们放在了中心位置。

## 分享这篇文章