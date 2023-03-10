# 快速提示:如何在 JavaScript 中对对象数组进行排序

> 原文：<https://www.sitepoint.com/sort-an-array-of-objects-in-javascript/>

如果你需要将一组对象按照一定的顺序排序，你可能会尝试使用 JavaScript 库。但是在这之前，请记住，您可以使用本机的 [Array.sort](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) 函数进行一些非常简洁的排序。

在本文中，我们将向您展示如何在 JavaScript 中使用字符串、数字和日期对一组对象进行排序。我们还将给出一些处理区分大小写、数组副本和为您处理所有这些的流行库的很好的技巧。

## 基本的数组排序(以及为什么它不起作用)

默认情况下，JavaScript `Array.sort`函数将数组中需要排序的每个元素转换成一个字符串，并按照 [Unicode 码位](https://en.wikipedia.org/wiki/Code_point)的顺序进行比较。

```
const foo = [9, 1, 4, 'zebroid', 'afterdeck'];
foo.sort(); // returns [ 1, 4, 9, 'afterdeck', 'zebroid' ]

const bar = [5, 18, 32, new Set, { user: 'Eleanor Roosevelt' }];
bar.sort(); // returns [ 18, 32, 5, { user: 'Eleanor Roosevelt' }, Set {} ] 
```

你可能想知道为什么 32 在 5 之前。不符合逻辑，是吧？嗯，事实上是的。发生这种情况是因为数组中的每个元素首先被转换成一个字符串，并且按照 Unicode 顺序，`"32"`在`"5"`之前。

单独使用`Array.sort`对于排序一组对象来说不是很有用。幸运的是，sort 方法带有一个可选的`compareFunction`参数，我们可以用它来对我们的对象数组进行排序。

## 如何在 JavaScript 中对对象数组进行排序

要对对象数组进行排序，请使用带有比较函数的 sort()方法。一个`compareFunction` 按照自己定义的逻辑应用规则对数组进行排序。它们允许我们按照字符串、整数、日期或任何其他自定义属性对对象数组进行排序。我们将在本文后面讨论比较函数如何工作的细节。

在这个演示中，我们将使用一组歌手，并按照他们的乐队名 按字母顺序排列:

```
const singers = [
  { name: 'Steven Tyler', band: 'Aerosmith', born: 1948 },
  { name: 'Karen Carpenter', band: 'The Carpenters', born: 1950 },
  { name: 'Kurt Cobain', band: 'Nirvana', born: 1967 },
  { name: 'Stevie Nicks', band: 'Fleetwood Mac', born: 1948 },
]; 
```

下面的`compare`函数比较每个波段的(大写)名称:

```
function compare(a, b) {
  // Use toUpperCase() to ignore character casing
  const bandA = a.band.toUpperCase();
  const bandB = b.band.toUpperCase();

  let comparison = 0;
  if (bandA > bandB) {
    comparison = 1;
  } else if (bandA < bandB) {
    comparison = -1;
  }
  return comparison;
}

singers.sort(compare);

/* returns [
  { name: 'Steven Tyler', band: 'Aerosmith',  born: 1948 },
  { name: 'Stevie Nicks', band: 'Fleetwood Mac', born: 1948 },
  { name: 'Kurt Cobain', band: 'Nirvana', born: 1967 },
  { name: 'Karen Carpenter', band: 'The Carpenters', born: 1950 }
] */ 
```

要反转排序顺序，您可以反转`compare`函数的返回值:

```
function compare(a, b) {
  ...
  //invert return value by multiplying by -1
  return comparison * -1;
}
```

### 比较函数如何工作

`compareFunction`返回一个数字，该数字用于通过比较其两个输入(`a`和`b`)来确定排序顺序。很简单，如果整数小于 0，`a`就会出现在`b`之前。如果大于 0，`b`会出现在`a`之前。如果正好是 0，则保持原来的顺序。然而，你决定这个数字取决于你。

让我们看一个简单的数字数组的例子:

```
const nums = [79, 48, 12, 4];

function compare(a, b) {
  if (a > b) return 1;
  if (b > a) return -1;

  return 0;
}

nums.sort(compare);
// => 4, 12, 48, 79 
```

我们可以稍微重构一下，因为从`b`中减去`a`也会得到返回值。这个比较函数从最小到最大对一组数字进行排序:

```
function compareNums(a, b) {
  return a - b;
}
nums.sort(compareNums)
```

它也可以表示为箭头函数，而不必在别处定义比较函数:

```
nums.sort((a, b) => a - b); 
```

如果你不熟悉箭头函数，你可以在这里阅读更多关于它们的内容:[JavaScript 中的箭头函数](https://www.sitepoint.com/arrow-functions-javascript/)。

如您所见，compare 函数可以用多种方式编写，而`sort()`方法将按照指令运行。

### 创建动态排序函数

让我们通过使它更加动态来结束我们先前的例子。让我们创建一个排序函数，您可以用它来对一个对象数组进行排序，这些对象的值要么是字符串，要么是数字。该函数有两个参数—我们要排序的关键字和结果的顺序(即升序或降序):

```
const singers = [
  { name: 'Steven Tyler', band: 'Aerosmith', born: 1948 },
  { name: 'Karen Carpenter', band: 'The Carpenters', born: 1950 },
  { name: 'Kurt Cobain', band: 'Nirvana', born: 1967 },
  { name: 'Stevie Nicks', band: 'Fleetwood Mac', born: 1948 },
];

function compareValues(key, order = 'asc') {
  return function innerSort(a, b) {
    if (!a.hasOwnProperty(key) || !b.hasOwnProperty(key)) {
      // property doesn't exist on either object
      return 0;
    }

    const varA = (typeof a[key] === 'string')
      ? a[key].toUpperCase() : a[key];
    const varB = (typeof b[key] === 'string')
      ? b[key].toUpperCase() : b[key];

    let comparison = 0;
    if (varA > varB) {
      comparison = 1;
    } else if (varA < varB) {
      comparison = -1;
    }
    return (
      (order === 'desc') ? (comparison * -1) : comparison
    );
  };
} 
```

这是你如何使用它:

```
// array is sorted by band, in ascending order by default
singers.sort(compareValues('band'));

// array is sorted by band in descending order
singers.sort(compareValues('band', 'desc'));

// array is sorted by name in ascending order
singers.sort(compareValues('name'));

// array is sorted by date if birth in descending order
singers.sort(compareValues('born', 'desc'));
```

在上面的代码中， [hasOwnProperty 方法](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)用于检查指定的属性是否在每个对象上定义，并且没有通过原型链继承。如果没有在两个对象上定义，该函数返回`0`，这使得排序顺序保持不变(即对象相对于彼此保持不变)。

操作符的[类型也用于检查属性值的数据类型。这允许该函数确定对数组进行排序的正确方式。例如，如果指定属性的值是一个`string`，则使用一个`toUpperCase`方法将其所有字符转换为大写，因此在排序时忽略字符大小写。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)

您可以调整上述函数以适应其他数据类型，以及您的脚本可能具有的任何其他需求。

## 用于排序数组的流行库

您可能没有时间或耐心用普通的 JavaScript 创建自己的排序函数。时间就是金钱，代码需要时间。幸运的是，有各种各样的库可以满足您所有的数组排序需求。这里有一个包含排序函数的助手库的简短列表…排名不分先后；)

*   [数组排序](https://www.npmjs.com/package/array-sort)
*   [下划线. js](http://underscorejs.org/#sortBy)
*   [sugarjs](https://sugarjs.com/docs/#/Array/sortBy)
*   [洛达什](https://lodash.com/docs/4.17.4#sortBy)

## 快速提示:按日期对对象数组进行排序

要按日期字符串对对象数组进行排序，您只需提供一个比较函数，该函数首先解析日期字符串，然后将它们相减:

```
const singers = [
  { name: 'Steven Tyler', band: 'Aerosmith', birthdate: 'March 26 1948' },
  { name: 'Karen Carpenter', band: 'The Carpenters', birthdate: 'March 2 1950' },
  ...
];

function compareDates(a, b) {
  return Date.parse(new Date(a.birthdate)) - Date.parse(new Date(b.birthdate))
}
```

## 快速提示:对数组进行排序而不修改它

与许多其他 JavaScript 数组函数不同，`Array.sort`是[方法中的一种，它变异](https://www.sitepoint.com/immutable-array-methods-javascript/)(改变)它排序的数组，而不是返回一个新的数组。为了避免这种情况，您可以创建一个要排序的数组的新实例，并修改它。这可以通过使用数组方法或扩展语法来创建数组的副本。

```
const baz = ['My cat ate my homework', 37, 9, 5, 17];
baz.sort(); // baz array is modified
console.log(baz); // shows [ 17, 37, 5, 9, 'My cat ate my homework' ] 
```

使用 [Array.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) 创建数组的副本:

```
const sortedBaz = baz.slice().sort(); 
// a new instance of the baz array is created and sorted 
```

或者，您可以使用[扩展运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)获得相同的效果:

```
const sortedBaz = [...baz].sort(); 
// a new instance of the baz array is created and sorted 
```

两种情况下的输出是相同的，可以在对任何对象数组进行排序之前使用。

```
console.log(baz); // ['My cat ate my homework', 37, 9, 5, 17];
console.log(sortedBaz); // [ 17, 37, 5, 9, 'My cat ate my homework' ]
```

## 快速提示:一种不区分大小写的按字符串排序数组的方法

在前面的例子中，我们希望对一个对象数组进行排序，这些对象的值要么是字符串，要么是数字。然而，如果您知道您将只处理值为字符串的对象，您可以使用 JavaScript 的`localeCompare`方法稍微整理一下代码。

此方法返回一个数字，该数字指示一个字符串在排序顺序中是位于给定字符串之前、之后还是与其相同。它支持不区分大小写的数组排序:

```
['bjork', 'Bjork', 'Björk'].sort();
// [ 'Bjork', 'Björk', 'bjork' ]

['bjork', 'Bjork', 'Björk'].sort((a, b) => a.localeCompare(b));
//  [ 'bjork', 'Bjork', 'Björk' ] 
```

根据我们的`compareValues`函数，这意味着我们可以写:

```
function compareValues(key, order = 'asc') {
  return function innerSort(a, b) {
    if (!a.hasOwnProperty(key) || !b.hasOwnProperty(key)) return 0;
    const comparison = a[key].localeCompare(b[key]);

    return (
      (order === 'desc') ? (comparison * -1) : comparison
    );
  };
} 
```

你可以在 MDN 上阅读更多关于 [localeCompare 的信息。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)

## 结论

现在您已经有了——使用普通 JavaScript 对一组对象进行排序的简短介绍。尽管许多库都提供了这种动态排序能力，但是自己实现这种功能并不困难。此外，了解引擎盖下发生的事情也很有好处。

为了最全面地理解原生 JavaScript 的基础，我们推荐 [JavaScript:新手到忍者](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition?utm_source=blog&utm_medium=articles)。从头开始学习 JavaScript，包括 ES6，并通过构建一系列项目将您的新知识付诸实践。

## 分享这篇文章