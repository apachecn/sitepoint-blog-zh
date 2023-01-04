# 揭示 JavaScript 的“this”关键字的内部工作原理

> 原文：<https://www.sitepoint.com/inner-workings-javascripts-this-keyword/>

知道一种编程语言并不意味着你理解它或者正确地使用它。JavaScript 也是一样。尽管这是一门容易学习的语言，但对于新手来说，甚至对于经验丰富的程序员来说，都有许多陷阱。

让没有经验的开发人员困惑的一件事是`this`关键字是如何工作的。简单地说，`this`是一个引用别名——只是知道它具体引用了什么，这是棘手的部分。

本文旨在消除困惑，并提供对关键字`this`内部工作原理的深入了解。

## 那么，`this`到底是什么？

简而言之，`this`是一个特殊的标识符关键字——在每个函数的范围内自动定义——指向被执行函数的“所有者”。但是，要完全理解其棘手的本质，我们需要回答两个关键问题:

### `this`是如何创建的？

每次调用 JavaScript 函数时，都会创建一个新的对象，其中包含传递了哪些参数、如何调用函数、从哪里调用函数等信息。该对象的主要属性之一是`this`引用，它自动绑定到函数是方法的对象。

**注意:**出于好奇，这在 ECMAScript 语言规范的 [10.4.3 和其链接的章节中有详细描述。](http://www.ecma-international.org/ecma-262/5.1/#sec-10.4.3)

```
var car = {
  brand: "Nissan",
  getBrand: function(){
    console.log(this.brand);
  }
};

car.getBrand();
// output: Nissan
```

[JS 斌](https://jsbin.com/cevoqi/2/embed?js,console)


在这个例子中，`this.brand`中使用的`this`是对`car`对象的引用。所以，`this.brand`和`car.brand`是一样的。

### `this`指的是什么？

传递给所有函数的`this`值基于运行时调用函数的**上下文**。`this`的作用域不关心函数如何声明以及在哪里声明，而是关心从哪里调用它们(即上下文)。

JavaScript 代码的每一行都在一个[执行上下文](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)中运行。每次进入新的执行上下文时，`this`引用的对象都被重新定义，并保持固定，直到它被转移到不同的上下文。为了找到执行上下文(和`this`绑定),我们需要找到调用点——代码中调用函数的位置(不是声明函数的位置)。

让我们在下面的示例中演示这一点:

```
var brand = 'Nissan';
var myCar = {brand: 'Honda'};

var getBrand = function() {
  console.log(this.brand);
};

myCar.getBrand = getBrand;
myCar.getBrand();
// output: Honda

getBrand();
// output: Nissan
```

[JS 斌](https://jsbin.com/kopuri/1/embed?js,console)


尽管`myCar.getBrand()`和`getBrand()`都指向同一个函数，但是`this`的值是不同的，因为它基于调用`getBrand()`的上下文。

我们已经知道，在一个函数内，`this`被绑定到该函数是方法的对象。在第一个函数调用中，对象是`myCar`，而在第二个函数调用中，对象是`window` ( `getBrand()`与`window.getBrand()`相同)。所以，不同的语境产生不同的结果。

## 无效上下文

现在，让我们看看`this`放在不同的上下文中是指什么。

### 全球范围

所有 JavaScript 运行时都有一个唯一的对象，叫做*全局对象*。在浏览器中，全局对象是`window`对象。在 Node.js 中，它被称为`global`对象。

在全局执行上下文中(任何函数之外)，`this`指的是全局对象，无论是否处于严格模式。

### 局部范围

在函数内部，`this`的值取决于函数的调用方式。有三种主要变化:

#### `this`用于简单的函数调用

第一种变化是独立的函数调用，我们直接调用函数。

```
function simpleCall(){
  console.log(this);
}

simpleCall();
// output: the Window object
```

在这种情况下，调用不会设置`this`的值。因为代码不是在严格模式下运行，所以`this`的值必须总是一个对象，所以它默认为全局对象。

在[严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope/Strict_mode)中，当进入执行上下文时，`this`的值保持不变。如果没有定义，它就保持未定义状态，正如我们在下面的例子中看到的:

```
function simpleCall(){
  "use strict";
  console.log(this);
}

simpleCall();
// output: undefined
```

#### `this`用于一个对象的方法

我们可以将一个函数存储在一个对象的属性中，这将把它转换成一个我们可以通过该对象调用的方法。当一个函数作为一个对象的方法被调用时，它的`this`值被设置为调用该方法的对象。

```
var message = {
  content: "I'm a JavaScript Ninja!",
  showContent: function() {
    console.log(this.content);
  }
};

message.showContent();   // output: I'm a JavaScript Ninja!
```

[JS 斌](https://jsbin.com/wokabi/1/embed?js,console)


这里，`showContent()`是`message`对象的一个方法，因此`this.content`等于`message.content`。

#### `this`用于构造函数中

我们可以通过`new`操作符调用一个函数。在这种情况下，函数变成了一个构造函数——对象的工厂。与上面讨论的简单函数调用和方法调用不同，构造函数调用传递一个全新的对象作为`this`的值，并隐式返回新对象作为结果。

当一个函数被用作构造函数(带有`new`关键字)时，它的`this`值被绑定到新构造的对象。如果我们错过了`new`关键字，那么它将是一个常规函数，`this`将指向`window`对象。

```
function Message(content){
  this.content = content;
  this.showContent = function(){
    console.log(this.content);
  };
}

var message = new Message("I'm JavaScript Ninja!");

message.showContent();
// output: I'm JavaScript Ninja!
```

[JS 斌](https://jsbin.com/jebela/2/embed?js,console)


在上面的例子中，我们有一个名为`Message()`的构造函数。通过使用`new`操作符，我们创建了一个名为`message`的全新对象。我们还向构造函数传递了一个字符串，它将这个字符串设置为新对象的`content`属性。在最后一行代码中，我们看到这个字符串被成功输出，因为`this`指向新创建的对象，而不是构造函数本身。

## 如何成功操纵`this`

在这一节中，我们将研究一些控制`this`行为的内置机制。

在 JavaScript 中，所有的函数都是对象，因此它们可以有方法。所有函数都有的两个方法是 [apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 和 [call()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) 。我们可以使用这些方法将上下文更改为我们需要的任何内容，从而显式设置`this`的值。

`apply()`方法有两个参数:一个设置`this`的对象，和一个传递给函数的(可选)参数数组。

`call()`方法的工作方式与`apply()`完全相同，但是我们单独传递参数，而不是以数组的形式传递。

让我们来看看它的实际应用:

```
function warrior(speed, strength){
  console.log(
    "Warrior: " + this.kind +
    ", weapon: " + this.weapon +
    ", speed: " + speed +
    ", strength: " + strength
  );
}

var warrior1 = {
  kind: "ninja",
  weapon: "shuriken"
};

var warrior2 = {
  kind: "samurai",
  weapon: "katana"
};

warrior.call(warrior1, 9, 5);
// output: Warrior: ninja, weapon: shuriken, speed: 9, strength: 5
warrior.apply(warrior2, [6, 10]);
// output: Warrior: samurai, weapon: katana, speed: 6, strength: 10
```

[JS 斌](https://jsbin.com/sadaz/1/embed?js,console)


这里我们有一个工厂函数`warrior()`，通过使用不同的战士对象来创建不同类型的战士。因此，在那个工厂函数中，`this`将指向我们使用`call()`和/或`apply()`传入的不同对象。

在第一个函数调用中，我们使用`call()`方法将`this`设置为`warrior1`对象，并传递我们需要的其他参数，用逗号分隔。在第二个函数调用中，我们做了几乎相同的事情，但是这次我们传入了`warrior2`对象，必要的参数放在一个数组中。

除了`apply()`和`call()` ECMAScript 5 增加了 [bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 方法，这也允许我们设置当一个函数或方法被调用时，哪个特定的对象将被绑定到`this`。让我们考虑下面的例子:

```
function warrior(kind){
  console.log(
    "Warrior: " + kind +
    ". Favorite weapon: " + this.weapon +
    ". Main mission: " + this.mission
  );
}

var attributes = {
  weapon: "shuriken",
  mission: "espionage"
};

var ninja = warrior.bind(attributes, "ninja");

ninja();
// output: Warrior: ninja. Favorite weapon: shuriken. Main mission: espionage
```

[JS 斌](https://jsbin.com/wawuna/2/embed?js,console)


在这个例子中，`bind()`方法以类似的方式使用，但是与`call()`和`apply()`方法不同，`warrior.bind()`创建一个新函数(与`warrior()`具有相同的主体和范围)，而不是修改原来的`warrior()`函数。新函数的行为和旧函数一样，但是它的接收者绑定到了`attributes`对象，而旧函数保持不变。

## 摘要

所以，就这样了。这几乎是你需要知道的关于`this`关键字的一切，以便正确地和更自信地使用它。当然，在这个过程中，您可能会遇到一些棘手的问题和一些常见的问题。这些将在下一篇文章中探讨，敬请关注。

## 分享这篇文章