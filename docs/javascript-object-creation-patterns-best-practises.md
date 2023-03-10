# JavaScript 对象创建:模式和最佳实践

> 原文：<https://www.sitepoint.com/javascript-object-creation-patterns-best-practises/>

在本文中，我将带您浏览 JavaScript 对象创建的各种风格，以及每种风格如何以渐进的方式建立在其他风格的基础上。

JavaScript 有多种创建对象的风格，新手和老手都会被这些选择弄得不知所措，不知道应该使用哪一种。但是，尽管种类繁多，每一种的语法看起来又有多么不同，但它们的相似之处可能比你想象的要多。

## 对象文字

我们旅程的第一站是 JavaScript 对象创建的绝对最简单的方法——对象文字。JavaScript 宣称对象可以被“无中生有”地创建——没有类，没有模板，没有原型——只是*噗！*，有方法和数据的对象:

```
var o = {
  x: 42,
  y: 3.14,
  f: function() {},
  g: function() {}
}; 
```

但是有一个缺点。如果我们需要在其他地方创建相同类型的对象，那么我们将结束复制粘贴对象的方法、数据和初始化。我们需要一种方法来创建不止一个对象，而是一系列对象。

## 工厂功能

JavaScript 对象创建之旅的下一站是工厂函数。这是创建一系列共享相同结构、接口和实现的对象的最简单的方法。我们不是直接创建一个对象文字，而是从一个函数返回一个对象文字。这样，如果我们需要多次或在多个地方创建同一类型的对象，我们只需要调用一个函数:

```
function thing() {
  return {
    x: 42,
    y: 3.14,
    f: function() {},
    g: function() {}
  };
}

var o = thing(); 
```

但是有一个缺点。这种创建 JavaScript 对象的方法会导致内存膨胀，因为每个对象都包含每个函数的唯一副本。理想情况下，我们希望每个对象*只共享*其函数的一个副本。

## 原型链

JavaScript 为我们提供了一种跨对象共享数据的内置机制，称为**原型链**。当我们访问一个对象的属性时，它可以通过委托给其他对象来完成这个请求。我们可以使用它并更改我们的工厂函数，使它创建的每个对象只包含该特定对象独有的数据，并将所有其他属性请求委托给单个共享对象:

```
var thingPrototype = {
  f: function() {},
  g: function() {}
};

function thing() {
  var o = Object.create(thingPrototype);

  o.x = 42;
  o.y = 3.14;

  return o;
}

var o = thing(); 
```

事实上，这是一种非常常见的模式，该语言已经内置了对它的支持。我们不需要创建自己的共享对象(原型对象)。相反，在每个函数旁边会自动为我们创建一个原型对象，我们可以将共享数据放在那里:

```
thing.prototype.f = function() {};
thing.prototype.g = function() {};

function thing() {
  var o = Object.create(thing.prototype);

  o.x = 42;
  o.y = 3.14;

  return o;
}

var o = thing(); 
```

但是有一个缺点。这将导致一些重复。在每一个这样的委托原型工厂函数中，`thing`函数的第一行和最后一行将几乎一字不差地重复。

## ES5 Classes

我们可以通过将重复的行移动到它们自己的功能中来隔离它们。该函数将创建一个对象，该对象委托给其他任意函数的原型，然后使用新创建的对象作为参数调用该函数，最后返回该对象:

```
function create(fn) {
  var o = Object.create(fn.prototype);

  fn.call(o);

  return o;
}

// ...

Thing.prototype.f = function() {};
Thing.prototype.g = function() {};

function Thing() {
  this.x = 42;
  this.y = 3.14;
}

var o = create(Thing); 
```

事实上，这也是一种常见的模式，以至于该语言对它有一些内置的支持。我们定义的`create`函数实际上是`new`关键字的基本版本，我们可以直接用`new`替换`create`:

```
Thing.prototype.f = function() {};
Thing.prototype.g = function() {};

function Thing() {
  this.x = 42;
  this.y = 3.14;
}

var o = new Thing(); 
```

我们现在已经到达了我们通常所说的“ES5 类”。它们是对象创建函数，将共享数据委托给原型对象，并依靠`new`关键字来处理重复的逻辑。

但是有一个缺点。又啰嗦又难看，实现继承更啰嗦又难看。

## ES6 Classes

JavaScript 的一个相对较新的补充是 ES6 类，它为做同样的事情提供了明显更干净的语法:

```
class Thing {
  constructor() {
    this.x = 42;
    this.y = 3.14;
  }

  f() {}
  g() {}
}

const o = new Thing(); 
```

## 比较

多年来，我们 JavaScripters 人员与原型链有着分分合合的关系，今天您可能会遇到的两种最常见的样式是类语法，它严重依赖于原型链，以及工厂函数语法，它通常根本不依赖于原型链。这两种风格在性能和功能上略有不同。

### 表演

如今，JavaScript 引擎被大量优化，以至于几乎不可能查看我们的代码并推断什么会更快。衡量至关重要。然而，有时甚至测量也会让我们失望。通常情况下，每六周发布一次更新的 JavaScript 引擎，有时性能会有重大变化，我们以前采取的任何措施，以及我们基于这些措施做出的任何决定，都会被抛弃。因此，我的经验法则是倾向于最官方和最广泛使用的语法，假设它将接受最多的审查，并且在大多数时候是最有性能的。现在，这就是类的语法，在我写这篇文章的时候，类的语法大约比工厂函数返回文字快 3 倍。

### 特征

随着 ES6 的出现，类和工厂函数之间仅有的一些特性差异消失了。今天，工厂函数和类都可以实现真正的私有数据——工厂函数使用闭包，类使用弱映射。两者都可以通过将其他属性混合到它们自己的对象中来实现多重继承工厂功能，类也可以通过将其他属性混合到它们的原型、类工厂或代理中来实现多重继承工厂功能。如果需要，工厂函数和类都可以返回任意对象。并且都提供了简单的语法。

## 结论

总的来说，我更喜欢使用类语法来创建 JavaScript 对象。它是标准的，简单、干净、快速，并且提供了从前只有工厂才能提供的所有功能。

*这篇文章由[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien/)和[塞巴斯蒂安·塞茨](https://www.sitepoint.com/community/users/m3g4p0p/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章