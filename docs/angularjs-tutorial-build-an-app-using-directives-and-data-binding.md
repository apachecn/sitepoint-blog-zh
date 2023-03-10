# AngularJS —使用指令和数据绑定构建应用程序

> 原文：<https://www.sitepoint.com/angularjs-tutorial-build-an-app-using-directives-and-data-binding/>

AngularJS 很快获得了最具前瞻性的 JavaScript 框架之一的声誉，这是有充分理由的。Angular 由谷歌支持和开发，它采用了一种方法来处理你的前端，这种方法起初可能看起来有点奇怪，但你很快就会想知道为什么你要用其他方式来做事情。

Angular 让开发人员能够编写前端代码，而无需直接操作 DOM。本教程将通过构建一个使用指令和数据绑定来定义动态视图和控制器的应用程序，让您开始使用这个框架。

如果你熟悉 coffee script(Angular 不要求)，你会对这篇文章更感兴趣，但是 JavaScript 的工作知识应该足够了。

你可能以前见过很多 Todo 应用程序，所以让我们来创建一些有趣的东西——零和十字！

我们将从标记我们的板开始。

Angular 声称扩展了 HTML 的词汇表，而不是将 DOM 隐藏在 JavaScript 后面。基本原理是 HTML 本身已经很好了，但是我们可以添加更多的元素和属性来构建一个你已经熟悉的强大的动态模板语言。

我们的游戏板只是一张简单的桌子。如果我们用一厢情愿的方式编程，我们真正想要做的就是迭代游戏棋盘，为每个棋盘输出一个单元格。这样做的真实代码非常接近我们的设想:

```
<table>
  <tr ng-repeat="row in board.grid">
    <td ng-repeat="cell in row">
      {{ cell.marker }}
    </td>
  </tr>
</table>
```

等等，那些有趣的东西和胡子支架是干什么用的？让我们退后一点，一步一步来。

```
<tr ng-repeat="row in board.grid">
```

## angularjs 指令

