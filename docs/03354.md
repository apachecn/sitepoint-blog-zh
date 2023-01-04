# 函数式 JavaScript 中的 Currying 初学者指南

> 原文：<https://www.sitepoint.com/currying-in-functional-javascript/>

Currying，或部分应用程序，是一种函数式技术，对于熟悉更传统的 JavaScript 编写方式的人来说，听起来可能会感到困惑。但是如果应用得当，它实际上可以使您的函数式 JavaScript 更具可读性。

## 可读性更强，更灵活

函数式 JavaScript 的优势之一是代码更短、更紧凑，尽可能用最少的几行代码就能抓住要点，而且重复较少。有时这会以牺牲可读性为代价；在您熟悉函数式编程的工作方式之前，以这种方式编写的代码可能更难阅读和理解。

如果你以前遇到过 currying 这个词，但从来不知道它是什么意思，你可以被原谅，因为你认为它是一些异国情调的，辛辣的技术，你不需要费心。但是 currying 实际上是一个非常简单的概念，它解决了处理函数参数时的一些常见问题，同时为开发人员提供了一系列灵活的选择。

## 什么是 Currying？

简而言之，currying 是一种构造函数的方法，它允许部分应用函数的参数。这意味着你可以传递一个函数期望的所有参数并得到结果，或者传递这些参数的子集并得到一个等待其余参数的函数。真的就这么简单。

Currying 是 Haskell 和 Scala 等围绕函数概念构建的语言中的基本元素。JavaScript 具有功能性，但是 currying 不是默认内置的(至少在当前版本的语言中不是)。但是我们已经知道了一些函数技巧，我们也可以在 JavaScript 中使用 currying。

为了让您了解这是如何工作的，让我们用 JavaScript 创建我们的第一个 curry 函数，使用熟悉的语法来构建我们想要的 curry 功能。作为一个例子，让我们想象一个用名字问候某人的函数。我们都知道如何创建一个简单的 greet 函数，它接受一个名称和一个问候语，并将带有名称的问候语记录到控制台:

```
var greet = function(greeting, name) {
  console.log(greeting + ", " + name);
};
greet("Hello", "Heidi"); //"Hello, Heidi" 
```

该函数需要将名称和问候语作为参数传递，才能正常工作。但是我们可以使用简单的嵌套 currying 来重写这个函数，这样基本函数只需要一个问候，它返回另一个函数，该函数接受我们想要问候的人的名字。

## 我们的第一道咖喱菜

```
var greetCurried = function(greeting) {
  return function(name) {
    console.log(greeting + ", " + name);
  };
}; 
```

对我们编写函数的方式的这一微小调整，使我们能够为任何类型的问候创建一个新函数，并向这个新函数传递我们想要问候的人的姓名:

```
var greetHello = greetCurried("Hello");
greetHello("Heidi"); //"Hello, Heidi"
greetHello("Eddie"); //"Hello, Eddie" 
```

我们也可以直接调用原来的 curried 函数，只需将每个参数放在一组独立的括号中，一个接一个地传递:

```
greetCurried("Hi there")("Howard"); //"Hi there, Howard" 
```

为什么不在你的浏览器中尝试一下呢？

