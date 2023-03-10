# JavaScript 中有哪些回调？

> 原文：<https://www.sitepoint.com/callbacks-javascript/>

**当你开始学习 JavaScript 的时候，用不了多久你就会听到“回调函数”这个名词。回调是 JavaScript 执行模型不可或缺的一部分，很好地理解它们是什么以及它们是如何工作的非常重要。**

1.  [什么是 JavaScript 回调？](#whatarejavascriptcallbacks)
2.  为什么我们需要回调函数？
    *   JavaScript 是一种事件驱动语言
    *   [一阶和高阶函数](#firstclassandhigherorderfunctions)
3.  [如何创建回调函数](#howtocreateacallbackfunction)
4.  [不同种类的回调函数](#differentkindsofcallbackfunctions)
    *   [匿名函数](#anonymousfunctions)
    *   [箭头功能](#arrowfunctions)
    *   [命名函数](#namedfunctions)
5.  【JavaScript 回调函数的常见用例
    *   [数组方法](#arraymethods)
    *   [Node.js](#nodejs)
6.  [同步与异步回调](#synchronousvsasynchronouscallbacks)
    *   [同步回调函数](#synchronouscallbackfunctions)
    *   [异步回调函数](#asynchronouscallbackfunctions)
7.  [使用回调时需要注意的事项](#thingstobeawareofwhenusingcallbacks)
    *   [小心 JavaScript 回调地狱](#bewareofjavascriptcallbackhell)
    *   [更喜欢现代的流量控制方法](#prefermoremodernmethodsofflowcontrol)

## 什么是 JavaScript 回调？

在 JavaScript 中，**回调**是作为参数传递给第二个函数的函数。接收回调的函数决定是否以及何时执行回调:

```
function myFunction(callback) {
  // 1\. Do something
  // 2\. Then execute the callback
  callback()
}

function myCallback() {
  // Do something else
}

myFunction(myCallback); 
```

在上面的例子中，我们有两个函数:`myFunction`和`myCallback`。顾名思义，`myCallback`是作为回调函数使用的，我们把它作为参数传递给`myFunction`。`myFunction`可以在准备好的时候执行回调。

很多博客文章会说回调之所以被称为回调，是因为你在告诉某个函数，当它准备好了一个答案的时候给你回电。一个不太容易混淆的名字应该是“callafter”:也就是说，在完成所有其他事情之后调用这个函数。

## 为什么我们需要回调函数？

你会经常听到有人说 JavaScript 是**单线程**。这意味着它一次只能做一件事。当执行缓慢的操作时——比如从远程 API 获取数据——这可能会有问题。如果你的程序在数据返回之前一直处于冻结状态，那就不会有好的用户体验。

JavaScript 避免这个瓶颈的方法之一是使用回调。我们可以将第二个函数作为参数传递给负责数据获取的函数。然后开始数据获取请求，但是 JavaScript 解释器没有等待响应，而是继续执行程序的其余部分。当从 API 接收到响应时，回调函数被执行，并且可以对结果做一些事情:

```
function fetchData(url, cb) {
  // 1\. Make API request to url
  // 2\. If response successful, execute callback
  cb(res);
}

function callback(res) {
  // Do something with results
}

// Do something
fetchData('https://sitepoint.com', callback);
// Do something else 
```

### JavaScript 是一种事件驱动的语言

你还会听到有人说 JavaScript 是一种**事件驱动语言**。这意味着它可以侦听和响应事件，同时继续执行进一步的代码，而不会阻塞它的单个线程。

它是怎么做到的呢？你猜对了:复试。

想象一下，如果您的程序将一个事件监听器附加到一个按钮上，然后坐在那里等待某人点击该按钮，同时拒绝做任何其他事情。那就太棒了！

使用回调，我们可以指定应该运行某个代码块来响应特定事件:

```
function handleClick() {
  // Do something (e.g. validate a form)
  // in response to the user clicking a button
}

document.querySelector('button').addEventListener('click', handleClick); 
```

在上面的例子中，`handleClick`函数是一个回调函数，它被执行以响应网页上发生的动作(点击按钮)。

使用这种方法，我们可以对尽可能多的事件做出反应，同时让 JavaScript 解释器自由地继续做它需要做的任何事情。

### 一阶和高阶函数

在学习回调的时候，你可能会遇到的另外两个术语是“一级函数”和“高阶函数”。这些听起来很可怕，但实际上并不可怕。

当我们说 JavaScript 支持**一级函数**时，这意味着我们可以像对待常规值一样对待函数。我们可以将它们存储在一个变量中，我们可以从另一个函数中返回它们，正如我们已经看到的，我们可以将它们作为参数传递。

至于**高阶函数**，它们只是简单的函数，要么接受一个函数作为参数，要么返回一个函数作为结果。有几个原生 JavaScript 函数也是高阶函数，比如`setTimeout`。让我们用它来演示如何创建和运行回调。

## 如何创建回调函数

模式同上:创建一个回调函数，并将其作为参数传递给高阶函数:

```
function greet() {
  console.log('Hello, World!');
}

setTimeout(greet, 1000); 
```

`setTimeout`函数延迟一秒执行`greet`函数，并记录“Hello，World！”到控制台。

*注意:如果你对`setTimeout`不熟悉，可以看看我们流行的 [setTimeout JavaScript 函数:示例指南](https://www.sitepoint.com/javascript-settimeout-function-examples/)。*

我们也可以把它变得稍微复杂一点，给`greet`函数传递一个需要问候的人的名字:

```
function greet(name) {
  console.log(`Hello, ${name}!`);
}

setTimeout(() => greet('Jim'), 1000); 
```

注意，我们使用了一个箭头函数来包装对`greet`的原始调用。如果我们没有这样做，函数将会立即执行，而不是延迟一段时间。

如您所见，在 JavaScript 中创建回调有多种方式，这很好地将我们带到了下一节。

## 不同种类的回调函数

部分归功于 JavaScript 对一级函数的支持，在 JavaScript 中有各种声明函数的方法，因此也有各种在回调中使用它们的方法。

我们现在来看看这些，考虑一下它们的优缺点。

### 匿名函数

到目前为止，我们已经命名了我们的函数。这通常被认为是很好的实践，但绝不是强制性的。考虑下面的例子，它使用回调函数来验证一些表单输入:

```
document.querySelector('form').addEventListener('submit', function(e)  {
  e.preventDefault();
  // Do some data validation
  // If everything looks ok, then...
  this.submit();
}); 
```

如您所见，回调函数是未命名的。没有名字的函数定义被称为**匿名函数**。匿名函数适用于只在一个地方调用函数的短脚本。而且，因为它们是内联声明的，所以它们也可以访问其父级的作用域。

### 箭头功能

ES6 引入了箭头功能。由于它们的语法简洁，并且它们有一个隐式返回值，所以它们通常用于执行简单的一行程序，如下面的示例，该示例从数组中过滤重复值:

```
const arr = [1, 2, 2, 3, 4, 5, 5];
const unique = arr.filter((el, i) => arr.indexOf(el) === i);
// [1, 2, 3, 4, 5] 
```

但是要注意，它们不绑定自己的`this`值，而是从它们的父作用域继承。这意味着，在前面的例子中，我们不能使用箭头函数来提交表单:

```
document.querySelector('form').addEventListener('submit', (e) => {
  ...
  // Uncaught TypeError: this.submit is not a function
  // `this` points to the window object, not to the form
  this.submit();
}); 
```

箭头函数是我近年来最喜欢的 JavaScript 新增功能之一，开发人员肯定应该熟悉它们。如果你想了解更多关于箭头函数的信息，请查看我们的 JavaScript 中的[箭头函数:如何使用 Fat &简明语法](https://www.sitepoint.com/arrow-functions-javascript/)教程。

### 命名函数

JavaScript 中创建命名函数主要有两种方式:[函数表达式和函数声明](https://www.sitepoint.com/when-to-use-a-function-expression-vs-function-declaration/)。两者都可以和回调一起使用。

**函数声明**包括使用`function`关键字创建一个函数并给它命名:

```
function myCallback() {... }
setTimeout(myCallback, 1000); 
```

**函数表达式**包括创建一个函数并将其赋给一个变量:

```
const myCallback = function() { ... };
setTimeout(myCallback, 1000); 
```

或者:

```
const myCallback = () => { ... };
setTimeout(myCallback, 1000); 
```

我们还可以标记用关键字`function`声明的匿名函数:

```
setTimeout(function myCallback()  { ... }, 1000); 
```

以这种方式命名或标记回调函数的好处是有助于调试。让我们让函数抛出一个错误:

```
setTimeout(function myCallback() { throw new Error('Boom!'); }, 1000);

// Uncaught Error: Boom!
// myCallback  file:///home/jim/Desktop/index.js:18
// setTimeout handler*  file:///home/jim/Desktop/index.js:18 
```

使用一个命名函数，我们可以看到错误发生的确切位置。但是，看看当我们删除名称时会发生什么:

```
setTimeout(function() { throw new Error('Boom!'); }, 1000);

// Uncaught Error: Boom!
// <anonymous>  file:///home/jim/Desktop/index.js:18
// setTimeout handler*  file:///home/jim/Desktop/index.js:18 
```

在这个小而独立的例子中，这没什么大不了的，但是随着代码库的增长，这是需要注意的。甚至有一个 [ESLint 规则](https://eslint.org/docs/latest/rules/func-names)来强制这种行为。

## JavaScript 回调函数的常见用例

JavaScript 回调函数的用例非常广泛且多样。正如我们所见，它们在处理异步代码(比如 Ajax 请求)和响应事件(比如表单提交)时非常有用。现在，让我们再看几个可以找到回调的地方。

### 数组方法

您遇到回调的另一个地方是在 JavaScript 中使用数组方法时。随着您在编程过程中的进步，您会越来越多地做这件事。例如，假设您想要对一个数组中的所有数字求和，请考虑这个简单的实现:

```
const arr = [1, 2, 3, 4, 5];
let tot = 0;
for(let i=0; i<arr.length; i++) {
  tot += arr[i];
}
console.log(tot); //15 
```

虽然这种方法可行，但一个更简洁的实现可能会使用`Array.reduce`,您猜对了，它使用回调对数组中的所有元素执行操作:

```
const arr = [1, 2, 3, 4, 5];
const tot = arr.reduce((acc, el) => acc + el);
console.log(tot);
// 15 
```

### 节点. js

还应该注意的是， [Node.js](https://www.sitepoint.com/an-introduction-to-node-js/) 及其整个生态系统严重依赖基于回调的代码。例如，下面是规范 Hello，World 的节点版本！示例:

```
const http = require('http');

http.createServer((request, response) => {
  response.writeHead(200);
  response.end('Hello, World!');
}).listen(3000);

console.log('Server running on http://localhost:3000'); 
```

无论您是否曾经使用过 Node，这段代码现在应该很容易理解。本质上，我们需要节点的`http`模块并调用它的`createServer`方法，我们向它传递一个匿名箭头函数。每当节点在端口 3000 上接收到请求时，就会调用这个函数，它会以 200 状态和文本“Hello，World！”

Node 还实现了一种称为**错误优先回调**的模式。这意味着回调的第一个参数是为错误对象保留的，回调的第二个参数是为任何成功的响应数据保留的。

以下是 Node 文档中显示如何读取文件的示例:

```
const fs = require('fs');
fs.readFile('/etc/hosts', 'utf8', function (err, data) {
  if (err) {
    return console.log(err);
  }
  console.log(data);
}); 
```

在本教程中，我们不想深入研究 Node，但希望这种代码现在应该更容易阅读。

## 同步与异步回调

回调是同步执行还是异步执行取决于调用它的函数。让我们看几个例子。

### 同步回调函数

当代码是**同步**时，它从上到下逐行运行。操作一个接一个地发生，每个操作等待前一个操作完成。我们已经在上面的`Array.reduce`函数中看到了一个同步回调的例子。

为了进一步说明这一点，这里有一个使用`Array.map`和`Array.reduce`来计算逗号分隔数字列表中最大数字的演示:

见笔 [回归基础:JavaScript 中什么是回调函数？(1)](https://codepen.io/SitePoint/pen/zYaZwrV)by site point([@ site point](https://codepen.io/SitePoint))
on[code pen](https://codepen.io)。