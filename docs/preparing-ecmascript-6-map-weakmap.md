# 准备 ECMAScript 6:地图和 WeakMap

> 原文：<https://www.sitepoint.com/preparing-ecmascript-6-map-weakmap/>

如果您正在关注关于 ECMAScript 6 的这个系列，那么您已经了解了一些可用于 [`String`](https://www.sitepoint.com/es6-string-methods-string-prototype/) 和 [`Array`](https://www.sitepoint.com/es6-array-methods-array-prototype/) 类型的新方法。JavaScript 的新版本也引入了几个新的数据类型。在这篇文章中，我们将讨论`Map`和它的弱对应`WeakMap`。

请记住，如果你想填充我们将在本教程中涉及的内容，你可以使用[保罗·米勒](http://paulmillr.com)的 [es6-shim](https://github.com/paulmillr/es6-shim/) 。

## `Map`

地图是编程中最常用的数据结构之一。映射是将键与值相关联的对象，与值的类型无关(数字、字符串、对象等)。对于不了解地图的人，我们来讨论一个简单的例子。在典型的结构化数据库表中，每个条目(表中的一行)与一个 ID 相关联。所以，你有这样的东西:

```
ID 1 -> Aurelio De Rosa, Italy
ID 2 -> Colin Ihrig, USA
ID 3 -> John Doe, USA

```

在像 Java 和 C#这样的语言中，你有一个允许你实例化地图的类。在 PHP 等其他语言中，你可以使用关联数组创建一个地图。在 ECMAScript 6 之前，JavaScript 是缺少这种数据结构的语言之一。现在，这种数据类型已经存在，它被称为`Map`。

JavaScript 映射非常强大，因为它们允许使用任何值(对象和原始值)作为键或值。与使用`Object`类型创建的地图相比，这是最重要的区别之一。事实上，使用对象文字创建的映射只允许字符串作为它们的键。此外，正如我们稍后将看到的，`Map`类型有一个方法可以轻松地检索其中包含的元素数量，而对于对象，您必须手动循环遍历它们，检查元素是否属于对象本身并且没有被继承(使用传统的`hasOwnProperty()`)。

现在我已经向您介绍了这种新的数据类型，让我们来看看有哪些可用的属性和方法。

### `Map.prototype.size`

`size`属性返回`Map`对象中元素的数量。这是一个很好的补充，我在前面提到过，因为感谢它，你不用自己去数元素了。

### `Map.prototype.constructor()`

`Map`对象的构造函数用于实例化新对象，并接受一个名为`iterable`的可选参数。后者是一个数组或一个 iterable 对象，其元素是键/值对(两元素数组)。这些元素都将被添加到新地图中。例如，您可以写:

```
var array = [['key1', 'value1'], ['key2', 100]];
var map = new Map(array);
```

### `Map.prototype.set()`

`set()`方法用于向映射添加新元素(键/值对)。如果使用的密钥已经存在，则关联的值将被新值替换。其签名如下:

```
Map.prototype.set(key, value)
```

其中`key`是您要使用的密钥，`value`是要存储的值。这个方法修改它所调用的地图，但也返回新的地图。

这种方法目前在 Firefox、Internet Explorer 11 以及 Chrome 和 Opera 中实现在一个标志后面(“启用实验性 JavaScript”)。

### `Map.prototype.get()`

`get()`方法返回与所提供的键相关联的值。如果没有找到键，该方法返回`undefined`。该方法的签名如下所示，其中`key`是您想要使用的密钥。

```
Map.prototype.get(key)
```

这种方法目前在 Firefox、Internet Explorer 11 以及 Chrome 和 Opera 中实现在一个标志后面(“启用实验性 JavaScript”)。

### `Map.prototype.delete()`

`delete()`方法从 map 中删除与所提供的键相关联的元素。如果元素被成功移除，它返回`true`，否则返回`false`。此方法的签名如下所示:

```
Map.prototype.delete(key)
```

`key`代表要删除的元素的键。

这种方法目前在 Firefox、Internet Explorer 11 以及 Chrome 和 Opera 中都有实现(你必须激活通常的标志)。

### `Map.prototype.has()`

`has()`是验证具有给定键的元素是否存在的方法。如果找到了密钥，它返回`true`，否则返回`false`。此方法的签名如下所示:

```
Map.prototype.has(key)
```

其中`key`是您要搜索的关键字。

这种方法目前在 Firefox、Internet Explorer 11 以及 Chrome 和 Opera 中实现在一个标志后面(“启用实验性 JavaScript”)。

### `Map.prototype.clear()`

`clear()`方法是一种从`Map`对象中移除所有元素的便捷方式。该方法没有返回值(这意味着它返回`undefined`)。`clear()`的签名如下所示:

```
Map.prototype.clear()
```

`clear()`目前在 Firefox，Internet Explorer 11，以及 Chrome 和 Opera 中实现在通常的旗帜后面。

### `Map.prototype.forEach()`

正如我们可以在数组上循环，使用`forEach()`方法执行回调函数，同样的事情也可以用在地图上。`forEach()`的签名如下所示:

```
Map.prototype.forEach(callback[, thisArg])
```

`callback`是为 map 中的每个元素执行的回调函数，`thisArg`用于设置回调的上下文(`this`)。该方法没有返回值(这意味着它返回`undefined`)。`callback`接收三个参数，它们是:

*   `value`:被处理元素的值
*   `key`:被处理元素的键
*   `map`:正在处理的`Map`对象

Firefox、Internet Explorer 11 以及 Chrome 和 Opera after a flag 都支持这种方法。

### `Map.prototype.entries()`

`entries()`是获取一个`Iterator`对象来遍历地图元素的方法。我已经在谈论`Array`型的新 [`keys()`方法时提到过这种类型的物体。这种方法的特征是:](https://www.sitepoint.com/es6-array-methods-array-prototype/)

```
Map.prototype.entries()
```

这种方法目前被 Firefox、Chrome 和 Opera 支持在一面旗帜后面。

### `Map.prototype.keys()`

`keys()`方法与`entries()`非常相似，但是它只返回元素的键。其签名如下:

```
Map.prototype.keys()
```

这种方法目前被 Firefox、Chrome 和 Opera 支持在一面旗帜后面。

### `Map.prototype.values()`

类似于`keys()`我们有`values()`。它返回一个包含地图元素值的`Iterator`对象。其签名如下:

```
Map.prototype.values()
```

这种方法目前被 Firefox、Chrome 和 Opera 支持在一面旗帜后面。

## `WeakMap`

`WeakMap`与`Map`非常相似，但没有什么重要的区别。首先是一个`WeakMap`只接受对象作为键。这意味着`{}`、`function(){}`(记住函数继承自`Object`)和你自己的类的实例是允许的，但是`'key'`、`10`和其他原始数据类型是不允许的。

另一个重要的区别是，如果没有任何其他对充当键的对象的引用，`WeakMap`对象不会阻止垃圾收集(引用是*弱*)。由于这种差异，无法一次检索键(例如`Map`的`Map.prototype.keys()`方法)或多个元素(例如`Map.prototype.values()`和`Map.prototype.entries()`)。Mozilla 开发者网络(MDN)很好地解释了其中的原因:

> WeakMap 键是不可枚举的(也就是说，没有给你一个键列表的方法)。如果是的话，这个列表将依赖于垃圾收集的状态，引入了不确定性。

作为前一点的进一步结果，没有可用的`size`属性。

同样值得注意的是，Chrome 37 和 Opera 24(撰写本文时的最新版本)支持不带标志的`WeakMap`及其方法，而对于`Map`来说，情况并非如此。

## 把所有的放在一起

到目前为止，您已经了解了所有关于`Map`和`WeakMap`数据类型及其方法的知识。在本节中，我们将把它们付诸实践，以便您可以更好地了解它们的威力。除了向您展示一些代码，我们还将为您提供演示，以便您可以现场使用它们。

在第一个演示中，我们将看到一个`Map`对象和它的方法。

```
// Creates a new Map object
var mapObj = new Map();
// Defines an object that will be used a key in the map
var objKey = {third: 'c'};

// Adds a new element having a String as its key and a String as its value
mapObj.set('first', 'a');
// Adds a new element having a Number as its key and an Array as its value
mapObj.set(2, ['b']);
// Adds a new element having an Object as its key and a Number as its value
mapObj.set(objKey, 3);
// Adds a new element having an Array as its key and a String as its value
mapObj.set(['crazy', 'stuff'], 'd');

// Checks whether an element having a key of "2" exists in the map. Prints "true"
console.log(mapObj.has(2));

// Checks whether an element having a key of "test" exists in the map. Prints "false"
console.log(mapObj.has('test'));

// Retrieves the element having key of "first". Prints "a"
console.log(mapObj.get('first'));

// Retrieves the element having key of "['crazy', 'stuff']". Prints "undefined" because even if the value of this array are identical to the one used to set a value, they are not the same array
console.log(mapObj.get(['crazy', 'stuff']));

// Retrieves the element having as a key the value of objKey. Prints "3" because it's exactly the same object using to set the element
console.log(mapObj.get(objKey));

// Retrieves the element having key of "empty". Prints "undefined"
console.log(mapObj.get('empty'));

// Retrieves the map size. Prints "4"
console.log(mapObj.size);

// Deletes the element having key of "first". Prints "true"
console.log(mapObj.delete('first'));

// Retrieves the map size. Prints "3"
console.log(mapObj.size);

// Loops over each element of the map
mapObj.forEach(function(value, key, map) {
    // Prints both the value and the key
    console.log('Value ' + value + ' is associated to key ' + key);
});

var entries = mapObj.entries();
var entry = entries.next();
// Loops over each element of the map
while(!entry.done) {
    // Prints both the value and the key
    console.log('Value ' + entry.value[1] + ' is associated to key ' + entry.value[0]);
    entry = entries.next();
}

var values = mapObj.values();
var value = values.next();
// Loops over each value of the map
while(!value.done) {
    // Prints the value
    console.log('Value: ' + value.value);
    value = values.next();
}

var keys = mapObj.keys();
var key = keys.next();
// Loops over each key of the map
while(!key.done) {
    // Prints the key
    console.log('Key: ' + key.value);
    key = keys.next();
}

// Deletes all the elements of the map
mapObj.clear();

// Retrieves the map size. Prints "0"
console.log(mapObj.size);
```

前面代码的现场演示如下所示，也可以作为 JSFiddle 获得[。](https://jsfiddle.net/zzb2vkyo/embedded/result/)

[https://jsfiddle.net/zzb2vkyo/embedded/](https://jsfiddle.net/zzb2vkyo/embedded/)

在第二个演示中，我们将看到如何使用一个`WeakMap`对象。

```
// Creates a new WeakMap object
var weakMapObj = new WeakMap();
// Defines an object that will be used a key in the map
var objKey1 = {a: 1};
// Defines another object that will be used a key in the map
var objKey2 = {b: 2};

// Adds a new element having an Object as its key and a String as its value
weakMapObj.set(objKey1, 'first');
// Adds a new element having an Object as its key and a String as its value
weakMapObj.set(objKey2, 'second');
// Adds a new element having a Function as its key and a Number as its value
weakMapObj.set(function(){}, 3);

// Checks whether an element having as its key the value of objKey1 exists in the weak map. Prints "true"
console.log(weakMapObj.has(objKey1));

// Retrieve the value of element associated with the key having the value of objKey1\. Prints "first"
console.log(weakMapObj.get(objKey1));

// Deletes the element having key of objKey1\. Prints "true"
console.log(weakMapObj.delete(objKey1));

// Deletes all the elements of the weak map
weakMapObj.clear();
```

前面代码的现场演示如下所示，也可以作为 JSFiddle 获得[。](https://jsfiddle.net/99wq8jxp/embedded/result/)

[https://jsfiddle.net/99wq8jxp/embedded/](https://jsfiddle.net/99wq8jxp/embedded/)

## 结论

在本教程中，我介绍了新的`Map`和`WeakMap`数据类型。前者是对该语言的一个很好的补充，因为大多数开发人员已经模拟地图很长时间了。它的弱对应物在你的日常工作中不会经常用到，但是在某些情况下它可能会非常适合。为了巩固所讨论的概念，我强烈建议您使用所提供的演示。玩得开心！

## 分享这篇文章