# JavaScript 数字的乐趣

> 原文：<https://www.sitepoint.com/fun-with-javascript-numbers/>

数据类型是每一种编程语言的重要组成部分，数字可能是所有数据类型中最重要的。毕竟，电脑真的只是昂贵的计算器。像任何有价值的编程语言一样，JavaScript 支持数字数据。然而，就像 JavaScript 的许多其他方面一样，数字有几个错综复杂的地方，如果不小心的话，可能会被咬到。本文探讨了数字数据及其一些古怪之处。

*注意:在阅读本文之前，你应该对 [JavaScript 数据类型](https://www.sitepoint.com/beginners-guide-javascript-variables-and-datatypes/#datatypes "JavaScript Data Types")* 有一个基本的熟悉。

## `Number`型

在 JavaScript 中，所有数字都使用数字数据类型表示。这包括整数、实数、十六进制数和用科学记数法写的数。以下示例通过对各种数字应用 [`typeof`](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Operators/typeof "typeof - JavaScript | MDN") 运算符来验证这一点。本例中的每个`typeof`应用都返回`number`。

```
typeof(100);
typeof(3.14);
typeof(0xDEADBEEF);
typeof(7.89e2);
```

在前面的例子中，数字采用了多种格式。但是，在内部，所有的 JavaScript 数字实际上都表示为[IEEE 754](http://en.wikipedia.org/wiki/IEEE_floating_point "IEEE floating point - Wikipedia")浮点数据。这很重要，因为这意味着 JavaScript 没有整数的概念，尽管该语言有 [`parseInt()`](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/parseInt "parseInt - JavaScript | MDN") 函数。这也意味着 JavaScript 数学不是 100%准确的。例如，考虑下面的表达式。

```
(0.1 + 0.2) === 0.3
```

如果您不熟悉浮点数，您肯定会认为这个表达式的计算结果是`true`。毕竟 0.1 + 0.2 确实等于 0.3。然而，由于浮点数的工作方式，总和实际上是 0.3000000000000004。差别很小，但足以导致整个表达式求值为`false`。

## 正负零

IEEE 754 标准的另一个奇怪之处是带符号的零。这导致[两个零](http://css.dzone.com/articles/javascripts-two-zeros "JavaScripts Two Zeros  | Web Builder Zone")——一个正的零，+0，和一个负的零，-0。这可能看起来很奇怪，但乐趣才刚刚开始。显然，这是两个不同的值，否则只会有一个零。但是，如果显示其中一个零，符号将被删除。例如，下面的代码试图并排显示两个零值。

```
alert((+0) + " " + (-0));
// displays 0 0
```

更糟糕的是，JavaScript 的比较运算符似乎也无法区分这两个值，如下例所示。

```
alert((+0 === -0));
// displays true
alert((-0 < +0));
// displays false
```

这个问题有一个相当简单的解决方法。在 JavaScript 中，除以零得到`Infinity`。类似地，除以负零得到`-Infinity`。因此，要判断一个数是否等于-0，我们必须检查它是零，然后以它为分母进行除法运算，并检查如下所示的`-Infinity`。

```
function isNegativeZero(x) {
  return (x === 0 && (1/x) === -Infinity);
}
```

## 不是一个数字

JavaScript 实际上定义了一个名为 Not-a-Number 或`[NaN](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/NaN "NaN - JavaScript | MDN")`的数字。`NaN`是一个 [falsy](http://cjihrig.com/blog/truthy-and-falsy-in-javascript/ "Truthy and Falsy in JavaScript") 值，用于将非数字表示为数字。这个值很有趣，因为它的名字本身就排除了它是一个数字，然而`typeof(NaN)`是`number`。`NaN`也很有趣，因为它是 JavaScript 中唯一不等于自身的值。例如，下面的代码将返回`false`。

```
alert((NaN === NaN));
// displays false
```

不使用比较运算符，您可以使用 [`isNaN()`](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/isNaN "isNaN - JavaScript | MDN") 函数来测试`NaN`，如下所示。

```
isNaN(1);
// returns false
isNaN(NaN);
// returns true
```

然而，`isNaN()`也很有趣，因为它可能会误导人。如果你传入一个可以强制为数字的值，`isNaN()`将返回`false`。在下面的例子中，`isNaN()`是用几个明显不是数字的值调用的。然而，每个调用都返回`false`。

```
isNaN(true);
isNaN(false);
isNaN("");
isNaN(null);
// all return false
```

检查`NaN`的更好方法是利用它不等于自身的事实。以下函数使用严格不等式对`NaN`进行测试。这个函数将只为值`NaN`返回`true`。

```
function isNotANumber(x) {
  return x !== x;
}
```

## 其他有趣的时光

还有一些其他情况会导致数字问题。首先，您应该小心那些允许像`Infinity`、`NaN`和`undefined`这样的全局属性被重新定义为新值的旧浏览器。例如，如果频繁使用`NaN`，下面的代码可能会产生很多问题。幸运的是，现代浏览器将忽略对前面提到的属性的赋值。[严格模式](http://cjihrig.com/blog/javascripts-strict-mode-and-why-you-should-use-it/ "JavaScript’s Strict Mode and Why You Should Use It")更进一步，将这些无声的失败转化为错误。

```
NaN = 1;
...
isNaN(NaN);
// now returns false
```

诊断错误的另一个乐趣来自于添加数字和字符串。这方面的一个例子如下所示。在这种情况下，字符串串联会覆盖加法。这使得`foo`被转换成字符串`"100"`。最终的结果是字符串`"1001"`，与预期值 101 相差甚远。这种类型的错误比您想象的更常见，并且往往在读取用户输入时发生。

```
var foo = 100;
var bar = "1";

alert(foo + bar);
// displays "1001"
```

## 结论

本文探讨了 JavaScript 中数字的一些特性。希望您现在了解为什么会出现这些问题，以及如何避免它们。而且，虽然你可能不会经常遇到像负零这样的情况，但至少现在你有所准备。

## 分享这篇文章