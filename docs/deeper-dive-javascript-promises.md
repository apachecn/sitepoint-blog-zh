# JavaScript 承诺概述

> 原文：<https://www.sitepoint.com/deeper-dive-javascript-promises/>

在本教程中，我们将学习如何用 JavaScript 创建和处理承诺。我们将看看链接承诺、错误处理和一些最近添加到语言中的承诺方法。

## JavaScript 承诺是什么？

在 JavaScript 中，有些操作是异步的。这意味着当操作完成时，它们产生的结果或值不是立即可用的。

一个**承诺**是一个特殊的 JavaScript 对象，它表示这种异步操作的最终结果。它充当操作结果的代理。

## 糟糕的过去:回调函数

在 JavaScript 出现之前，处理异步操作的首选方式是使用回调。回调是当异步操作的结果准备好时运行的函数。例如:

```
setTimeout(function() {
  console.log('Hello, World!');
}, 1000); 
```

这里，`setTimeout`是一个异步函数，在指定的毫秒数后运行它所传递的回调函数。在这种情况下，它记录“Hello，World！”一秒钟后发送到控制台。

现在，假设我们想在五秒钟内每秒记录一条消息。看起来会像这样:

```
setTimeout(function() {
  console.log(1);
  setTimeout(function() {
    console.log(2);
    setTimeout(function() {
      console.log(3);
      setTimeout(function() {
        console.log(4);
        setTimeout(function() {
          console.log(5);
        }, 1000);
      }, 1000);
    }, 1000);
  }, 1000);
}, 1000); 
```

以这种方式使用多个嵌套回调的异步 JavaScript 既容易出错又难以维护。经常被称为 [**回调地狱**](https://www.sitepoint.com/saved-from-callback-hell/) ，甚至还有[自己的网页](http://callbackhell.com/)。

诚然，这是一个人为的例子，但它足以说明这一点。在现实场景中，我们可能会进行 Ajax 调用，用结果更新 DOM，然后等待动画完成。或者，我们的服务器可能从客户端接收输入，验证输入，更新数据库，写入日志文件，最后发送响应。在这两种情况下，我们还需要处理发生的任何错误。

使用嵌套回调来完成这样的任务会很痛苦。幸运的是，promises 为我们提供了更清晰的语法，使我们能够链接异步命令，使它们一个接一个地运行。

## 如何创建 JavaScript Promise 对象

创建承诺的基本语法如下:

```
const promise = new Promise((resolve, reject) => {
  //asynchronous code goes here
}); 
```

我们首先使用`Promise`构造函数实例化一个新的 promise 对象，并传递给它一个回调函数。回调使用两个参数，`resolve`和`reject`，这两个参数都是函数。我们所有的异步代码都放在回调函数中。

如果一切运行成功，通过调用`resolve`来实现承诺。如果出现错误，通过调用`reject`拒绝承诺。我们可以将值传递给这两个方法，然后它们将在消费代码中可用。

要了解这在实践中是如何工作的，请考虑下面的代码。这会向 web 服务发出一个异步请求，返回一个 JSON 格式的随机 dad 笑话:

```
const promise = new Promise((resolve, reject) => {
  const request = new XMLHttpRequest();
  request.open('GET', 'https://icanhazdadjoke.com/');
  request.setRequestHeader('Accept', 'application/json');

  request.onload = () => {
    if (request.status === 200) {
      resolve(request.response); // we got data here, so resolve the Promise
    } else {
      reject(Error(request.statusText)); // status is not 200 OK, so reject
    }
  };

  request.onerror = () => {
    reject(Error('Error fetching data.')); // error occurred, reject the  Promise
  };

  request.send(); // send the request
}); 
```

### 承诺建造者

我们首先使用`Promise`构造函数创建一个新的 promise 对象。构造函数用于包装还不支持承诺的函数或 API，比如上面的`XMLHttpRequest`对象。传递给 promise 构造函数的回调包含用于从远程服务获取数据的异步代码。(注意，我们在这里使用了一个[箭头函数](https://www.sitepoint.com/arrow-functions-javascript/)。)在回调函数中，我们创建了一个对 https://icanhazdadjoke.com/[的 Ajax 请求，它以 JSON 格式返回一个随机的爸爸笑话。](https://icanhazdadjoke.com/)

当从远程服务器收到一个成功的响应时，它被传递给`resolve`方法。在任何错误发生的情况下——无论是在服务器上还是在网络级别上——用一个`Error`对象调用`reject`。

### `then`法

当我们实例化一个 promise 对象时，我们得到了一个将来可用的数据代理。在我们的例子中，我们希望从远程服务返回一些数据。那么，我们如何知道数据何时可用呢？这就是使用`Promise.then()`功能的地方:

```
const promise = new Promise((resolve, reject) => { ... });

promise.then((data) => {
  console.log('Got data! Promise fulfilled.');
  document.body.textContent = JSON.parse(data).joke;
}, (error) => {
  console.error('Promise rejected.');
  console.error(error.message);
}); 
```

这个函数可以接受两个参数:一个成功回调和一个失败回调。这些回调在承诺完成(即完成或拒绝)时被调用。如果承诺实现了，将使用我们传递给`resolve`的实际数据触发成功回调。如果承诺被拒绝，将调用失败回调。我们传递给`reject`的任何内容都将作为参数传递给这个回调函数。

我们可以在下面的 CodePen 演示中试用这段代码。要查看一个新的随机笑话，点击嵌入页面右下角的*重新运行*按钮。

参见笔 [JavaScript Promises(1)](https://codepen.io/SitePoint/pen/zYjVdqY)by site point([@ site point](https://codepen.io/SitePoint))
上 [CodePen](https://codepen.io) 。