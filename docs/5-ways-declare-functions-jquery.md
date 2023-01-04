# 在 jQuery 中声明函数的 5 种不同方式

> 原文：<https://www.sitepoint.com/5-ways-declare-functions-jquery/>

在本文中，我们研究了几种定义 JavaScript 功能块的方法。

对于初学者来说，选择哪种方式来声明 JavaScript 函数可能会很困惑，有几种不同的语法选项。每一种都有优点、缺点和合适的用法，它们会让你大吃一惊。

## 1.常规 JavaScript 函数

在 JavaScript 中声明函数的第一个也是最明显的方法是使用`function`声明。一个名为`multiply()`的函数采用两个参数`x`和`y`，将它们相乘，然后返回值，可以用以下语法实现:

```
function multiply(x,y) {
  return x * y;
}

console.log( multiply(2, 2) );
// output: 4 
```

以这种方式定义的函数(一个函数声明)被 *[提升](https://www.sitepoint.com/javascript-hoisting/)* 到当前作用域的顶部。可以将`console.log()`放在函数之前，它仍然可以工作。

## 2.JavaScript 函数表达式

同样的函数可以写成显式设置变量的表达式:

```
const multiply = function(x,y) {
  return x * y;
}

console.log( multiply(2, 2) );
// output: 4 
```

该功能是**而不是**提升的，所以只有定义后才能使用。

## 3.对象文字方法定义

在 JavaScript 中，函数被视为与任何其他值类型相同，因此您可以在另一个对象中定义一个函数。例如:

```
const mathLib = {

  // property
  PI: 3.14,

  // multiply(x,y) method
  multiply: function(x, y) {
    return x * y;
  },

  // divide(x,y) method
  divide: function(x, y) {
    return x / y;
  }

}

console.log( mathLib.multiply(2, 2) );
// output: 4 
```

同样，这个对象方法只能在被定义后被调用。

## 4.ES2015 箭头功能

[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)不需要`function`关键字，但只能赋给变量或匿名使用(比如在回调或事件处理程序中)。圆括号(`( )`)中的参数后跟一个箭头(`=>`)，表示下一个代码块中的函数:

```
const multiply = (x, y) => { return x * y; };

console.log( multiply(2, 2) );
// output: 4 
```

由于我们只有一个语句，`return`是隐式的，对于相同的功能，可以省略括号，语法更短:

```
const multiply = (x, y) => x * y; 
```

在函数只有一个参数的情况下，这些括号也可以删除:

```
const square = x => x ** 2; 
```

尽管单个参数仍然需要括号:

```
const estimatePI = () => 22 / 7; 
```

箭头函数自动将`this`赋给直接外部作用域中的值，因此不需要使用`.bind(this)`。

## 5.jQuery 扩展函数

[jQuery](https://jquery.com/) 是一个 JavaScript 库，所以创建函数也差不多。然而，jQuery 的功能可以通过添加您自己的定制方法来扩展。`jQuery.fn.extend()`方法扩展了 jQuery prototype ( `$.fn`)对象，因此新功能可以链接到主`jQuery()`函数。

例如，以下代码定义了新的`check`和`uncheck` jQuery 方法来修改复选框输入字段:

```
jQuery.fn.extend({

  // check checkboxes
  check: function() {
    return this.each(function() {
      this.checked = true;
    });
  },

  // uncheck checkboxes
  uncheck: function() {
    return this.each(function() {
      this.checked = false;
    });
  }

});

// check all checkboxes on the page
$( "input[type='checkbox']" ).check(); 
```

## 保持它的功能

函数语法通常是个人喜好的问题，但是要确保你的代码是可读的。使用一个`function`语句可能比在几周时间内用一个聪明但不可读的箭头和括号的大杂烩来迷惑自己更好。

## 分享这篇文章