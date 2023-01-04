# ES6 实践:新的字符串方法— String.prototype.*

> 原文：<https://www.sitepoint.com/es6-string-methods-string-prototype/>

**在我上一篇关于 [ES6 数组方法](https://www.sitepoint.com/es6-array-methods-array-prototype/)的文章中，我介绍了 ECMAScript 6 中支持`Array`类型的新方法。在本教程中，您将学习使用字符串的新 ES6 方法:`String.prototype.*`**

我们将开发几个例子，并提及它们可用的 polyfills。请记住，如果您想使用单个库来填充它们，您可以使用由 [Paul Miller](http://paulmillr.com) 开发的 [es6-shim](https://github.com/paulmillr/es6-shim/) 。

## String.prototype.startsWith()

每种现代编程语言中最常用的函数之一是验证字符串是否以给定子串开头。在 ES6 之前，JavaScript 没有这个功能，也就是说你得自己写。以下代码显示了开发人员通常如何对其进行多项填充:

```
if (typeof String.prototype.startsWith !== 'function') {
  String.prototype.startsWith = function (str){
    return this.indexOf(str) === 0;
  };
} 
```

或者，可选地:

```
if (typeof String.prototype.startsWith !== 'function') {
  String.prototype.startsWith = function (str){
    return this.substring(0, str.length) === str;
  };
} 
```

这些片段仍然有效，但是它们并没有准确地再现新出现的`String.prototype.startsWith()`方法所做的事情。新方法具有以下语法:

```
String.prototype.startsWith(searchString[, position]); 
```

您可以看到，除了子字符串之外，它还接受第二个参数。`searchString`参数指定您想要验证的子字符串是字符串的开始。`position`表示开始搜索的位置。`position`的默认值为 0。如果字符串以提供的子字符串开始，方法返回`true`，否则返回`false`。记住方法是区分大小写的，所以“hello”和“Hello”是不同的。

此方法的一个使用示例如下所示:

```
const str = 'hello!';
let result = str.startsWith('he');

// prints "true"
console.log(result);

// verify starting from the third character
result = str.startsWith('ll', 2);

// prints "true"
console.log(result); 
```

前面代码的现场演示如下所示，也可以在 JSBin 获得[。](https://SitePointEditors.jsbin.com/cotafab/edit?js,console)

[ES6 在行动:jsbin.com 的新字符串方法](https://jsbin.com/cotafab/embed?js,console)