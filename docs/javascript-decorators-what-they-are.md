# JavaScript 装饰器:它们是什么以及何时使用它们

> 原文：<https://www.sitepoint.com/javascript-decorators-what-they-are/>

随着 ES2015+的推出，随着 transpilation 变得越来越普遍，你们中的许多人将会在真实代码或教程中遇到更新的语言功能。JavaScript decorators 是第一次遇到这些特性时经常让人摸不着头脑的特性之一。

由于在 Angular 2+中的使用，装饰者变得流行起来。在 Angular 中，由于 TypeScript，decorators 是可用的，但在 JavaScript 中，它们目前是第二阶段的提案，这意味着它们应该是该语言未来更新的一部分。让我们来看看什么是装饰者，以及如何使用它们来使你的代码更干净、更容易理解。

![](img/5a17fb4b831de40970db23bd54c1e19f.png)

## 什么是室内设计师？

最简单的形式是，装饰器只是用一段代码包装另一段代码的一种方式——字面意思是“装饰”它。这是一个你以前可能听说过的概念，叫做**函数合成**，或者**高阶函数**。

在许多用例中，这在标准 JavaScript 中已经是可能的，只需调用一个函数来包装另一个函数:

```
function doSomething(name) {
  console.log('Hello, ' + name);
}

function loggingDecorator(wrapped) {
  return function() {
    console.log('Starting');
    const result = wrapped.apply(this, arguments);
    console.log('Finished');
    return result;
  }
}

const wrapped = loggingDecorator(doSomething); 
```

这个例子在变量`wrapped`中产生了一个新函数，它可以以与`doSomething`函数完全相同的方式被调用，并将做完全相同的事情。不同之处在于，它会在调用包装的函数之前和之后进行一些日志记录:

```
doSomething('Graham');
// Hello, Graham

wrapped('Graham');
// Starting
// Hello, Graham
// Finished 
```

## 如何使用 JavaScript 装饰器

装饰者在 JavaScript 中使用了一种特殊的语法，它们以一个`@`符号为前缀，直接放在被装饰的代码之前。

