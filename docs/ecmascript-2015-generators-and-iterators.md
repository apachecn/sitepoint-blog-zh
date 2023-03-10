# ES6 生成器和迭代器:开发者指南

> 原文：<https://www.sitepoint.com/ecmascript-2015-generators-and-iterators/>

ES6 为 JavaScript 语言带来了许多新特性。其中的两个特性，生成器和迭代器，极大地改变了我们在更复杂的前端代码中编写特定函数的方式。

虽然它们彼此配合得很好，但是它们实际上做的事情可能有点令人困惑，所以让我们来看看它们。

## 迭代程序

迭代是编程中的一种常见实践，通常用于循环遍历一组值，或者转换每个值，或者以某种方式使用或保存它以备后用。

在 JavaScript 中，我们总是有类似这样的`for`循环:

```
for (var i = 0; i < foo.length; i++){
  // do something with i
} 
```

但是 ES6 给了我们一个选择:

```
for (const i of foo) {
  // do something with i
} 
```

这可以说是更干净、更容易使用的方式，让我想起了 Python 和 Ruby 之类的语言。但是，关于这种新的迭代，还有一些非常重要的事情需要注意:它允许您直接与数据集的元素进行交互。

假设我们想知道数组中的每个数字是否是质数。我们可以通过设计一个函数来做到这一点。它可能看起来像这样:

```
function isPrime(number) {
  if (number < 2) {
    return false;
  } else if (number === 2) {
    return true;
  }

  for (var i = 2; i < number; i++) {
    if (number % i === 0) {
      return false;
      break;
    }
  }

  return true;
} 
```

不是世界上最好的，但它是有效的。下一步是循环遍历我们的数字列表，用我们闪亮的新函数检查每个数字是否都是质数。这很简单:

```
var possiblePrimes = [73, 6, 90, 19, 15];
var confirmedPrimes = [];

for (var i = 0; i < possiblePrimes.length; i++) {
  if (isPrime(possiblePrimes[i])) {
    confirmedPrimes.push(possiblePrimes[i]);
  }
}

// confirmedPrimes is now [73, 19] 
```

同样，它可以工作，但是它很笨拙，这种笨拙很大程度上取决于 JavaScript 处理`for`循环的方式。但是，在 ES6 中，我们在新的迭代器中获得了一个几乎是 Pythonic 式的选项。所以前面的`for`循环可以写成这样:

```
const possiblePrimes = [73, 6, 90, 19, 15];
const confirmedPrimes = [];

for (const i of possiblePrimes){
   if ( isPrime(i) ){
      confirmedPrimes.push(i);
   }
}

// confirmedPrimes is now [73, 19] 
```

这要干净得多，但最引人注目的是`for`循环。变量`i`现在表示名为`possiblePrimes`的数组中的实际项目。所以，我们不用再用索引来调用它了。这意味着我们可以只调用`i`，而不是在循环中调用`possiblePrimes[i]`。

在幕后，这种迭代正在利用 ES6 明亮闪亮的 [Symbol.iterator()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator) 方法。这个坏男孩负责描述迭代，当被调用时，返回一个 JavaScript 对象，该对象包含循环中的下一个值和一个`done`键，根据循环是否结束，该键可以是`true`或`false`。

如果你对这类细节感兴趣，你可以在杰克·阿奇博尔德写的名为[迭代器将迭代](http://jakearchibald.com/2014/iterators-gonna-iterate/)的精彩博客文章中读到更多。当我们深入到本文的另一个方面:发电机时，它还会让您很好地了解在引擎盖下发生了什么。

## 发电机

生成器，也称为“迭代器工厂”，是一种创建特定迭代的新型 JavaScript 函数。他们给了你特殊的，自定义的方法来循环东西。

好吧，那这意味着什么？让我们看一个例子。假设我们想要一个函数，每次调用它时，它都会给出下一个质数。同样，我们将使用前面的`isPrime`函数来检查一个数是否是质数:

```
function* getNextPrime() {
  let nextNumber = 2;

  while (true) {
    if (isPrime(nextNumber)) {
      yield nextNumber;
    }
    nextNumber++;
  }
} 
```

如果你习惯了 JavaScript，有些东西会看起来有点像巫毒教，但它实际上并不太坏。我们在关键字`function`后面有一个奇怪的星号，但是这只是告诉 JavaScript 我们正在定义一个生成器。

另一个有趣的地方是`yield`关键字。这其实就是你调用的时候一个生成器吐出来的东西。它大致相当于`return`，但是它保持函数的状态，而不是每当你调用它时都重新运行。它在运行时“记得”自己的位置，所以下次你调用它时，它会从停止的地方继续运行。

这意味着我们可以这样做:

```
const nextPrime = getNextPrime(); 
```

然后在我们想要获得下一个质数的时候调用`nextPrime`——你猜对了:

```
console.log(nextPrime.next().value); // 2
console.log(nextPrime.next().value); // 3
console.log(nextPrime.next().value); // 5
console.log(nextPrime.next().value); // 7 
```

您也可以只调用`nextPrime.next()`，这在您的生成器不是无限的情况下很有用，因为它返回一个这样的对象:

```
console.log(nextPrime.next());
// {value: 2, done: false} 
```

这里，`done`键告诉你这个函数是否已经完成了它的任务。在我们的例子中，我们的函数永远不会结束，理论上可以给我们所有的素数直到无穷大(当然，如果我们有那么多的计算机内存)。

## 酷，那么我现在可以使用生成器和迭代器了吗？

虽然 ECMAScript 2015 已经完成，并且已经存在了几年，但浏览器对其功能的支持——特别是生成器——还远远没有完成。如果你真的想使用这些和其他现代功能，你可以看看像 [Babel](https://babeljs.io/) 和 [Traceur](https://github.com/google/traceur-compiler) 这样的 transpilers，它们会将你的 ECMAScript 2015 代码转换成它的等效(在可能的情况下)ECMAScript 5 代码。

也有许多在线编辑器支持 ECMAScript 2015，或者专门关注它，特别是脸书的[再生器](https://facebook.github.io/regenerator/)和 [JS Bin](http://jsbin.com) 。如果你只是想尝试一下，感受一下 JavaScript 现在是如何编写的，这些都值得一看。

## 结论

生成器和迭代器为我们解决 JavaScript 问题的方法提供了很多新的灵活性。迭代器允许我们用更 Pythonic 化的方式编写`for`循环，这意味着我们的代码看起来更干净，更易读。

生成器函数使我们能够编写函数，这些函数能记住你最后一次看到它们时它们在哪里，并能从它们停止的地方恢复。就实际记忆量而言，他们也可以是无限的，这在某些情况下非常方便。

对这些生成器和迭代器的支持很好。除了 Internet Explorer 之外，Node 和所有现代浏览器都支持它们。如果你需要支持旧的浏览器，你最好的选择是使用 transpiler，比如 [Babel](https://babeljs.io/) 。

## 分享这篇文章