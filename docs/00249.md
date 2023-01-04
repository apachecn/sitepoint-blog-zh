# 不可变数组方法:如何编写非常干净的 JavaScript 代码

> 原文：<https://www.sitepoint.com/immutable-array-methods-javascript/>

在我们的[JavaScript](https://www.sitepoint.com/variable-assignment-mutation-javascript)中的变量赋值和变异指南中，我们研究了变量变异的问题以及如何管理它们。在本文中，我们将看到改变原始数组的数组方法是如何给我们制造困难的。但这并不都是悲观的。我们将编写不可变的数组方法来修复这些问题——现在您就可以开始在代码中使用它们了。

在我的新书 [**学习用 JavaScript 编码**](https://www.sitepoint.com/premium/books/learn-to-code-with-javascript/) 中，更详细地探索这个主题，并了解现代 JavaScript。

## JavaScript 中的数组突变

JavaScript 中的数组只是对象，这意味着它们可以变异。事实上，许多内置数组方法会使数组本身发生变异。这可能意味着上面的黄金法则被打破了，只是通过使用一个内置的方法。

这里有一个例子，展示了它如何可能导致一些问题:

```
const numbers = [1,2,3];
const countdown = numbers.reverse();
```

这段代码看起来不错。我们有一个名为`numbers`的数组，我们希望另一个名为`countdown`的数组以相反的顺序列出数字。这似乎很有效。如果你检查`countdown`变量的值，这就是我们所期望的:

```
countdown
<< [3,2,1]
```

该操作的不幸副作用是，`reverse()`方法也改变了`numbers`数组。这根本不是我们想要的:

```
numbers
<< [3,2,1]
```

更糟糕的是，这两个变量都引用同一个数组，因此我们随后对其中一个变量所做的任何更改都会影响到另一个变量。假设我们使用`Array.prototype.push()`方法将值`0`添加到`countdown`数组的末尾。它将对`numbers`数组做同样的事情(因为它们都引用同一个数组):

```
countdown.push(0)
<< 4
countdown
<< [3,2,1,0]
numbers
<< [3,2,1,0]
```

这种副作用可能会被忽视——尤其是在大型应用程序中——并导致一些很难跟踪的错误。

### JavaScript 中的可变数组方法

并且`reverse`并不是唯一导致这种变异的数组方法。下面是一个数组方法列表，这些方法会改变调用它们的数组:

*   [Array.prototype.pop()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)
*   [Array.prototype.push()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)
*   [Array.prototype.shift()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)
*   [array . prototype . un shift()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)
*   [array . prototype . reverse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)
*   [Array.prototype.sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
*   [Array.prototype.splice()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)

有点令人困惑的是，数组也有一些方法不会改变原始数组，而是返回一个新数组:

*   [Array.prototype.slice()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
*   [Array.prototype.concat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)
*   [Array.prototype.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
*   [Array.prototype.filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

这些方法将根据它们执行的操作返回一个新数组。例如，`map()`方法可用于将数组中的所有数字加倍:

```
const numbers = [1,2,3];
const evens = numbers.map(number => number * 2);
<< [2,4,6]
```

现在，如果我们检查`numbers`数组，我们可以看到它没有受到调用方法的影响:

```
numbers
<< [1,2,3]
```

似乎没有任何理由可以解释为什么有些方法会改变数组，而有些方法不会。但是最近增加的趋势是使它们不变异。很难记住哪个做哪个。

Ruby 使用 bang 符号的方式很好地解决了这个问题。任何导致调用它的对象永久改变的方法都以失败告终。`[1,2,3].reverse!`将反转数组，而`[1,2,3].reverse`将返回一个元素被反转的新数组。

## 不可变数组方法:让我们来解决这种混乱的变化！

我们已经确定突变可能是有害的，并且许多数组方法会导致它们。让我们看看如何避免使用它们。

写一些函数返回一个新的数组对象而不是改变原来的数组并不困难。这些函数是我们的不可变数组方法。

因为我们不打算[猴子补丁](https://en.wikipedia.org/wiki/Monkey_patch) `Array.prototype`，这些函数将总是接受数组本身作为第一个参数。

### 流行音乐

让我们从编写一个新的`pop`函数开始，该函数返回原始数组的副本，但没有最后一项。注意`Array.prototype.pop()`返回从数组末尾弹出的值:

```
const pop = array => array.slice(0,-1);
```

这个函数使用`Array.prototype.slice()`返回数组的副本，但是删除了最后一项。第二个参数-1 意味着<q>在结束前停止切片 1 个位置。</q>我们可以在下面的例子中看到这是如何工作的:

```
const food = ['🍏','🍌','🥕','🍩'];
pop(food)
<< ['🍏','🍌','🥕']
```

### 推

接下来，让我们创建一个`push()`函数，它将返回一个新的数组，但是在末尾附加了一个新元素:

```
const push = (array, value) => [...array,value];
```

这使用了 spread 运算符来创建数组的副本。然后，它将作为第二个参数提供的值添加到新数组的末尾。这里有一个例子:

```
const food = ['🍏','🍌','🥕','🍩'];
push(food,'🍆')
<< ['🍏','🍌','🥕','🍩','🍆']
```

### 换档和非换档

类似地，我们可以编写`Array.prototype.shift()`和`Array.prototype.unshift()`的替换:

```
const shift = array => array.slice(1);
```

对于我们的`shift()`函数，我们只是从数组中切掉第一个元素，而不是最后一个。这可以从下面的例子中看出:

```
const food = ['🍏','🍌','🥕','🍩'];
shift(food)
<< ['🍌','🥕','🍩']
```

我们的`unshift()`方法将返回一个新数组，新值附加到数组的*开头的*处:

```
const unshift = (array,value) => [value,...array];
```

spread 操作符允许我们在数组中以任意顺序放置值。我们简单地将新值*放在原始数组副本*之前。我们可以在下面的例子中看到它是如何工作的:

```
const food = ['🍏','🍌','🥕','🍩'];
unshift(food,'🍆')
<< ['🍆','🍏','🍌','🥕','🍩']
```

### 反面的

现在让我们试着写一个`Array.prototype.reverse()`方法的替代。它将以相反的顺序返回数组的副本，而不是改变原始数组:

```
const reverse = array => [...array].reverse();
```

这个方法仍然使用`Array.prototype.reverse()`方法，但是应用于我们使用 spread 操作符制作的原始数组的副本。在一个对象*被创建后，立即对其进行*变异并没有错，这就是我们在这里所做的。我们可以在下面的例子中看到它的工作原理:

```
const food = ['🍏','🍌','🥕','🍩'];
reverse(food)
<< ['🍩','🥕','🍌','🍏']
```

### 接合

最后，我们来处理一下`Array.prototype.splice()`。这是一个非常通用的函数，所以我们不会完全重写它的功能(尽管这将是一个[有趣的尝试](https://codepen.io/SitePoint/pen/jOBMYVg)。(提示:使用 spread 运算符和`splice()`。)相反，我们将关注 slice 的两个主要用途:从数组中移除项和向数组中插入项。

### 移除数组项目

让我们从返回一个新数组的函数开始，但是移除了给定索引处的一个项:

```
const remove = (array, index) => [...array.slice(0, index),...array.slice(index + 1)];
```

这使用了`Array.prototype.slice()`将数组分成两半——我们想要移除的项目的任何一边。第一个片段返回一个新数组，复制原始数组的元素，直到索引位于指定为参数的元素之前。第二个片段返回一个数组，其中包含我们要移除的元素之后的元素，一直到原始数组的末尾。然后我们使用 spread 操作符将它们放在一个新的数组中。

我们可以通过尝试移除下面的`food`数组中索引 2 处的项目来检查这一点:

```
const food = ['🍏','🍌','🥕','🍩'];
remove(food,2)
<< ['🍏','🍌','🍩']
```

### 添加数组项目

最后，让我们编写一个函数，它将返回一个新数组，并在特定索引处插入一个新值:

```
const insert = (array,index,value) => [...array.slice(0, index), value, ...array.slice(index)];
```

这与`remove()`函数的工作方式类似。它创建了数组的两个部分，但这次包括了所提供的索引处的元素。当我们将这两个片段放在一起时，我们将提供的值作为它们之间的参数插入。

我们可以通过尝试在我们的`food`数组中间插入一个纸杯蛋糕表情符号来检查这一点:

```
const food = ['🍏','🍌','🥕','🍩']
insert(food,2,'🧁')
<< ['🍏','🍌','🧁','🥕','🍩']
```

现在我们有了一组不可变的数组方法，这些方法不涉及原始数组。我已经把它们都保存在 CodePen 的一个地方，所以你可以随意复制它们并在你的项目中使用它们。您可以通过使它们成为单个对象的方法来命名它们，或者只在需要时使用它们。

对于大多数数组操作，这些应该足够了。如果需要执行不同的操作，请记住黄金法则:首先使用 spread 操作符复制原始数组。然后，*立即*对该副本应用任何变异方法。

## 结论

在本文中，我们看到了 JavaScript 如何使数组方法变得困难，这些方法将原始数组作为语言的一部分。然后我们编写了自己的不可变数组方法来替换这些函数。

你能想到有没有其他的数组方法会受益于不可变版本？为什么不在推特上告诉我呢？

如果你想了解现代 JavaScript，别忘了看看我的新书 [**学习用 JavaScript 编码**](https://www.sitepoint.com/premium/books/learn-to-code-with-javascript/) 。

## 分享这篇文章