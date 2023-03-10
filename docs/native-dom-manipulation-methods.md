# 你应该知道的 6 种受 jQuery 启发的原生 DOM 操作方法

> 原文：<https://www.sitepoint.com/native-dom-manipulation-methods/>

jQuery 发布时，其迅速流行背后的一个主要原因是它可以轻松地选择 DOM 元素、遍历它们并修改它们的内容。但那是 2006 年的事了。在那些日子里，我们还停留在 Internet Explorer 7 上，ECMAScript 5 还需要几年的时间。

![6 jQuery-inspired Native DOM Manipulation Methods](img/96989ddcd4f4342e7bdabfb84991ecb0.png)

幸运的是，从那以后发生了很多变化。浏览器变得更加符合标准，原生 JavaScript 也有了长足的进步。随着情况的改善，我们已经看到有人质疑我们是否还需要 jQuery。我不打算在这里讨论这个问题，而是想提供一些思路，因为我提出了六种原生 DOM 操作方法，它们都是受这个伟大的库的启发。这些措施如下:

*   [append()](https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/append)
*   [前置()](https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/prepend)
*   [之后()](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/after)
*   [之前()](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/before)
*   [replaceWith()](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/replaceWith)
*   [remove()](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/remove)

在本文中，我想研究这些原生 DOM 操作方法和它们的 jQuery 对应物之间的异同。那么希望下次您发现自己为了一两个方便的方法而包含 jQuery 时，您可以选择使用普通 JavaScript 的强大功能。

## 1.追加()

[append](https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/append) 方法执行插入操作，即向 DOM 树添加节点。顾名思义，它*将传递的参数追加*到它被调用的节点的子节点列表中。考虑下面的例子:

```
const parent = document.createElement('div')
const child1 = document.createElement('h1')
parent.append(child1)
parent.outerHTML
// <div><h1></h1></div>

const child2 = document.createElement('h2')
parent.append(child2)
parent.outerHTML
// <div><h1></h1><h2></h2></div> 
```

