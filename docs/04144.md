# JavaScript 中的不变性

> 原文：<https://www.sitepoint.com/immutability-javascript/>

不变性是函数式编程的核心原则，也为面向对象程序提供了很多东西。在本文中，我将展示不变性到底是什么，如何在 JavaScript 中使用这个概念，以及它为什么有用。

## 什么是不变性？

教科书对可变性的定义是<q>易受改变或变更的影响。</q>在编程中，我们用这个词来表示状态允许随时间变化的对象。不可变的值恰恰相反——一旦被创建，就永远不能改变。

如果这看起来很奇怪，请允许我提醒你，我们一直使用的许多价值观实际上是不变的。

```
var statement = "I am an immutable value";
var otherStr = statement.slice(8, 17);
```

我想没有人会对第二行没有改变`statement`中的字符串感到惊讶。事实上，没有字符串方法改变它们操作的字符串，它们都返回新的字符串。原因是字符串是不可变的——它们不能改变，我们只能创建新的字符串。

字符串不是 JavaScript 内置的唯一不可变的值。数字也是不变的。你能想象这样一种环境吗，评估表达式`2 + 3` *会改变数字`2`的含义*？这听起来很荒谬，但是我们一直在对我们的对象和数组这样做。

## 在 JavaScript 中，可变性比比皆是

在 JavaScript 中，字符串和数字在设计上是不可变的。但是，请考虑以下使用数组的示例:

```
var arr = [];
var v2 = arr.push(2);
```

`v2`的值是多少？如果数组的行为与字符串和数字一致，`v2`将包含一个新数组，其中有一个元素——数字 2。然而，事实并非如此。取而代之的是，`arr`引用被更新为包含该数字，而`v2`包含了`arr`的新长度。

想象一个`ImmutableArray`型。受字符串和数字行为的启发，它将具有以下行为:

```
var arr = new ImmutableArray([1, 2, 3, 4]);
var v2 = arr.push(5);

arr.toArray(); // [1, 2, 3, 4]
v2.toArray();  // [1, 2, 3, 4, 5]
```

类似地，可以代替大多数对象使用的不可变映射将具有“设置”属性的方法，这些方法实际上并不设置任何内容，而是返回具有所需更改的新对象:

```
var person = new ImmutableMap({name: "Chris", age: 32});
var olderPerson = person.set("age", 33);

person.toObject(); // {name: "Chris", age: 32}
olderPerson.toObject(); // {name: "Chris", age: 33}
```

就像`2 + 3`不会改变数字 2 或 3 的含义一样，一个人庆祝 33 岁生日也不会改变他们曾经是 32 岁的事实。

## 实践中 JavaScript 的不变性

JavaScript 还没有不可变的列表和映射，所以我们现在需要一个第三方库。有两个非常好的。第一个是 [Mori](https://github.com/swannodette/mori) ，它支持在 JavaScript 中使用 ClojureScript 的持久数据结构和支持 API。另一个是由脸书的开发者编写的 [immutable.js](https://github.com/facebook/immutable-js) 。对于这个演示，我将使用 immutable.js，因为 JavaScript 开发人员更熟悉它的 API。

在这个演示中，我们将看看如何使用不可变数据处理 Minesweeper。棋盘由一个不可变的地图表示，其中最有趣的数据是`tiles`。这是一个不变地图的不变列表，其中每个地图代表棋盘上的一块瓷砖。整个过程都是用 JavaScript 对象和数组初始化的，然后通过 immutable.js `fromJS`函数使之永久化:

```
function createGame(options) {
  return Immutable.fromJS({
    cols: options.cols,
    rows: options.rows,
    tiles: initTiles(options.rows, options.cols, options.mines)
  });
}
```

核心游戏逻辑的其余部分被实现为函数，这些函数将这个不可变的结构作为它们的第一个参数，并返回一个新的实例。最重要的功能是`revealTile`。当被调用时，它会将牌标记为已显示。对于可变数据结构，这将非常容易:

```
function revealTile(game, tile) {
  game.tiles[tile].isRevealed = true;
}
```

然而，对于上面提出的那种不可变的结构，这可能会变成一种考验:

```
function revealTile(game, tile) {
  var updatedTile = game.get('tiles').get(tile).set('isRevealed', true);
  var updatedTiles = game.get('tiles').set(tile, updatedTile);
  return game.set('tiles', updatedTiles);
}
```

唷！幸运的是，这种事情很常见。因此，我们的工具包为此提供了方法:

```
function revealTile(game, tile) {
  return game.setIn(['tiles', tile, 'isRevealed'], true);
}
```

现在`revealTile`函数返回*一个新的不可变实例*，其中一个瓦片不同于之前的版本。`setIn`是空安全的，如果键的任何部分不存在，它将用空对象填充。在扫雷棋盘的情况下，这是不可取的，因为丢失一个方块意味着我们试图在棋盘外显示一个方块。这可以通过使用`getIn`在操作之前寻找图块来缓解:

```
function revealTile(game, tile) {
  return game.getIn(['tiles', tile]) ?
    game.setIn(['tiles', tile, 'isRevealed'], true) :
    game;
}
```

如果瓷砖不存在，我们只是返回现有的游戏。这是实践中不变性的一个快速体验，要更深入，请查看这个代码笔，它包含了扫雷游戏规则的完整实现。

## 性能呢？

您可能认为这会产生糟糕的性能，在某些方面您是对的。每当您向不可变对象添加内容时，我们都需要通过复制现有值来创建一个新实例，并向其添加新值。这肯定会比对单个对象进行变异占用更多的内存，并且在计算上更具挑战性。

因为不可变对象从不改变，所以可以使用一种称为“结构化共享”的策略来实现它们，这种策略产生的内存开销比您预期的要少得多。与内置数组和对象相比，仍然会有开销，但它是恒定的，并且通常会被不变性带来的其他好处所掩盖。实际上，在许多情况下，使用不可变数据会提高应用程序的整体性能，即使某些孤立的操作会变得更加昂贵。

## 改进的变更跟踪

任何 UI 框架中最困难的任务之一是变异跟踪。这是一个如此普遍的挑战，EcmaScript 7 提供了一个单独的 API 来帮助以更好的性能跟踪对象突变:`Object.observe()`。虽然许多人对这个 API 感到兴奋，但其他人觉得它是错误问题的答案。在任何情况下，它都没有适当地解决突变跟踪问题:

```
var tiles = [{id: 0, isRevealed: false}, {id: 1, isRevealed: true}];
Object.observe(tiles, function () { /* ... */ });

tiles[0].id = 2;
```

`tiles[0]`对象的突变不会触发我们的突变观察器，因此所提出的突变跟踪机制甚至在最琐碎的用例中也失败了。在这种情况下，不变性有什么帮助？给定的应用程序状态`a`，以及潜在的新应用程序状态`b`:

```
if (a === b) {
  // Data didn't change, abort
}
```

如果应用程序状态没有更新，它将是和以前一样的实例，我们不需要做任何事情。这确实要求我们跟踪保存状态的引用，但是整个问题现在已经简化为管理单个引用。

## 结论

我希望这篇文章已经为您提供了一些关于不变性如何帮助您改进代码的背景知识，并且所提供的示例可以对这种工作方式的实际方面有所启发。不变性正在上升，这不会是你今年读到的关于这个主题的最后一篇文章。试试看，我保证你会和我一样兴奋。