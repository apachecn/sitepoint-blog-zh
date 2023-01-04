# 在 JavaScript 中实现记忆化

> 原文：<https://www.sitepoint.com/implementing-memoization-in-javascript/>

程序经常浪费时间调用反复计算相同结果的函数。递归和数学函数尤其如此。一个完美的例子就是[斐波那契数](http://en.wikipedia.org/wiki/Fibonacci_number "Fibonacci number - Wikipedia")发生器。斐波那契数列是一系列整数，从 0 和 1 开始，其中每个值都是数列中前两个数的和。根据这个定义，前十个斐波那契数列是:0，1，1，2，3，5，8，13，21，34。从编程的角度来看，第 *n <sup>第</sup>斐波纳契数通常使用以下函数递归计算。*

```
function fibonacci(n) {
  if (n === 0 || n === 1)
    return n;
  else
    return fibonacci(n - 1) + fibonacci(n - 2);
}
```

该函数对于较小的“n”值表现良好。然而，随着“n”的增加，性能会迅速下降。这是因为这两个递归调用重复了相同的工作。比如计算第 50 个<sup>第</sup>个斐波那契数，递归函数必须调用 400 亿次以上(具体是 40730022147 次)！更糟糕的是，计算第 51 个<sup>第</sup>个数字需要这项工作重复将近两次。如果该函数能够记住它之前计算的内容，那么重复工作的问题就可以得到缓解。

## 记忆基础

[记忆化](http://en.wikipedia.org/wiki/Memoization "Memoization - Wikipedia")是一种编程技术，它试图通过缓存先前计算的结果来提高函数的性能。因为 JavaScript 对象的行为类似于关联数组，所以它们是充当缓存的理想候选对象。每次调用内存化的函数时，它的参数都被用来索引缓存。如果数据存在，那么它可以被返回，而不需要执行整个函数。但是，如果数据没有被缓存，则执行该函数，并将结果添加到缓存中。

在下面的例子中，原始的 Fibonacci 函数被重写以包括记忆化。在该示例中，一个自执行匿名函数返回一个内部函数 f()，该函数用作斐波那契函数。当 f()被返回时，它的闭包允许它继续访问“memo”对象，该对象存储了它以前的所有结果。每次执行 f()时，它首先检查当前值“n”是否存在结果。如果是，则返回缓存的值。否则，执行原始的斐波那契代码。注意“memo”是在 f()之外定义的，这样它就可以在多次函数调用中保持其值。回想一下，最初的递归函数被调用了 400 亿次来计算第 50 个斐波纳契数。通过实施记忆化，这个数字下降到 99。

```
var fibonacci = (function() {
  var memo = {};

  function f(n) {
    var value;

    if (n in memo) {
      value = memo[n];
    } else {
      if (n === 0 || n === 1)
        value = n;
      else
        value = f(n - 1) + f(n - 2);

      memo[n] = value;
    }

    return value;
  }

  return f;
})();
```

## 处理多个参数

在前面的示例中，该函数接受单个参数。这使得实现缓存变得相当简单。不幸的是，大多数函数需要多个参数，这使得缓存的索引变得复杂。要用多个参数来记忆一个函数，要么高速缓存必须变成多维的，要么所有的参数必须组合起来形成一个索引。

在多维方法中，缓存成为对象的层次结构，而不是单个对象。然后，每个维度由一个参数进行索引。以下示例为 Fibonacci 函数实现了多维缓存。在这个例子中，函数接受一个额外的参数“x”，它什么也不做。每次调用该函数时，代码都检查“x”维度是否存在，如果不存在，就初始化它。从那时起,“x”维度用于缓存“n”值。结果是函数调用 fibonacci("foo "，3)和 fibonacci("bar "，3)不会被视为相同的结果。

```
var fibonacci = (function() {
  var memo = {};

  function f(x, n) {
    var value;

    memo[x] = memo[x] || {};

    if (x in memo && n in memo[x]) {
      value = memo[x][n];
    } else {
      if (n === 0 || n === 1)
        value = n;
      else
        value = f(x, n - 1) + f(x, n - 2);

      memo[x][n] = value;
    }

    return value;
  }

  return f;
})();
```

多维缓存的替代方案是一个单一的缓存对象，它由函数的所有参数的组合来索引。在这种方法下，参数被转换成一个数组，然后用于索引缓存。每个函数都有一个名为“ [arguments](https://developer.mozilla.org/en/JavaScript/Reference/Functions_and_function_scope/arguments "arguments - MDN") 的内置对象，其中包含传入的参数。“arguments”是一种被称为类似于[数组的对象](http://nfriedly.com/techblog/2009/06/advanced-javascript-objects-arrays-and-array-like-objects/ "Advanced Javascript: Objects, Arrays, and Array-Like objects")的对象。它类似于数组，但不能用于索引缓存。因此，必须首先将其转换为实际数组。这可以使用 array slice()方法来完成。然后可以使用数组表示来索引缓存，如前所示。下面的例子说明了这是如何实现的。注意，附加变量“slice”被定义为对 array slice()方法的引用。通过存储这个引用，可以避免重复计算 Array.prototype.slice()的开销。然后使用 call()方法将 slice()应用于“参数”。

```
var fibonacci = (function() {
  var memo = {};
  var slice = Array.prototype.slice;

  function f(x, n) {
    var args = slice.call(arguments);
    var value;

    if (args in memo) {
      value = memo[args];
    } else {
      if (n === 0 || n === 1)
        value = n;
      else
        value = f(x, n - 1) + f(x, n - 2);

      memo[arguments] = value;
    }

    return value;
  }

  return f;
})();
```

## 缓存对象参数

这里给出的记忆化方案不能很好地处理对象参数。当对象用作索引时，它们首先被转换为字符串表示形式，如“[object Object]”。这会导致多个对象错误地映射到同一个缓存位置。这种行为可以通过在索引之前对对象参数执行[字符串化](http://cjihrig.com/blog/native-browser-support-for-json-part-2-of-2/ "Native Browser Support for JSON (Part 2 of 2)")来纠正。不幸的是，这也减缓了记忆过程。下面的示例创建一个通用的 memoized 函数，该函数将一个对象作为参数。注意，使用 [JSON.stringify()](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/JSON/stringify "stringify - MDN") 将对象参数字符串化，以便在缓存中创建一个索引。

```
var foo = (function() {
  var memo = {};

  function f(obj) {
    var index = JSON.stringify(obj);

    if (index in memo) {
      return memo[index];
    } else {
      // memoized function contents
      return (memo[index] = function_value);
    }

  }

  return f;
})();
```

## 自动记忆

在前面的所有例子中，函数都被显式修改以添加记忆。也有可能在根本不修改功能的情况下实现记忆基础设施。这很有用，因为它允许功能逻辑与记忆逻辑分开实现。这是通过创建一个效用函数来实现的，该效用函数将一个函数作为输入，并对其应用记忆。下面的 memoize()函数接受一个函数“func”作为输入。memoize()返回一个新函数，该函数在“func”周围包装了一个缓存机制。注意，这个函数不处理对象参数。为了处理对象，需要一个循环，根据需要逐个检查每个参数。

```
function memoize(func) {
  var memo = {};
  var slice = Array.prototype.slice;

  return function() {
    var args = slice.call(arguments);

    if (args in memo)
      return memo[args];
    else
      return (memo[args] = func.apply(this, args));

  }
}
```

## 限制

在实现记忆时，有几件事必须记住。首先，通过存储旧的结果，记忆化的函数消耗了额外的内存。在 Fibonacci 示例中，额外的内存消耗是无限的。如果内存使用是一个问题，那么应该使用固定大小的缓存。与内存化相关的开销也使得快速执行或不常执行的函数不切实际。

记忆化的最大限制是它只能通过引用透明的函数来实现自动化。如果一个函数的输出只依赖于它的输入，并且不会引起任何副作用，那么这个函数就被认为是引用透明的。对引用透明函数的调用可以用它的返回值代替，而不改变程序的语义。斐波那契函数在引用上是透明的，因为它只取决于“n”的值。在下面的例子中，函数 foo()不是引用透明的，因为它使用了一个全局变量“bar”。因为“bar”可以在 foo()之外修改，所以不能保证每个输入值的返回值都相同。在这个例子中，对 foo()的两个调用返回值 2 和 3，即使传递给这两个调用的参数是相同的。

```
var bar = 1;

function foo(baz) {
  return baz + bar;
}

foo(1);
bar++;
foo(1);
```

## 要记住的事情

*   通过缓存以前函数调用的结果，内存化有可能提高性能。
*   记忆化的函数存储一个由它们的输入参数索引的缓存。如果参数存在于缓存中，则返回缓存的值。否则，将执行该函数，并将新计算的值添加到缓存中。
*   在用作索引之前，对象参数应该字符串化。
*   记忆可以自动应用于引用透明的函数。
*   对于不常调用或快速执行的函数，记忆可能不理想。

## 分享这篇文章