在这一点上，如果你问这和本地的 [appendChild](https://developer.mozilla.org/en/docs/Web/API/Node/appendChild) 方法有什么不同，你会被原谅。嗯，第一个区别是`append()`可以一次接受多个参数，相应的节点将被追加到子列表中，就像 [jQuery append](http://api.jquery.com/append/) 方法一样。继续前面的片段:

```
const child3 = document.createElement('p')
const child4 = document.createElement('p')
const child5 = document.createElement('p')
parent.append(child3, child4, child5)
parent.outerHTML
/* Outputs:
<div>
  <h1></h1>
  <h2></h2>
  <p></p>
  <p></p>
  <p></p>
</div>
*/ 
```

此外，参数甚至可以是字符串。因此，在使用`appendChild()`时，必须使用相当冗长的语法:

```
parent.appendChild(document.createTextNode('just some text')) 
```

与`append()`相同的操作更短:

```
parent.append('just some text')
parent.outerHTML
/* Outputs:
<div>
  <h1></h1>
  <h2></h2>
  <p></p>
  <p></p>
  <p></p>
  just some text
</div>
*/ 
```

该字符串被转换成一个文本节点，所以任何 HTML 都不会被解析:

```
parent.append('<p>foo</p>')
parent.outerHTML
/* Outputs:
<div>
  <h1></h1>
  <h2></h2>
  <p></p>
  <p></p>
  <p></p>
  just some text
  &lt;p&gt;foo&lt;/p&gt;
</div>
*/ 
```

这与 [jQuery 方法](http://api.jquery.com/append/#append-content-content)不同，后者解析标记字符串，生成相应的节点并插入 DOM 树。

通常情况下，如果追加的节点已经存在于树中，则首先从其旧位置移除它:

```
const myParent = document.createElement('div')
const child = document.createElement('h1')
myParent.append(child)
const myOtherParent = document.createElement('div')
const myOtherParent.append(child)
myOtherParent.outerHTML
// <div><h1></h1></div>

myParent.outerHTML
// <div></div>" 
```

`append()`和`appendChild()`的最后一个区别是，后者返回追加的节点，而前者返回`undefined`。

## 2.前置()

[前置](https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/prepend)方法与`append()`非常相似。添加了子节点，但是这一次它们被*添加到调用该方法的节点的子节点列表的前面*,就在第一个子节点之前:

```
const parent = document.createElement('div')
const child1 = document.createElement('p')
parent.prepend(child1)
parent.outerHTML
// <div><p></p></div>

const child2 = document.createElement('h2')
parent.prepend('just some text', child2)
parent.outerHTML
/* Outputs:
<div>
  just some text
  <h2></h2>
  <p></p>
</div>
*/ 
```

方法的返回值是`undefined`。对应的 jQuery 方法是 [prepend()](http://api.jquery.com/prepend/) 。

## 3.在()之后

方法后的[是另一种插入方法，但这次必须在子节点上调用，也就是有明确父节点的节点。节点作为相邻的同级节点插入，如下例所示:](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/after)

```
const parent = document.createElement('ul')
const child = document.createElement('li')
child.append('First item')
parent.append(child)
child.after(document.createElement('li'))
parent.outerHTML
// <ul><li>First item</li><li></li></ul> 
```

返回值是`undefined`，在 jQuery 中类似的操作是在()之后的[。](http://api.jquery.com/after/)

## 4.之前()

在之前的[方法类似于`after()`，但是现在节点被插入到子节点之前:](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/before)

```
const parent = document.createElement('ul')
const child = document.createElement('li')
child.append('First item')
parent.append(child)

const child1 = document.createElement('li')
child1.append('Second item')

const child2 = document.createElement('li')
child2.append('Third item')

child.before(child1, child2)

parent.outerHTML
/* Outputs:
<ul>
  <li>Second item</li>
  <li>Third item</li>
  <li>First item</li>
</ul>
*/ 
```

同样，返回值是`undefined.`相应的 jQuery 方法是 [before()](http://api.jquery.com/before/) 。

## 5.替换为()

假设我们想用一个 DOM 节点替换另一个。当然，它们可能有孩子，所以这个操作将替换整个 DOM 子树。在介绍这组方便的方法之前，我们会使用 [replaceChild()](https://developer.mozilla.org/en-US/docs/Web/API/Node/replaceChild) :

```
const parent = document.createElement('ul')
parent.innerHTML = ` <li>first</li>
  <li>second</li>
  <li>third</li> `
parent.outerHTML
// <ul><li>first</li><li>second</li><li>third</li></ul>"

const secondChild = parent.children[1]

const newSecondChild = document.createElement('li')
newSecondChild.innerHTML = '<a href="#">second</a>'

secondChild.parentNode.replaceChild(newSecondChild, secondChild)
parent.outerHTML
/* Outputs:
<ul>
  <li>first</li>
  <li><a href="#">second</a></li>
  <li>third</li>
</ul>
*/ 
```

( *innerHTML 和模板文字用于简化树的构造*

同样的操作可以用更简单的方式用替换[来执行:](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/replaceWith)

```
parent = document.createElement('ul')
parent.innerHTML = ` <li>first</li>
  <li>second</li>
  <li>third</li> `
secondChild = parent.children[1]

newSecondChild = document.createElement('li')
newSecondChild.innerHTML = '<a href="#">second</a>'

secondChild.replaceWith(newSecondChild) 
```

除了语法更短之外，这种新方法的一个特性是它接受几个参数，允许用一列其他节点替换一个节点。继续之前的交互式 JavaScript 会话:

```
const newerSecondChild = document.createElement('li')
newerSecondChild.append('another item')
const newThirdChild = document.createElement('li')
newThirdChild.append('yet another item')
newSecondChild.replaceWith(newerSecondChild, newThirdChild)
parent.outerHTML
/* Outputs:
<ul>
  <li>first</li>
  <li>another item</li>
  <li>yet another item</li>
  <li>third</li>
</ul>
*/ 
```

在这里，方法的返回值也是`undefined`。您可以将此与谐音 [jQuery 方法](http://api.jquery.com/replaceWith/)进行比较。

## 移除()

如何从 DOM 树中移除节点？“旧的”方法是 [removeChild()](https://developer.mozilla.org/en-US/docs/Web/API/Node/removeChild) 。如其名称所示，必须在要删除的节点`n`的父节点上调用它:

```
n.parentNode.removeChild(n) 
```

然而，使用 [remove()](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/remove) ，操作要简单得多:

```
const parent = document.createElement('ul')
const n = document.createElement('li')
parent.append(n)
parent.outerHTML
// <ul><li></li></ul>

n.remove()
parent.outerHTML
// <ul></ul> 
```

与 jQuery 中的[模拟操作](http://api.jquery.com/remove/)的不同之处在于如何处理连接到被移除节点的事件监听器。jQuery 删除所有绑定的事件和与元素关联的数据，而本地方法不涉及事件侦听器:

```
const parent = document.createElement('ul')
const n = document.createElement('li')
parent.append(n)

n.addEventListener('test', console.log.bind(console))

const e = new Event('test')
n.dispatchEvent(e)
Event {isTrusted: false, type: "test", ...

n.remove()
n.dispatchEvent(e)
Event {isTrusted: false, type: "test", ... 
```

这种行为更类似于 jQuery [detach](http://api.jquery.com/detach/) 方法。

## 浏览器支持

在撰写本文时，[在桌面浏览器上对前五种便捷方式`prepend()`、`append()`、`before()`、`after()`和`replaceWith()`的支持状态](http://caniuse.com/#feat=dom-manip-convenience)如下:

*   Chrome [实现了它们](https://www.chromestatus.com/feature/5723067795898368)，从版本 54 开始。
*   Firefox [从版本 49 开始支持它们](https://bugzilla.mozilla.org/show_bug.cgi?id=911477)。
*   Safari [从版本 10 开始支持它们](https://bugs.webkit.org/show_bug.cgi?id=74648)。
*   Opera 从版本 41 开始支持它们。
*   令人失望的是，它们在 Internet Explorer 中不受支持，在微软 Edge 中也不受支持(尽管对于 Edge 来说，该功能正在开发中。

方法得到了更广泛的支持(T2)。微软 Edge 从版本 14 开始实施。

对于那些还没有提供这些方法的浏览器，有几种聚合填充是可用的。 [childNode.js](https://github.com/seznam/JAK/blob/master/lib/polyfills/childNode.js) 就是其中之一，其他的 polyfills 可以在专用于这些方法的 [MDN](https://developer.mozilla.org/en-US/) 页面上找到，本文已经引用过了。

## 7.奖励方法:insertAdjacentHTML

在结束之前，说几句关于[的话。它提供了类似于上面列出的前四种方法的插入操作——`append()`、`prepend()`、`after()`、`before()`——并且要添加的内容由一个 HTML 字符串指定:](https://developer.mozilla.org/en-US/docs/Web/API/Element/insertAdjacentHTML)

```
const parent = document.createElement('div')
parent.insertAdjacentHTML('beforeend', '<p>A paragraph</p>')
parent.insertAdjacentHTML('beforeend', '<p>Another paragraph</p>')
parent.insertAdjacentHTML('afterbegin', '<p>Yet another paragraph</p>')

const grandParent = document.createElement('div')
grandParent.append(parent)

parent.insertAdjacentHTML('afterend', '<div class="after"></div>')
parent.insertAdjacentHTML('beforebegin', '<div class="before"></div><div class="before2"></div>')

grandParent.outerHTML
/* Outputs:
<div>
  <div class="before"></div>
  <div class="before2"></div>
  <div>
    <p>Yet another paragraph</p>
    <p>A paragraph</p>
    <p>Another paragraph</p>
  </div>
  <div class="after"></div>
</div>
*/ 
```

请注意，我们必须使`parent`节点成为另一个节点的子节点，以便能够使用位置参数`beforebegin`和`afterend`。

幸运的是，`insertAdjacentHTML()`在随处可见[。](http://caniuse.com/#feat=insertadjacenthtml)

## 结论

现在，我们即将结束对这些受 jQuery 启发的 DOM 方法的简要概述。我希望在本文的过程中，我已经展示了原生 DOM API 是如何发展的，以及这些原生方法如何能够简单地替换它们的 jQuery 对应物。

但是你怎么看？这有助于打破 jQuery 的依赖性吗？还是缺乏 IE 支持是交易的破坏者？我很乐意在下面的评论中听到你的意见。

*这篇文章由[塞巴斯蒂安·塞茨](https://www.sitepoint.com/author/sseitz)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章