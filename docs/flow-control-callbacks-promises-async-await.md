# 现代 JS 中的流控制:对 Async/Await 承诺的回调

> 原文：<https://www.sitepoint.com/flow-control-callbacks-promises-async-await/>

**JavaScript 经常被宣称是*异步*。那是什么意思？对发展有什么影响？这种方法近年来有什么变化？**

考虑以下代码:

```
result1 = doSomething1();
result2 = doSomething2(result1); 
```

大多数语言同步处理每一行*。第一行运行并返回结果。一旦第一条生产线完成*，第二条生产线就会运行，而不管需要多长时间*。*

 *## 单线程处理

JavaScript 在单个处理线程上运行。当在浏览器选项卡中执行时，其他所有操作都会停止。这是必要的，因为对页面 DOM 的更改不能发生在并行线程上；让一个线程重定向到不同的 URL，而另一个线程试图追加子节点，这是很危险的。

这对用户来说很少是显而易见的，因为处理在小块中进行得很快。例如，JavaScript 检测按钮点击，运行计算，并更新 DOM。完成后，浏览器可以自由处理队列中的下一个项目。

(旁注:PHP 等其他语言也使用单线程，但可能由 Apache 等多线程服务器管理。同时对同一个 PHP 页面的两个请求可以启动两个线程运行 PHP 运行时的独立实例。)

## 与回调异步进行

单线程引发了一个问题。当 JavaScript 调用一个“慢”进程比如浏览器中的 Ajax 请求或者服务器上的数据库操作时会发生什么？这个操作可能需要几秒钟甚至几分钟。浏览器在等待响应时会被锁定。在服务器上，Node.js 应用程序将无法处理进一步的用户请求。