[jsbin.com](https://jsbin.com/basoyi/embed?js,console)T2【上 JS 斌】

## 咖喱所有的东西！

有趣的是，现在我们已经学会了如何修改我们的传统函数来使用这种方法来处理参数，我们可以用任意多的参数来做这件事:

```
var greetDeeplyCurried = function(greeting) {
  return function(separator) {
    return function(emphasis) {
      return function(name) {
        console.log(greeting + separator + name + emphasis);
      };
    };
  };
}; 
```

我们对四个参数和两个参数有同样的灵活性。无论嵌套进行到什么程度，我们都可以创建新的自定义函数，以适合我们目的的多种方式来问候我们选择的尽可能多的人:

```
var greetAwkwardly = greetDeeplyCurried("Hello")("...")("?");
greetAwkwardly("Heidi"); //"Hello...Heidi?"
greetAwkwardly("Eddie"); //"Hello...Eddie?" 
```

更重要的是，当我们在原来的 curried 函数上创建自定义变体时，我们可以传递尽可能多的参数，创建能够接受适当数量的附加参数的新函数，每个参数都在自己的一组括号中单独传递:

```
var sayHello = greetDeeplyCurried("Hello")(", ");
sayHello(".")("Heidi"); //"Hello, Heidi."
sayHello(".")("Eddie"); //"Hello, Eddie." 
```

我们可以很容易地定义从属变量:

```
var askHello = sayHello("?");
askHello("Heidi"); //"Hello, Heidi?"
askHello("Eddie"); //"Hello, Eddie?" 
```

[jsbin.com](https://jsbin.com/tetugu/embed?js,console&height=450px)T2【上 JS 斌】

## Currying 传统功能

您可以看到这种方法是多么强大，尤其是当您需要创建许多非常详细的自定义函数时。唯一的问题是语法。当您构建这些 curried 函数时，您需要继续嵌套返回的函数，并使用需要多组括号的新函数来调用它们，每组括号都包含自己独立的参数。会变得很乱。

为了解决这个问题，一种方法是创建一个快速而肮脏的 currying 函数，该函数将采用现有函数的名称，而该函数是在没有所有嵌套返回的情况下编写的。currying 函数需要取出该函数的参数列表，并使用这些参数返回原始函数的 curry 版本:

```
var curryIt = function(uncurried) {
  var parameters = Array.prototype.slice.call(arguments, 1);
  return function() {
    return uncurried.apply(this, parameters.concat(
      Array.prototype.slice.call(arguments, 0)
    ));
  };
}; 
```

为了使用它，我们给它传递一个函数名，这个函数可以接受任意数量的参数，以及我们想要预先填充的任意数量的参数。我们得到的是一个等待剩余参数的函数:

```
var greeter = function(greeting, separator, emphasis, name) {
  console.log(greeting + separator + name + emphasis);
};
var greetHello = curryIt(greeter, "Hello", ", ", ".");
greetHello("Heidi"); //"Hello, Heidi."
greetHello("Eddie"); //"Hello, Eddie." 
```

和以前一样，当从我们的 curried 原函数构造导数函数时，我们不限制我们想要使用的参数的数量:

```
var greetGoodbye = curryIt(greeter, "Goodbye", ", ");
greetGoodbye(".", "Joe"); //"Goodbye, Joe." 
```

[jsbin.com](https://jsbin.com/hayaka/embed?js,console&height=475px)T2【上 JS 斌】

## 认真对待携带

我们的小 currying 函数可能不能处理所有的边缘情况，例如缺少参数或可选参数，但是只要我们严格遵守传递参数的语法，它就能做好工作。

一些函数式 JavaScript 库，如 [Ramda](http://ramdajs.com/) 有更灵活的 currying 函数，可以分解函数所需的参数，并允许您单独或成组传递它们，以创建定制的 curried 变体。如果你想广泛使用 currying，这可能是一条路要走。

无论您选择如何在编程中添加 currying，无论您只想使用嵌套括号还是希望包含更健壮的承载函数，为 curried 函数提供一致的命名约定都将有助于提高代码的可读性。一个函数的每一个派生变体都应该有一个名字，这个名字可以清楚地表明它的行为方式，以及它所期望的参数。

## 参数顺序

奉承时要记住的一件重要事情是参数的顺序。使用我们所描述的方法，您显然希望您最有可能从一个变体替换到下一个变体的参数是传递给原始函数的最后一个参数。

提前考虑争论的顺序会让你更容易计划奉承，并把它应用到你的工作中。无论如何，在设计函数的时候，考虑参数从最少到最有可能改变的顺序并不是一个坏习惯。

## 结论

Currying 是函数式 JavaScript 中非常有用的技术。它允许您生成一个小型的、易于配置的函数库，这些函数库行为一致、使用快捷，并且在阅读您的代码时能够被理解。将 currying 添加到您的编码实践中将鼓励在您的代码中使用部分应用的函数，避免大量潜在的重复，并且可能帮助您养成命名和处理函数参数的更好习惯。

如果你喜欢这篇文章，你可能也会喜欢这个系列的其他文章:

*   [函数式 JavaScript 简介](https://www.sitepoint.com/introduction-functional-javascript/)
*   [JavaScript 中的高阶函数](https://www.sitepoint.com/higher-order-functions-javascript/)
*   [函数式 JavaScript 中的递归](https://www.sitepoint.com/recursion-functional-javascript/)

## 分享这篇文章