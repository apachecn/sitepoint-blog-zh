# CoffeeScript 中的列表处理

> 原文：<https://www.sitepoint.com/list-processing-in-coffeescript/>

所以你正在编写你的 CoffeeScript，你有一个需要处理的列表。在 JavaScript 中，有很多方法可以让你的生活变得更简单，但是 CoffeeScript 给了你简洁和一致的 T2 语法的优势。与循环、jQuery `forEach()`调用和对(优秀的)下划线库的调用的混搭不同，您可以以简洁易读的方式完成所有工作。

如果你从中间开始，你会认为你在读一个 Python 教程。事实上，如果您是 Python 老手，您可以跳过这篇文章，开始猜测，可能对您自己也有好处。但是，对于我们其他人来说，这里有一个我最喜欢的在 ~~Python~~ CoffeeScript 中处理列表的三个技巧的纲要。

## 切片和切块

假设你正在创建一个混合的赛车/购物游戏，Cario Mart。在游戏过程中，你让人们买蘑菇和贝壳用在他们的车上(不要问)。为了让游戏更有趣和更有竞争力，你想让第六、第七和第八名的玩家(输的玩家)的东西更便宜。在 CoffeeScript 中有一种方法可以做到这一点，假设玩家的列表是根据他们所处的位置排序的:

```
if player == players[5] or player == players[6] or player == players[7]
  blueShroom.cheaperFor(player)
```

本质上，你是在比较有问题的球员和排名最后的三名球员。然而，你在重复你自己。这里有一个稍微好一点的实现，只提到一次有问题的玩家:

```
if player in [players[5], players[6], players[7]]
  blueShroom.cheaperFor(player)
```

但是这里有一个充分利用 CoffeeScript 语法的实现:

```
if player in players[5..7]
  blueShroom.cheaperFor(player)
```

现在，如果你想改变任何事情(你比较的是哪个球员，你用的是哪个球员名单，你想影响哪些地方)，你只需要在一个地方改变一件事。最后一个优化——假设你的游戏突然扩展到 9 个人，而不是 8 个人。目前，第 6-8 名的玩家将受益，而可怜的第 9 名玩家将遭殃。在我们继续之前，让我们解决这个问题，使任何处于第 6 位和较低福利的人:

```
if player in players[5..]
  blueShroom.cheaperFor(player)
```

## 列出理解

所以你在第 7 位，你刚买了一个蓝色蘑菇。现在你想用它。在这种情况下，一个蓝色蘑菇进入其他人的油箱，使他们的汽车随机燃烧。哎哟！那会让他们落后几秒钟。你现在需要从你的代码中找到一种攻击除了你自己以外的所有人的方法。

这个版本只利用了 CoffeeScript 的语法，以关键字`unless`和`is`的形式，并在语句末尾加载了条件，但在列表方面做得不多。

```
players.forEach (target) ->
  blueShroom.poison(target) unless target is buyer
```

您还会注意到第二行是一个匿名函数。CoffeeScript 几乎会让你忘记这一点。这里有一个版本使代码更加模糊，更接近英语。

```
for target in players
  blueShroom.poison target unless target is buyer
```

这里有一个使用列表理解的例子:

```
blueShroom.poison target for target in players unless target is buyer
```

哇，这是迄今为止最像英语的代码！如果你不确定发生了什么，回头看看第一个版本，它看起来像代码。这里有一个使用了更多的 CoffeeScript 关键字，在英语中稍微流畅一些:

```
blueShroom.poison target for target in players when target isnt buyer
```

注意，这个语法不适用于前两个版本，只有第三个版本使用了列表理解。

尽管这很棒，但列出理解有两个陷阱:

1.  List comprehensions 返回一个列表(在本例中，是中毒目标的列表)。如果你想“堆叠”清单理解，这是很好的，但是如果你堆叠了两个以上，它会很快变得混乱。如果去掉可选的圆括号和方括号，情况尤其如此。
2.  因为这看起来很像英语，所以有时你可能会写一个有效的英语句子，却惊讶于它不能编译。尽管 CoffeeScript 尽了很大努力让你的代码像英语一样，但如果你错误地认为它们是一样的，你将很快被带回现实。

## 泼溅

多么有趣的词。这和我们的情况也很相关。你看，我们正试图让我们的 Cario Mart 游戏在网络上运行，我们希望有一种方法可以方便地向多个玩家发送消息。这里有一个我们编写的函数来帮助我们:

```
tellPlayers = (message) ->
  player.socket.emit(message) for player in players
```

一些需要注意的事情。首先，很好地使用了列表理解！这些东西的用途将会出现在数量惊人的地方。第二，我们在这里做的是在 WebSocket 上向每个连接的玩家发送消息。你不必担心这里到底发生了什么，或者 T2 为什么我们要这样做，但最基本的是每个游戏有八个客户端，都连接到一个中央服务器。这段代码在服务器上，并向游戏的所有玩家(客户端)发送消息。

如果信息很简单，比如`gameEnded`，这种方法非常有效。但是如果当游戏结束时，我们想把赢家的名字发给每个人呢？我们可以这样写:

```
tellPlayers = (message, argument) ->
  player.socket.emit(message, argument) for player in players
```

这现在可以了，但是如果我们在有人被武器击中时(比如一个蓝色的小房间)发送一条‘啪’的信息会怎么样呢？在这里，我们想知道武器，打中的玩家，发出攻击的玩家。这是三个论点！解决这个问题的方法是通过 splats。

```
tellPlayers = (message, arguments...) ->
  player.socket.emit(message, arguments...) for player in players
```

当你把三个点放在后面，这意味着论点实际上是一个论点列表。可以有 1、3、0 或任意数量的参数。这就像你把列表和所有的个人论点扔在墙上。

这是另一个例子。我们通过套接字将玩家名单发送给每个客户，名单按种族排序。服务器端代码将如下所示:

```
tellPlayers('listOfPlayers', players...)
```

这调用了我们之前的函数。在客户端，我们有以下内容:

```
socket.on 'listOfPlayers', (first, second, third, rest....) ->
  celebrate(first)
  celebrate(second)
  celebrate(third)
  hands.clap() for hands in rest
```

使用 splat 通过套接字传递整个玩家列表，然后当收到列表时，它挑选出前三个进行庆祝，而`rest...` splat 吸收其余的。

重要的是，当你调用`tellPlayers`时，你使用 splat 来表示`players`。否则，它会将整个列表作为第一个参数，导致客户端庆祝所有八个玩家，然后庆祝两个`null`值，然后没有人鼓掌。我们不能这样！

## 你的选择

本文向您介绍了 CoffeeScript 提供的三种最流行的列表处理技术。在 CoffeeScript 中，还有许多其他的小快捷方式和语法炸弹在等着你。访问[主页](http://CoffeeScript.org)并试用它们！

此外，SitePoint 刚刚发布了其 Jump Start 系列的最新产品: [Jump Start: CoffeeScript](https://www.sitepoint.com/books/coffeescript1/) 。了解如何使用 CoffeeScript 制作一个令人敬畏的充满忍者的 HTML5 2D 平台…全部在一个周末！

最后，在 [Learnable](https://learnable.com/jumpcasts/get-started-with-coffeescript-56) 节目中，Paul Annesley 将带你浏览 CoffeeScript 上的一小段视频。令人兴奋的饮料！

## 分享这篇文章