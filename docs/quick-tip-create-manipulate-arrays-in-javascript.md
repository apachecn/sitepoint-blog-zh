# 如何在 JavaScript 中创建和操作数组

> 原文：<https://www.sitepoint.com/quick-tip-create-manipulate-arrays-in-javascript/>

了解如何使用 JavaScript 数组。我们将介绍数组的创建过程，改变数组的长度，以及添加、删除和替换条目，

`Array`对象的`length`属性是许多 JavaScript 新手不理解的。许多人错误地认为长度告诉你一个数组中到底有多少个条目，然而这只是对某些数组而言。一些初学者甚至没有意识到`length`是数组的一个可写属性。为了弄清楚`length`属性是如何工作的，让我们来看看当我们自己改变它的值或者运行一些更新数组的操作导致长度改变时会发生什么。

让我们从头开始。一个 JavaScript 数组有一个名为`length`的属性，并且可选地有编号的属性，其名称在 0 到 4294967294 之间，包括 0 和 4294967294。它还有许多操作属性的方法，其中一些我们将作为 length 属性如何工作的一部分来研究。请注意，JavaScript 不支持[关联数组](https://en.wikipedia.org/wiki/Associative_array)，因此尽管您可以向数组添加命名属性，但它们并不构成数组的一部分，并且将被所有数组方法忽略。它们也不会影响长度。

为了更容易地显示在我们处理各种语句时数组属性发生了什么，我们将在每段代码后运行下面的函数。这将把数组的长度和所有带编号的属性记录到浏览器的控制台中。

```
var test = function(array) {
  console.log('length:'+ array.length);
  array.forEach(function(element, index, array) {
    console.log(index + ':' + element);
  });
};
```

*这篇文章由[克里斯·佩里](https://www.sitepoint.com/author/cperry/)和[马塞罗·拉罗卡](https://www.sitepoint.com/author/mlarocca/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 在 JavaScript 中创建数组

我们将从查看在 JavaScript 中创建数组的不同方法开始。前两个例子创建的数组只设置了长度，根本没有编号的条目。后两个创建从 0 到比长度小 1 的编号条目。

长度大于编号属性数量的数组称为**稀疏数组**，而长度等于编号属性数量的数组称为**密集数组**。

```
//Creates an array with no numbered entries

var arr = new Array(5);
test(arr);
// length: 5

var arr = [];
arr.length = 5;
test(arr);
// length: 5 
```

注意，在创建新数组时，数组文字符号[是首选的](http://stackoverflow.com/q/885156/1136887)。

```
var arr = ['a', 'b', 'c', 'd', 'e'];
test(arr);
// length:5, 0:a, 1:b, 2:c, 3:d, 4:e

var arr = [undefined, undefined, undefined, undefined, undefined];
test(arr);
// length:5, 0:undefined, 1:undefined, 2:undefined, 3:undefined, 4:undefined 
```

处理编号属性的数组方法(在我们的例子中是`forEach`)将只处理那些已经存在的属性。如果你使用一个`for`或`while`循环来处理数组，那么这个循环也会尝试处理那些不存在的属性，数组会将不存在的条目识别为`undefined`。您的代码将无法区分上面的最后一个例子和前两个例子。当您不确定是否在处理密集数组时，应该始终使用数组方法来处理数组。

## 在 JavaScript 中更改数组的长度

下面的例子看看如果我们为数组设置一个小于当前长度的新长度会发生什么。

```
var arr = ['a', 'b', 'c', 'd', 'e', 'f'];
test(arr);
// length:6, 0:a, 1:b, 2:c, 3:d, 4:e, 5:f

arr.length = 5;
test(arr);
// length:5, 0:a, 1:b, 2:c, 3:d, 4:e

var arr = ['a','b','c','d','e','f',,,];
test(arr);
// length:8, 0:a, 1:b, 2:c, 3:d, 4:e, 5:f

arr.length = 7;
test(arr);
// length:7, 0:a, 1:b, 2:c, 3:d, 4:e, 5:f 
```

注意，当使用`[]`符号创建数组时，每个条目都由一个后跟逗号的值组成。如果省略该值，则不会为该位置创建任何属性。只有在为该属性提供了值的情况下，才可以省略最后一个逗号，否则长度将减少一个。

## 在 JavaScript 中从数组中移除条目

JavaScript 提供了三种方法 [pop](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/pop) 、 [shift](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift) 和 [splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) ，可以从数组中删除条目，从而减少数组的长度。在每种情况下，调用都会返回移除的值。

```
// pop() removes the last element from an array
var arr = ['a','b','c','d','e','f'];
var el = arr.pop();
test(arr); // length:5, 0:a, 1:b, 2:c, 3:d, 4:e
console.log(el); // f

// shift() removes the first element from an array
var arr = ['a','b','c','d','e','f'];
var el = arr.shift();
test(arr); // length:5, 0:b, 1:c, 2:d, 3:e, 4:f
console.log(el); // a

// splice() can remove existing elements
var arr1 = ['a','b','c','d','e','f'];
var arr2 = arr1.splice(0,2); // remove 2 elements starting at index 0
test(arr1); // length:4, 0:c, 1:d, 2:e, 3:f
test(arr2); // length:2, 0:a, 1:b

var arr1 = ['a','b','c','d','e','f',,,'i'];
var arr2 = arr1.splice(6,2); // remove 2 elements starting at index 6
test(arr1); // length:7, 0:a, 1:b, 2:c, 3:d, 4:e, 5:f, 6:i
test(arr2); // length:2 
```

## 如何向数组添加条目

我们可以在数组中添加一个新的条目，只需在数组中指定一个位置，该位置的编号属性尚不存在。我们还可以使用 JavaScript 提供的三种方法之一( [push](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push) 、 [unshift](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift) 和 [splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) )来插入新条目，并在必要时移动旧条目。

```
var arr = ['a','b','c','d','e','f',,,'i'];
arr[11] = 'l';
test(arr);
// length:12, 0:a, 1:b, 2:c, 3:d, 5:f, 8:i, 11:l

// push() adds one or more elements to the end of an array
var arr = ['a','b','c','d','e','f',,,,];
arr.push('j');
test(arr);
// length:10, 0:a, 1:b, 2:c, 3:d, 5:f, 9:j

// unshift() adds one or more elements to the beginning of an array
var arr = ['a','b','c','d','e','f',,,,];
arr.unshift('x');
test(arr);
// length:10, 0:x, 1:a, 2:b, 3:c, 4:d, 5:e, 6:f

arr1 = ['a','b','c','d','e','f',,,'i'];
arr2 = arr1.splice(6,0,'g','h'); // removes 0 elements from index 6, and inserts 'g', 'h'
test(arr1); // length:11, 0:a, 1:b, 2:c, 3:d, 5:f, 6:g, 7:h, 10:i
test(arr2); // length:0 
```

## 替换 JavaScript 数组中的条目

当我们给一个已经存在的条目分配一个新值时，这个条目只是得到一个新值，数组的其余部分不受影响。此外，通过组合我们已经看到的`splice()`方法的变体，我们可以替换现有的条目或填充数组中的空白。

```
var arr1 = ['a','b','c','d','e','f',,,'i'];
var arr2 = arr1.splice(6,2,'g','h');
test(arr1); // length:9, 0:a, 1:b, 2:c, 3:d, 4:e, 5:f, 6:g, 7:h, 8:i
test(arr2); // length:2 
```

## 结论

上面的例子应该让你对数组的`length`属性的工作原理有了更好的了解。这可以大于或等于数组中的条目数。当它相等时，我们有一个密集阵列，当它更大时，我们有一个稀疏阵列。一个特定的数组方法具体做什么可能取决于在一个稀疏数组中是否真的有一个属性对应于一个给定的位置。如果我们改变一个数组的长度，它会删除数组中任何大于新长度的编号属性。如果长度等于编号属性的数量，我们增加长度，然后我们将密集数组转换为稀疏数组。用于在数组中删除和添加属性的数组方法将在需要的地方移动现有条目，并且还将保留和移动属性之间的任何间隙。

## 分享这篇文章