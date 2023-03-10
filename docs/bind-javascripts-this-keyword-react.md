# 在 React、ES6 和 ES7 中绑定 JavaScript 的 this 关键字的 6 种方法

> 原文：<https://www.sitepoint.com/bind-javascripts-this-keyword-react/>

*本文由[布鲁诺·莫塔](https://www.sitepoint.com/author/bmota)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

Javascript 的`this`关键字每天都是许多开发人员困惑的根源。与具有严格类模型的语言不同，并不总是清楚`this`将在代码中引用什么，尤其是在处理回调函数时，因为您无法控制回调函数的调用点。

对于其他一些代码来说，重新绑定您正在使用的函数的上下文是很简单的――使用`new`关键字和一些构建在`Function.prototype`上的方法。这引入了一整类令人困惑的场景，你经常会看到回调驱动的代码分散在对`.bind(this)`的调用中。

## 问题是

因为 React 使用`this`关键字来引用每个类内部的组件上下文，所以它也继承了这种混乱。您可能习惯于在 React 组件中看到这样的代码。

```
this.setState({ loading: true });

fetch('/').then(function loaded() {
  this.setState({ loading: false });
}); 
```

这段代码产生了一个`TypeError`，因为`this.setState is not a function`。这是因为当对承诺的回调被调用时，函数的内部上下文被改变了，并且`this`引用了错误的对象。让我们来看看可以防止这种情况发生的方法。

## 选项

其中一些替代方法是在 Javascript 中已经使用多年的老技术，另一些是专门针对 React 的，还有一些甚至还不能在浏览器中工作，但我们还是会探索它们。

### 1.别名是这个

这种方法比 React 存在的时间要长得多，它涉及到在组件范围的顶层创建对`this`的第二个引用。

```
var component = this;
component.setState({ loading: true });

fetch('/').then(function loaded() {
  component.setState({ loading: false });
}); 
```

这种方法是轻量级的，对于初学者来说非常容易理解(尽管可能不清楚你为什么这样做)。它给你一个视觉上的保证，你将会提到正确的上下文。

这有点像你在违背语言本身的语义，但这是一个简单的解决方案，而且效果很好。

### 2.捆绑这个

下一个选项是在运行时将正确的上下文注入回调函数。

```
this.setState({ loading: true });

fetch('/').then(function loaded() {
  this.setState({ loading: false });
}.bind(this)); 
```

Javascript 中的所有函数都有一个[绑定方法](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind)，允许您指定`this`的值。一旦函数被“绑定”，上下文就不能被覆盖，这意味着我们保证`this`将引用正确的东西。

这种方法对于其他程序员来说有点难以理解，如果您正在处理深度嵌套的异步代码，那么您会发现自己必须记住在进行过程中绑定每个函数。

### 3.反应组件方法

React 允许您在组件类上定义任意的方法，当您使用`React.createClass`创建组件时，这些方法会自动与`this`的正确上下文绑定。这允许您将回调代码移到组件上。

```
React.createClass({
  componentWillMount: function() {
    this.setState({ loading: true });

    fetch('/').then(this.loaded);
  },
  loaded: function loaded() {
    this.setState({ loading: false });
  }
}); 
```

如果您不在组件中做太多工作(您可能也不应该做太多工作),这可能是一个非常好的解决方案。).它允许你使用命名函数，简化你的代码，忘记正确的上下文。事实上，如果你试图`.bind(this)`到一个组件方法上，那么 React 会警告你正在做不必要的工作。

> bind():将组件方法绑定到组件。React 会以一种高性能的方式自动为您完成这项工作，因此您可以安全地删除此调用。

