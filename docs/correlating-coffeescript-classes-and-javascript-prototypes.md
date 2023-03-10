# 关联 CoffeeScript 类和 JavaScript 原型

> 原文：<https://www.sitepoint.com/correlating-coffeescript-classes-and-javascript-prototypes/>

CoffeeScript 有类，但是既然 CoffeeScript 只是 JavaScript，那么那些类是从哪里来的呢？在本文中，我们将分解从 CoffeeScript 类及其子类中输出的 JavaScript 代码，以了解这种神奇是如何发生的。

## 警告:前面有 JavaScript

本文涉及一些非常高级的 JavaScript。我们没有时间详细解释每个结构。它还假设您已经阅读了我之前关于[原型](https://www.sitepoint.com/prototypes-in-javascript/ "Prototypes in JavaScript")的文章，并且理解 CoffeeScript 类。当然，你可以现在就停止阅读，继续无知地写代码，就像你可以不了解自己的胃而吃东西一样。但实际上，你应该留下来，了解你正在使用的东西的内部结构。

## 取消分配

以下面的 CoffeeScript 为例:

```
class Bourgeoisie
  constructor: (@age, @privilegeConstant) ->
```

前面的代码转换成了这个 JavaScript:

```
var Bourgeoisie;
Bourgeoisie = (function() {
  function Bourgeoisie(age, privilegeConstant) {
    this.age = age;
    this.privilegeConstant = privilegeConstant;
  }
  return Bourgeoisie;
})();
```

最外面的变量`Bourgeoisie`被赋予一个[生命](http://benalman.com/news/2010/11/immediately-invoked-function-expression/#iife "Ben Alman - Immediately-Invoked Function Expression (IIFE)")，它本质上是一个用于控制作用域的构造。生活的模式如下所示。

```
(function(){
  //lots of code
  return result
})();
```

只有被归还的东西才能到达外面的世界。在这种情况下，返回的是一个内部的`Bourgeoisie`构造函数。构造函数将属性附加到正在构造的实例上。当它被返回时，构造函数被赋值给 `Bourgeoisie`外的*变量。接下来，我们添加以下函数。*

```
class Bourgeoisie
  constructor: (@age, @privilegeConstant) ->

  worry: ->
    console.log("My stocks are down 1%!")

  profit: (hardWork, luck) ->
    return (@age - 23) * hardWork * (luck + @privilegeConstant)
```

这转化为下面的 JavaScript。

```
var Bourgeoisie;
Bourgeoisie = (function() {
  function Bourgeoisie(age, privilegeConstant) {
    this.age = age;
    this.privilegeConstant = privilegeConstant;
  }
  Bourgeoisie.prototype.worry = function() {
    return console.log("My stocks are down 1%!");
  };
  Bourgeoisie.prototype.profit = function(hardWork, luck) {
    return (this.age - 23) * hardWork * (luck + this.privilegeConstant);
  };
  return Bourgeoisie;
})();
```

注意，我们使用构造函数的`prototype`属性来添加更多的函数。这样做是将函数放入每个实例的`__proto__`属性中，以便可以随意使用。因此，当我们创建一个`Bourgeoisie`的新实例时，`age`和`privilegeConstant`变量被放在实例上，而`worry()`和`profit()`函数被放在实例的原型上。使用这个例子作为父类，让我们探索继承。

## 遗产

以下面的`Senator`类为例，它继承自`Bourgeoisie`。注意，`Bourgeoisie`的代码没有包括在内，因为它没有改变。

```
class Senator extends Bourgeoisie
  worry: ->
    console.log("My polls are down 1%!")
```

现在，让我们看看这个简单的类在 JavaScript 中是什么样子的。

```
var Senator,
  __hasProp = {}.hasOwnProperty,
  __extends = function(child, parent) {
    for (var key in parent) {
      if (__hasProp.call(parent, key)) {
        child[key] = parent[key];
      }
    }
    function ctor() { this.constructor = child; }
    ctor.prototype = parent.prototype;
    child.prototype = new ctor();
    child.__super__ = parent.prototype;
    return child;
  };

Senator = (function(_super) {
  __extends(Senator, _super);
  function Senator() {
    return Senator.__super__.constructor.apply(this, arguments);
  }

  Senator.prototype.worry = function() {
    return console.log("My polls are down 1%!");
  };

  return Senator;

})(Bourgeoisie);
```

天啊。让我们一步一步来。下面的代码声明了`Senator`变量，并创建了一个`hasOwnProperty()`方法的快捷方式。

```
var Senator,
  __hasProp = {}.hasOwnProperty,
```

下一段代码启动了`__extends()`函数。第一部分手动复制父对象的每个属性，并将其放在子对象上。记住指向函数的指针只是变量，所以函数也是以这种方式传输的。

```
__extends = function(child, parent) {
  for (var key in parent) {
    if (__hasProp.call(parent, key)) {
      child[key] = parent[key];
    }
  }
...
```

下一个片段更难解析。首先，我们创建一个名为`ctor()`的函数，它最初只包含一个构造函数。然后，我们将该构造函数的`prototype`赋给`parent`，并将子函数的`prototype`赋给构造函数的一个新实例。

```
...
function ctor() { this.constructor = child; }
ctor.prototype = parent.prototype;
child.prototype = new ctor();
	...
```

咻！这对我们有什么好处？嗯，构造函数的原型充当父类，这意味着实例将有一个包含父类所有属性的`__proto__`属性。如果你理解了我第一次解释原型时的讨论，这并不太复杂。令人困惑的部分是原型和构造函数看似无限的回归。

你看，`ctor()`有一个构造函数属性`child`，它有一个新的实例`ctor()`作为它的原型。这给了我们`child.prototype.constructor = child`。如果你在 Chrome 开发工具中检查这一点，你会得到一个无限的回归。幸运的是，这似乎不会影响性能，但它仍然是一个令人困惑的架构。

谢天谢地，最后一部分(如下所示)简单多了。`child`被赋予一个属性`__super__`，该属性被赋予父级的`prototype`。这是我们的原型继承的实现不容易复制的，当你想在子节点上定义一个新的函数，但仍然引用父节点的函数时，这将是非常有用的。我们将在`Senator`的代码中看到这一点。

```
...
  child.__super__ = parent.prototype;
  return child;
};
```

最后，我们返回`child`。需要澄清的是，这是`child`的类定义(或原型)，而不是一个特定的实例。我们刚刚讨论的代码被创建一次，然后用于每个继承。

## 参议员的遗产

下面一段代码是特定于`Senator`的继承的。请注意，生命结构已被修改，以接受一个参数。传入的参数是`Bourgeoisie`，在生命中被称为`_super`。同样，返回的`Senator`被分配给生命外部的`Senator`。

```
Senator = (function(_super) {
  __extends(Senator, _super);

  function Senator() {
    return Senator.__super__.constructor.apply(this, arguments);
  }

  Senator.prototype.worry = function() {
    return console.log("My polls are down 1%!");
  };

  return Senator;

})(Bourgeoisie);
```

我们在代码块中做的第一件事是调用`__extends()`，它将`Senator`(孩子)和`_super`(父母)作为参数。这里以通常的方式定义了`worry()`函数，覆盖了父版本。`profit()`功能在`Bourgeoisie`上，因此通过`__proto__`继承。更有趣的是构造函数，我们现在将讨论它。

## 构建新实例

`Senator`的构造函数如下所示。

```
function Senator() {
  return Senator.__super__.constructor.apply(this, arguments);
}
```

为了使这一点更容易理解，考虑以下功能等效的语句。这段代码只是使用传入的参数调用父原型上的构造函数。CoffeeScript 创建的第一个定义做了同样的事情，但是使用了大量的参数。

```
function Senator(age, privilegeConstant){
  return Senator.__super__.constructor(age, privilegeConstant);
}
```

JavaScript 中的`arguments`变量将传递给函数的所有参数放在一个类似 object 的数组中，即使它们没有在函数定义中明确命名。我们使用的另一个 JavaScript 技巧是`apply()`函数。`apply()`允许你指定一个函数的参数，以及`this`的值。总之，我们接受任意数量的参数，并将它们全部传递给父原型的构造函数。为了传递任意数量的参数，我们使用了`apply()`函数。

## 结论

我们已经通过研究生成的 JavaScript 代码看到了 CoffeeScript 类是如何创建和扩展的。我们还讨论了类的所有基本特性。请注意，JavaScript 的下一个正式版本将包含自己的类实现。它们将以类似于 CoffeeScript 类编译成原型的方式编译成原型。敬请关注。

## 分享这篇文章