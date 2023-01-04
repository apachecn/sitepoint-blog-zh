# ES6 在行动:let 和 const

> 原文：<https://www.sitepoint.com/es6-let-const/>

**在本教程中，我将介绍`let`和`const`，随着 ES6 的到来，JavaScript 中增加了两个新的关键字。它们通过提供一种定义块范围变量和常量的方法来增强 JavaScript。**

本文是 ES6 引入的众多 JavaScript 新特性之一，包括 [Map 和 WeakMap](https://www.sitepoint.com/preparing-ecmascript-6-map-weakmap/) 、 [Set 和 WeakSet](https://www.sitepoint.com/preparing-ecmascript-6-set-weakset/) 、可用于[字符串](https://www.sitepoint.com/es6-string-methods-string-prototype/)、[数字](https://www.sitepoint.com/es6-number-methods/)和[数组](https://www.sitepoint.com/es6-array-methods-array-prototype/)的新方法，以及可用于函数的[新语法。](https://www.sitepoint.com/preparing-ecmascript-6-new-function-syntax/)

## `let`

直到 ES5，JavaScript 只有两种类型的作用域，函数作用域和全局作用域。这给来自其他语言(如 C、C++或 Java)的开发人员带来了许多挫折和意想不到的行为。JavaScript 缺少块范围，这意味着变量只能在定义它的块中访问。块是在开始和结束的花括号里面的一切。让我们看看下面的例子:

```
function foo() {
  var par = 1;
  if (par >= 0) {
    var bar = 2;
    console.log(par); // prints 1
    console.log(bar); // prints 2
  }
  console.log(par); // prints 1
  console.log(bar); // prints 2
}
foo(); 
```

运行此代码后，您将在控制台中看到以下输出:

```
1
2
1
2 
```

大多数来自上述语言的开发人员所期望的是，在`if`块之外，你不能访问`bar`变量。例如，在 C 中运行等价的代码会导致错误`'bar' undeclared at line ...`，它指的是在`if`之外使用`bar`。

随着数据块范围的出现，这种情况在 ES6 中有所改变。ECMA 组织成员知道他们不能改变关键字`var`的行为，因为那会破坏向后兼容性。所以他们决定引入一个新的关键词叫做`let`。后者可用于定义变量，将其范围限制在声明它们的块中。此外，与`var`不同，使用`let`声明的变量不会被[提升](https://www.sitepoint.com/back-to-basics-javascript-hoisting/)。如果在遇到变量的`let`声明之前引用一个块中的变量，这会导致一个`ReferenceError`。但这在实践中意味着什么呢？是不是只对新手好？一点也不！

为了解释你为什么会喜欢`let`，请考虑下面这段摘自我的文章 [5 更多 JavaScript 面试练习](https://www.sitepoint.com/5-javascript-interview-exercises/)的代码:

```
var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
  nodes[i].addEventListener('click', function() {
    console.log('You clicked element #' + i);
  });
} 
```

在这里，您可以认识到一个众所周知的问题，它来自变量声明、它们的作用域和事件处理程序。如果你不知道我在说什么，去看看我提到的那篇文章，然后再回来。

多亏了 ES6，我们可以通过使用`let`在`for`循环中声明`i`变量来轻松解决这个问题:

```
var nodes = document.getElementsByTagName('button');
for (let i = 0; i < nodes.length; i++) {
  nodes[i].addEventListener('click', function() {
    console.log('You clicked element #' + i);
  });
} 
```

Node 和所有现代浏览器都支持`let`语句。然而，在 Internet Explorer 11 中有几个问题，你可以在 [ES6 兼容性表](https://kangax.github.io/compat-table/es6/)中读到。

展示`var`和`let`区别的现场演示如下所示，也可从 JSBin 的[获得:](https://jsbin.com/husifed/edit?html,js,console,output)

[ES6 在行动:让 jsbin.com](https://jsbin.com/husifed/embed?console,output)