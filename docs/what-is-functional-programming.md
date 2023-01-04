# 什么是函数式编程？

> 原文：<https://www.sitepoint.com/what-is-functional-programming/>

作为一名程序员，你可能想写优雅的、可维护的、可伸缩的、可预测的代码。函数式编程(FP)的原理可以极大地帮助实现这些目标。

函数式编程是一种范式或风格，它重视不变性、一流的函数、引用透明性和纯函数。如果这些话对你没有意义，不要担心！我们将在本文中分解所有这些术语。

函数式编程是从 lambda calculus 发展而来的，lambda calculus 是一种围绕函数抽象和概括构建的数学系统。因此，许多函数式编程语言看起来非常数学化。不过，好消息是:您不需要使用函数式编程语言来将函数式编程原则引入您的代码。在这篇文章中，我们将使用 JavaScript，它有很多特性使它可以服从于函数式编程，同时又不受这种范式的束缚。

## 函数式编程的核心原则

既然我们已经讨论了什么是函数式编程，那么让我们来谈谈 FP 背后的核心原则。

### 纯函数

我喜欢把函数想象成机器——它们接受输入或参数，然后输出一些东西，返回值。纯函数没有“副作用”或与函数输出无关的动作。一些潜在的副作用可能是打印一个值或者用`console.log`将其注销，或者在函数之外操纵变量。

这是一个不纯函数的例子:

```
let number = 2;

function squareNumber() {
  number = number * number; // impure action: manipulating variable outside function
  console.log(number); // impure action: console log-ing values
  return number;
}

squareNumber(); 
```

下面这个函数是纯的。它接受输入并产生输出。

```
// pure function
function squareNumber(number) {
  return number * number;
}

squareNumber(2); 
```

纯函数独立于函数之外的状态运行，所以它们不应该依赖于全局状态，或者自身之外的变量。在第一个例子中，我们使用在函数外部创建的`number`变量，并在函数内部设置它。这违背了原则。如果你非常依赖于不断变化的全局变量，你的代码将是不可预测和难以追踪的。找出哪里出了问题，为什么价值观会改变，将会变得更加困难。相反，只使用函数的输入、输出和局部变量可以简化调试。

此外，函数应该遵循*参照透明*，这意味着给定某个输入，它们的输出将总是相同的。在上面的函数中，如果我将`2`传递给函数，它将总是返回`4`。举两个例子，对于 API 调用或生成随机数来说，情况并非如此。给定相同的输入，输出可能返回也可能不返回。

```
// Not referentially transparent
Math.random();
// 0.1406399143589343
Math.random();
// 0.26768924082159495ß 
```

### 不变

函数式编程也优先考虑*不变性*，或者不直接修改数据。不变性带来可预测性——您知道数据的价值，它们不会改变。它使代码变得简单、可测试，并且能够在分布式和多线程系统上运行。

当我们处理数据结构时，不变性经常发挥作用。JavaScript 中很多数组方法直接修改数组。例如，`.pop()`直接从数组的末尾删除一个项目，而`.splice()`允许你取数组的一部分。相反，在函数范式中，我们将复制数组，并在此过程中删除我们希望消除的元素。

```
// We are mutating myArr directly
const myArr = [1, 2, 3];
myArr.pop();
// [1, 2] 
```

```
// We are copying the array without the last element and storing it to a variable
let myArr = [1, 2, 3];
let myNewArr = myArr.slice(0, 2);
// [1, 2]
console.log(myArr); 
```

### 一流的功能

在函数式编程中，我们的函数是一流的，这意味着我们可以像使用任何其他值一样使用它们。我们可以创建函数数组，将它们作为参数传递给其他函数，并将它们存储在变量中。

```
let myFunctionArr = [() => 1 + 2, () => console.log("hi"), x => 3 * x];
myFunctionArr[2](2); // 6

const myFunction = anotherFunction => anotherFunction(20);
const secondFunction = x => x * 10;
myFunction(secondFunction); // 200 
```

### 高阶函数

[高阶函数](https://www.sitepoint.com/higher-order-functions-javascript/)是做两件事之一的函数:它们要么接受一个函数作为它的一个或多个参数，要么返回一个函数。JavaScript 内置了许多第一类高阶函数，比如`map`、`reduce`和`filter`，我们可以用它们来与数组交互。

[`filter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 用于从旧数组返回新数组，旧数组只包含符合我们提供的条件的值。

```
const myArr = [1, 2, 3, 4, 5];

const evens = myArr.filter(x => x % 2 === 0); // [2, 4] 
```

[`map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 用于遍历数组中的各项，根据提供的逻辑修改每一项。在下面的例子中，我们通过向 map 传递一个将我们的值乘以 2 的函数，将数组中的每一项加倍。

```
const myArr = [1, 2, 3, 4, 5];

const doubled = myArr.map(i => i * 2); // [2, 4, 6, 8, 10] 
```

[`reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 允许我们基于输入的数组输出单个值——它经常被用来对数组求和、展平数组或以某种方式对值进行分组。

```
const myArr = [1, 2, 3, 4, 5];

const sum = myArr.reduce((i, runningSum) => i + runningSum); // 15 
```

您也可以自己实现其中的任何一个！例如，您可以像这样创建一个过滤函数:

```
const filter = (arr, condition) => {
  const filteredArr = [];

  for (let i = 0; i < arr.length; i++) {
    if (condition(arr[i])) {
      filteredArr.push(arr[i]);
    }
  }

  return filteredArr;
}; 
```

第二种类型的高阶函数，即返回其他函数的函数，也是一种相对常见的模式。例如:

```
const createGreeting = greeting => person => `${greeting}  ${person}`

const sayHi = createGreeting("Hi")
console.log(sayHi("Ali")) // "Hi Ali"

const sayHello = createGreeting("Hello")
console.log(sayHi("Ali")) // "Hello Ali" 
```

[奉承](https://www.sitepoint.com/currying-in-functional-javascript/)是一种相关的技巧，你可能也有兴趣去研究一下！

### 功能组成

功能组合是指将多个简单的功能组合起来，以创建更复杂的功能。所以，你可以有一个结合了一个`average`函数和一个`sum`函数的`averageArray`函数，这个函数对数组的值求和。单个功能很小，可以重复用于其他目的，组合起来执行更完整的任务。

```
const sum = arr => arr.reduce((i, runningSum) => i + runningSum);
const average = (sum, count) => sum / count;
const averageArr = arr => average(sum(arr), arr.length); 
```

## 利益

函数式编程导致模块化代码。您有一些可以反复重用的小函数。了解每个函数的具体功能意味着查明 bug 和编写测试应该很简单，特别是因为函数输出应该是可预测的。

此外，如果您尝试使用多个内核，您可以在这些内核之间分配函数调用，这样可以提高计算效率。

## 如何使用函数式编程？

你不需要完全转向函数式编程来融合所有这些思想。您甚至可以将许多想法与面向对象编程结合使用，面向对象编程通常被认为是它的对手。

例如，React 合并了许多像不可变状态这样的[功能原则](https://medium.com/@andrea.chiarelli/the-functional-side-of-react-229bdb26d9a6)，但也主要使用类语法很多年了。它也可以在几乎任何编程语言中实现——您不需要编写 Clojure 或 Haskell，除非您真的想这样做。

即使你不是一个纯粹主义者，函数式编程原则也能给你的代码带来积极的结果。

## 分享这篇文章