[`ng-repeat`](http://docs.angularjs.org/api/ng.directive:ngRepeat) 是一个有角度的*指令*，提供的 HTML 扩展之一。它允许我们迭代一个集合，为其中的每一项实例化模板。在我们的例子中，我们告诉 Angular 为我们的板的网格属性中的每一行重复`<tr>`——现在假设`grid`是一个二维数组，而`board`是窗口上的一个对象。

```
<td ng-repeat="cell in row">
  {{ cell.marker }}
</td>
```

然后，我们使用另一个`ng-repeat`指令来迭代该行中的单元格。这里的双花括号表示一个使用角度 [*数据绑定*](http://docs.angularjs.org/guide/dev_guide.templates.databinding) 的 [*表达式*](http://docs.angularjs.org/guide/expression)—`td`的内容将被替换为相应单元格的`marker`属性。

到目前为止很简单，对吧？您会立即对结果标记的外观有所了解。我们不需要使用像 jQuery 这样沉重的东西来创建新元素并填充它们，我们只需要将模板显式化。这更容易维护——我们只需查看我们的 HTML，就能确切地知道 DOM 将在哪里以及如何被改变，而不是追踪一些我们不记得写过的晦涩的 JavaScript。

既然我们可以可视化我们的电路板的状态，我们将通过定义`board`的真正含义为它提供一个数据源。

```
app = angular.module('ngOughts', ['ng'])
```

Web 从添加一些为我们应用程序定义角度模块的 JavaScript 开始。第一个参数是我们应用的名字，`['ng']`意味着我们需要 Angular[‘ng’模块](http://docs.angularjs.org/api/ng)，它提供核心的 Angular 服务。

我们调整我们的 HTML 来表明我们将使用带有 [`ng-app`](http://docs.angularjs.org/api/ng.directive:ngApp) 指令的应用程序模块。

```
<html ng-app='ngOughts'>
```

## MVC——定义控制器和视图

这就是 Angular 的 [MVC 性质](http://docs.angularjs.org/guide/dev_guide.mvc)发挥作用的地方。我们添加了更多的 JS 来调用我们新创建的应用程序模块上的`controller`函数，传递我们控制器的名称和实现它的函数。

```
app.controller "BoardCtrl", ($scope) ->
```

在这种情况下，我们的控制器函数接受一个参数， [`$scope`](http://docs.angularjs.org/guide/scope) ，这是我们的控制器的一个*依赖项*。Angular 使用 [*依赖注入*](http://docs.angularjs.org/guide/di) 来为我们提供这个服务对象，从我们的函数参数的名称中推断出正确的对象(还有一个替代语法也允许缩小)。

我们现在添加一个 [`ng-controller`](http://docs.angularjs.org/api/ng.directive:ngController) 指令到我们的 HTML 模板来连接它到我们的控制器:

```
<body ng-controller="BoardCtrl">
  <table>
    <tr ng-repeat="row in board.grid">
      ...
    </tr>
  </table>
</body>
```

同样，就像带有控制器名称的属性一样简单。事情变得有趣起来——嵌套在我们的`body`标记中的元素现在可以访问`$scope`服务对象。我们的`ng-repeat`属性将在`BoardCtrl`范围内寻找 board 变量，所以让我们定义一下:

```
app.controller "BoardCtrl", ($scope, Board) ->
    $scope.board = new Board
```

现在我们有进展了。我们将一个`Board`注入到我们的控制器中，实例化它并使它在`BoardCtrl`的范围内可用。

让我们继续实际实现一个简单的`Board`类。

```
class Board
  SIZE = 3
  EMPTY  = ' '
  NOUGHT = 'O'
  CROSS  = 'X'
  PLAYER_MARKERS = [NOUGHT, CROSS]
  constructor: ->
    @reset()
  reset: ->
    @grid = [1..SIZE].map ->
      [1..SIZE].map ->
        new Cell(EMPTY)
  class Cell
    constructor: (@marker) ->
```

## 添加工厂

然后我们可以定义一个 [*工厂*](http://docs.angularjs.org/guide/dev_guide.services.creating_services) ，它返回`Board`类，允许它被注入到我们的控制器中。

```
angular.module("ngOughts").factory "Board", ->
  Board
```

可以在`factory`函数中直接定义`Board`，或者甚至将`Board`放在窗口对象上，但是在这里保持它的独特性允许我们在与 AngularJS 隔离的情况下测试`Board`,并鼓励重用。

所以现在我们有一个空板子。令人兴奋的东西，对不对？让我们设置一下，单击单元格
会在那里放置一个标记。

```
<table>
  <tr ng-repeat="row in board.grid">
    <td ng-repeat="cell in row" ng-click="board.playCell(cell)">
      {{ cell.marker }}
    </td>
  </tr>
</table>
```

我们已经为每个`<td>`元素添加了一个 [`ng-click`](http://docs.angularjs.org/api/ng.directive:ngClick) 指令。当表格单元格被点击时，我们将使用被点击的单元格对象调用棋盘上的`playCell`函数。填写`Board`实现:

```
class Board
  SIZE = 3
  EMPTY  = ' '
  NOUGHT = 'O'
  CROSS  = 'X'
  PLAYER_MARKERS = [NOUGHT, CROSS]
  constructor: ->
    @reset()
  reset: ->
    @current_player = 0
    @grid = [1..SIZE].map ->
      [1..SIZE].map ->
        new Cell(EMPTY)
  playCell: (cell) ->
    return if cell.hasBeenPlayed()
    cell.mark(@currentPlayerMarker())
    @switchPlayer()
  currentPlayerMarker: ->
    PLAYER_MARKERS[@current_player]
  switchPlayer: ->
    @current_player ^= 1
  class Cell
    constructor: (@marker) ->
    mark: (@marker) ->
    hasBeenPlayed: ->
      @marker != EMPTY
```

## 双向数据绑定

好了，现在我们已经更新了电路板模型，我们需要返回并更新视图，对吗？

没有。角度数据绑定是*双向的*——它观察模型的变化并将它们传播回视图。类似地，更新视图将会更新相应的模型。我们的标记将在我们的`Board`内部`grid`中更新，并且`<td>`的内容将立即改变以反映这一点。

这减少了您以前需要编写的大量脆弱的、依赖选择器的样板代码。您可以专注于您的应用程序逻辑和行为，而不是管道。

如果我们知道谁赢了就好了。让我们实现这一点。我们将在这里省略检查 win 条件的代码，但它会出现在最终代码中。比方说，当我们找到一个 win 时，我们在组成它的每个单元格上设置`winning`属性。

然后，我们可以将`<td>`更改为如下所示:

```
<td ng-repeat="cell in row" ng-click="board.playCell(cell)" ng-class="{'winning': cell.winning}">
  {{ cell.marker }}
</td>
.winning {
  background: green;
  color: white;
}
```

如果`winning`为真， [`ng-class`](http://docs.angularjs.org/api/ng.directive:ngClass) 将把‘winning’CSS 类应用到`<td>`，让我们设置一个愉快的绿色背景来纪念我们的胜利。你说重赛？我们需要一个板复位按钮:

```
<button ng-click="board.reset()">reset board</button>
```

将它添加到我们的控制器中，我们将在单击按钮时调用`reset`。棋盘标记将被清除，所有 CSS 类都被清除，我们准备好再次开始——我们不需要更新任何 DOM 元素。

让我们为我们的胜利幸灾乐祸吧:

```
<h1 ng-show="board.won">{{ board.winning_marker }} won the game!</h1>
```

[`ng-show`](http://docs.angularjs.org/api/ng.directive:ngShow) 指令允许我们在游戏获胜时有条件地显示`<h1>`元素，数据绑定允许我们插入获胜者的标记。简单又有表现力。

## 更加可组合、可测试的应用

有趣的是，我们的大部分代码都处理普通的旧 JavaScript。这是有意的——没有扩展框架对象，只是编写和调用 JS。这种方法更适合于轻量级的可组合、可测试的应用程序。我们的设计关注点被 MVC 分开，但是我们不需要写一堆代码来把事情联系在一起。

不过 AngularJS 也不是没有限制。许多人抱怨官方文档和相对陡峭的学习曲线，一些人担心 SEO，另一些人则对非标准 HTML 属性和元素的使用感到厌恶。

这些问题都有解决方案，AngularJS 独特的 web 开发方法绝对值得花些时间去探索。

你可以在 [Plunkr](http://plnkr.co/edit/tM9M28Gq9Vzj2ecLVsHY?p=preview) 上看到最终的代码，或者从 GitHub 下载。

对本文的评论已经关闭。对 AngularJS 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章