解决方案是异步处理。不是[等待完成](https://www.sitepoint.com/delay-sleep-pause-wait/)，而是告诉进程在结果准备好时调用另一个函数。这被称为*回调*，它作为参数传递给任何异步函数。例如:

```
doSomethingAsync(callback1);
console.log('finished');

// call when doSomethingAsync completes
function callback1(error) {
  if (!error) console.log('doSomethingAsync complete');
} 
```

接受回调函数作为参数(只传递对该函数的引用，所以开销很小)。`doSomethingAsync()`用多长时间不重要；我们只知道`callback1()`会在未来的某个时候被处决。控制台将显示:

```
finished
doSomethingAsync complete 
```

你可以在[回到基础:JavaScript 中的回调是什么？](https://www.sitepoint.com/callbacks-javascript/)

### 回调地狱

通常，回调只能由一个异步函数调用。因此，可以使用简洁、匿名的内联函数:

```
doSomethingAsync(error => {
  if (!error) console.log('doSomethingAsync complete');
}); 
```

一系列的两个或多个异步调用可以通过嵌套回调函数来串行完成。例如:

```
async1((err, res) => {
  if (!err) async2(res, (err, res) => {
    if (!err) async3(res, (err, res) => {
      console.log('async1, async2, async3 complete.');
    });
  });
}); 
```

不幸的是，这引入了**回调地狱**——一个臭名昭著的概念，甚至[都有自己的网页](http://callbackhell.com/)！代码很难阅读，当添加错误处理逻辑时，情况会变得更糟。

回调地狱在客户端编码中比较少见。如果您正在进行 Ajax 调用，更新 DOM 并等待动画完成，它可能会深入两到三个层次，但它通常是可管理的。

在操作系统或服务器进程上，情况有所不同。Node.js API 调用可以接收文件上传，更新多个数据库表，写入日志，并在发送响应之前进行进一步的 API 调用。

你可以在[中阅读更多关于回调地狱的内容，从回调地狱](https://www.sitepoint.com/saved-from-callback-hell/)中保存。

## 承诺

[ES2015 (ES6)推出承诺](https://www.sitepoint.com/overview-javascript-promises/)。回调仍然在表面之下使用，但是 Promises 提供了一个更清晰的语法，即*链接*异步命令，使它们串行运行(在[的下一节](#asynchronouschaining)中有更多相关内容)。

要启用基于承诺的执行，必须更改基于异步回调的函数，以便它们立即返回一个承诺对象。该对象*承诺*在未来某个时候运行两个函数(作为参数传递)中的一个:

*   **`resolve`** :处理成功完成时运行的回调函数
*   **`reject`** :发生故障时运行的可选回调函数。

在下面的例子中，一个数据库 API 提供了一个接受回调函数的`connect()`方法。一旦连接建立或失败，外部的`asyncDBconnect()`函数立即返回新的承诺并运行`resolve()`或`reject()`:

```
const db = require('database');

// connect to database
function asyncDBconnect(param) {

  return new Promise((resolve, reject) => {

    db.connect(param, (err, connection) => {
      if (err) reject(err);
      else resolve(connection);
    });

  });

} 
```

Node.js 8.0+提供了一个 [util.promisify()实用程序](https://nodejs.org/api/util.html#util_util_promisify_original)，用于将基于回调的函数转换为基于承诺的替代函数。有几个条件:

1.  回调必须作为最后一个参数传递给异步函数，并且
2.  回调函数必须预期一个错误，后跟一个值参数。

示例:

```
// Node.js: promisify fs.readFile
const
  util = require('util'),
  fs = require('fs'),
  readFileAsync = util.promisify(fs.readFile);

readFileAsync('file.txt'); 
```

各种客户端库也提供 promisify 选项，但是您可以自己用几行代码创建一个:

```
// promisify a callback function passed as the last parameter
// the callback function must accept (err, data) parameters
function promisify(fn) {
  return function() {
      return new Promise(
        (resolve, reject) => fn(
          ...Array.from(arguments),
        (err, data) => err ? reject(err) : resolve(data)
      )
    );
  }
}

// example
function wait(time, callback) {
  setTimeout(() => { callback(null, 'done'); }, time);
}

const asyncWait = promisify(wait);

ayscWait(1000); 
```

### 异步链接

任何返回承诺的东西都可以启动一系列在`.then()`方法中定义的异步函数调用。每个都被传递了来自前一个`resolve`的结果:

```
asyncDBconnect('http://localhost:1234')
  .then(asyncGetSession)      // passed result of asyncDBconnect
  .then(asyncGetUser)         // passed result of asyncGetSession
  .then(asyncLogAccess)       // passed result of asyncGetUser
  .then(result => {           // non-asynchronous function
    console.log('complete');  //   (passed result of asyncLogAccess)
    return result;            //   (result passed to next .then())
  })
  .catch(err => {             // called on any reject
    console.log('error', err);
  }); 
```

同步功能也可以在`.then()`块中执行。返回值被传递给下一个`.then()`(如果有的话)。

`.catch()`方法定义了一个函数，该函数在任何先前的`reject`被触发时被调用。在这一点上，没有进一步的`.then()`方法将被运行。您可以在整个链中使用多个`.catch()`方法来捕获不同的错误。

ES2018 引入了一种`.finally()`方法，不管结果如何，它都会运行任何最终逻辑，例如，清理、关闭数据库连接等。目前只有 Chrome 和 Firefox 支持它，但是技术委员会 39 已经发布了一个[。终于()聚划算](https://github.com/tc39/proposal-promise-finally/blob/fd934c0b42d59bf8d9446e737ba14d50a9067216/polyfill.js)。

```
function doSomething() {
  doSomething1()
  .then(doSomething2)
  .then(doSomething3)
  .catch(err => {
    console.log(err);
  })
  .finally(() => {
    // tidy-up here!
  });
} 
```

### 使用 Promise.all()的多个异步调用

Promise `.then()`方法一个接一个运行异步函数。如果顺序不重要——例如，初始化不相关的组件——那么同时启动所有异步函数并在最后一个(最慢的)函数运行`resolve`时结束会更快。

这可以用`Promise.all()`来实现。它接受一组函数并返回另一个承诺。例如:

```
Promise.all([ async1, async2, async3 ])
  .then(values => {           // array of resolved values
    console.log(values);      // (in same order as function array)
    return values;
  })
  .catch(err => {             // called on any reject
    console.log('error', err);
  }); 
```

如果任何一个异步函数调用`reject`，则`Promise.all()`立即终止。

### 使用 Promise.race()的多个异步调用

`Promise.race()`与`Promise.all()`类似，只是在*第一个*承诺解决或拒绝后，会立即解决或拒绝。只有最快的基于承诺的异步函数才能完成:

```
Promise.race([ async1, async2, async3 ])
  .then(value => {            // single value
    console.log(value);
    return value;
  })
  .catch(err => {             // called on any reject
    console.log('error', err);
  }); 
```

### 前途无量？

承诺减少了回拨的麻烦，但也带来了自身的问题。

教程经常没有提到*整个承诺链是异步的*。任何使用一系列承诺的函数要么返回自己的承诺，要么在最终的`.then()`、`.catch()`或`.finally()`方法中运行回调函数。

我也有一个表白:*承诺困惑了我很久*。语法通常看起来比回调更复杂，有很多地方会出错，调试也会有问题。然而，学习基础知识是很重要的。

其他有前途资源:

*   [MDN 承诺文件](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
*   JavaScript 承诺:简介
*   [JavaScript 承诺…邪恶的细节](http://www.mattgreer.org/articles/promises-in-wicked-detail/)
*   [承诺异步编程](http://exploringjs.com/es6/ch_promises.html)

## 异步/等待

承诺可能会令人生畏，所以 [ES2017](https://www.sitepoint.com/es2017-whats-new/) 推出了`async`和`await`。虽然它可能只是语法上的糖，但它让承诺更加甜蜜，你可以完全避免`.then()`链。考虑以下基于承诺的示例:

```
function connect() {

  return new Promise((resolve, reject) => {

    asyncDBconnect('http://localhost:1234')
      .then(asyncGetSession)
      .then(asyncGetUser)
      .then(asyncLogAccess)
      .then(result => resolve(result))
      .catch(err => reject(err))

  });
}

// run connect (self-executing function)
(() => {
  connect();
    .then(result => console.log(result))
    .catch(err => console.log(err))
})(); 
```

使用`async` / `await`重新编写:

1.  外部函数前面必须有一个`async`语句，并且
2.  对基于异步承诺的函数的调用必须以`await`开头，以确保处理在下一个命令执行之前完成。

```
async function connect() {

  try {
    const
      connection = await asyncDBconnect('http://localhost:1234'),
      session = await asyncGetSession(connection),
      user = await asyncGetUser(session),
      log = await asyncLogAccess(user);

    return log;
  }
  catch (e) {
    console.log('error', err);
    return null;
  }

}

// run connect (self-executing async function)
(async () => { await connect(); })(); 
```

有效地使每个调用看起来像是同步的，同时不会阻碍 JavaScript 的单个处理线程。此外，`async`函数总是返回一个承诺，因此它们可以被其他`async`函数调用。

`async` / `await`代码可能不会更短，但有相当大的好处:

1.  语法更清晰。括号少了，出错的也少了。
2.  调试更容易。可以在任何`await`语句上设置断点。
3.  错误处理更好。`try` / `catch`块可以像同步码一样使用。
4.  支持是好的。在所有浏览器(IE 和 Opera Mini 除外)和 Node 7.6+中都有实现。

也就是说，并非一切都是完美的…

### 承诺，承诺

`async` / `await`还是靠承诺，承诺最终靠回调。你需要理解承诺是如何工作的，没有直接等同于`Promise.all()`和`Promise.race()`的东西。很容易忘记`Promise.all()`，这比使用一系列不相关的`await`命令更有效率。

### 异步在同步循环中等待

在某个时候，你会尝试在同步循环中调用一个异步函数。例如:

```
async function process(array) {
  for (let i of array) {
    await doSomething(i);
  }
} 
```

没用的。这也不会:

```
async function process(array) {
  array.forEach(async i => {
    await doSomething(i);
  });
} 
```

循环本身保持同步，并且总是在其内部异步操作之前完成。

ES2018 引入了异步迭代器，除了`next()`方法返回一个承诺之外，它就像常规迭代器一样。因此，`await`关键字可以和`for … of`循环一起使用，以连续运行异步操作。例如:

```
async function process(array) {
  for await (let i of array) {
    doSomething(i);
  }
} 
```

然而，在异步迭代器实现之前，最好的方法可能是用`map`将项目数组到`async`函数中，然后用`Promise.all()`运行它们。例如:

```
const
  todo = ['a', 'b', 'c'],
  alltodo = todo.map(async (v, i) => {
    console.log('iteration', i);
    await processSomething(v);
});

await Promise.all(alltodo); 
```

这有并行运行任务的好处，但是不可能将一次迭代的结果传递给另一次迭代，并且映射大型数组的计算开销可能很大。

### 尝试/捕捉丑陋

如果您在任何失败的`await`两边省略了`try` / `catch`，功能将会自动退出。如果你有一长串异步`await`命令，你可能需要多个`try` / `catch`模块。

一个替代方案是高阶函数，它捕捉错误，因此`try` / `catch`块变得不必要(感谢 [@wesbos](https://twitter.com/wesbos/status/911309291545559041) 的建议):

```
async function connect() {

  const
    connection = await asyncDBconnect('http://localhost:1234'),
    session = await asyncGetSession(connection),
    user = await asyncGetUser(session),
    log = await asyncLogAccess(user);

  return true;
}

// higher-order function to catch errors
function catchErrors(fn) {
  return function (...args) {
    return fn(...args).catch(err => {
      console.log('ERROR', err);
    });
  }
}

(async () => {
  await catchErrors(connect)();
})(); 
```

但是，在应用程序必须以不同于其他应用程序的方式对某些错误做出反应的情况下，此选项可能不实用。

尽管有一些缺陷，`async` / `await`是 JavaScript 的一个优雅的补充。更多资源:

*   MDN [异步](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)和[等待](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)
*   [异步功能——友好地做出承诺](https://developers.google.com/web/fundamentals/primers/async-functions)
*   [TC39 异步功能规范](https://tc39.github.io/ecmascript-asyncawait/)
*   [用异步函数简化异步编码](https://www.sitepoint.com/simplifying-asynchronous-coding-async-functions/)

## JavaScript 之旅

异步编程是 JavaScript 中无法避免的挑战。回调在大多数应用程序中是必不可少的，但是很容易陷入深度嵌套的函数中。

承诺抽象回调，但是有许多语法陷阱。转换现有的功能可能是一件苦差事，而且链看起来仍然很乱。

幸运的是，`async` / `await`提供了清晰度。代码看起来是同步的，但它不能独占单个处理线程。它将改变你编写 JavaScript 的方式，甚至会让你感激承诺——如果你以前没有的话！

## 分享这篇文章*