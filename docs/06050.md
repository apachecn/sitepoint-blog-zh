# JavaScript 生成器和防止回调地狱

> 原文：<https://www.sitepoint.com/javascript-generators-preventing-callback-hell/>

Node.js 是构建 I/O 密集型 web 应用程序的最佳技术之一。JavaScript 的单线程特性为这类应用程序提供了许多优于其他编程语言中基于线程的执行模型的优势。但是，这些优势是有代价的。由于[回调地狱](http://callbackhell.com/)和异步错误处理，编写大型节点应用程序可能很困难。

有许多解决方案可以克服这些问题。[纤维](https://github.com/laverdet/node-fibers)和[承诺](http://promisesaplus.com/)就是其中的两个。一些开发人员更喜欢使用这些解决方案，但这完全取决于个人偏好。即将发布的 ECMAScript 版本 6 也引入了生成器作为回调地狱的解决方案。本文为您提供了关于生成器的适当介绍，并展示了如何使用它们来解决上述问题。

## 先决条件

正如我提到的，生成器是 JavaScript 中的一个新特性。Node (0.10.x)的当前稳定版本不包括生成器。所以，我们需要安装当前不稳定的 [0.11.x](http://nodejs.org/dist/v0.11.9/) 版本，以便使用发电机。一旦安装了 0.11.x，就可以通过将`--harmony-generators`标志传递给 Node 来启用生成器，如下所示。

`node --harmony-generators <filename.js>`

## 发电机 101

简单地说，生成器是一种充当迭代器的函数(注意下面代码示例中的`*`)。生成器可以包含任何有效的 JavaScript 代码。让我们编写我们的第一个生成器(如下所示)。

```
function* HelloGen() {
  yield 100;
  yield 400;
}

var gen = HelloGen();

console.log(gen.next()); // {value: 100, done: false}
console.log(gen.next()); // {value: 400, done: false}
console.log(gen.next()); // {value: undefined, done: true}
```

`yield`是一个特殊的关键字，它从生成器中发出一个新项目。我们可以使用`next()`从生成器中获取值。一旦我们到达迭代器的末尾，返回的对象将包含`done: true`。任何数据类型都可以`yield` ed，包括函数、数字、数组、对象。

值也可以传递给生成器，如下所示。

```
function* HelloGen2() {
  var a = yield 100;
  var b = yield a + 100;

  console.log(b);
}

var gen2 = HelloGen2();

console.log(gen2.next());     // {value: 100, done: false}
console.log(gen2.next(500));  // {value: 600, done: false}
console.log(gen2.next(1000)); // {value: undefined, done: true}
// prints 1000
```

## 防止回调地狱

那么，如何使用生成器来避免回调地狱呢？首先，您需要理解一个简单的技术，我们将在生成器中大量使用它来编写没有回调的代码。

### 理解思维

thunk 是一个部分求值的函数，它接受单个回调作为参数。在生成器中，我们将放弃 thunks 来编写没有回调的程序。下面显示了一个简单的 thunk。

```
function(callback) {
  fs.readFile('myfile.md', 'utf8', callback);
}
```

Thunks 也可以动态创建，如下所示。

```
function readFile(filename) {
  return function(callback) {
    fs.readFile(filename, 'utf8', callback);
  };
}
```

### 使用`co`

[`co`](https://github.com/visionmedia/co) 是一个很好的模块，它有助于结合使用 thunks 和 generators 来创建没有回调的 Node.js 应用程序。稍后我将向您展示它的内部工作原理。现在让我们试试`co`，它可以使用命令`npm install co`安装。下面显示了一个简单的应用程序，它使用了前面例子中的`co`和`readFile()` thunk。

```
var co = require('co');

co(function* () {
  var file1 = yield readFile('file1.md');
  var file2 = yield readFile('file2.md');

  console.log(file1);
  console.log(file2);
})();
```

如你所见，我们不再使用回调了。这为我们提供了一种简单的方法来轻松编写大型模块化节点应用程序。

#### `co`如何在内部工作

你可能想知道`co`内部是如何工作的。这就是它的神奇之处。

*   首先，它调用`next(null)`并得到一个 thunk。
*   然后，它评估 thunk 并保存结果。
*   然后，它调用`next(savedResult)`。
*   重复这些步骤，直到`next()`返回`{done: true}`。

如果你喜欢样本代码，这里有一个最小版本的`co`向你展示它是如何在内部工作的。`co`比这更复杂，因为它能更好地处理错误，并且支持承诺。

```
function co(generator) {
  var gen = generator();

  function nextItem(err, result) {
    var item = gen.next(result);

    if (!item.done) {
      item.value(nextItem);
    }
  }

  nextItem();
}
```

#### 可与`co`一起使用的模块

`co`可用于任何使用 thunks 的模块。不幸的是，目前利用 thunks 的模块并不多。你可以在这里看到完整的支持列表[。使用类似](https://github.com/visionmedia/co/wiki) [`thu`](https://github.com/arunoda/node-thu) 和 [`thunkify`](https://github.com/visionmedia/node-thunkify) 的简单实用程序，您可以将任何节点模块包装为 thunks，以便与`co`一起使用。

## 结论

发电机是相当新的，通常不可用。然而，节点社区似乎表现出了很大的兴趣。最好的例子之一就是 [Koa](http://koajs.com/) 的发布。这是一个生成器友好的 Express 克隆，由构建 Express 的同一个团队构建。我相信随着时间的推移，社区会越来越支持发电机。

## 分享这篇文章