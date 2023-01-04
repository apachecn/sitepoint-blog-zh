# 进化出新的突变

> 原文：<https://www.sitepoint.com/evolving-a-new-mutation/>

我曾经是 [DOM 突变事件](https://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-MutationEvent "DOM2 MutationEvent (w3.org)")的忠实粉丝。它们为脚本提供了一种独特的方式来监控 <abbr title="Document Object Model">DOM</abbr> 中的变化，而不考虑导致它们的事件或动作。所以像`DOMNodeInserted`和`DOMAttrModified`这样的事件将会触发以响应节点的增加，或者响应属性的改变。

但是如果你从来没有使用过突变事件，这并不奇怪，因为大多数时候是*你*添加那些节点，或者改变那些属性，为什么你需要一个反应事件来处理你首先引起的事情呢？

因此它们主要用于解决库和框架中的问题，例如，响应源于匿名闭包的变化。对于许多浏览器扩展来说，它们也是相当常见的，它们提供了最简单的，有时甚至是唯一的方法来检测文档何时改变。

语法非常简单，就像任何其他事件一样:

```
element.addEventListener('DOMNodeInserted', function(e)
{
  console.log('Added ' + e.target.nodeName
    + ' to ' + element.nodeName);

}, false);
```

然而，这种简单性掩盖了一个潜在的问题——突变事件没有得到很好的实现，它们用性能和稳定性问题困扰着浏览器开发。它们启动过于频繁，运行缓慢且难以优化，并且是许多潜在崩溃错误的来源。

这就是为什么突变事件已经被[弃用](https://www.w3.org/TR/DOM-Level-3-Events/#events-mutationevents "DOM3 Mutation Events (w3.org)")大约两年了，Firefox 插件名义上不再允许包含它们。事实上，当我去年发布[灰尘选择器](https://addons.mozilla.org/en-US/firefox/addon/dust-me-selectors/ "Dust-Me Selectors for Firefox (addons.mozilla.org)")的更新时，我不得不请求特别许可才能继续使用它们！

*注意`DOMContentLoaded`是**而不是**一个突变事件，它只是有一个类似的名字。该事件不存在这样的问题，并且不鼓励使用它。*

## 你不能放弃一个好主意

尽管存在这些问题，突变事件的想法仍然是好的，不久 Mozilla 和谷歌(T2)的开发人员提出了一个新的提议(T3)，它很快被接受为 DOM 4 规范(T5)的一部分(T4)。

新的 <abbr title="Application Programming Interface">API</abbr> 被称为`MutationObserver`，它比突变事件复杂得多，但这种复杂性极大地提高了*的控制力和精确度。*

 *下面是一个简单的例子，它响应向`document.body`添加节点，并向控制台写入每个更改的摘要:

```
var watcher = new MutationObserver(function(mutations) 
{
  mutations.forEach(function(mutation)
  {
    for(var i = 0; i < mutation.addedNodes.length; i ++)
    {
      console.log('Added ' + mutation.addedNodes[i].nodeName + ' to ' + mutation.target.nodeName);
    }
  });
});
```

observer 回调被传递一个包含突变数据的对象，每个成员代表一个变化。这不同于突变事件，突变事件会为每一个变化分别触发回调！

每个突变对象中包含的数据取决于被观察的对象。在这种情况下，我们只关注目标元素的子元素的变化(由配置对象中的`childList`参数指定)，因此变异对象有一个`addedNodes`属性，它是对每个添加的节点的引用的集合。

下面是这个例子的演示，它可以在 **Firefox 14 或更高版本**和 **Chrome 18 或更高版本**中运行:

*   **[简单变异观察者](http://jspro.brothercake.com/mutation/simple.html)**

这个演示有一个按钮，你可以点击它向页面添加一个新的段落，每次出现这种情况，观察者都会做出响应。当然，在实践中你不会这样做——你只是使用`click`事件来触发它——但关键是**观察者可以对由*任何事情*** 引起的变化做出反应——包括(尤其是)你无法控制的脚本。

我相信你可以开始想象**用户脚本和浏览器扩展**的潜力，能够精确地响应 <abbr title="Document Object Model">DOM</abbr> 中的任何变化，无论它们是由脚本引起的，还是由直接的用户交互引起的(例如，当用户在`contentEditable`区域中键入时)。

## 一些令人惊讶的可能性

现在，如果你看火狐浏览器的演示，你会注意到控制台*已经显示了*的几个突变——甚至在你点击按钮之前。发生这些是因为观察者本身没有被包装在`DOMContentLoaded`中，所以脚本一执行它就开始工作。我偶然发现了这一点，只是因为我更喜欢尽可能以这种方式编写脚本，并且我意识到这些变化是**浏览器向`<body>`** 添加节点——也就是说，在包含`<script>`之后的每个节点都有一个节点。

Chrome 没有做到这一点——我只能怀疑这是被故意阻止的——因为这对于我们如何知道 DOM T2 脚本工作非常有意义。我们知道脚本是同步执行的，这就是为什么在完成渲染之前添加到`<body>`是可能的。因此，如果我们开始观察到 <abbr title="Document Object Model">DOM</abbr> 的变化，我们*应该*得到随后发生的每一个变化的通知，即使这些变化是由浏览器自己的渲染引起的。

这让我想起了几年前的一个想法，在文档的加载和渲染过程中，这个库可以为几个不同的点提供回调。我从未发展过这个想法，因为它需要如此残酷的黑客攻击——但使用突变观察者，它将是琐碎而干净的。我们所要做的就是在主体的开头添加观察者**，然后我们就可以坐下来看浏览器一个节点一个节点地绘制它了！**

请查看(在 Firefox 14 或更高版本中):

*   **[看着身体成长！](http://jspro.brothercake.com/mutation/body.html)**

## 更多日常可能性

然而在实践中，大多数突变观察者不需要像那样广泛，事实上，他们的技巧和精确是他们美丽的一部分。浏览器不必报告每一个微小的变化，只需要我们过滤数据来找到我们想要的东西(这对我们来说很繁琐，对浏览器来说效率很低)。有了突变观察者，你只需要处理你关心的东西，而且只处理你需要知道的时间。

下面是另一个例子，它监视元素文本的变化(即元素的`firstChild`文本节点)，一旦发生变化就停止监视:

```
(new MutationObserver(function(mutations, self) 
{
  mutations.forEach(function(mutation)
  {
    console.log('Changed text from "' + mutation.oldValue + '" to "' + mutation.target.nodeValue + '"');
  });

  self.disconnect();

})).observe(element.firstChild, { characterData : true, characterDataOldValue : true });
```

注意我在这里使用了稍微不同的语法——我没有将实例化保存到变量中，而是将它放在括号中，这样我们就可以将`observe()`命令直接链接到末尾。在观察器中，对实例本身的引用被传递给回调，然后我们可以使用该引用来断开连接。

## 结论

这是对突变观测器的广泛介绍，对于如何使用它们的细节还不够清楚；我甚至没有提到 Chrome 的实现是带前缀的(目前为`WebKitMutationObserver`)。但是我想主要关注这个新的 <abbr title="Application Programming Interface">API</abbr> 的背景，并开始对可能性感到兴奋！

如果有需求，我会写一篇后续文章，用大量代码详细探讨它们——但是现在，我建议您访问 MDN 的 [`MutationObserver`文档。在](https://developer.mozilla.org/en-US/docs/DOM/MutationObserver) [Mozilla Hacks 博客](https://hacks.mozilla.org/2012/05/dom-mutationobserver-reacting-to-dom-changes-without-killing-browser-performance/ "DOM MutationObserver — reacting to DOM changes without killing browser performance (hacks.mozilla.org)")上也有另一篇好文章。

当我听说突变事件正在消失时，我感到非常厌烦，因为还有什么能做同样的工作呢？事实证明，毕竟还有别的东西——而且比它好一百倍！

## 分享这篇文章*