*注:在撰写本文时，装饰者目前处于“[第二阶段草案](https://tc39.github.io/proposal-decorators/)”的形式，这意味着他们大部分已经完成，但仍有可能发生变化。*

你可以在同一段代码上使用任意多的装饰器，它们将按照你声明它们的顺序被应用。

例如:

```
@log()
@immutable()
class Example {
  @time('demo')
  doSomething() {
    //
  }
} 
```

这定义了一个类并应用了三个装饰器——两个应用于类本身，一个应用于类的属性:

*   `@log`可以记录所有对该类的访问
*   `@immutable`可以使类不可变——也许它在新实例上调用`Object.freeze`
*   `@time`将记录一个方法执行需要多长时间，并用一个独特的标签记录下来。

目前，使用 decorators 需要 transpiler 支持，因为目前还没有浏览器或节点版本支持它们。如果你使用的是 Babel，这可以通过使用[transform-decorators-legacy plugin](https://github.com/loganfsmyth/babel-plugin-transform-decorators-legacy)来实现。

注意:在这个插件中使用单词“legacy”是因为它支持处理装饰者的 Babel 5 方式，当它们被标准化时，这很可能与最终的形式不同。

## 为什么要用装修工？

虽然函数式组合在 JavaScript 中已经是可能的，但是将相同的技术应用到其他代码片段(例如类和类属性)中要困难得多，甚至是不可能的。

decorator 提案增加了对类和属性装饰器的支持，可以用来解决这些问题，未来的 JavaScript 版本可能会增加对其他麻烦的代码区域的装饰器支持。

Decorators 还允许在代码周围应用这些包装器时使用更干净的语法，从而减少对您所写内容的实际意图的影响。

## 不同类型的装饰工

目前，只支持类和类成员上的装饰器类型。这包括属性、方法、getters 和 setters。

装饰器实际上只不过是返回另一个函数的函数，并且是用被装饰项目的适当细节来调用的。当程序第一次运行时，这些修饰函数被评估一次，被修饰的代码被替换为返回值。

### 类成员装饰者

属性装饰器应用于类中的单个成员，无论它们是属性、方法、getters 还是 setters。这个装饰函数是用三个参数调用的:

*   `target`:成员所在的类。
*   `name`:类中成员的名字。
*   `descriptor`:成员描述符。这基本上是已经传递给 [Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 的对象。

这里用的经典例子是`@readonly`。这实现起来非常简单:

```
function readonly(target, name, descriptor) {
  descriptor.writable = false;
  return descriptor;
} 
```

逐字更新属性描述符以将“可写”标志设置为假。

然后将它用于类属性，如下所示:

```
class Example {
  a() {}
  @readonly
  b() {}
}

const e = new Example();
e.a = 1;
e.b = 2;
// TypeError: Cannot assign to read only property 'b' of object '#<Example>' 
```

但是我们可以做得更好。我们实际上可以用不同的行为替换修饰函数。例如，让我们记录所有的输入和输出:

```
function log(target, name, descriptor) {
  const original = descriptor.value;
  if (typeof original === 'function') {
    descriptor.value = function(...args) {
      console.log(`Arguments: ${args}`);
      try {
        const result = original.apply(this, args);
        console.log(`Result: ${result}`);
        return result;
      } catch (e) {
        console.log(`Error: ${e}`);
        throw e;
      }
    }
  }
  return descriptor;
} 
```

这将整个方法替换为一个记录参数、调用原始方法然后记录输出的新方法。

注意，我们在这里使用了[扩展操作符](https://www.sitepoint.com/es6-destructuring-assignment/)来从提供的所有参数中自动构建一个数组，这是旧的`arguments`值的更现代的替代方法。

我们可以看到这种用法如下:

```
class Example {
  @log
  sum(a, b) {
    return a + b;
  }
}

const e = new Example();
e.sum(1, 2);
// Arguments: 1,2
// Result: 3 
```

您会注意到，我们不得不使用一种稍微有趣的语法来执行修饰的方法。这可能涵盖了它自己的一整篇文章，但简而言之，`apply`函数允许您调用函数，指定`this`值和调用它的参数。

更进一步，我们可以安排我们的装潢师进行一些辩论。例如，让我们重新编写我们的`log`装饰器如下:

```
function log(name) {
  return function decorator(t, n, descriptor) {
    const original = descriptor.value;
    if (typeof original === 'function') {
      descriptor.value = function(...args) {
        console.log(`Arguments for ${name}: ${args}`);
        try {
          const result = original.apply(this, args);
          console.log(`Result from ${name}: ${result}`);
          return result;
        } catch (e) {
          console.log(`Error from ${name}: ${e}`);
          throw e;
        }
      }
    }
    return descriptor;
  };
} 
```

现在这变得越来越复杂，但是当我们把它分解开来，我们就有了这个:

*   采用单个参数的函数`log`:`name`。
*   然后这个函数返回一个函数，这个函数本身就是一个装饰器。

这与前面的`log`装饰器相同，只是它使用了外部函数中的`name`参数。

然后按如下方式使用:

```
class Example {
  @log('some tag')
  sum(a, b) {
    return a + b;
  }
}

const e = new Example();
e.sum(1, 2);
// Arguments for some tag: 1,2
// Result from some tag: 3 
```

我们马上可以看到，这允许我们使用自己提供的标记来区分不同的日志行。

这是可行的，因为 JavaScript 运行时会直接评估`log('some tag')`函数调用，然后来自它的响应被用作`sum`方法的装饰器。

### 班级装饰者

类装饰器一次性应用于整个类定义。decorator 函数是用一个参数调用的，这个参数是被修饰的构造函数。

请注意，这适用于构造函数，而不是所创建的类的每个实例。这意味着，如果您想要操作实例，您需要通过返回构造函数的包装版本来自己操作。

一般来说，这些没有类成员装饰器有用，因为你在这里可以做的一切都可以用一个简单的函数调用以完全相同的方式来完成。你做的任何事情都需要返回一个新的构造函数来代替类构造函数。

回到我们的日志记录示例，让我们编写一个记录构造函数参数的示例:

```
function log(Class) {
  return (...args) => {
    console.log(args);
    return new Class(...args);
  };
} 
```

这里我们接受一个类作为我们的参数，并返回一个新的函数作为构造函数。这只是记录参数，并返回用这些参数构造的类的新实例。

例如:

```
@log
class Example {
  constructor(name, age) {
  }
}

const e = new Example('Graham', 34);
// [ 'Graham', 34 ]
console.log(e);
// Example {} 
```

我们可以看到，构造我们的示例类将注销所提供的参数，并且构造的值确实是`Example`的一个实例。正是我们想要的。

将参数传递给类装饰器的工作方式与传递给类成员完全相同:

```
function log(name) {
  return function decorator(Class) {
    return (...args) => {
      console.log(`Arguments for ${name}: args`);
      return new Class(...args);
    };
  }
}

@log('Demo')
class Example {
  constructor(name, age) {}
}

const e = new Example('Graham', 34);
// Arguments for Demo: args
console.log(e);
// Example {} 
```

## 真实世界的例子

### 核心装饰者

有一个名为 [Core Decorators](https://www.npmjs.com/package/core-decorators) 的奇妙的库，它提供了一些非常有用的公共装饰器，现在就可以使用。这些通常允许非常有用的公共功能(例如，方法调用的计时、弃用警告、确保值是只读的),但是使用了更干净的装饰语法。

### 反应

[React](https://www.sitepoint.com/getting-started-react-jsx/) 库很好地利用了高阶组件的概念。这些只是作为函数编写的反应组件，并包装另一个组件。

* * *

**购买我们的高级课程:[反应 ES6 方式](https://www.sitepoint.com/premium/courses/react-the-es6-way-2914)**

* * *

这些是用作装饰器的理想选择，因为这样做只需要很少的改动。例如， [react-redux 库](https://github.com/reactjs/react-redux)有一个函数`connect`，用于将 react 组件连接到 redux 存储。

一般来说，它的用法如下:

```
class MyReactComponent extends React.Component {}
export default connect(mapStateToProps, mapDispatchToProps)(MyReactComponent); 
```

但是，由于修饰语法的工作方式，可以用以下代码替换它，以实现完全相同的功能:

```
@connect(mapStateToProps, mapDispatchToProps)
export default class MyReactComponent extends React.Component {} 
```

### MobX

MobX 库广泛使用了 decorators，允许您轻松地将字段标记为可观察的或计算的，并将类标记为观察者。

## 摘要

类成员装饰器提供了一种很好的方式来包装类内部的代码，这种方式与您已经为独立的函数包装代码的方式非常相似。这为编写一些简单的助手代码提供了一个很好的方法，这些代码可以以一种非常简洁和易于理解的方式应用于很多地方。

使用这种设备的唯一限制是你的想象力！

## 分享这篇文章