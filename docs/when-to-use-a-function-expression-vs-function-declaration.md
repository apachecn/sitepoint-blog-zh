# 何时使用函数表达式与函数声明

> 原文：<https://www.sitepoint.com/when-to-use-a-function-expression-vs-function-declaration/>

在 JavaScript 中创建函数有两种方法:函数表达式和函数声明。在本文中，我们将讨论何时使用函数表达式和函数声明，并解释它们之间的区别。

函数声明已经使用了很长时间，但是函数表达式已经逐渐占据了主导地位。许多开发人员不确定何时使用其中一个，所以他们最终使用了错误的一个。

函数表达式和函数声明之间有一些关键的区别。让我们仔细看看这些区别，以及在代码中什么时候使用函数表达式，什么时候使用函数声明。

```
function funcDeclaration() {
    return 'A function declaration';
}

let funcExpression = function () {
    return 'A function expression';
} 
```

## 什么是函数声明？

函数声明是指创建一个函数并给它命名。在编写 function 关键字时，声明函数的名称，后跟函数名。例如:

```
function myFunction() {
  // do something
};
```

如您所见，函数名(`myFunction`)是在函数创建时声明的。这意味着您可以在定义函数之前调用它。

下面是一个函数声明的示例:

```
function add (a, b) {
  return a + b;
};
```

## 什么是函数表达式？

函数表达式是当你创建一个函数并把它赋给一个变量。这个函数是匿名的，这意味着它没有名字。例如:

```
let myFunction = function() {
  // do something
};
```

如您所见，该函数被分配给了`myFunction`变量。这意味着您必须在调用函数之前定义它。

下面是一个函数表达式的示例:

```
let add = function (a, b) {
  return a + b;
};
```

## 函数表达式和声明的区别

函数表达式和函数声明之间有一些关键的区别:

*   函数声明是悬挂的，而函数表达式不是。这意味着您可以在定义函数声明之前调用它，但是您不能用函数表达式这样做。
*   使用函数表达式，您可以在定义函数后立即使用它。对于函数声明，您必须等待整个脚本被解析。
*   函数表达式可以用作另一个函数的参数，但函数声明不能。
*   函数表达式可以是匿名的，而函数声明不能。

### 理解函数表达式中的作用域:JavaScript 提升差异

