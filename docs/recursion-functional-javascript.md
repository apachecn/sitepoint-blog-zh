# 函数式 JavaScript 中的递归

> 原文：<https://www.sitepoint.com/recursion-functional-javascript/>

在用 JavaScript 编程时，你可能遇到过递归函数的引用。你甚至可能试图自己构建(或解构)一些。但是你可能没有见过很多有效递归的例子。事实上，除了这种方法的奇特性质之外，您可能没有考虑过递归在何时何地有用，或者如果不小心使用它会有多危险。

## 递归有什么好处？

递归是一种迭代操作的技术，它让一个函数反复调用自己，直到得到一个结果。大多数循环可以用递归方式重写，在一些函数式语言中，这种循环方式是默认的。

然而，虽然 JavaScript 的函数式编码风格确实支持递归函数，但我们需要注意的是，大多数 JavaScript 编译器目前并没有优化到安全地支持它们。

当您需要在一个循环中用不同的参数重复调用同一个函数时，最好使用递归。虽然它可以在许多情况下使用，但对于解决涉及迭代分支的问题最有效，例如分形数学、排序或遍历复杂或非线性数据结构的节点。

函数式编程语言偏爱递归的一个原因是，它允许构造不需要用局部变量设置和维护状态的代码。递归函数自然也很容易测试，因为它们很容易以纯粹的方式编写，对于任何给定的输入都有特定且一致的返回值，并且对外部变量状态没有副作用。

## 环

可以应用递归的函数的经典例子是阶乘。这是一个函数，它返回一个数与前面的每个整数反复相乘的值，一直到 1。

例如，三的阶乘是:

```
3 × 2 × 1 = 6
```

六的阶乘是:

```
6 × 5 × 4 × 3 × 2 × 1 = 720
```

你可以看到这些结果变大的速度有多快。你也可以看到我们一遍又一遍地重复同样的行为。我们取一次乘法运算的结果，再乘以比第二个值小一的数。然后，我们一次又一次地这样做，直到我们达到一个。

使用 for 循环，不难创建一个函数来迭代执行这个操作，直到返回正确的结果:

```
var factor = function(number) {
  var result = 1;
  var count;
  for (count = number; count > 1; count--) {
    result *= count;
  }
  return result;
};
console.log(factor(6));
// 720
```

这是可行的，但是从函数式编程的角度来看，这并不是很好。为了支持 for 循环并返回结果，我们必须使用几个维护和跟踪状态的局部变量。如果我们能够抛弃 for 循环，采用一种更具功能性的 JavaScript 方法，不是更简洁吗？

## 递归

我们知道 [JavaScript 将让我们编写以函数为参数的函数](https://www.sitepoint.com/higher-order-functions-javascript/)。那么，如果我们想使用我们正在编写的实际函数，并在运行它的上下文中执行它。

这可能吗？当然是了！例如，以一个简单的`while`循环为例，如下所示:

```
var counter = 10;
while(counter > 0) {
    console.log(counter--);
}
```

当这样做的时候，`counter`的值已经被改变了，但是当我们慢慢地从循环中吸取状态时，循环已经完成了打印出它所保存的每个值的工作。

同一循环的递归版本可能看起来更像这样:

```
var countdown = function(value) {
    if (value > 0) {
        console.log(value);
        return countdown(value - 1);
    } else {
        return value;
    }
};
countdown(10);
```

你看到我们如何在`countdown`函数的定义中调用`countdown`函数了吗？JavaScript 像老板一样处理这个问题，只是做你希望做的事情。每次执行`countdown`时，JavaScript 都会跟踪它是从哪里被调用的，然后通过函数调用栈反向工作，直到它完成。我们的函数也避免了修改任何变量的状态，但是仍然利用传入的值来控制递归。

回到阶乘的例子，我们可以像这样重写前面的函数来使用递归:

```
var factorial = function(number) {
  if (number <= 0) { // terminal case
    return 1;
  } else { // block to execute
    return (number * factorial(number - 1));
  }
};
console.log(factorial(6));
// 720
```

以这种方式编写代码允许我们以无状态的方式描述整个过程，并且没有副作用。同样值得注意的是，在进行任何计算之前，我们首先测试传递给函数的参数值。我们希望任何将要调用自己的函数在到达它们的终端外壳时快速干净地退出。对于以这种方式计算的阶乘，当传入的数字为零或负数时，就会出现终端情况(如果需要，我们也可以测试负值并返回不同的消息)。

## 尾部调用优化

当代 JavaScript 实现的一个问题是，它们没有一个标准的方法来防止递归函数无限地自我堆积，并蚕食内存，直到它们超过引擎的容量。JavaScript 递归函数需要跟踪它们每次被调用的位置，这样它们就可以在正确的点重新开始。

在许多函数式语言中，比如 Haskell 和 Scheme，这是使用一种叫做尾部调用优化的技术来管理的。通过尾部调用优化，递归函数中的每个连续循环都会立即发生，而不是堆积在内存中。

理论上，尾部调用优化是 ECMAScript 6 标准的一部分，ECMAScript 6 目前是 JavaScript 的下一个版本，然而它还没有被大多数平台完全实现。

## 蹦床功能

有一些方法可以在必要时强制 JavaScript 以安全的方式执行递归函数。例如，可以构建一个定制的 trampoline 函数来迭代地管理递归执行，一次只在堆栈上保留一个操作。以这种方式使用的 Trampoline 函数可以利用 JavaScript 将函数绑定到特定上下文的能力，从而使递归函数反弹，一次建立一个结果，直到循环完成。这将避免创建等待执行的操作的深度堆栈。

实际上，为了安全起见，使用蹦床功能通常会降低性能。此外，我们通过以递归方式编写函数而获得的许多优雅和可读性，在 JavaScript 中使用这种方法所必需的代码卷积中丢失了。

如果你很好奇，我鼓励你阅读更多关于这个概念的内容，并在下面的讨论中分享你的想法。你可以从 StackOverflow 上的一个简短的[线程开始，然后探索由](http://stackoverflow.com/questions/25228871/how-to-understand-trampoline-in-javascript)[唐·泰勒](https://taylodl.wordpress.com/2013/06/07/functional-javascript-tail-call-optimization-and-trampolines/)和[马克·麦克唐纳](http://www.integralist.co.uk/posts/js-recursion.html)撰写的一些文章，这些文章更深入地挖掘了 JavaScript 中蹦床的起伏。

## 我们还没到那一步

递归是一种强大的技术，值得了解。在许多情况下，递归是解决复杂问题最直接的方法。但是，在 ECMAScript 6 在我们需要它的地方实现尾部调用优化之前，我们需要非常小心如何以及在哪里应用递归。

## 分享这篇文章