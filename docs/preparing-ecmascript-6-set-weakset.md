# 准备 ECMAScript 6: Set 和 WeakSet

> 原文：<https://www.sitepoint.com/preparing-ecmascript-6-set-weakset/>

在我最近的一篇名为[准备 ECMAScript 6: Map 和 WeakMap](https://www.sitepoint.com/preparing-ecmascript-6-map-weakmap/) 的文章中，我向您介绍了 ECMAScript 6 中可用的两种新数据类型:`Map`和它的弱对应类型`WeakMap`。在本教程中，我们将介绍另一对相似的数据类型，叫做`Set`和`WeakSet`。它们与`Map`和`WeakMap`有很多相似之处，尤其是在可用的方法方面。然而，正如我们将在这里讨论的，它们有不同的范围。

正如我在之前所有讨论 ECMAScript 6 的文章中所指出的，如果你想填充我们将要讨论的内容，你可以使用[保罗·米勒](http://paulmillr.com)的 [es6-shim](https://github.com/paulmillr/es6-shim/) 。

## `Set`

顾名思义，`Set`数据类型表示一组元素(一个集合)。正如数学概念所暗示的，这意味着一个集合只允许你存储相同的元素一次(例如，字符串“test”不能存储两次)。像其他 JavaScript 数据类型一样，并不强制存储相同类型的元素，所以在同一个集合中，可以存储数组、数字、字符串等等。

同样值得注意的是，集合中的单个元素不能被检索，例如使用`get()`方法。原因是，一个元素既没有键，也没有索引，您可以通过引用来检索它。但是因为您可以验证一个元素是否包含在一个给定的`Set`实例中，所以您不需要一个`get()`方法。例如，如果您知道字符串“test”包含在一个集合中，您不需要检索它，因为您已经有了那个值。正如您将在本教程中了解到的，仍然可以检索*存储的所有*元素。

"但是什么时候这种数据类型是合适的呢？"你可能会问。好吧，假设你需要存储一些元素的 id。当遇到这些情况时，你不会想要重复的。在这种情况下，在 ECMAScript 5 中，大多数人可能都使用数组或对象来存储元素。问题是每次新元素进来时，您必须检查它是否已经被添加，以避免重复。如果您使用数组，您会得到如下代码:

```
var collection = [1, 2, 3, 4, 5];
var newElements = [4, 8, 10];

for(var i = 0; i < newElements.length; i++) {
   if (collection.indexOf(newElements[i]) === -1) {
      collection.push(newElements[i]);
   }
}
```

使用`Set`数据类型，您可以简化前面的代码，如下所示:

```
var collection = new Set([1, 2, 3, 4, 5]);
var newElements = [4, 8, 10];

for(var i = 0; i < newElements.length; i++) {
   collection.add(newElements[i]);
}
```

现在你知道了什么是`Set`以及什么时候使用它，我们来讨论一下暴露的属性和方法。

### `Set.prototype.size`

属性返回一个`Set`实例中元素的数量。这类似于`Array`数据类型的`length`。

### `Set.prototype.constructor()`

您可能知道，构造函数用于实例化新对象。它接受一个名为`iterable`的可选参数，该参数是一个数组或一个 iterable 对象，其元素将被添加到新集合中。下面显示了一个基本的使用示例:

```
var array = [1, 2, "test", {a: 10}];
var set = new Set(array);
```

### `Set.prototype.add()`

如果一个新元素还不存在，那么`add()`方法将它添加到集合中；否则不会添加该元素。此方法的特征如下:

```
Set.prototype.add(value)
```

其中`value`是要存储的元素。这个方法修改它所调用的集合，但也返回新的集合，允许链接。如何使用此类功能的示例如下所示:

```
var set = new Set();
set.add("test").add(1).add({});
```

这种方法目前已经在 Firefox、Internet Explorer 11、Chrome 38 和 Opera 25 中实现。在 Chrome 之前的版本和 Opera 之前的版本中，在激活标志“Enable Experimental JavaScript”之后支持该方法。

### `Set.prototype.delete()`

同样，我们可以添加元素，也可以从集合中删除它们。为此，我们可以使用`delete()`方法。它接受要删除的值，如果元素被成功删除，则返回`true`，否则返回`false`。此方法的签名如下所示:

```
Set.prototype.delete(value)
```

`value`代表您想要删除的元素。

这种方法目前已经在 Firefox、Internet Explorer 11、Chrome 38 和 Opera 25 中实现。在 Chrome 之前的版本和 Opera 之前的版本中，你必须激活普通标志。

### `Set.prototype.has()`

`has()`方法是`Set`数据类型与`Map`共有的方法之一。它允许我们验证一个元素是否存在于集合中。如果找到该值，则返回`true`，否则返回`false`。此方法的签名如下:

```
Set.prototype.has(value)
```

其中`value`是您要搜索的值。

这种方法目前已经在 Firefox、Internet Explorer 11、Chrome 38 和 Opera 25 中实现。在 Chrome 之前的版本和 Opera 之前的版本中，在激活标志“Enable Experimental JavaScript”之后支持该方法。

### `Set.prototype.clear()`

与在`Map`上定义的方法一样，`clear()`方法是一种从`Set`实例中移除所有元素的便捷方法。该方法没有返回值(这意味着它返回`undefined`)。`clear()`的签名如下:

```
Set.prototype.clear()
```

`clear()`目前在 Firefox、Internet Explorer 11、Chrome 38 和 Opera 25 中实现。在 Chrome 之前的版本和 Opera 之前的版本中，你必须激活普通标志。

### `Set.prototype.forEach()`

与`Map`相同的另一种方法是`forEach()`。我们可以用它来按照插入顺序遍历存储在集合中的元素。`forEach()`的签名如下:

```
Set.prototype.forEach(callback[, thisArg])
```

`callback`是对集合中的每个元素运行的函数。`thisArg`参数用于设置回调的上下文(`this`)。`callback`接收三个参数:

*   `value`:被处理元素的值
*   `value`:被处理元素的值
*   `set`:被处理的`Set`对象

如您所见，正在处理的值被传递了两次。原因是为了保持方法与`Map`和`Array`中实现的`forEach()`一致。

Firefox、Internet Explorer 11、Chrome 38 和 Opera 25 都支持这种方法。在 Chrome 之前的版本和 Opera 之前的版本中，你必须激活普通标志。

### `Set.prototype.entries()`

`entries()`方法使我们能够获得一个`Iterator`来遍历集合的元素。按照插入顺序，`Iterator`包含集合中每个元素的`value`–`value`对的数组。这个重复的原因和之前一样:为了和`Map`的方法保持一致。这种方法的特征是:

```
Set.prototype.entries()
```

目前火狐、Chrome 38、Opera 25 都支持这种方式。在 Chrome 之前的版本和 Opera 之前的版本中，你必须激活普通标志。

### `Set.prototype.values()`

属于这种数据类型的另一个方法是`values()`。它返回一个`Iterator`对象，按照插入顺序包含集合中元素的值。其签名如下:

```
Set.prototype.values()
```

目前火狐、Chrome 38、Opera 25 都支持这种方式。在 Chrome 之前的版本和 Opera 之前的版本中，在激活标志“Enable Experimental JavaScript”之后支持该方法。

### `Set.prototype.keys()`

奇怪的是，`Set`也有一个`keys()`方法。它执行的操作和`values()`一样，所以我就不描述了。

## `WeakSet`

`WeakSet`是`Set`数据类型的弱对应。一个`WeakSet`只接受对象作为它的值。这意味着`{}`、`function(){}`(从`Object`继承的函数)和您自己的类的实例是允许的，但是`"test"`、`1`和其他原始数据类型是不允许的。

另一个重要的区别是，如果没有存储任何其他对对象的引用，那么`WeakSet`对象不会阻止垃圾收集(引用是*弱*)。由于这种差异，没有任何方法可以一次检索值或多个元素，比如`Set.prototype.values()`和`Set.prototype.entries()`。此外，与`WeakMap`类似，没有`size`房产可用。

最后一点，我想强调的是，Chrome 37 和 Opera 24 支持不带标志的`WeakSet`及其方法，而对于`Set`则不是这样。较新版本的 Chrome 38 和 Opera 25 默认支持`Set`及其方法。

## 把所有的放在一起

现在您已经看到了`Set`和`WeakSet`数据类型的所有方法和属性，是时候将它们付诸实践了。在本节中，我开发了两个演示，以便您可以使用这些方法，并对它们的功能有更好的了解。正如你所注意到的，我没有使用`Set.prototype.keys()`方法，因为我认为它只擅长迷惑开发者。

在第一个演示中，我将使用一个`Set`对象及其方法，除了`Set.prototype.keys()`。

```
// Creates a new Set object
var set = new Set();
// Defines an array will be stored in the set
var arr = [4, 1, 9];

// Adds a new Number to the set
set.add(1);
// Adds a new String to the set
set.add('Aurelio De Rosa');
// Adds a new Object to the set
set.add({name: 'John Doe'});
// Adds a new Array element to the set
set.add(arr);

// Checks whether the string "test" is stored in the set. Prints "false"
console.log(set.has('test'));

// Checks whether the number "1" is stored in the set. Prints "true"
console.log(set.has(1));

// Retrieves the set size. Prints "4"
console.log(set.size);

// Deletes the object {name: 'Aurelio De Rosa'}. Prints "false" because even if it has the same values and properties, it's a different object
console.log(set.delete({name: 'Aurelio De Rosa'}));

// Retrieves the set size. Prints "4"
console.log(set.size);

// Deletes the array arr. Prints "true" because it's the same array
console.log(set.delete(arr));

// Retrieves the set size. Prints "3"
console.log(set.size);

// Loops over each element of the set
set.forEach(function(value, samevalue, set) {
    // Prints the value twice
    console.log('Value ' + value + ' is the same as ' + samevalue);
});

var entries = set.entries();
var entry = entries.next();
// Loops over each element of the set
while(!entry.done) {
    // Prints both the value and the key
    console.log('Value ' + entry.value[1] + ' is the same as ' + entry.value[0]);
    entry = entries.next();
}

var values = set.values();
var value = values.next();
// Loops over each value of the set
while(!value.done) {
    // Prints the value
    console.log('Value: ' + value.value);
    value = values.next();
}

// Deletes all the elements in the set
set.clear();

// Retrieves the set size. Prints "0"
console.log(set.size);
```

前面代码的现场演示如下所示，也可以作为 JSFiddle 获得[。](https://jsfiddle.net/rhpjjppc/embedded/result/)

[https://jsfiddle.net/rhpjjppc/embedded/](https://jsfiddle.net/rhpjjppc/embedded/)

在第二个演示中，我们将看到如何使用一个`WeakSet`对象。

```
// Creates a new WeakSet object
var weakset = new WeakSet();
// Defines an object that will be stored in the set
var obj = {name: 'Aurelio De Rosa'};

// Adds an object to the set
weakset.add(obj);
// Adds a function to the set
weakset.add(function(){});
// Adds another object to the set
weakset.add({name: 'John Doe'});

// Checks whether the Object {name: 'John Doe'} exists in the weak set. Prints "false" because despite the fact that the passed object and the stored one have the same values and properties, they are different objects
console.log(weakset.has({name: 'John Doe'}));

// Checks whether the Object obj exists in the weak set. Prints "true" because it's the same object
console.log(weakset.has(obj));

// Deletes the obj element. Prints "true"
console.log(weakset.delete(obj));

// Deletes the function(){} element. Prints "false" because the passed function and the stored one they are different functions (objects)
console.log(weakset.delete(function(){}));

// Deletes all the elements of the weak set
weakset.clear();
```

前面代码的现场演示如下所示，也可以作为 JSFiddle 获得[。](https://jsfiddle.net/hqmaovLb/embedded/result/)

[https://jsfiddle.net/hqmaovLb/embedded/](https://jsfiddle.net/hqmaovLb/embedded/)

## 结论

在本教程中，我介绍了新的`Set`和`WeakSet`数据类型。除了`Map`和`WeakMap`，它们是 ECMAScript 6 中最有趣的新类型。我希望你喜欢这篇文章，并学到一些有趣的东西。

## 分享这篇文章