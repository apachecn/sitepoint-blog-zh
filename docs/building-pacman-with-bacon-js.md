# 用 Bacon.js 构建吃豆人游戏

> 原文：<https://www.sitepoint.com/building-pacman-with-bacon-js/>

JavaScript 支持异步编程。这可能是一种祝福，也可能是一种诅咒，导致了“回调地狱”的概念。有一些处理组织异步代码的实用程序库，比如 [Async.js](https://github.com/caolan/async) ，但是仍然很难有效地遵循异步代码的控制流和推理。

在本文中，我将通过使用一个名为 Bacon.js 的库，向您介绍*反应式编程*的概念，它有助于处理 JavaScript 的异步特性。

## 让我们积极应对

反应式编程是关于异步数据流的。它用可观察模式替换迭代器模式。这不同于命令式编程，在命令式编程中，你主动迭代数据来处理东西。在反应式编程中，您订阅数据并异步地对事件做出反应。

巴特·德·斯梅特在本次演讲中解释了这种转变。André Staltz 在本文的[中深入探讨了反应式编程。](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

一旦你变得被动，一切都变成了异步数据流:服务器上的数据库、鼠标事件、承诺和服务器请求。这让您避免了所谓的“回调地狱”，并为您提供了更好的错误处理。这种方法的另一个强大的特性是能够将流组合在一起，这为您提供了更好的控制和灵活性。贾法尔·侯赛因在本次演讲中解释了这些概念。

Bacon.js 是一个反应式编程库，它是 [RxJS](https://github.com/Reactive-Extensions/RxJS) 的替代品。在接下来的部分中，我们将使用 Bacon.js 构建一个著名游戏“吃豆人”的版本。

## 设置项目

要安装 Bacon.js，您可以通过在 CLI 上运行以下命令来使用 [Bower](http://bower.io/) :

```
$ bower install bacon
```

一旦安装了库，您就可以开始工作了。

### PacmanGame API 和 UnicodeTiles.js

对于外观和感觉，我将使用基于文本的系统，这样我就不必处理资产和精灵。为了避免自己创建，我将使用一个名为 [UnicodeTiles.js](http://tapiov.net/unicodetiles.js/) 的强大库。

首先，我构建了一个名为`PacmanGame`的类，它处理游戏逻辑。以下是它提供的方法:

*   `PacmanGame(parent)`:创建一个吃豆人游戏对象
*   `start()`:开始游戏
*   `tick()`:更新游戏逻辑，渲染游戏
*   生出一个新的幽灵
*   更新游戏中的每一个幽灵
*   `movePacman(p1V)`:向指定方向移动 Pacman

此外，它公开了以下回调:

*   `onPacmanMove(moveV)`:如果存在，当用户通过按键请求 Pacman 移动时调用

因此，要使用这个 API，我们将`start`游戏，定期调用`spawnGhost`来产生幽灵，监听`onPacmanMove`的回调，每当发生这种情况，调用`movePacman`来实际移动吃豆人。我们也定期给`updateGhosts`打电话，更新幽灵的动向。最后，我们定期调用`tick`来更新更改。重要的是，我们将使用 Bacon.js 来帮助我们处理事件。

在我们开始之前，让我们创建我们的游戏对象:

```
var game = new PacmanGame(parentDiv);
```

我们创建一个新的`PacmanGame`,传递一个父 DOM 对象`parentDiv`,游戏将被渲染到其中。现在我们已经准备好构建我们的游戏了。

## 事件流或可观察事件

一个*事件流*是一个可观察对象，您可以订阅它来异步观察事件。使用这三种方法可以观察到三种类型的事件:

*   [`observable.onValue(f)`](http://baconjs.github.io/api.html#observable-onvalue) :监听值事件，这是处理事件最简单的方法。
*   [`observable.onError(f)`](http://baconjs.github.io/api.html#observable-onerror) :监听错误事件，用于处理流中的错误。
*   [`observable.onEnd(f)`](http://baconjs.github.io/api.html#observable-onend) :监听流已经结束的事件，没有移动值可用。

### 创建流

现在我们已经看到了事件流的基本用法，让我们看看如何创建一个。Bacon.js 提供了几个方法,您可以使用它们从 jQuery 事件、Ajax promise、DOM EventTarget、简单回调甚至数组创建事件流。

另一个关于事件流的有用概念是时间的概念。也就是说，事件可能会在未来某个时间发生。例如，这些方法创建在某个时间间隔传递事件的事件流:

*   [`Bacon.interval(interval, value)`](http://baconjs.github.io/api.html#bacon-interval) :以给定的间隔无限重复`value`。
*   [`Bacon.repeatedly(interval, values)`](http://baconjs.github.io/api.html#bacon-repeatedly) :无限重复给定间隔的`values`。
*   [`Bacon.later(delay, value)`](http://baconjs.github.io/api.html#bacon-later) :给定`delay`后产生`value`。

为了更好的控制，你可以使用 [`Bacon.fromBinder()`](http://baconjs.github.io/api.html#bacon-frombinder) 滚动你自己的事件流。我们将在游戏中通过创建一个`moveStream`变量来展示这一点，该变量为我们的 Pacman 动作产生事件。

```
var moveStream = Bacon.fromBinder(function(sink) {
   game.onPacmanMove = function(moveV) {
      sink(moveV);
   };
});
```

我们可以用一个值调用`sink`,这个值将发送一个事件，观察者可以监听这个事件。对`sink`的调用在我们的`onPacmanMove`回调中——也就是每当用户按下一个键请求 Pacman 移动时。所以我们创建了一个可观察对象，它发出关于 Pacman 移动请求的事件。

注意，我们用普通值`moveV`调用了`sink`。这将用值`moveV`推动移动事件。我们也可以推送类似 [`Bacon.Error`](http://baconjs.github.io/api.html#bacon-error) ，或者 [`Bacon.End`](http://baconjs.github.io/api.html#bacon-end) 的事件。

让我们创建另一个事件流。这一次，我们要发出事件通知，以产生一个幽灵。我们将为此创建一个`spawnStream`变量:

```
var spawnStream = Bacon.sequentially(800, [
   PacmanGame.GhostColors.ORANGE,
   PacmanGame.GhostColors.BLUE,
   PacmanGame.GhostColors.GREEN,
   PacmanGame.GhostColors.PURPLE,
   PacmanGame.GhostColors.WHITE,
]).delay(2500);
```

[`Bacon.sequentially()`](http://baconjs.github.io/api.html#bacon-sequentially) 创建一个以给定间隔传送`values`的流。在我们的例子中，它会每隔 800 毫秒产生一个重影颜色。我们也有一个调用`delay()`的方法。它延迟流，因此事件将在 2.5 秒的延迟后开始发出。

### 事件流和大理石图的方法

在这一节中，我将列出一些可以在事件流中使用的更有用的方法:

*   [`observable.map(f)`](http://baconjs.github.io/api.html#observable-map) :映射值并返回新的事件流。
*   [`observable.filter(f)`](http://baconjs.github.io/api.html#observable-filter) :用给定的谓词过滤值。
*   [`observable.takeWhile(f)`](http://baconjs.github.io/api.html#observable-takewhile) :当给定谓词为真时取。
*   [`observable.skip(n)`](http://baconjs.github.io/api.html#observable-skip) :跳过流中的第一个`n`元素。
*   [`observable.throttle(delay)`](http://baconjs.github.io/api.html#observable-throttle) :通过一些`delay`节流。
*   [`observable.debounce(delay)`](http://baconjs.github.io/api.html#observable-debounce) :通过一些`delay`节流。
*   [`observable.scan(seed, f)`](http://baconjs.github.io/api.html#observable-scan) 用给定的种子值和累加器函数扫描流。这将流缩减为一个值。

关于事件流的更多方法，请参见[官方文档页面](http://baconjs.github.io/api.html#common-methods-in-eventstreams-and-properties)。从[大理石图](http://rxmarbles.com/)可以看出`throttle`和`debounce`的区别:

```
// `source` is an event stream.
//
var throttled = source.throttle(2);

// source:    asdf----asdf----
// throttled: --s--f----s--f--

var debounced = source.debounce(2);

// source:             asdf----asdf----
// source.debounce(2): -----f-------f--
```

如您所见，`throttle`照常限制事件，而`debounce`仅在给定的“静默期”后发出事件。

这些实用方法简单却非常强大，能够概念化和控制流，从而控制其中的数据。我推荐观看[这篇关于网飞如何利用这些简单方法创建自动完成框的演讲。](http://youtu.be/XRYN2xt11Ek?t=20m42s)

### 观察事件流

到目前为止，我们已经创建并操作了事件流，现在我们将通过订阅流来观察事件。

回想一下我们之前创建的`moveStream`和`spawnStream`。现在让我们订阅它们:

```
moveStream.onValue(function(moveV) {
   game.movePacman(moveV);
});

spawnStream.onValue(function(ghost) {
   game.spawnGhost(ghost);
});
```

尽管你可以使用 [stream.subscribe()](http://baconjs.github.io/api.html#stream-subscribe) ，来订阅一个流，你也可以使用 [stream.onValue()](http://baconjs.github.io/api.html#stream-onvalue) 。不同之处在于，`subscribe`将发出我们之前见过的三种类型的事件，而`onValue`将只发出类型为 [`Bacon.Next`](http://baconjs.github.io/api.html#bacon-next) 的事件。那就是它将省略 [`Bacon.Error`](http://baconjs.github.io/api.html#bacon-error) 和 [`Bacon.End`](http://baconjs.github.io/api.html#bacon-end) 事件。

当一个事件到达`spawnStream`(每 800 毫秒发生一次)，它的值将是一个重影颜色，我们使用这个颜色来产生一个重影。当事件到达`moveStream`时，回想一下当用户按下一个键来移动 Pacman 时发生的情况。我们用方向`moveV`来称呼`game.movePacman`:这伴随着事件，所以吃豆人移动。

### 组合事件流和`Bacon.Bus`

您可以组合事件流来创建其他流。有许多方法可以组合事件流，下面是其中的几种:

*   [`Bacon.combineAsArray(streams)`](http://baconjs.github.io/api.html#bacon-combineasarray) :合并事件流，这样结果流将有一个值数组作为它的值。
*   [`Bacon.zipAsArray(streams)`](http://baconjs.github.io/api.html#bacon-zipasarray) :将流压缩成新的流。来自每个流的事件被成对组合。
*   [`Bacon.combineTemplate(template)`](http://baconjs.github.io/api.html#bacon-combinetemplate) :使用模板对象组合事件流。

我们来看一个`Bacon.combineTemplate`的例子:

```
var password, username, firstname, lastname; // <- event streams
var loginInfo = Bacon.combineTemplate({
   magicNumber: 3,
   userid: username,
   passwd: password,
   name: { first: firstname, last: lastname }
});
```

如你所见，我们使用一个模板将事件流——即`password`、`username`、`firstname`和`lastname`——组合成一个名为`loginInfo`的组合事件流。每当一个事件流得到一个事件，`loginInfo` stream 就会发出一个事件，将所有其他模板组合成一个模板对象。

还有另外一种 Bacon.js 结合流的方式，那就是 [`Bacon.Bus()`](http://baconjs.github.io/api.html#bus) 。`Bacon.Bus()`是一个事件流，允许您将值推入流中。它还允许将其他流插入总线。我们将用它来构建游戏的最后一部分:

```
var ghostStream = Bacon.interval(1000, 0);

ghostStream.subscribe(function() {
   game.updateGhosts();
});

var combinedTickStream = new Bacon.Bus();

combinedTickStream.plug(moveStream);
combinedTickStream.plug(ghostStream);

combinedTickStream.subscribe(function() {
   game.tick();
});
```

现在我们使用`Bacon.interval`创建另一个流——`ghostStream`。该流将每 1 秒发出 0。这次我们`subscribe`来了，叫`game.updateGhosts`来驱鬼。这是为了每 1 秒钟移动一次幽灵。注意注释掉的`game.tick`，还记得我们`moveStream`中的另一个`game.tick`？两个流都更新游戏，最后调用`game.tick`来呈现变化，所以我们可以产生第三个流——这两个流的组合——并在组合流中调用`game.tick`,而不是在每个流中调用`game.tick`。

为了合并流，我们可以使用`Bacon.Bus`。这是我们游戏中的最终事件流，我们称之为`combinedTickStream`。然后我们`plug`将`moveStream`和`ghostStream`都放入其中，最后`subscribe`放入其中并调用其中的`game.tick`。

就这样，我们结束了。剩下唯一要做的就是用`game.start();`开始游戏。

## `Bacon.Property`还有更多例子

[培根。属性](http://baconjs.github.io/api.html#property)，是一个无功属性。想象一个反应性的属性，它是一个数组的和。当我们向数组中添加一个元素时，reactive 属性将会做出反应并自我更新。要使用`Bacon.Property`，您可以订阅它并监听更改，或者使用 [property.assign(obj，method)](http://baconjs.github.io/api.html#property-assign) 方法，该方法在属性更改时调用给定`object`的`method`。下面是一个如何使用`Bacon.Property`的例子:

```
var source = Bacon.sequentially(1000, [1, 2, 3, 4]);

var reactiveValue = source.scan(0, function(a, b) {
   return a + b;
});

// 0 + 1 = 1
// 1 + 2 = 3
// 3 + 3 = 6
// 6 + 4 = 10
```

首先，我们创建一个事件流，它产生一个给定数组的值——1、2、3 和 4——间隔 1 秒，然后我们创建一个反应属性，它是一个`scan`的结果。这将为`reactiveValue`分配 1、3、6 和 10 个值。

## 了解更多信息并进行现场演示

在本文中，我们通过构建一个 Pacman 游戏，用 Bacon.js 介绍了反应式编程。它简化了我们的游戏设计，通过事件流的概念给了我们更多的控制和灵活性。完整的源代码可以在 [GitHub](https://github.com/eguneys/pacman-unicode) 获得，现场演示可以在[这里](http://www.eguneys.com/games/pacman-unicode/index.html)获得。

以下是一些更有用的链接:

*   [Bacon.js API 引用](http://baconjs.github.io/api.html)
*   [培根视频简介. js](https://www.youtube.com/watch?v=XE692Clb5LU)
*   [RxJS 网站](https://rxjs.dev/guide/overview)
*   [Highland.js 高级流库](http://highlandjs.org)
*   Bodil Stokke 为敏锐的飞行员设计的反应式游戏程序

## 分享这篇文章