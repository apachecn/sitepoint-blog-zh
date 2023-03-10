# ES6 在行动:新的数字方法

> 原文：<https://www.sitepoint.com/es6-number-methods/>

本文涵盖了 ES6 (ECMAScript 6)中新的和改进的数字方法。

这是关于 ES6 新特性系列的一部分，其中我们还讨论了可用于[字符串](https://www.sitepoint.com/es6-string-methods-string-prototype/)和[数组](https://www.sitepoint.com/es6-array-methods-array-prototype/)数据类型的新方法，以及像[地图和 WeakMap](https://www.sitepoint.com/preparing-ecmascript-6-map-weakmap/) 这样的新数据类型。

我将向您介绍添加到`Number`数据类型中的新方法和常量。正如我们将看到的，所涉及的一些数字方法根本不是新的，但是它们已经被改进和/或移到了正确的对象下(例如，`isNaN()`)。和往常一样，我们还会用一些例子将学到的新知识付诸实践。所以，事不宜迟，我们开始吧。

## Number.isInteger()

我要覆盖的第一个方法是`Number.isInteger()`。这是 JavaScript 的新增内容，这可能是你过去自己定义和使用过的。它确定传递给函数的值是否是整数。如果传递的值是整数，这个方法返回`true`，否则返回`false`。这种方法的实现非常简单，但最好是原生的。重建该功能的一个可能的解决方案是:

```
Number.isInteger = Number.isInteger || function (number) {
  return typeof number === 'number' && number % 1 === 0;
}; 
```

只是为了好玩，我尝试重新创建这个函数，结果用了一种不同的方法:

```
Number.isInteger = Number.isInteger || function (number) {
  return typeof number === 'number' && Math.floor(number) === number;
}; 
```

这两个函数都很好，也很有用，但是它们不符合 ECMAScript 6 规范。所以，如果你想填充这个方法，你需要一些更复杂的东西，我们很快就会看到。现在，让我们从发现`Number.isInteger()`的语法开始:

```
Number.isInteger(number) 
```

`number`参数代表要测试的值。

下面是使用这种方法的一些例子:

```
// prints 'true'
console.log(Number.isInteger(19));

// prints 'false'
console.log(Number.isInteger(3.5));

// prints 'false'
console.log(Number.isInteger([1, 2, 3])); 
```

前面代码的现场演示如下所示，也可以从 JSBin 获得。

[jsbin.com 上的 JS Bin](https://jsbin.com/valahe/embed?js,console)