# JavaScript 事件委托比您想象的要简单

> 原文：<https://www.sitepoint.com/javascript-event-delegation-is-easier-than-you-think/>

如果您想在 web 页面中添加一点 JavaScript 交互性，您可能听说过 JavaScript 事件委托，并认为这是一种只有铁杆 JavaScript 程序员才担心的复杂设计模式。事实是，如果您已经知道如何添加 JavaScript 事件处理程序，实现起来很容易。

JavaScript 事件是网页上所有交互性的基础(我指的是真正的交互性，而不是那些微不足道的 CSS 下拉菜单)。在传统的事件处理中，您可以根据需要在每个元素中添加或删除事件处理程序。然而，事件处理程序可能会导致内存泄漏和性能下降——您拥有的越多，风险就越大。JavaScript 事件委托是一种简单的技术，通过这种技术可以向父元素添加一个事件处理程序，从而避免向多个子元素添加事件处理程序。

## 它是如何工作的？

事件委托利用了 JavaScript 事件的两个经常被忽略的特性:[事件冒泡](http://www.quirksmode.org/js/events_order.html)和[目标元素](http://www.quirksmode.org/js/events_properties.html#target)。当一个事件在一个元素上被触发时，例如鼠标点击一个按钮，同样的事件也会在该元素的所有祖先上被触发。这个过程被称为事件冒泡；事件从原始元素冒泡到 DOM 树的顶端。任何事件的目标元素都是原始元素，即我们示例中的按钮，它存储在事件对象的属性中。使用事件委托，可以向一个元素添加一个事件处理程序，等待一个事件从一个子元素中冒出来，并很容易地确定事件来自哪个元素。

## 这对我有什么帮助？

设想一个有 10 列 100 行的 HTML 表格，当用户点击表格单元格时，您希望发生一些事情。例如，我曾经不得不使那个大小的表格的每个单元格在点击时都是可编辑的。向 1000 个单元格中的每一个添加事件处理程序将是一个主要的性能问题，并且可能是浏览器崩溃内存泄漏的一个来源。相反，使用事件委托，您将只向`table`元素添加一个事件处理程序，拦截`click`事件并确定哪个单元格被单击。

## 它在代码中是什么样子的？

代码很简单；我们只需要担心检测目标元素。假设我们有一个 ID 为“`report`”的`table`元素，并且我们为`click`事件添加了一个事件处理程序，它将调用`editCell`函数。`editCell`函数需要为已经上升到`table`的事件确定目标元素。预计我们将编写一些需要该功能的事件处理函数，我们将把它放在一个名为`getEventTarget`的独立函数中:

```
function getEventTarget(e) {
  e = e || window.event;
  return e.target || e.srcElement;
}
```

变量`e`代表事件对象，我们只需要少量跨浏览器代码就可以访问并返回目标元素，在 Internet Explorer 中存储在`srcElement`属性中，在其他浏览器中存储在`target`属性中。

接下来是调用`getEventTarget`函数的`editCell`函数。一旦我们有了对目标元素的引用，就由我们来确保该元素是我们所期望的:

```
function editCell(e) {
  var target = getEventTarget(e);
  if(target.tagName.toLowerCase() === 'td') {
    // DO SOMETHING WITH THE CELL
  }
}
```

在`editCell`函数中，我们通过检查标签名来确认目标元素是一个表格单元格。这种检查可能过于简单；如果事件的目标是表格单元格中的另一个元素呢？可能需要添加代码来查找父元素`td`的快速修改。如果有些单元格不可编辑怎么办？在这种情况下，我们可以将不可编辑的单元格添加一个特定的类名，并在使其可编辑之前检查目标元素是否没有该类名值。有许多选项可供选择，您只需要选择一个适合您的应用程序。

## 有哪些利弊？

JavaScript 事件委托的好处是:

*   **需要设置和驻留在内存中的事件处理程序更少**。这是最重要的。更好的性能和更少的崩溃。
*   DOM 更新后不需要重新附加处理程序。如果页面内容是动态生成的，例如通过 Ajax，那么在加载或卸载元素时，就不需要添加和删除事件处理程序。

潜在的问题可能不太明显，但是一旦你意识到它们，就很容易避免:

*   您的事件管理代码有成为性能瓶颈的风险，所以尽可能保持精简。
*   并非所有事件都是泡沫。`blur`、`focus`、`load`和`unload`事件不会像其他事件那样冒泡。`blur`和`focus`事件实际上可以使用捕获阶段(在浏览器而不是 IE 中)来访问，而不是冒泡阶段，但那是另外一个故事了。
*   管理一些鼠标事件时需要小心。如果您的代码正在处理`mousemove`事件，那么您就面临着产生性能瓶颈的严重风险，因为`mousemove`事件被如此频繁地触发。`mouseout`事件有一个[古怪的行为](http://www.quirksmode.org/js/events_mouse.html#link8)，这很难用事件委派来管理。

## 摘要

使用主要库的 JavaScript 事件委托示例有: [jQuery](http://www.danwebb.net/2008/2/8/event-delegation-made-easy-in-jquery) 、 [Prototype](http://devthatweb.com/view/basic-event-delegation-in-prototype) 和 [Yahoo！UI](http://icant.co.uk/sandbox/eventdelegation/) 。你也可以找到不使用任何库的例子，比如来自[可用类型博客](http://usabletype.com/weblog/event-delegation-without-javascript-library/)的这个例子。

事件委托是一个方便的工具，如果需要的话，可以放在你的工具箱里，而且很容易实现。

## 分享这篇文章