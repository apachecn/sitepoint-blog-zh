# 快速提示:JavaScript 中的工厂函数是什么

> 原文：<https://www.sitepoint.com/factory-functions-javascript/>

如果不学习函数和对象，你就无法成为一名 JavaScript 程序员，当一起使用时，它们是我们开始一个强大的对象范例所需的构建块，这个范例叫做[组合](https://en.wikipedia.org/wiki/Composition_over_inheritance)。今天我们将看看使用工厂函数来组合函数、对象和承诺的一些惯用模式。

当一个函数返回一个对象时，我们称之为*工厂函数*。

让我们看一个简单的例子。

```
function createJelly() {
  return {
    type: 'jelly',
    colour: 'red'
    scoops: 3
  };
} 
```

每次我们调用这个工厂，它都会返回 jelly 对象的一个新实例。

值得注意的是，我们不必在工厂名前加上前缀`create`，但这可以让其他人更清楚地理解函数的意图。属性也是如此，但它通常可以帮助我们区分流经程序的对象。

## 参数化工厂函数

像所有函数一样，我们可以用改变返回对象形状的参数来定义我们的工厂。

```
function createIceCream(flavour='Vanilla') {
  return {
    type: 'icecream',
    scoops: 3,
    flavour
  }
} 
```

理论上，您可以使用带有数百个参数的参数化工厂来返回非常具体且深度嵌套的对象，但是正如我们将看到的，这完全不符合组合的精神。

## 可组合的工厂函数

根据一个工厂定义另一个工厂有助于我们将复杂的工厂分解成更小的、可重用的片段。

例如，我们可以创建一个甜点工厂，它是根据以前的果冻和冰淇淋工厂定义的。

```
function createDessert() {
  return {
    type: 'dessert',
    bowl: [
      createJelly(),
      createIceCream()
    ]
  };
} 
```

我们可以组成工厂来构建任意复杂的对象，而不需要我们去摆弄[新的](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/new)或[这个](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/this)。

可以用*来表达的对象有-a* 关系，而不是*是-a* 可以用复合来实现，而不是继承。

例如，继承。

```
// A trifle *is a* dessert

function Trifle() {
  Dessert.apply(this, arguments);
}

Trifle.prototype = Dessert.prototype;

// or

class Trifle extends Dessert {
  constructor() {
    super();
  }
} 
```

我们可以用构图来表达同样的想法。

```
// A trifle *has* layers of jelly, custard and cream. It also *has a* topping.

function createTrifle() {
  return {
    type: 'trifle',
    layers: [
      createJelly(),
      createCustard(),
      createCream()
    ],
    topping: createAlmonds()
  };
} 
```

## 异步工厂函数

不是所有的工厂都准备好立即返回数据。例如，有些人必须先获取数据。

在这些情况下，我们可以定义返回承诺的工厂。

```
function getMeal(menuUrl) {
  return new Promise((resolve, reject) => {
    fetch(menuUrl)
      .then(result => {
        resolve({
          type: 'meal',
          courses: result.json()
        });
      })
      .catch(reject);
  });
} 
```

这种深度嵌套的缩进会使异步工厂难以阅读和测试。将它们分解成多个不同的工厂，然后将它们组合起来通常会很有帮助。

```
function getMeal(menuUrl) {
  return fetch(menuUrl)
    .then(result => result.json())
    .then(json => createMeal(json));
}

function createMeal(courses=[]) {
  return {
    type: 'meal',
    courses
  };
} 
```

当然，我们也可以使用回调来代替，但是我们已经有了像`Promise.all`这样的工具来组合返回承诺的工厂。

```
function getWeeksMeals() {
  const menuUrl = 'jsfood.com/';

  return Promise.all([
    getMeal(`${menuUrl}/monday`),
    getMeal(`${menuUrl}/tuesday`),
    getMeal(`${menuUrl}/wednesday`),
    getMeal(`${menuUrl}/thursday`),
    getMeal(`${menuUrl}/friday`)
  ]);
} 
```

我们使用`get`而不是`create`作为命名约定，以表明这些工厂做一些异步工作并返回承诺。

## 功能和方法

到目前为止，我们还没有看到任何用方法返回对象的工厂，这是故意的。这是因为一般来说，*我们不需要*。

工厂允许我们把数据和计算分开。

这意味着我们将始终能够将我们的对象序列化为 JSON，这对于在会话之间持久化它们、通过 HTTP 或 WebSockets 发送它们以及将它们放入数据存储中是很重要的。

例如，我们可以不在 jelly 对象上定义 eat 方法，而是定义一个新的函数，它将一个对象作为参数并返回一个修改后的版本。

```
function eatJelly(jelly) {
  if(jelly.scoops > 0) {
    jelly.scoops -= 1;
  }
  return jelly;
} 
```

对于那些喜欢不改变数据结构而编程的人来说,[一点语法帮助](https://babeljs.io/docs/plugins/transform-object-rest-spread/)使这成为一种可行的模式。

```
function eat(jelly) {
  if(jelly.scoops > 0) {
    return { ...jelly, scoops: jelly.scoops - 1 };
  } else {
    return jelly;
  }
} 
```

现在，与其写:

```
import { createJelly } from './jelly';

createJelly().eat(); 
```

我们会写:

```
import { createJelly, eatJelly } from './jelly';

eatJelly(createJelly()); 
```

最终结果是一个函数，它接受一个对象并返回一个对象。

我们把返回一个对象函数叫做什么？一个工厂！

## 高级工厂

将工厂作为高阶函数传递给我们大量的控制权。例如，我们可以用这个概念来创造*增强子*。

```
function giveTimestamp(factory) {
  return (...args) => {
    const instance = factory(...args);
    const time = Date.now();
    return { time, instance };
  };
}

const createOrder = giveTimestamp(function(ingredients) {
  return {
    type: 'order',
    ingredients
  };
}); 
```

这个增强器采用一个现有的工厂并包装它来创建一个返回带有时间戳的实例的工厂。

或者，如果我们想确保工厂返回不可变的对象，我们可以用一个*冻结器*来增强它。

```
function freezer(factory) {
  return (...args) => Object.freeze(factory(...args)));
}

const createImmutableIceCream = freezer(createIceCream);

createImmutableIceCream('strawberry').flavour = 'mint'; // Error! 
```

## 结论

正如一位[明智的程序员](https://www.youtube.com/watch?v=4anAwXYqLG8)曾经说过的:

> 从无抽象中恢复比从错误的抽象中恢复要容易得多。

JavaScript 项目有变得难以测试和重构的趋势，因为我们经常被鼓励去构建复杂的抽象层。

原型和类用复杂且不自然的工具实现了一个简单的想法，比如`new`和`this`，即使在它们被添加到语言中多年后的今天，这些工具仍然会引起[各种各样的困惑](http://stackoverflow.com/questions/tagged/javascript%20this?mode=all)。

对象和函数对大多数背景的程序员都有意义，并且都是 JavaScript 中的原始类型，所以可以说工厂根本不是抽象！

使用这些简单的构建模块使我们的代码对没有经验的程序员更友好，这绝对是我们都应该关心的事情。工厂鼓励我们用具有自然组合能力的原语对复杂和异步的数据建模，也不强迫我们达到高级抽象。当我们坚持简单的时候，JavaScript 更可爱！

## 分享这篇文章