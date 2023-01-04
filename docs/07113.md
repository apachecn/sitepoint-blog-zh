# 回归基础:阵列附加

> 原文：<https://www.sitepoint.com/back-to-basics-array-extras/>

数组是许多编程语言中的基本数据结构，JavaScript 也不例外。为了抽象出使用数组的许多细节，JavaScript 提供了一组称为 array extras 的函数。本文描述了各种数组附加项及其用途。

## 背景

几乎所有的数组操作都是通过循环每个数组元素来执行的，一次一个。例如，下面的代码使用一个`for`循环将数组的所有元素记录到调试控制台。

```
var foo = ["a", "b", "c", "d"];

for (var i = 0, len = foo.length; i < len; i++) {
  console.log(foo[i]);
}
```

首先，也是最重要的，您应该理解前面的例子是非常好的 JavaScript 代码。但是，如果有几个复杂的循环，跟踪变量会变得很乏味。Array extras 允许我们用函数调用替换整个循环，这通常会提高代码的可读性。现在，让我们看看各种阵列附加功能。

## `forEach()`

像许多数组附加函数一样，`forEach()`方法是一个*高阶函数*——一个接收另一个函数作为参数的函数。`forEach()`不是遍历数组元素，而是依次调用每个元素的回调函数。回调函数接受三个参数——当前数组元素、数组索引和数组本身。在下面的代码中，原来的例子被重写以使用`forEach()`方法。

```
["a", "b", "c", "d"].forEach(function(element, index, array) {
  console.log(element);
});
```

注意，`forEach()`的使用消除了对循环和数组下标符号的需要。此外，由于 JavaScript 使用函数级范围，`forEach()`回调函数提供了一个新的范围，允许变量名被重用。一个缺点是为数组中的每个元素调用一个函数会影响性能。幸运的是，这种损失通常可以忽略不计。还要注意，你可以在回调函数后给`forEach()`传递一个可选参数。如果存在，第二个参数定义回调函数中使用的`this`值。

## `map()`

`map()`功能与`forEach()`几乎相同。唯一的区别是`map()`返回一个包含回调函数返回值的数组。例如，下面的代码使用`map()`来计算输入数组中每一项的平方根。然后结果以数组的形式返回并显示。还要注意，额外的数组与内置的 JavaScript 函数兼容，比如`Math.sqrt()`。

```
var sqrts = [1, 4, 9, 16, 25].map(Math.sqrt);

console.log(sqrts);
// displays "[1, 2, 3, 4, 5]"
```

## `filter()`

与`forEach()`和`map()`一样，`filter()`方法采用一个回调函数和可选的`this`值。和`map()`一样，`filter()`根据回调函数的返回值返回一个数组。不同的是`filter()`回调函数应该返回一个布尔值。如果返回值为 true，则数组元素被添加到结果数组中。例如，下面的代码从原始数组中删除所有不以字母 x 开头的元素。在本例中，针对每个数组元素测试一个正则表达式(作为`this`值传递)。

```
["x", "abc", "x1", "xyz"].filter(RegExp.prototype.test, /^x/);
```

## `every()`和`some()`

`every()`和`some()`函数也在每个数组元素上运行一个回调函数。如果每个回调函数都返回`true`，那么`every()`返回`true`，否则返回`false`。类似地，如果至少有一个回调函数返回`true`，则`some()`返回`true`。在下面的例子中，`every()`和`some()`用于测试数组元素是否少于五个。在这种情况下，`every()`返回`false`，因为最后一个元素等于 5。但是，`some()`返回`true`，因为至少有一个元素少于五个。注意，`index`和`array`参数是存在的，但是在回调函数中被省略了，因为在这个例子中不需要它们。

```
var foo = [1, 2, 3, 4, 5];
var every = foo.every(function(element) {
  return element < 5;
});
var some = foo.some(function(element) {
  return element < 5;
});
// every = false, some = true
```

## `reduce()`和`reduceRight()`

`reduce()`方法处理数组中的每个元素，从头开始，并计算单个值。`reduce()`接受一个回调函数和一个可选的初始值作为参数。如果初始值不存在，则使用第一个数组元素。`reduce()`回调函数不同于我们到目前为止看到的其他回调函数，因为它有四个参数——前一个值、当前值、索引和数组。

归约运算的一个常见例子是对数组中的所有值求和。下面的例子就是这样做的。第一次调用回调函数，`previous`等于 1，`current`等于 2。在随后的调用中，总和累加到最终值 15。

```
var sum = [1, 2, 3, 4, 5].reduce(function(previous, current, index, array) {
  return previous + current;
});
// sum = 15
```

`reduceRight()`方法的工作方式与`reduce()`相同，除了处理从数组的末尾开始，然后移向开头。

## `indexOf()`和`lastIndexOf()`

方法在数组中搜索特定的元素，并返回第一个匹配的索引。如果没有找到匹配项，`indexOf()`返回-1。`indexOf()`将要搜索的元素作为第一个参数。第二个可选参数用于指定搜索的起始索引。例如，下面的代码在数组中查找字母 z 的前两个位置。要找到第二个匹配项，我们只需找到第一个匹配项，然后在它之后再次开始搜索。

```
var foo = ["a", "z", "b", "z"];
var first = foo.indexOf("z");
var second = foo.indexOf("z", first + 1);
// first = 1, second = 3
```

`lastIndexOf()`方法的工作方式完全相同，只是它从数组的末尾开始搜索。

## 结论

利用额外的数组可以产生干净、简洁的代码。遗憾的是，一些较老的浏览器不支持这些方法。但是，您可以通过检查`Array.prototype`对象(即`Array.prototype.forEach`)来检测这些方法。如果缺少一个方法，您可以轻松地提供自己的实现。

## 分享这篇文章