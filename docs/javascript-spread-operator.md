# 快速提示:如何在 JavaScript 中使用 Spread 运算符

> 原文：<https://www.sitepoint.com/javascript-spread-operator/>

在本教程中，你将学习在 JavaScript 中使用 spread 操作符的不同方法，以及 spread 和 rest 操作符之间的主要区别。

用三个点(`...`)表示，JavaScript spread 操作符是在 ES6 中引入的。它可以用来将集合和数组中的元素扩展成单个的元素。

spread 运算符可用于创建和克隆数组和对象，将数组作为函数参数传递，从数组中删除重复项，等等。

## 句法

spread 运算符只能用于 iterable 对象。它必须用在 iterable 对象之前，没有任何分隔。例如:

```
console.log(...arr); 
```

## 功能参数

以 [Math.min()](https://www.sitepoint.com/javascript-math-functions/) 方法为例。此方法至少接受一个数字作为参数，并返回传递的参数中最小的数字。

如果您有一个数字数组，并且您想找到这些数字的最小值，在没有 spread 操作符的情况下，您需要使用它们的索引逐个传递元素，或者使用 [apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法将数组的元素作为参数传递。例如:

```
const numbers = [15, 13, 100, 20];
const minNumber = Math.min.apply(null, numbers);
console.log(minNumber); // 13 
```

请注意，第一个参数是`null`，因为第一个参数用于改变调用函数的`this`的值。

对于将数组元素作为参数传递给函数来说，spread 运算符是一种更方便、可读性更强的解决方案。例如:

```
const numbers = [15, 13, 100, 20];
const minNumber = Math.min(...numbers);
console.log(numbers); // 13 
```

你可以在这个活生生的例子中看到它:

参见 [CodePen](https://codepen.io) 上的笔 [使用函数 JS](https://codepen.io/SitePoint/pen/xxjJRJj) 中的 Spread 运算符 by site point([@ site point](https://codepen.io/SitePoint))
。