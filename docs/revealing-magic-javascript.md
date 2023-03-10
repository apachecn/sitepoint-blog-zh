# 揭示 JavaScript 的魔力

> 原文：<https://www.sitepoint.com/revealing-magic-javascript/>

我们每天都要使用大量的工具。不同的库和框架是我们日常工作的一部分。我们使用它们是因为我们不想为每一个项目重新发明轮子，即使我们不明白在引擎盖下发生了什么。在本文中，我们将揭示一些发生在最流行的图书馆中的神奇过程。我们还将看看是否能复制它们的行为。

## 从字符串创建 DOM 元素

随着单页面应用的兴起，我们正在用 JavaScript 做很多事情。我们应用程序的大部分逻辑已经转移到浏览器中。生成或替换页面上的元素是一项常见任务。类似如下所示的代码已经变得非常普遍。

```
var text = $('<div>Simple text</div>');

$('body').append(text);
```

结果是一个新的`<div>`元素被添加到文档的主体中。这个简单的操作只用一行 jQuery 就完成了。如果没有 jQuery，代码会稍微复杂一点，但不会太复杂:

```
var stringToDom = function(str) {
  var temp = document.createElement('div');

  temp.innerHTML = str;
  return temp.childNodes[0];
}
var text = stringToDom('<div>Simple text</div>');

document.querySelector('body').appendChild(text);
```

我们定义了自己的实用方法`stringToDom`来创建一个临时的`<div>`元素。我们改变了它的`innerHTML`属性，最后我们简单地返回了第一个孩子，这实际上是我们所需要的。它以同样的方式工作。但是，我们将观察到以下代码的不同结果:

```
var tableRow = $('<tr><td>Simple text</td></tr>');
$('body').append(tableRow);

var tableRow = stringToDom('<tr><td>Simple text</td></tr>');
document.querySelector('body').appendChild(tableRow);
```

从视觉上看，在页面上，没有任何区别。然而，如果我们用 Chrome 的开发工具检查生成的标记，我们会得到一个有趣的结果:

![Creating a DOM element from a string](img/f49d227dea0319ed5aa995918b5815be.png)

看起来我们的`stringToDom`函数仅仅创建了一个文本节点，而不是真正的`<tr>`标签。但与此同时，jQuery 设法做到了这一点。问题是包含 HTML 元素的字符串是通过浏览器中的解析器运行的。该解析器忽略没有放在正确上下文中的标签，我们得到的只是一个文本节点。没有表格的表格行对浏览器无效。

jQuery 通过创建正确的上下文并只提取需要的部分成功地解决了这个问题。如果我们深入研究一下库的代码，我们会看到这样一个图:

```
var wrapMap = {
  option: [1, '<select multiple="multiple">', '</select>'],
  legend: [1, '<fieldset>', '</fieldset>'],
  area: [1, '<map>', '</map>'],
  param: [1, '<object>', '</object>'],
  thead: [1, '<table>', '</table>'],
  tr: [2, '<table><tbody>', '</tbody></table>'],
  col: [2, '<table><tbody></tbody><colgroup>', '</colgroup></table>'],
  td: [3, '<table><tbody><tr>', '</tr></tbody></table>'],
  _default: [1, '<div>', '</div>']
};
wrapMap.optgroup = wrapMap.option;
wrapMap.tbody = wrapMap.tfoot = wrapMap.colgroup = wrapMap.caption = wrapMap.thead;
wrapMap.th = wrapMap.td;
```

每个需要特殊处理的元素都分配了一个数组。我们的想法是构造正确的 DOM 元素，并根据嵌套层次来获取我们需要的内容。例如，对于`<tr>`元素，我们需要创建一个带有`<tbody>`子元素的表。所以，我们有两层嵌套。

有了地图，我们就要找出最终想要什么样的标签。下面的代码从`<tr><td>Simple text</td></tr>`中提取`tr`

```
var match = /&lt;\s*\w.*?&gt;/g.exec(str);
var tag = match[0].replace(/&lt;/g, '').replace(/&gt;/g, '');
```

剩下的就是找到合适的上下文并返回 DOM 元素。下面是函数`stringToDom`的最后一个变体:

```
var stringToDom = function(str) {
  var wrapMap = {
    option: [1, '<select multiple="multiple">', '</select>'],
    legend: [1, '<fieldset>', '</fieldset>'],
    area: [1, '<map>', '</map>'],
    param: [1, '<object>', '</object>'],
    thead: [1, '<table>', '</table>'],
    tr: [2, '<table><tbody>', '</tbody></table>'],
    col: [2, '<table><tbody></tbody><colgroup>', '</colgroup></table>'],
    td: [3, '<table><tbody><tr>', '</tr></tbody></table>'],
    _default: [1, '<div>', '</div>']
  };
  wrapMap.optgroup = wrapMap.option;
  wrapMap.tbody = wrapMap.tfoot = wrapMap.colgroup = wrapMap.caption = wrapMap.thead;
  wrapMap.th = wrapMap.td;
  var element = document.createElement('div');
  var match = /<\s*\w.*?>/g.exec(str);

  if(match != null) {
    var tag = match[0].replace(/</g, '').replace(/>/g, '');
    var map = wrapMap[tag] || wrapMap._default, element;
    str = map[1] + str + map[2];
    element.innerHTML = str;
    // Descend through wrappers to the right content
    var j = map[0]+1;
    while(j--) {
      element = element.lastChild;
    }
  } else {
    // if only text is passed
    element.innerHTML = str;
    element = element.lastChild;
  }
  return element;
}
```

请注意，我们正在检查字符串中是否有一个标签—`match != null`。如果不是，我们简单地返回一个文本节点。仍然使用临时的`<div>`，但是这一次我们传递正确的标签，这样浏览器可以创建一个有效的 DOM 树。最后，通过使用一个`while`循环，我们会越来越深，直到我们到达想要的标签。

下面是一个展示我们实现的代码笔:

参见 [CodePen](http://codepen.io) 上 Krasimir tson ev([@ Krasimir](http://codepen.io/krasimir))的 Pen [xlCgn](http://codepen.io/krasimir/pen/xlCgn/) 。