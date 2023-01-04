# ES6 在行动:新阵列。*和 Array.prototype.*方法

> 原文：<https://www.sitepoint.com/es6-array-methods-array-prototype/>

**在本文中，我们将使用`Array.*`和`Array.prototype.*`来讨论 ES6 中大多数与`Array`类型一起工作的新方法。**

当讨论它们时，当我描述一个“类”方法时，我将写`Array.method()`,当我概述一个“实例”方法时，我将写`Array.prototype.method()`。

我们还将看到一些使用示例，并提到几个聚合填充。如果你需要一个多填充库，你可以使用由[保罗·米勒](http://paulmillr.com)开发的 [es6-shim](https://github.com/paulmillr/es6-shim/) 。

## 数组. from()

我要提的第一个方法是`Array.from()`。它从类似数组或可迭代的对象创建一个新的`Array`实例。此方法可用于解决数组类对象的一个老问题，大多数开发人员使用以下代码来解决该问题:

```
// typically arrayLike is arguments
var arr = [].slice.call(arrayLike); 
```

`Array.from()`的语法如下所示:

```
Array.from(arrayLike[, mapFn[, thisArg]]) 
```

其参数的含义是:

*   `arrayLike`:类似数组或可迭代的对象
*   `mapFn`:调用包含的每个元素的函数
*   `thisArg`:用作`mapFn`函数的上下文(`this`)的值。

现在我们已经知道了它的语法和参数，让我们来看看这个方法是如何工作的。在下面的代码中，我们将创建一个函数，该函数接受数量可变的参数，并返回一个数组，该数组包含以下两个元素:

```
function double(arr) {
  return Array.from(arguments, function(elem) {
    return elem * 2;
  });
}

const result = double(1, 2, 3, 4);

// prints [2, 4, 6, 8]
console.log(result); 
```

前面代码的现场演示如下所示，也可以在 JSBin 获得[。](https://SitePointEditors.jsbin.com/micufak/edit?js,console)

[ES6 在行动:jsbin.com 上的新阵列方法](https://jsbin.com/micufak/embed?js,console)