# JavaScript 中的原型

> 原文：<https://www.sitepoint.com/prototypes-in-javascript/>

JavaScript 有原型，原型很怪异。事实上，这很奇怪，以至于一些编译成 JavaScript 的语言(比如 CoffeeScript)试图掩盖这一事实，试图呈现一个更健康、更容易理解的包。然而，一旦你学会使用原型，它们会成为你武器库中一个非常有用的工具。

## 无阶级社会

所以你在你的工作室里，制作你的热门游戏系列中的最新游戏，“谋杀的召唤。”你已经做了大概八个了，所以你决定改变一下，用 JavaScript 编写整个代码。很简单，对吧？但是有一个小问题——JavaScript 说它是面向对象的，但是你不知道如何定义一个类。事实证明，不能用类！至少不是传统意义上的。但肯定有对象。

## 你的第一个对象

```
soldier = new Object()
```

前面的代码创建了一个对象，并将其命名为`soldier`。你想让士兵做什么？跳怎么样？您可以将`A`按钮映射到您在别处定义的`jump()`函数，如下所示。

```
soldier.a = jump
```

您实际上是将士兵的`a`属性设置为一个函数。请注意，我们没有包括括号。这是因为当 JavaScript 中的函数包含括号时，它会调用该函数。如果不包括括号，它只返回对函数本身的引用。所以，如果你调用`soldier.a()`，它会执行函数。但是，如果你写没有括号的`soldier.a`，那么它将返回实际的函数。

另外，请注意，您是直接在对象上分配一个函数。到目前为止，我们注意到的是 JavaScript 的怪癖，但与原型系统没有直接关系。因此，假设您对 JavaScript 脚本编写相当有经验，您可以继续将功能分配给其他按钮，如下所示。

```
soldier.b = punch
soldier.x = reload
soldier.r = machineGun
```

## 复制粘贴地狱

现在，你想创造一个和第一个玩家几乎一模一样的第二个玩家，只不过他们用的是狙击枪而不是机关枪。有两种方法可以做到这一点。首先是重复代码，如下所示。

```
sniper = new Object()
sniper.a = jump
sniper.b = punch
sniper.x = reload
sniper.r = snipe
```

有用，但是很繁琐。特别是因为你向游戏设计师承诺，他可以在游戏中放置 100 个不同类型的角色，每个角色都有相同的 200 个动作的不同组合。这需要大量的重复代码、大量的复制粘贴和大量的潜在错误。

## 原型拯救

所以，你用一个`object()`函数重新编码你的狙击手，如下所示。我们将回到`object()`的实际实现。

```
sniper = object(soldier)
sniper.r = snipe
```

你加载你的游戏只是为了确保，并开始扮演狙击手。果然，`A`按钮让你的角色跳起来。你按下`X`按钮，它重新加载。你按下`R`按钮，它就发射狙击枪。看来狙击手继承了士兵的所有特征，然后覆盖了映射到`R`的功能。如果你用英语解释，你会说“狙击手就像一名士兵，只不过它射出的是狙击步枪。”注意英语句子和定义狙击手的代码的相似性。

## 功能盗用

让我们实施一个受伤的狙击手。他们就像一个普通的狙击手，除了当他们试图跳的时候会痛得大叫。

```
woundedSniper = object(sniper)
woundedSniper.a = function(){console.log('aaaargh my leg!')}
```

这里，我们使用了一个匿名函数，而不是之前命名的函数。这很好，但这意味着当我们定义`woundedSoldier`类时，我们必须做一些稍微不同的事情。

```
woundedSoldier = object(soldier)
woundedSoldier.a = woundedSniper.a
```

你看到了吗？你偷了另一个物体的功能！试着用常规的面向对象语言来做这件事。我谅你也不敢！(请注意:这是一个实际的挑战。如果有人完成了它，我不会感到惊讶，但如果它像这样简单或美丽，我会感到惊讶。)现在该看一下`object()`函数了。

## 在窗帘后面

```
function object(o){
  function F(){}
  F.prototype = o;
  return new F();
}
```

`object()`接受一个参数(`o`，或者我们习惯上称之为父对象)。它创建了一个虚拟的“类”函数`F()`，用一个空的构造函数完成。它将类的`prototype`属性设置为您传入的参数。然后，它返回虚拟类的一个实例。道格拉斯·克洛克福特已经[正式认可了`object(o)`方法而不是阶级制度](http://javascript.crockford.com/prototypal.html "Prototypal Inheritance")。事实上，这就是我们得到函数的地方。

## `prototype`属性

留给你的一个挥之不去的问题是，那个`prototype`属性是什么。也许在你自己的代码中会有用？如果您在创建狙击手时将`F.prototype`打印到控制台，您会得到以下输出:

```
{ a: [Function], b: [Function], x: [Function], r: [Function] }
```

您会发现这些是我们从各种按钮到功能的映射。这是意料之中的，因为我们已经将士兵对象分配给了虚拟类`F`的原型。然而，我们随后创建了一个虚拟类的实例，并将其分配给狙击手。如果你问狙击手的原型是什么，你就得到`undefined`。发生了什么事？当你按下`X`按钮时，狙击手如何知道该做什么？

更奇怪的是——当你创建`woundedSniper`时，`F`的原型只返回一个到`R`按钮的映射。但是，显然`woundedSniper`可以装弹和出拳。我们如何解释这一切？

## __proto__

令人困惑的是，有两个原型属性*。当调用构造函数时，`prototype`被赋给`__proto__`属性。`sniper.__proto__`返回以下内容:*

```
{ a: [Function], b: [Function], x: [Function], r: [Function] }
```

更有趣的是，`sniper.__proto__`有自己的`__proto__`。`sniper.__proto__.__proto__`给你`soldier`继承`Object`的功能。同时，`prototype`只是在构造函数中用来设置实际对象的`__proto__`属性。

## 结论

通过我们对`object(o)`函数的使用和对`__proto__`的发现，我们看到原型代码可以做和经典面向对象代码一样多的事情。动态定义唯一实例的能力，以及随时将函数从一个实例直接转移到另一个实例的能力，使它最终变得更加强大。

## 进一步阅读

这篇文章的部分灵感来自于[史蒂夫·耶格的博客文章/书](http://steve-yegge.blogspot.com/2008/10/universal-design-pattern.html)。阅读那篇文章，关注即将发布的关于 CoffeeScript 如何用 JavaScript 原型创建传统的类系统的文章。

## 分享这篇文章