需要记住的是，这个 **[自动绑定并不适用于 ES2015 的](https://facebook.github.io/react/docs/reusable-components.html#no-autobinding)** 。如果您使用它们来声明您的组件，那么您将不得不使用其他替代方法之一。

### 4.ES2015 箭头

ES2015 规范引入了用于编写函数表达式的[箭头函数语法](https://www.sitepoint.com/javascript-arrow-functions/)。除了比常规函数表达式更简洁之外，它们还可以有隐式返回，最重要的是，它们总是使用封闭范围内的值`this`。

```
this.setState({ loading: true });

fetch('/').then(() => {
  this.setState({ loading: false });
}); 
```

不管使用多少级嵌套，箭头函数总是有正确的上下文。

不幸的是，我们已经失去了命名函数的能力。这使得调试更加困难，因为引用该函数的堆栈跟踪会将其标记为`(anonymous function)`。

如果你使用类似 [Babel](http://babeljs.io) 的编译器将 ES2015 代码转换成 ES5，那么你会发现有一些有趣的特性需要注意。

*   在某些情况下，如果函数被赋给了一个变量，编译器可以推断出它的名字。
*   编译器使用*别名这个*方法来维护上下文。

```
const loaded = () => {
  this.setState({ loading: false });
};

// will be compiled to

var _this = this;
var loaded = function loaded() {
  _this.setState({ loading: false });
}; 
```

### 5.ES2016 绑定语法

目前有一个关于 [ES2016 (ES7)绑定语法](https://github.com/zenparsing/es-function-bind)的提议，它引入了一个新的操作符`::`。绑定操作符需要在左边有一个值，在右边有一个函数，这个语法使用 LHS 作为这个函数的值来绑定 RHS 函数。

以`map`的这个实现为例。

```
function map(f) {
  var mapped = new Array(this.length);

  for(var i = 0; i < this.length; i++) {
    mapped[i] = f(this[i], i);  
  }

  return mapped;
} 
```

与 lodash 不同，我们不需要将数据作为参数传递，而是允许我们编写代码使`map`看起来像是我们数据的一个成员。

```
[1, 2, 3]::map(x => x * 2)
// [2, 4, 6] 
```

曾经厌倦过这样使用代码吗？

```
[].map.call(someNodeList, myFn);
// or
Array.from(someNodeList).map(myFn); 
```

该操作符将允许您直接在类似数组的结构上使用 map 函数。

```
someNodeList::map(myFn); 
```

我们也可以在 React 组件中使用这种语法。

```
this.setState({ loading: true });

fetch('/').then(this::() => {
  this.setState({ loading: false });
}); 
```

我第一个承认这种语法有点可怕。

虽然了解这个操作符很有意思，但它在这个上下文中并不是特别有用。它有许多与`.bind(this)`相同的缺点(事实上，这就是 Babel 编译它的目的)，如果你嵌套你的代码，你不得不一次又一次地使用它。这很可能会让其他各种能力的程序员感到困惑。

React 组件上下文可能不是 bind 操作符的未来，但是如果您感兴趣的话，可以看看一些很好的项目，在这些项目中，react 组件上下文得到了很好的应用(比如 [mori-ext](https://www.npmjs.com/package/mori-ext) )。

### 6.特定方法

有些函数允许您将一个显式值作为参数传递给`this`。一个例子是 [`map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ，它接受这个值作为它的最终参数。

```
items.map(function(x) {
  return <a onClick={this.clicked}>x</a>;
}, this); 
```

虽然这可以工作，但它不是一个一致的界面。大多数函数不接受这个参数，所以您可能更喜欢这里讨论的其他选项。

## 结论

我们已经看到了一系列不同的方法来确保您在函数中得到正确的上下文，但是您应该使用哪一种呢？

如果**性能**是一个问题，那么别名`this`可能是最快的方法。虽然您可能不会注意到差异，直到您处理了成千上万的组件，甚至到那时，在它成为问题之前会出现许多瓶颈。

如果你更关心**调试**，那么使用允许你写命名函数的选项之一，最好是组件方法，因为它们也会为你处理一些性能问题。

在 [Astral Dynamics](http://astraldynamics.co.uk) 中，我们已经找到了一个合理的折衷方案，即主要使用命名组件方法和 arrow 函数，但只有当我们编写非常短的内联函数时，才不会导致堆栈跟踪问题。这使得我们可以编写易于调试的组件，而不会在真正计算时失去箭头函数的简洁本质。

当然，这主要是主观的，您可能会发现您更喜欢用箭头函数和绑定语法来迷惑您的同事。毕竟，谁不喜欢通过阅读代码库来找到这些呢？

```
this.setState({ loading: false });

fetch('/')
  .then((loaded = this::() => {
    var component = this;
    return this::(() =>
      this::component.setState({ loaded: false });
    }).bind(React);
  }.bind(null))); 
```

## 分享这篇文章