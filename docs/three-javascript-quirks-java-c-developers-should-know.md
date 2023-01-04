# Java/C 开发人员应该知道的三个 JavaScript 怪癖

> 原文：<https://www.sitepoint.com/three-javascript-quirks-java-c-developers-should-know/>

JavaScript 可能是一种骗人的语言，也可能是一种真正的痛苦，因为它不是 100%一致的。众所周知，它确实有*不好的部分*，应该避免混淆或冗余的特性:臭名昭著的 [with 语句](http://www.2ality.com/2011/06/with-statement.html)、[隐式全局变量](http://yuiblog.com/blog/2006/06/01/global-domination/)和[比较不稳定行为](http://dorey.github.io/JavaScript-Equality-Table/)可能是最著名的。

JavaScript 是历史上最成功的火焰生成器之一！除了它的缺点(在新的 ECMAScript 规范中已经部分解决了)，大多数程序员讨厌 JavaScript 有两个原因:

*   他们错误地认为 DOM 等同于 JavaScript 语言，而 JavaScript 语言有一个非常糟糕的 API。
*   他们从 C 和 Java 这样的语言来到 JavaScript。他们被 JavaScript 的语法所愚弄，相信它和那些命令式语言的工作方式是一样的。这种误解会导致困惑、沮丧和错误。

这就是为什么，一般来说，JavaScript 的名声比它应得的要差。

在我的职业生涯中，我注意到一些模式:大多数具有 Java 或 C/C++背景的开发人员认为 JavaScript 中的语言特性是相同的，但它们完全不同。

本文收集了最棘手的问题，比较了 Java 方式和 JavaScript 方式，展示了不同之处，并强调了 JavaScript 的最佳实践。

## 辖域

大多数开发人员开始从事 JavaScript 工作是因为他们是被迫的，几乎每个人都是在花时间学习语言之前就开始编写代码。每个这样的开发人员都至少被 JavaScript scope 骗过一次。

因为 JavaScript 的语法非常类似于(故意的)C 族语言，用花括号分隔`function`、`if`和`for`的主体，所以人们有理由期待词法*块级的*范围。不幸的是，事实并非如此。

首先，在 JavaScript 中，变量范围是由函数决定的，而不是由括号决定的。换句话说，`if`和`for`体不会创建新的作用域，在它们体内声明的变量实际上是**提升的**，即在声明它的最内层函数的开始处创建，否则就是在全局作用域的开始处创建。

其次，`with`语句的出现迫使 JavaScript 的作用域是动态的，直到运行时才能确定。听到不推荐使用`with`语句时，您可能不会感到惊讶:去掉了`with`的 JavaScript 实际上是一种词汇范围的语言，也就是说，范围可以完全通过查看代码来确定。

形式上，在 JavaScript 中，名字有四种进入作用域的方式:

*   语言定义的:默认情况下，所有作用域都包含名称`this`和`arguments`。
*   形式参数:为函数声明的任何(形式)参数的范围都是该函数的主体。
*   函数声明。
*   变量声明。

另一个更复杂的问题是分配给没有使用`var`关键字声明(隐式)的变量的隐式全局范围。当没有显式绑定就调用函数时，这种疯狂的行为与将全局作用域隐式赋值给`this`引用成对出现(在接下来的小节中会有更多相关内容)。

在深入研究细节之前，让我们清楚地陈述一下可以用来避免混淆的好模式:

使用*严格模式* ( `'use strict';`)，将所有变量和函数声明移到每个函数的顶部；避免在`for`和`if`块中声明变量，以及在这些块中声明函数(出于不同的原因，这超出了本文的范围)。

### 提升

[提升](https://www.sitepoint.com/back-to-basics-javascript-hoisting/)是一种简化，用于解释声明的实际行为。被提升的变量在包含它们的函数的最开始被声明，并被初始化为`undefined`。那么赋值发生在原始声明所在的实际行中。

看一下下面的例子:

```
function myFunction() {
  console.log(i);
  var i = 0;
  console.log(i);
  if (true) {
    var i = 5;
    console.log(i);
  }
  console.log(i);
}
```

您希望控制台输出什么值？你会对下面的输出感到惊讶吗？

```
undefined
0
5
5
```

在`if`块中，`var`语句没有声明变量`i`的本地副本，而是覆盖了之前声明的副本。注意，第一个`console.log`语句打印变量`i`的实际值，它被初始化为`undefined`。您可以使用`"use strict";`指令作为函数中的第一行来测试它。在[严格模式下](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)变量**在使用前必须**声明，但是你可以检查 JavaScript 引擎不会抱怨声明。另一方面，要知道你不会因为重新声明一个`var`而抱怨:如果你想捕捉这样的 bug，你最好用 linter 比如 [JSHint](http://www.jshint.com/) 或者 [JSLint](http://www.jslint.com/) 来处理你的代码。

现在让我们再看一个例子来强调变量声明的另一个容易出错的用法:

```
var notNull = 1;
function test() {
  if (!notNull) {
    console.log("Null-ish, so far", notNull);
    for(var notNull = 10; notNull <= 0; notNull++){
      //..
    }
    console.log("Now it's not null", notNull);
  }
  console.log(notNull);
}
```

尽管您可能会有不同的预期，但是执行`if`主体是因为在`test()`函数中声明了一个名为`notNull`的变量的本地副本，并且它被*提升为*。类型强制在这里也起了作用。

### 函数声明与函数表达式

提升不仅仅适用于变量，*函数表达式*实际上是变量，*函数声明*也是提升的。这个主题需要比我在这里所做的更仔细地对待，但是简而言之，函数声明的行为大部分像函数表达式，除了它们的声明被移到了它们作用域的开始。

请考虑以下显示函数声明行为的示例:

```
function foo() {
    // A function declaration
    function bar() {
        return 3;
    }
    return bar();

    // This function declaration will be hoisted and overwrite the previous one
    function bar() {
        return 8;
    }
}
```

现在，将其与显示函数表达式行为的示例进行比较:

```
function foo() {
    // A function expression
    var bar = function() {
        return 3;
    };
    return bar();

    // The variable bar already exists, and this code will never be reached
    var bar = function() {
        return 8;
    };
}
```

有关这些概念的更多信息，请参见参考资料部分。

### 随着

以下示例显示了只能在运行时确定范围的情况:

```
function foo(y) {
  var x = 123;
  with(y) {
    return x;
  }
}
```

如果`y`有一个名为`x`的字段，那么函数`foo()`将返回`y.x`，否则将返回`123`。这种编码实践是运行时错误的一个可能来源，因此强烈建议您避免使用`with`语句。

### 展望未来:ECMAScript 6

ECMAScript 6 规范将增加第五种方式来增加块级范围: [`let`语句](https://www.sitepoint.com/es6-let-const/)。考虑下面的代码:

```
function myFunction() {
  console.log(i);
  var i = 0;
  console.log(i);
  if (false) {
    let i = 5;
    console.log(i);
  }
  console.log(i);
}
```

在 ECMAScript 6 中，在`if`的主体中用`let`声明`i`将会在`if`块中创建一个新的局部变量。作为一个非标准的选择，可以如下声明`let`块:

```
var i = 6;
let (i = 0, j = 2) {
  /* Other code here */
}
// prints 6
console.log(i);
```

在上面的代码中，变量`i`和`j`只存在于块内部。在撰写本文时，[对`let`](http://kangax.github.io/compat-table/es6/#let) 的支持是有限的，甚至对 Chrome 也是如此。

### 概括地说，范围

下表总结了不同语言的范围:

| 特征 | Java 语言(一种计算机语言，尤用于创建网站) | 计算机编程语言 | Java Script 语言 | 警告信息 |
| 范围 | 词汇(块) | 词法(函数、类或模块) | 是 | 它的工作方式与 Java 或 C 非常不同 |
| 块范围 | 是 | 不 | ` let '关键字(ES6) | 再次警告:这不是 Java！ |
| 提升 | 不会吧！ | 不 | 是 | 对于变量和函数表达式，只需要声明。对于函数声明，定义也被提升 |

## 功能

JavaScript 的另一个被误解的特性是函数，特别是因为在像`Java`这样的命令式编程语言中，没有函数这个概念。

事实上，JavaScript 是一种函数式编程语言。嗯，不是像 [Haskell](https://www.haskell.org/) 那样的纯函数式编程语言——毕竟它仍然有命令式风格，可变性是被鼓励的，而不是简单地被允许，就像 [Scala](http://www.scala-lang.org/) 一样。然而，JavaScript 可以被用作纯粹的函数式编程语言，函数调用没有任何副作用。

### 一等公民

JavaScript 中的函数可以像其他类型一样处理，例如`String`和`Number`:它们可以存储在变量中，作为参数传递给函数，由函数返回，以及存储在数组中。函数也可以有属性，可以动态改变，这是因为…

### 目标

对于大多数 JavaScript 新手来说，一个非常令人惊讶的事实是，函数实际上是对象。在 JavaScript 中，每个函数实际上都是一个`Function`对象。`Function`构造函数创建一个新的`Function`对象:

```
var func = new Function(['a', 'b', 'c'], '');
```

这(几乎)相当于:

```
function func(a, b, c) { }
```

我说它们几乎是等价的，因为使用`Function`构造函数效率较低，会产生一个匿名函数，并且不会对其创建上下文创建一个闭包。`Function`对象总是在全局范围内创建。

`Function`，函数类型，建立在`Object`之上。通过检查您声明的任何函数，可以很容易地看出这一点:

```
function test() {}
//  prints  "object"
console.log(typeof test.prototype);
//  prints  function Function() { [native code] }
console.log(test.constructor);
```

这意味着函数可能并且确实有属性。其中一些被分配给创建时的功能，如`name`或`length`。这些属性分别返回函数定义中参数的名称和数量。

考虑下面的例子:

```
function func(a, b, c) { }
//  prints "func"
console.log(func.name);
//  prints 3
console.log(func.length);
```

但是您甚至可以自己为任何函数设置新属性:

```
function test() {
  console.log(test.custom);
}
test.custom = 123;
//  prints 123
test();
```

### 简而言之的功能

下表描述了 Java、Python 和 JavaScript 中的函数:

| 特征 | Java 语言(一种计算机语言，尤用于创建网站) | 计算机编程语言 | Java Script 语言 | 警告信息 |
| 作为内置类型运行 | Java 8 语言 | 是 | 是 |  |
| 回调/命令模式 | 对象(或 Java 8 的 lambdas) | 是 | 是 | 函数(回调)具有可以被“客户端”修改的属性 |
| 动态创作 | 不 | 不 | ` eval `-` function '对象 | “eval”有安全问题，“Function”对象可能会意外工作 |
| 性能 | 不 | 不 | 可以有属性 | 不能限制对函数属性的访问 |

## 关闭

如果我必须选择我最喜欢的 JavaScript 特性，毫无疑问，我会选择闭包。JavaScript 是第一个引入闭包的主流编程语言。如您所知，Java 和 Python 长期以来一直有一个弱化版本的闭包，在这个版本中，您只能从封闭范围中读取(一些)值。

例如，在 Java 中，匿名内部类提供了类似闭包的功能，但有一些限制。例如，只有 final 局部变量可以在它们的作用域内使用——换句话说，它们的值是可以读取的。

JavaScript 允许对外部范围变量和函数的完全访问。它们可以被读取、写入，如果需要的话，甚至可以被本地定义隐藏起来:您可以在“作用域”一节中看到所有这些情况的例子。

更有趣的是，在闭包中创建的函数会记住创建它的环境。通过结合闭包和函数嵌套，您可以让外部函数返回内部函数，而无需执行它们。此外，在声明局部变量的函数执行结束后很久，外部函数的局部变量仍存在于内部函数的闭包中。这是一个非常强大的特性，但它也有缺点，因为这是 JavaScript 应用程序中内存泄漏的常见原因。

几个例子将阐明这些概念:

```
function makeCounter () {
  var i = 0;

  return function displayCounter () {
    console.log(++i);
  };
}
var counter = makeCounter();
//  prints 1
counter();
//  prints 2
counter();
```

上面的`makeCounter()`函数创建并返回另一个函数，该函数跟踪它所创建的环境。虽然当变量`counter`被赋值时`makeCounter()`的执行已经结束，但是局部变量`i`被保存在`displayCounter`的闭包里，因此可以在它的体内访问。

如果我们再次运行`makeCounter`，它将创建一个新的闭包，为`i`创建一个不同的条目:

```
var counterBis = makeCounter();
//  prints 1
counterBis();
//  prints 3
counter();
//  prints 2
counterBis();
```

为了让它更有趣一点，我们可以更新`makeCounter()`函数，使它接受一个参数:

```
function makeCounter(i) {
  return function displayCounter () {
    console.log(++i);
  };
}
var counter = makeCounter(10);
//  prints 11
counter();
//  prints 12
counter();
```

外部函数参数也保存在闭包中，所以这次我们不需要声明局部变量。对`makeCounter()`的每次调用都会记住我们设置的初始值，并进行计数。

闭包对于许多基本的 JavaScript 模式来说是至关重要的:命名空间、模块、私有变量、[内存化](https://www.sitepoint.com/implementing-memoization-in-javascript/)就是其中最著名的。

作为一个例子，让我们看看如何模拟一个对象的私有变量:

```
function Person(name) {
  return {
    setName: function(newName) {
      if (typeof newName === 'string' && newName.length > 0) {
        name = newName;
      } else {
        throw new TypeError("Not a valid name");
      }
    },
    getName: function () {
      return name;
    }
  };
}

var p = Person("Marcello");

// prints "Marcello"
a.getName();

// Uncaught TypeError: Not a valid name
a.setName();

// Uncaught TypeError: Not a valid name
a.setName(2);
a.setName("2");

// prints "2"
a.getName();
```

使用这种模式，利用闭包，我们可以用自己的 setter 和 getter 为属性名创建包装器。ES5 使这变得容易得多，因为您可以用 getters 和 setters 为对象的属性创建对象，并以最细微的方式控制对属性本身的访问。

### 简而言之，闭包

下表描述了 Java、Python 和 JavaScript 中的闭包:

| 特征 | Java 语言(一种计算机语言，尤用于创建网站) | 计算机编程语言 | Java Script 语言 | 警告信息 |
| 关闭 | 在匿名内部类中被弱化，只读 | 嵌套定义中的弱化只读 | 是 | 内存泄漏 |
| 记忆模式 | 必须使用共享对象 | 可能使用列表或字典 | 是 | 最好使用懒惰评估 |
| 名称空间/模块模式 | 不需要 | 不需要 | 是 |  |
| 私有属性模式 | 不需要 | 不可能 | 是 | 可能会让人困惑 |

## 结论

在本文中，我介绍了 JavaScript 的三个特性，这些特性经常被来自不同语言的开发人员误解，尤其是 Java 和 c。如果你想深入研究这些话题，这里有一份你可以阅读的文章列表:

*   [JavaScript 中的作用域](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html)
*   [函数声明 vs 函数表达式](http://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/)
*   [`Let`语句和`let`块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)



## 分享这篇文章