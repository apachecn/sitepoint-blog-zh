# 揭开 JavaScript 变量范围和提升的神秘面纱

> 原文：<https://www.sitepoint.com/demystifying-javascript-variable-scope-hoisting/>

在变量中存储值是编程中的一个基本概念。变量的“作用域”决定了它在整个程序中何时可用，何时不可用。理解 JavaScript 中的变量范围是构建坚实的语言基础的关键之一。

本文将解释 JavaScript 的作用域系统是如何工作的。您将了解声明变量的不同方式、局部作用域和全局作用域之间的差异，以及称为“[提升](https://www.sitepoint.com/javascript-hoisting/)”的东西——这是一种 JavaScript 怪癖，可以将看似无害的变量声明变成一个微妙的 bug。

## 变量作用域

在 JavaScript 中，变量的作用域由变量声明的位置控制，它定义了程序中可以访问特定变量的部分。

目前，在 JavaScript 中有三种**方法来声明变量:使用旧的`var`关键字，使用新的`let`和`const`关键字。在 ES6 之前，使用`var`关键字是声明变量的唯一方式，但现在我们可以使用`let`和`const`，它们有更严格的规则，使代码更不容易出错。我们将探究以下三个关键词之间的区别。**

范围规则因语言而异。JavaScript 有**两个**作用域:*全局*和*局部*。局部作用域有两种变化:旧的*功能作用域*，以及 ES6 引入的新的*块作用域*。值得注意的是，函数作用域实际上是块作用域的一种特殊类型。

### 全球范围

在脚本中，最外层的作用域是全局作用域。在此范围内声明的任何变量都成为全局变量，可以从程序中的任何地方访问:

```
// Global Scope

const name = "Monique";

function sayHi() {
  console.log(`Hi ${name}`);
}

sayHi();
// Hi Monique 
```

如这个简单的例子所示，变量`name`是全局的。它是在全局范围内定义的，在整个程序中都是可访问的。

尽管这看起来很方便，但是在 JavaScript 中不鼓励使用全局变量。例如，这是因为它们可能会被其他脚本或程序中的其他地方覆盖。

### 局部范围

块中声明的任何变量都属于那个特定的块，并成为局部变量。

JavaScript 中的函数定义了使用`var`、`let`和`const`声明的变量的作用域。在该函数中声明的任何变量只能从该函数和任何嵌套函数中访问。

一个代码块(`if`、`for`等)。)只为用`let`和`const`关键字声明的变量定义了作用域。`var`关键字仅限于函数范围，这意味着新范围只能在函数内部创建。

`let`和`const`关键字具有块范围，这为声明它们的任何块创建了一个新的局部范围。您还可以在 JavaScript 中定义独立的代码块，它们类似地限定了一个范围:

```
{
  // standalone block scope
} 
```

函数和块范围可以嵌套。在这种情况下，通过多个嵌套的作用域，变量可以在它自己的作用域内或从内部作用域访问。但是在其范围之外，变量是不可访问的。

## 一个帮助可视化范围的简单示例

为了把事情说清楚，我们用一个简单的比喻。世界上每个国家都有国界。这些边界内的一切都属于国家的范围。每个国家都有许多城市，每个城市都有自己的城市范围。国家和城市就像 JavaScript 函数或块。他们有自己的地方范围。各大洲也是如此。虽然它们的大小很大，但是它们也可以被定义为语言环境。

另一方面，世界上的海洋不能被定义为具有局部范围，因为它们实际上包裹了所有局部对象——大陆、国家和城市——因此，它们的范围被定义为全球范围。让我们在下一个例子中想象一下:

```
var locales = {
  europe: function() {          // The Europe continent's local scope
    var myFriend = "Monique";

    var france = function() {   // France country's local scope
      var paris = function() {  // The Paris city's local scope
        console.log(myFriend);  // output: Monique
      };

      paris();
    };

    france();
  }
};

locales.europe(); 
```

见笔 [变量作用域:1](https://codepen.io/SitePoint/pen/bGBJeoL)by site point([@ site point](https://codepen.io/SitePoint))
on[code Pen](https://codepen.io)。