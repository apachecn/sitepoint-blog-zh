# 快速提示:如何在 JavaScript 中声明变量

> 原文：<https://www.sitepoint.com/how-to-declare-variables-javascript/>

学习 JavaScript 的基础之一是理解如何使用变量。变量是所有可能类型值的容器，如数字、字符串或数组(见[数据类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures))。每个变量都有一个名称，以后可以在应用程序中使用(例如，读取它的值)。

在这个快速技巧中，您将学习如何使用变量以及各种声明之间的区别。

## 声明、初始化和赋值之间的区别

在我们开始学习各种声明之前，让我们看看变量的生命周期。

![Variable lifecycle flowchart](img/b76ad45f1a3e40a4f02c66d0c5ce4c2f.png)

1.  **声明**:在相应的作用域内使用给定的名称注册变量(解释如下——例如在函数内)。
2.  **初始化**:当你声明一个变量时，它会被自动初始化，这意味着 JavaScript 引擎会为这个变量分配内存。
3.  **赋值**:这是将一个特定值赋给变量的时候。

## 声明类型

> **注意**:虽然`var`自最初发布以来就在 JavaScript 中可用，但`let`和`const`仅在 ES6 (ES2015)及更高版本中可用。浏览器兼容性见[本页](http://caniuse.com/#feat=const)。

### 定义变量

语法:

```
var x; // Declaration and initialization
x = "Hello World"; // Assignment

// Or all in one
var y = "Hello World"; 
```

这一声明可能是最受欢迎的，因为直到 [ECMAScript 6](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Introduction#JavaScript_and_the_ECMAScript_Specification) 才出现了替代方案。用`var`声明的变量在封闭函数的范围内可用。如果没有封闭函数，它们是全局可用的。

示例:

```
function sayHello(){
  var hello = "Hello World";
  return hello;
}
console.log(hello); 
```

这将导致错误`ReferenceError: hello is not defined`，因为变量`hello`仅在函数`sayHello`中可用。但是下面的将会起作用，因为变量将被全局声明——在相同的作用域`console.log(hello)`中:

```
var hello = "Hello World";
function sayHello(){
  return hello;
}
console.log(hello); 
```

### 让

语法:

```
let x; // Declaration and initialization
x = "Hello World"; // Assignment

// Or all in one
let y = "Hello World"; 
```

`let`是现代 JavaScript 中`var`的后代。它的作用域不仅仅限于封闭函数，还包括它的封闭块语句。块语句是`{`和`}`中的所有内容(例如 if 条件或循环)。`let`的好处是它减少了出错的可能性，因为变量只在较小的范围内可用。

示例:

```
var name = "Peter";
if(name === "Peter"){
  let hello = "Hello Peter";
} else {
  let hello = "Hi";
}
console.log(hello); 
```

这将导致一个错误`ReferenceError: hello is not defined`,因为`hello`只在封闭块内部可用——在这种情况下是`if`条件。但是下面的将会工作:

```
var name = "Peter";
if(name === "Peter"){
  let hello = "Hello Peter";
  console.log(hello);
} else {
  let hello = "Hi";
  console.log(hello);
} 
```

### 常数

语法:

```
const x = "Hello World"; 
```

从技术上讲，常数不是变量。常数的特殊性在于，你需要在声明它的时候赋值，没有办法重新赋值。一个`const`被限制在封闭块的范围内，就像`let`一样。

只要在应用程序运行期间某个值不能改变，就应该使用常量，因为当您试图覆盖它们时，会收到错误通知。

## 偶然的全球创造

你可以在全局上下文中(也就是在任何函数之外)写所有上面提到的声明，但是即使在一个函数中，如果你忘记在赋值前写`var`、`let`或`const`，这个变量将自动成为全局变量。

示例:

```
function sayHello(){
  hello = "Hello World";
  return hello;
}
sayHello();
console.log(hello); 
```

上面的代码将把`Hello World`输出到控制台，因为在赋值`hello =`之前没有声明，因此变量是全局可用的。

> 注意:为了避免意外声明全局变量，你可以使用[严格模式](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja/)。

## 提升和暂时死区

`var`和`let` / `const`的另一个区别是[可变提升](https://www.sitepoint.com/javascript-hoisting/)。变量声明总是在内部被提升(移动)到当前作用域的顶部。这意味着:

```
console.log(hello);
var hello;
hello = "I'm a variable"; 
```

相当于:

```
var hello;
console.log(hello);
hello = "I'm a variable"; 
```

这种行为的一个迹象是两个例子都将`undefined`记录到控制台。如果`var hello;`不总是在顶端，它会抛出一个`ReferenceError`。

这种被称为吊装的行为适用于`var`，也适用于`let` / `const`。如上所述，在声明之前访问一个`var`变量将返回`undefined`，因为这是 JavaScript 在初始化它时分配的值。

但是在声明之前访问一个`let` / `const`变量会抛出一个错误。这是因为它们在代码中声明之前是不可访问的。从进入变量的作用域到到达它们的声明之间的时间被称为时间死区——即变量不可访问的时间。

你可以在文章[揭秘 JavaScript 变量作用域和提升](https://www.sitepoint.com/demystifying-javascript-variable-scope-hoisting/)中阅读更多关于提升的内容。

## 结论

为了减少出错的可能性，您应该尽可能使用`const`和`let`。如果你真的需要使用`var`,那么一定要将声明移动到作用域的顶部，因为这样可以避免不必要的提升行为。

## 分享这篇文章