类似于`let`语句，函数声明[被提升到其他代码的顶部](https://www.sitepoint.com/javascript-hoisting/)。

函数表达式不会被提升。这允许它们从定义局部变量的作用域中保留局部变量的副本。

通常，函数声明和函数表达式可以互换使用。但是有时函数表达式会产生更容易理解的代码，而不需要临时函数名。

## 如何在表达式和声明之间选择

那么，什么时候应该使用函数表达式而不是函数声明呢？

答案取决于你的需求。如果你需要一个更灵活的函数或者一个没有被提升的函数，那么函数表达式就是一个不错的选择。如果你需要一个可读性更强、更容易理解的函数，那么就使用函数声明。

如您所见，这两种语法是相似的。最明显的区别是函数表达式是匿名的，而函数声明是有名字的。

今天，当你需要做一些函数表达式不能做的事情时，你通常会使用函数声明。如果你不需要做任何只有函数声明才能做的事情，那么一般最好使用函数表达式。

当需要创建递归函数时，或者当需要在定义函数之前调用函数时，可以使用函数声明。作为一个经验法则，当你不需要做这些事情的时候，使用函数表达式来获得更干净的代码。

### 函数声明的好处

使用函数声明有几个主要好处。

*   它可以让你的代码更具可读性。如果你有一个很长的函数，给它命名可以帮助你跟踪它在做什么。
*   **函数声明被吊起**，这意味着它们在你的代码中定义之前是可用的。如果您需要在定义函数之前使用它，这会很有帮助。

### 函数表达式的好处

函数表达式也有一些好处。

*   它们比函数声明更灵活。您可以创建函数表达式，并将其分配给不同的变量，这在您需要在不同的地方使用同一个函数时会很有帮助。
*   **函数表达式没有被吊起**，所以在代码中定义之前不能使用。如果您想确保函数只在定义后才使用，这很有帮助。

### 何时选择函数声明还是函数表达式

在大多数情况下，很容易找出哪种定义函数的方法最适合您的需要。这些指南将帮助你在大多数情况下快速做出决定。

**在下列情况下使用函数声明:**

*   你需要一个可读性更强、更容易理解的函数(比如一个长函数，或者一个你需要在不同地方使用的函数)
*   匿名函数不会满足您的需求
*   你需要创建一个递归的函数
*   您需要在定义函数之前调用它

**在下列情况下使用函数表达式:**

*   你需要一个更灵活的功能
*   你需要一个不被提升的函数
*   该函数应仅在定义时使用
*   该函数是匿名的，或者不需要名字供以后使用
*   您希望使用像立即调用函数表达式(life)这样的技术来控制函数的执行时间
*   您希望将该函数作为参数传递给另一个函数

也就是说，在很多情况下，函数表达式的灵活性成为了一种强大的资产。

## 解锁函数表达式:JavaScript 提升差异

有几种不同的方法可以让函数表达式变得比函数声明更有用。

*   关闭
*   其他函数的参数
*   立即调用函数表达式(IIFE)

### 用函数表达式创建闭包

当你想在函数执行前给它参数时，可以使用闭包。一个很好的例子就是循环通过一个`NodeList`的时候。

闭包允许您保留其他信息，比如索引，以防一旦函数执行后这些信息不可用。

```
function tabsHandler(index) {
    return function tabClickEvent(evt) {
        // Do stuff with tab.
        // The index variable can be accessed from within here.
    };
}

let tabs = document.querySelectorAll('.tab'),
    i;

for (i = 0; i &lt; tabs.length; i += 1) {
    tabs[i].onclick = tabsHandler(i);
} 
```

附加的事件处理程序稍后执行(在循环完成之后)，因此需要一个闭包来保留`for`循环的适当值。

```
// Bad code, demonstrating why a closure is needed
let i;

for (i = 0; i &lt; list.length; i += 1) {
    document.querySelector('#item' + i).onclick = function doSomething(evt) {
        // Do something with item i
        // But, by the time this function executes, the value of i is always list.length
    }
} 
```

通过从`for`循环中提取出`doSomething()`函数，更容易理解为什么会出现问题。

```
// Bad code, demonstrating why a closure is needed

let list = document.querySelectorAll('.item'),
    i,
    doSomething = function (evt) {
        // Do something with item i.
        // But, by the time this function executes, the value of i is not what it was in the loop.
    };

for (i = 0; i &lt; list.length; i += 1) {
    item[i].onclick = doSomething;
} 
```

这里的解决方案是将索引作为函数参数传递给外部函数，以便它可以将该值传递给内部函数。您通常会看到用于组织内部返回函数所需信息的处理函数。

```
// The following is good code, demonstrating the use of a closure

let list = ['item1', 'item2', 'item3'],
    i,
    doSomethingHandler = function (itemIndex) {
        return function doSomething(evt) {
            // now this doSomething function can retain knowledge of
            // the index variable via the itemIndex parameter,
            // along with other variables that may be available too.
            console.log('Doing something with ' + list[itemIndex]);
        };
    };

for (i = 0; i &lt; list.length; i += 1) {
    list[i].onclick = doSomethingHandler(i);
} 
```

了解更多关于[闭包及其用法](https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/)的信息。

### 将函数表达式作为参数传递

函数表达式可以直接传递给函数，而不必赋给中间临时变量。

你会经常看到它们以匿名函数的形式出现。下面是一个熟悉的 jQuery 函数表达式示例:

```
$(document).ready(function () {
    console.log('An anonymous function');
}); 
```

当使用像`forEach()`这样的方法时，函数表达式也被用来处理数组项。

它们也不必是未命名的匿名函数。将函数命名为表达式是一个好主意，这样有助于表达函数应该做什么，并有助于调试:

```
let productIds = ['12356', '13771', '15492'];

productIds.forEach(function showProduct(productId) {
    ...
}); 
```

### 立即调用函数表达式(IIFE)

IIFEs 有助于防止函数和变量影响全局范围。

中的所有属性都在匿名函数的范围内。这是一种常见的设计模式，用于防止您的代码在其他地方产生不想要的或不想要的副作用。

它还被用作模块模式，将代码块包含在易于维护的部分中。我们在[揭开 JavaScript 闭包、回调和生命](https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/)中对此进行了更深入的探讨。

这是一个简单的生活例子:

```
(function () {
    // code in here
}()); 
```

…当作为一个模块使用时，可以为您的代码带来一些易于实现的可维护性。

```
let myModule = (function () {
    let privateMethod = function () {
        console.log('A private method');
    },
    someMethod = function () {
        console.log('A public method');
    },
    anotherMethod = function () {
        console.log('Another public method');
    };

    return {
        someMethod: someMethod,
        anotherMethod: anotherMethod
    };
}()); 
```

## 结论

正如我们所看到的，函数表达式与函数声明没有本质上的不同，但是它们通常可以产生更清晰、更易读的代码。

它们的广泛使用使它们成为每个开发人员工具箱中必不可少的一部分。你在你的代码中有没有以我上面没有提到的有趣的方式使用函数表达式？评论一下，让我知道！

## 分享这篇文章