# 反应的不变性

> 原文：<https://www.sitepoint.com/immutability-react/>

在我之前的文章中，我写了关于 T2 不变性的方法和原因。我认为，不变性真正大放异彩的一个领域是跟踪变化，这是现代前端框架中的一大挑战。在本文中，我将给出一个不变性如何与脸书开发的库 [React](https://facebook.github.io/react/) 一起使用的例子。

本文不讨论 React 的基础。如果你需要这个库的介绍，可以阅读文章[React JavaScript 库](http://developer.telerik.com/featured/introduction-to-the-react-javascript-framework/)简介。

## 如何在 React 中使用不变性

在我之前的文章中，我实现了扫雷的核心游戏逻辑。在本文中，我们将看看 UI 层。在 React 中使用 [immutable.js](https://github.com/facebook/immutable-js) 对象时，我们需要考虑几个问题。第一个问题是，您不能像这样将不可变的映射或列表直接传递给 React 组件:

```
var data = Immutable.Map();
React.render(MyComponent(data), element);
```

这样做行不通的原因是 React 逐个属性地复制这个对象的内容，并将其与现有的`props`合并。这意味着 React 不会获得不可变的实例。它也不会获取地图中包含的数据，因为它没有作为对象的属性公开——您必须使用`get()`方法访问数据。

解决方案很简单:

```
var data = Immutable.Map();
React.render(MyComponent({data: data}), element);
```

在本文中，所有组件都将处理不可变的数据，因此我们将创建一个小包装器来避免一遍又一遍地重复:

```
function createComponent(def) {
  var component = React.createFactory(React.createClass(def));
  return function (data) {
    return component({data: data});
  };
}
```

有了这个组件，我们可以忘记包装器，直到我们需要在`render()`函数中访问它(这里我们需要把它称为`this.props.data`)。我们的组件将只定义`render()`函数，所以我们可以让包装器为我们做更多的工作:

```
function createComponent(render) {
  var component = React.createFactory(React.createClass({
    render: function () {
      return render(this.props.data);
    }
  }));

  return function (data) {
    return component({data: data});
  };
}
```

有了这些，定义和使用处理不可变数据的组件就变得轻而易举了:

```
var div = React.DOM.div;

var Tile = createComponent(function (tile) {
  if (tile.get('isRevealed')) {
    return div({className: 'tile' + (tile.get('isMine') ? ' mine' : '')},
               tile.get('threatCount') > 0 ? tile.get('threatCount') : '');
  }

  return div({
    className: 'tile'
  }, div({className: 'lid'}, ''));
});
```

如果瓷砖露出来了，我们渲染一个地雷，如果那里有一个；否则，我们渲染附近地雷的数量，除非该数量为 0。如果矿井没有显示出来，我们就用一个“盖子”来渲染它，CSS 会让它看起来像一个可点击的瓷砖。

React 组件的其余部分同样简单。还有一个障碍需要注意。React 组件可以接受一组子组件。我们必须确保在将不可变列表交付给 React 之前，将它们转换为数组:

```
var Row = createComponent(function (tiles) {
  return div({className: 'row'}, tiles.map(Tile).toJS());
});
```

在不可变列表上调用`map()`会产生一个新的不可变列表，而`toJS()`会返回一个 React 可以使用的数组表示。这些，以及 UI 组件的其余部分可以在[这个代码栏](http://codepen.io/SitePoint/pen/vONLNP)中看到，你也可以在那里玩游戏。

## 加快速度

在我的上一篇文章中，我提到跟踪变化可以得到极大的改善，因为我们可以简化 React 等库中昂贵的差分算法。当你给 React 一些新数据时，它会在所有组件上调用`shouldComponentUpdate()`函数。如果该函数返回`false`，React 不会将该组件与现有版本进行区分，因此库不会重新呈现组成该组件的元素。这潜在地节省了大量工作，并且可以导致性能的大幅度提高。

让我们考虑一下我们的游戏。当你显示一个方块时，整个游戏会被重新渲染一遍。然而，由于我们的不可变数据模型，所有没有改变的图块仍然是相同的精确引用。这些不需要重新呈现，因为对于不可变的数据，相同的引用意味着没有变化。为了让 React 了解这个细节，我们可以如下改进我们的组件包装器:

```
function createComponent(render) {
  var component = React.createFactory(React.createClass({
    shouldComponentUpdate: function (newProps, newState) {
      // Simplified, this app only uses props
      return newProps.data !== this.props.data;
    },

    render: function() {
      return render.call(this, this.props.data);
    }
  }));

  return function (data) {
    return component({data: data});
  };
}
```

这段简单的代码足以让我们的应用程序从明显慢于基于可变数据的应用程序，提升到几乎两倍的速度。这就是高效变更跟踪产生的影响！这个改进版本也在 CodePen 上[可用。如果你想仔细看看一些数字，也有一个](http://codepen.io/SitePoint/pen/ZGbQQy) [GitHub 库](https://github.com/sitepoint-editors/react-sweeper)，其中包括一些粗略的基准和多个实现。

## 降低复杂性

可以说，不可变数据的最大好处是它如何减少意外的复杂性。可变数据本质上比不可变数据更复杂，因为它纠缠着状态和时间。在可变数据中，时间是一个内在因素。事实上，访问两个不同时间点的值可能会得到两个不同的值。另一方面，不可变数据没有这个特性。如果您在两个不同的时间点检索一个不可变数据的值，那么您肯定会获得相同的值。这迫使我们对我们的数据如何随时间变化采取更有意识的立场。

对于不可变的数据，某些很难实现的特性，甚至对于可变的数据来说是不可能实现的，都变得微不足道了。这种功能的一个例子是应用程序范围的撤销。如果您的应用程序状态可以用一个不可变的值来表示，那么实现 undo 就是保存一个应用程序状态的版本列表，并提供一个按钮将应用程序状态重置为以前的版本。

让我们在扫雷项目中添加一个“撤销”功能。每当用户点击一个瓷砖，我们得到一个新的游戏对象来渲染。为了支持这一功能，我们将保留旧版本。实现此功能的代码如下所示:

```
var newGame = revealTile(game, tile);

if (newGame !== game) {
  gameHistory = gameHistory.push(newGame);
  game = newGame;
}

render();
```

然后是“撤销”按钮:

```
var UndoButton = createComponent(function () {
  return React.DOM.button({
    onClick: function () {
      channel.emit('undo');
    }
  }, 'Undo');
});
```

`channel`对象是一个事件发射器，所以我们需要一个监听这个事件的代码。除非我们已经穷尽了历史，否则我们会弹出最后一个版本，并将倒数第二个版本恢复为当前状态，并重新渲染游戏。这是通过以下代码完成的:

```
channel.on('undo', function () {
  if (history.size > 1) {
    gameHistory = gameHistory.pop();
    game = gameHistory.last();
    render();
  }
});
```

非常容易，不是吗？你能想象在一个状态由可变对象组成的应用程序中如何做这样的事情吗？通过访问我准备的的 [CodePen 或者下面的演示来玩带撤销的扫雷游戏(基本上是欺骗，但是嘿…):](http://codepen.io/SitePoint/pen/qdObZy)

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [qdObZy](http://codepen.io/SitePoint/pen/qdObZy/) 。