# 修复细节元素

> 原文：<https://www.sitepoint.com/fixing-the-details-element/>

HTML5 `<details>`元素是一个非常简洁的结构，但是它也有一个非常严重的可用性问题——如果你跟随一个散列链接，该链接的目标是一个折叠的`<details>`元素内的*,会发生什么呢？答案是什么都没有。就好像目标是隐藏的。但是我们可以用一点渐进增强的 JavaScript 和一个可访问的 polyfill 来解决这个问题。* 

## 介绍<details></details>

如果你还不熟悉[的细节和概要要素](http://html5doctor.com/the-details-and-summary-elements/ "The details and summary elements (HTML5 Doctor)")，这里有一个简单的例子:

```
<details open="open">
  <summary>This is the summary element</summary>
  <p>
    This is the expanding content
  </p>
</details>
```

`<summary>`元素如果存在，必须是第一个或最后一个子元素。其他的都算内容。默认情况下，内容是折叠的，除非定义了`open`属性。当用户单击摘要来打开和关闭它时，本机实现会更新该属性。目前，只有 Chrome 支持`<details>`标签。下图显示了 Chrome 如何渲染前面的示例。

![The details and summary element in Chrome](img/e84bfa018c69640c2d34dc4ce8c2e8b3.png)

Chrome 中的细节和摘要元素

它和普通的文本没有什么不同，除了一个小三角形，被称为 discloure 三角形。用户可以通过点击三角形或者`<summary>`元素内的任何地方来打开和关闭它。您也可以`Tab`到摘要，然后按`Enter`。

## 创建聚合填充

实现一个基本的 [polyfill 来模拟`<details>`标签](http://jspro.brothercake.com/fixing-details/polyfill.html)非常简单。polyfill 通过`open`属性的存在来标识本机实现，该属性是`open`属性的 DOM 映射。在本地实现中，我们不需要手动更新`open`属性，但是我们仍然需要更新它的 [ARIA 属性](https://www.sitepoint.com/accessibility-in-modern-interfaces/ "Accessibility in Modern Interface")，这些属性基于以下结构。

```
<details open="open">
  <summary>This is the summary element</summary>
  <div>
    <p>
      This is the expanding content
    </p>
  </div>
</details>
```

内部的`<div>`是折叠的内容。该脚本将一个`aria-expanded`属性绑定到该元素，当该元素打开和关闭时，该属性在`true`和`false`之间切换。该属性也被用作 CSS 选择器(如下所示)，它使用`display`可视地折叠内容。

```
details > div[aria-expanded="false"]
{
  display:none;
}
```

现在我们真的不需要*一个包装内容元素*，但是如果没有它，我们必须在每个内部元素*上分别设置`aria-expanded`和`display`*——这需要更多的工作，如果元素有不同的显示属性，可能会很不方便。在 IE7 中更是如此！出于某种原因，当用户手动打开和关闭它时，IE7 不会应用显示更改。但是，默认情况下它会应用它(这证明它理解选择器)，属性值的变化可以在 DOM 中看到。就好像它可以应用选择器，但不能再次取消应用。出于这个原因，我们也必须定义一个`style.display`变化，这使得拥有一个内容元素特别方便；由于我们不得不为 IE7 这么做，我们最终可以免费获得 IE6 支持！

polyfill 中另一个值得注意的重要事情是`addClickEvent`抽象，它处理触发键盘`click`事件的浏览器和不触发键盘`click`事件的浏览器之间的差异:

```
function addClickEvent(node, callback)
{
  var keydown = false;
  addEvent(node, 'keydown', function()
  {
    keydown = true;
  });
  addEvent(node, 'keyup', function(e, target)
  {
    keydown = false;
    if(e.keyCode == 13) { callback(e, target); }
  });
  addEvent(node, 'click', function(e, target)
  {
    if(!keydown) { callback(e, target); }
  });
}
```

对于像链接和按钮这样的元素，*本身*接受键盘焦点，当你按下`Enter`键时，所有的浏览器都会触发`click`事件。但是，我们的`<summary>`元素只接受焦点，因为我们添加了`tabindex`，这里的情况因浏览器而异。

这真的只是*差异*的问题——如果所有浏览器都以这种或那种方式运行，事情就简单了。但是，由于有不同的行为，我们必须使用一点狡猾。因此，我们定义了`keydown`和`keyup`事件来处理`Enter`键。这些事件还设置和清除一个标志，然后`click`事件引用该标志，因此它可以在处理鼠标和触摸事件时忽略重复的键盘事件。

## 突出散列问题

现在我们有了一个功能性的 polyfill，让我们再次链接到那个例子，但是这次包括一个指向第一个元素内容的 ID 的**片段标识符**(即一个散列链接):

*   [poly fill . html #第一内容](http://jspro.brothercake.com/fixing-details/polyfill.html#first-content)

由于目标元素位于折叠区域内，页面永远不会跳转到该位置——当目标保持隐藏时，它会停留在页面的顶部。在大多数情况下，用户不会理解那里发生了什么。也许他们会向下滚动，点击东西，最终找到他们要找的东西，但这不是好的可用性。

当点击一个*内部*散列链接时，同样问题的一个更糟糕的例子出现了——如果目标在一个折叠区域内，链接将什么也不做。不过令人高兴的是，这种情况很容易描述，因此很容易定义解决它的逻辑:

*   **如果**散列与该页面上的一个元素的 ID 相匹配，**和**该元素在`<details>`元素内，**然后**自动展开该元素，以及任何相同的祖先

一旦我们实现了这一点，我们将获得更好的行为，因为详细信息区域会自动扩展以显示位置目标:

*   **[omnifill . html # first-content](http://jspro.brothercake.com/fixing-details/omnifill.html#first-content)**

## 修复哈希问题

我们可以用下面的递归函数来解决散列问题。

```
function autostate(target, expanded, ancestor)
{
  if(typeof(ancestor) == 'undefined')
  {
    if(!(target = getAncestor(target, 'details')))
    {
      return null;
    }
    ancestor = target;
  }
  else
  {
    if(!(ancestor = getAncestor(ancestor, 'details')))
    {
      return target;
    }
  }

  statechange(ancestor.__summary, expanded);

  return autostate(target, expanded, ancestor.parentNode);
}
```

该函数接受一个`target`元素和一个`expanded=false`状态标志，并将识别目标是否在一个`<details>`元素中。如果是这样，它将其`<summary>`元素(保存为本地`__summary`属性)传递给`statechange`函数，后者应用必要的更改来扩展元素。接下来，再次使用 DOM 对任何祖先做同样的事情，这样我们就可以处理嵌套的实例。我们需要为原始目标和后续祖先设置单独的参数，这样我们就可以在所有递归结束时返回原始目标，也就是说，如果输入目标*是折叠区域内的*，则返回相同的目标，否则返回`null`。

然后，我们可以从内部页面链接上的`click`事件中调用`autostate`，以及在页面加载时为由`location.hash`匹配的元素调用它:

```
if(location.hash)
{
  autostate(document.getElementById(location.hash.substr(1)), false);
}
```

最初，我希望这就是函数的全部功能——获取目标，展开它的容器，然后让浏览器跳转到它的位置。但是，实际上这并不可靠，因为为了让它工作，在点击链接之前，元素必须被展开*，否则浏览器不会跳转到目标位置。我试图通过使用单独的`mousedown`、`keydown`和`touchstart`事件抢占链接动作来解决这个问题，这样在链接被跟踪之前目标就已经被扩展了。不幸的是，这非常复杂，而且*仍然*不可靠！*

所以，最终我发现最好的方法是使用`window.scrollBy`函数自动滚动浏览器，然后在链接上返回`true`，这样地址栏就会更新。这里我们需要由`autostate`函数返回的目标引用(或缺少目标引用)——如果它返回一个目标，那么滚动到目标的位置:

```
if(target = autostate(document.getElementById('hash'), false))
{
  window.scrollBy(0, target.getBoundingClientRect().top);
}
```

使用`getBoundingClientRect`函数提供了完美的数据，因为它告诉我们目标元素**相对于视窗**的位置(即相对于你在浏览器窗口内看到的文档部分)。这意味着它只滚动到找到目标所需的距离，这也是为什么我们使用`scrollBy`而不是`scrollTo`。但是，当处理默认的`location.hash`时，我们不会这样做，以便用普通的散列链接反映本机浏览器的行为——当你用位置散列刷新页面时，浏览器*不会*跳回目标位置，它只会在页面首次加载时这样做。

因此，为了获得这种行为，我们不能自动滚动定位目标。相反，我们必须允许本机跳转在适当的时间发生。我们通过用`DOMContentLoaded`推迟脚本的初始化来实现这一点(加上用于旧浏览器的备份`onload`，这意味着在脚本首先折叠其包含区域之前，页面已经*跳转到目标位置。*

## 结论

我认为这样的脚本是万能的。对于没有最新功能的浏览器来说，它不仅仅是一个聚合填充，因为它还增强了功能本身的可用性和可访问性，即使在已经支持它们的浏览器中也是如此。下面列出了本文中示例的下载文件。

*   [基本聚合填充示例](https://github.com/jsprodotcom/source/blob/master/details-element-polyfill.zip)
*   [扩展 omnifill 示例(支持哈希链接)](https://github.com/jsprodotcom/source/blob/master/details-element-omnifill.zip)

## 分享这篇文章