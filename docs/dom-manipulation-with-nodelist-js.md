# 消除 jQuery 膨胀——使用 NodeList.js 进行 DOM 操作

> 原文：<https://www.sitepoint.com/dom-manipulation-with-nodelist-js/>

近年来，jQuery 已经成为 web 上事实上的 JavaScript 库。它消除了许多跨浏览器的不一致性，并为客户端脚本添加了一个受欢迎的语法层。它抽象出的一个主要痛点是 DOM 操作，但是自从它出现以来，原生浏览器 API 已经有了显著的改进，并且您可能不需要 jQuery 的想法已经开始流行起来。

以下是一些原因:

1.  jQuery 包含了一堆你不需要或者不用的特性(所以权重是不必要的)。
2.  jQuery 对于太多的人来说是太多的东西。通常较小的库可以更好地完成某些任务。
3.  在 DOM 操作方面，浏览器 API 现在可以做 jQuery 能做的大部分事情。
4.  浏览器 API 现在更加同步，例如使用`addEventListener`而不是`attatchEvent`。

## 那么问题出在哪里？

问题是，与 jQuery 相比，使用普通(或普通)JavaScript 的 DOM 操作可能会很痛苦。这是因为你要读写更多冗余的代码，还要处理浏览器无用的 [NodeList](https://developer.mozilla.org/en/docs/Web/API/NodeList) 。

首先让我们看看根据 MDN 什么是`NodeList`:

NodeList 对象是节点的集合，例如由 Node.childNodes 和 document.querySelectorAll 方法返回的节点。

而且有时候还有[活节点列表](https://developer.mozilla.org/en-US/docs/Web/API/NodeList#A_sometimes-live_collection)(可能会让人混淆):

在某些情况下，NodeList 是一个动态集合，这意味着 DOM 中的变化会反映在集合中。例如，Node.childNodes 是活动的。

这可能是一个问题，因为您无法分辨哪些是活动的，哪些是静态的。除非您从`NodeList`中移除每个节点，然后检查`NodeList`是否为空。如果它是空的，那么你就有了一个活的`NodeList`(这只是一个坏主意)。

浏览器也没有提供任何有用的方法来操作这些`NodeList`对象。

例如，不幸的是，不能用`forEach`遍历节点:

```
var nodes = document.querySelectorAll('div');
nodes.forEach(function(node) {
  // do something
});
// Error: nodes.forEach is not a function 
```

所以你必须做:

```
var nodes = document.querySelectorAll('div');
for(var i = 0, l = nodes.length; i < l; i++) {
  var node = nodes[i];
  // do something
} 
```

或者甚至只能使用“黑客”:

```
[].forEach.call(document.querySelectorAll('div'), function(node) {
    // do something
}); 
```

浏览器的原生`NodeList`只有一个方法:[项](https://developer.mozilla.org/en-US/docs/Web/API/NodeList/item)。这通过索引从一个`NodeList`返回一个节点。当我们可以像访问数组一样访问那个节点时(使用`array[index]`)，这是完全没有用的:

```
var nodes = document.querySelectorAll('div');
nodes.item(0) === nodes[0]; // true 
```

这就是 [NodeList.js](https://github.com/eorroe/NodeList.js) 的用武之地——让用浏览器的原生 API 操作 DOM 像用 jQuery 一样简单，但只需要 4k。

## 解决方案

我创建 NodeList.js 是因为我一直使用原生 DOM APIs，但希望使它们更简洁，以便在编写代码时删除大量冗余(例如`for`循环)。

js 是原生 DOM APIs 的包装器，它允许您像处理单个节点一样处理节点数组(又名 my `NodeList`)。这比浏览器的本机`NodeList`对象提供了更多的功能。

如果这对你来说听起来不错，那么去从官方的 GitHub repo 中获取一份 NodeList.js 的副本，并跟随本教程的剩余部分。

### 用法:

选择 DOM 节点很简单:

```
$$(selector); // returns my NodeList 
```

这种方法在引擎盖下使用`querySelectorAll(selector)`。

## 但是它与 jQuery 相比如何呢？

很高兴你问了。让我们将 vanilla JS、jQuery 和 NodeList.js 放在一起。

假设我们有三个按钮:

```
<button></button>
<button></button>
<button></button> 
```

让我们将每个按钮的文本改为*“点击我”*:

### 香草 JS:

```
var buttons = document.querySelectorAll('button'); // returns browser's useless NodeList
for(var i = 0, l = buttons.length; i < l; i++) {
  buttons[i].textContent = 'Click Me';
} 
```

### jQuery:

```
$('button').text('Click Me'); 
```

### NodeList.js:

```
$$('button').textContent = 'Click Me'; 
```

这里我们看到 NodeList.js 可以有效地将一个`NodeList`作为单个节点。也就是说，我们引用了一个`NodeList`，我们只是将它的`textContent`属性设置为*“点击我”*。然后 NodeList.js 将为`NodeList`中的每个节点做这件事。很整洁，是吧？

如果我们想要方法链接(à la jQuery ),我们将执行以下操作，返回对`NodeList`的引用:

```
$$('button').set('textContent', 'Click Me'); 
```

现在让我们为每个按钮添加一个`click`事件监听器:

### 香草 JS:

```
var buttons = document.querySelectorAll('button'); // returns browser's useless NodeList
for(var i = 0, l = buttons.length; i < l; i++) {
  buttons[i].addEventListener('click', function() {
    this.classList.add('clicked');
  });
} 
```

### jQuery:

```
$('button').on('click', function() {
  $(this).addClass('click');
  // or mix jQuery with native using `classList`:
  this.classList.add('clicked');
}); 
```

### NodeList.js:

```
$$('button').addEventListener('click', function() {
  this.classList.add('clicked');
}); 
```

好了，jQuery `on`方法相当不错。我的库使用浏览器的原生 DOM APIs(因此是`addEventListener`)，但是它并没有阻止我们为方法创建一个别名:

```
$$.NL.on = $$.NL.addEventListener;

$$('button').on('click', function() {
  this.classList.add('clicked');
}); 
```

不错！这正好展示了我们添加自己的方法的方式:

```
$$.NL.myNewMethod = function() {
  // loop through each node with a for loop or use forEach:
  this.forEach(function(element, index, nodeList) {...}
  // where `this` is the NodeList being manipulated
} 
```

## 数组方法上的 NodeList.js

NodeList.js 确实继承了 Array.prototype，但不是直接继承，因为有些方法被修改了，所以用一个`NodeList`(节点数组)来使用它们是有意义的。

### 推动和取消移动

例如: [push](https://github.com/eorroe/NodeList.js#push) 和 [unshift](https://github.com/eorroe/NodeList.js#unshift) 方法只能以节点为参数，否则会抛出错误:

```
var nodes = $$('body');
nodes.push(document.documentElement);
nodes.push(1); // Uncaught Error: Passed arguments must be a Node 
```

所以`push`和`unshift`都返回`NodeList`以允许方法链接，这意味着它不同于 JavaScript 的原生`Array#push`或`Array#unshift`方法，它们接受任何东西并返回新的`Array`长度。如果我们想知道`NodeList`的长度，我们只需使用`length`属性。

这两种方法，就像 JavaScript 的本地`Array`方法一样，都会改变`NodeList`。

### 串联

[concat](https://github.com/eorroe/NodeList.js#concat) 方法将以下内容作为参数:

*   `Node`
*   `NodeList`(浏览器原生版本和 NodeList.js 版本)
*   `HTMLCollection`
*   `Array of Nodes`
*   `Array of NodeList`
*   `Array of HTMLCollection`

`concat`是一个[递归方法](https://en.wikipedia.org/wiki/Recursion_%28computer_science%29)，因此那些数组可以是我们喜欢的深度，并且将被展平。然而，如果传递的数组中有任何元素不属于`Node`、`NodeList`或`HTMLCollection`，它将抛出一个`Error`。

`concat`确实像 javascript 的`Array#concat`方法一样返回一个新的`NodeList`。

### 弹出、移动、映射、切片、过滤

[pop](https://github.com/eorroe/NodeList.js#pop) 和 [shift](https://github.com/eorroe/NodeList.js#shift) 方法都可以接受一个可选参数，以确定从`NodeList`到`pop`或`shift`有多少个节点。不像 JavaScript 的原生`Array#pop`或`Array#shift`，无论传递什么参数，总是从数组中选择一个元素。

如果每个映射值都是一个`Node`，[映射](https://github.com/eorroe/NodeList.js#map)方法将返回一个`NodeList`，否则返回一个映射值数组。

[切片](https://github.com/eorroe/NodeList.js#slice)和[过滤](https://github.com/eorroe/NodeList.js#filter)方法的行为就像它们在真实数组上做的一样，但是将返回一个`NodeList`。

由于 NodeList.js 不直接从`Array.prototype`继承，如果在 NodeList.js 加载后将一个方法添加到`Array.prototype`中，它将不会被继承。

您可以在这里查看其余的 [NodeList.js 数组方法。](https://github.com/eorroe/NodeList.js#array-methods)

## 特殊方法

NodeList.js 有四个独有的方法，还有一个名为`owner`的属性，相当于 jQuery 的`prevObject`属性。

### `get`和`set`方法:

有一些元素具有这种元素特有的属性(例如，锚标记上的`href`属性)。这就是为什么`$$('a').href`将返回`undefined`——因为它是一个属性，不是`NodeList`中的每个元素都继承它。这就是我们如何使用 [get 方法](https://github.com/eorroe/NodeList.js#get)来访问那些属性:

```
$$('a').get('href'); // returns array of href values 
```

[set 方法](https://github.com/eorroe/NodeList.js#set)可用于设置每个元素的属性:

```
$$('a').set('href', 'https://sitepoint.com/'); 
```

`set`也返回`NodeList`来允许方法链接。我们可以用在像`textContent`这样的事情上(两者是等价的):

```
$$('button').textContent = 'Click Me';

$$('button').set('textContent', 'Click Me'); // returns NodeList so you can method chain 
```

我们还可以在一次调用中设置多个属性:

```
$$('button').set({
    textContent: 'Click Me',
    onclick: function() {...}
}); 
```

而且以上都可以用任意属性来做，比如`style`:

```
$$('button').style; // this returns an `Array` of `CSSStyleDeclaration`

$$('button').style.set('color', 'white');

$$('button').style.set({
    color: 'white',
    background: 'lightblue'
}); 
```

### `call`法

[调用方法](https://github.com/eorroe/NodeList.js#call)允许你调用那些元素特有的方法(例如视频元素上的`pause`):

```
$$('video').call('pause'); // returns NodeList back to allow Method Chaining 
```

### `item`法

[项方法](https://github.com/eorroe/NodeList.js#item)相当于 [jQuery 的 eq 方法](https://api.jquery.com/eq/)。它返回一个仅包含所传递索引的节点的`NodeList`:

```
$$('button').item(1); // returns NodeList containing the single Node at index 1 
```

### `owner`属性

[所有者属性](https://github.com/eorroe/NodeList.js#the-owner-property)相当于 jQuery 的`prevObject`。

```
var btns = $$('button');
btns.style.owner === btns; // true 
```

`btns.style`返回一个样式数组，`owner`返回映射`style`的`NodeList`。

## NodeList.js 兼容性

我的库兼容所有主要的新浏览器，如下所述。

| 浏览器 | 版本 |
| --- | --- |
| 火狐浏览器 | 6+ |
| 旅行队 | 5.0.5+ |
| 铬 | 6+ |
| 工业管理学(Industrial Engineering) | 9+ |
| 歌剧 | 11.6+ |

## 结论

现在我们终于可以使用一个有用的`NodeList`对象了！

对于大约 4k 的 minified，您可以获得上面提到的所有功能，并且您可以在 NodeList.js 的 [GitHub 资源库中了解更多内容。](https://github.com/eorroe/NodeList.js)

因为 NodeList.js 使用浏览器作为依赖项，所以不需要做任何升级。每当浏览器向 DOM 元素添加新的方法/属性时，您将自动能够通过 NodeList.js 使用这些方法/属性。所有这一切意味着您需要担心的唯一不赞成的是浏览器放弃的方法。这些通常是使用率很低的，因为我们不能破坏网络。

你觉得怎么样？这是你会考虑使用的图书馆吗？有没有什么重要的功能缺失？我很乐意在下面的评论中听到你的意见。

## 分享这篇文章