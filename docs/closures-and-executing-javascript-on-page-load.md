# 闭包和在页面加载时执行 JavaScript

> 原文：<https://www.sitepoint.com/closures-and-executing-javascript-on-page-load/>

在我的另一个博客上，我刚刚[发表了一项新技术](http://simon.incutio.com/archive/2004/05/26/addLoadEvent)，用于在页面加载完成后执行一段 JavaScript。代码如下:

```
 function addLoadEvent(func) {
  var oldonload = window.onload;
  if (typeof window.onload != 'function') {
    window.onload = func;
  } else {
    window.onload = function() {
      oldonload();
      func();
    }
  }
}

addLoadEvent(nameOfSomeFunctionToRunOnPageLoad);
addLoadEvent(function() {
  /* more code to run on page load */ 
}); 

```

我的[另一篇文章](http://simon.incutio.com/archive/2004/05/26/addLoadEvent)介绍了为什么需要这种技术，并解释了它是如何工作的，但是我想在这里通过谈论上面的代码使用一种被称为*闭包*的 JavaScript 语言特性的方式来扩展这一点。

闭包由一个函数和定义它的词法环境(一组可用变量)组成。这是一个非常强大的概念，也是 JavaScript 等函数式编程语言中常见的概念。下面是一个简单的闭包例子:

```
 function createAdder(x) {
  return function(y) {
    return y + x;
  }
}

addThree = createAdder(3);
addFour = createAdder(4);

document.write('10 + 3 is ' + addThree(10) + '
');
document.write('10 + 4 is ' + addFour(10)); 
```

```
createAdder(x) is a function that returns a function. In JavaScript, functions are first-class objects: they can be passed to other functions as arguments and returned from functions as well. In this case, the function returned is itself a function that takes an argument and adds something to it.神奇之处在于:createAdder()返回的函数是一个闭包。它“记得”它被创造的环境。如果您将整数 3 传递给`createAdder` ,您将得到一个函数，该函数会将 3 添加到它的参数中。如果你传递 4，你会得到一个加 4 的函数。上面示例中的 addThree 和 addFour 函数就是这样创建的。
我们再来看看 `addLoadEvent`函数。它以一个回调函数作为参数，这个函数是您希望在页面加载后执行的函数。下面是两种情况:在第一种情况下，`window.onload`还没有一个函数分配给它，所以这个函数简单地将回调分配给`window.onload`。第二种情况是闭包出现的地方:window.onload 已经被赋值了。这个先前分配的函数首先保存在一个名为 oldonload 的变量中。然后创建一个全新的函数，首先执行 oldonload，然后执行新的回调函数。这个新功能被分配给`window.onload`。感谢闭包的神奇属性，它会“记住”最初的 onload 函数是什么。此外，您可以使用不同的参数多次调用 addLoadEvent 函数，它将构建一个函数链，确保无论您添加了多少个回调函数，当页面加载时，所有内容都将被执行。
闭包是一个非常强大的语言特性，但是需要一些时间来适应。维基百科上的这篇[文章提供了更深入的报道。](http://en.wikipedia.org/wiki/Closure_(computer_science))

```

## 分享这篇文章