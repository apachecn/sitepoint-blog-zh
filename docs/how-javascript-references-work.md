# 快速提示:JavaScript 引用如何工作

> 原文：<https://www.sitepoint.com/how-javascript-references-work/>

**TL；DR:JavaScript 中没有指针，引用的工作方式与我们通常在大多数其他流行的编程语言中看到的不同。在 JavaScript 中，从一个变量到另一个变量的引用是不可能的。并且，只有复合值(例如..对象或数组)可以通过引用来分配。**

![Morpheus: What if I told you, you can write references in JavaScript?](img/467ae9817a781103b4660e46555acba7.png)

整篇文章中使用了以下术语:

*   标量–单个值或数据单位(如整数、[布尔](https://www.sitepoint.com/boolean-data-type/)，字符串)
*   复合–由多个值组成(例如数组、对象、集合)
*   primitive——直接值，相对于包含实际值的引用。

JavaScript 的标量类型是原语，但一些语言，如 Ruby，有标量引用类型。注意，在 JavaScript 中，标量原始值是不可变的，而复合值是可变的。

本文原载于[媒体](https://medium.com/@naveenkarippai/learning-how-references-work-in-javascript-a066a4e15600#.xcf8lpvzy)。

## JavaScript 参考的底线是

1.  赋给变量的`typeof`值决定了该值是按值赋值还是按引用赋值存储。
2.  在变量赋值时，标量原始值(数字、字符串、布尔、未定义、空、符号)按值赋值，复合值按引用赋值。
3.  JavaScript 中的引用只指向包含的值，而不指向其他变量或引用。
4.  在 JavaScript 中，标量原始值是不可变的，而复合值是可变的。

## 按值分配的快速示例:

在下面的代码片段中，我们将一个标量原始值(一个数字)赋给一个变量，因此此处适用按值赋值。首先，变量`batman`被初始化，当变量`superman`被赋予存储在`batman`中的值时，它创建该值的新副本并存储它。当变量`superman`被修改时，`batman`不受影响，因为它们指向不同的值。

```
var batman = 7;
var superman = batman;   //assign-by-value
superman++;
console.log(batman);     //7
console.log(superman);   //8 
```

![Assign-by-value example](img/edb8afd904e4cfd7f2d1f3014278f742.png)

## 按引用分配的快速示例:

在下面的代码片段中，我们将一个复合值(一个数组)赋给一个变量，因此引用赋值在这里也适用。变量`flash`和`quicksilver`是对同一个值(又名共享值)的引用。当共享值被修改时，引用将指向更新的值。

```
var flash = [8,8,8];
var quicksilver = flash;   //assign-by-reference
quicksilver.push(0);
console.log(flash);        //[8,8,8,0]
console.log(quicksilver);  //[8,8,8,0] 
```

![Assign-by-reference example](img/e444672aef370c7cc85f09768e2299af.png)

## 如何创建新的参考

当变量中的复合值被重新赋值时，会创建一个新的引用。与大多数其他流行的编程语言不同，在 JavaScript 中，引用是指向存储在变量中的值的指针，而不是指向其他变量或引用的指针。

```
var firestorm = [3,6,3];
var atom = firestorm;   //assign-by-reference
console.log(firestorm); //[3,6,3]
console.log(atom);      //[3,6,3]
atom = [9,0,9];         //value is reassigned (create new reference)
console.log(firestorm); //[3,6,3]
console.log(atom);      //[9,0,9] 
```

![Creating a new reference](img/be750e5352c604c220a8b3d53a598fa3.png)

## 当值作为函数参数传递时，引用如何工作

在下面的代码片段中，变量`magneto`是一个复合值(数组)，因此它被赋值给变量(函数参数)`x`作为引用。

IIFE 内部调用的`Array.prototype.push`方法通过 JavaScript 引用改变变量`magneto`中的值。但是，变量`x`的重新分配创建了一个新的引用，对它的进一步修改不会影响对变量`magneto`的引用。

```
var magneto = [8,4,8];
(function(x) {        //IIFE
    x.push(99);
    console.log(x);   //[8,4,8,99]
    x = [1,4,1];      //reassign variable (create new reference)
    x.push(88);
    console.log(x);   //[1,4,1,88]
})(magneto);
console.log(magneto); //[8,4,8,99] 
```

## 如何更改复合变量中的原始值，通过 JavaScript 引用作为函数参数传递

这里的解决方案是修改引用所指向的现有复合值。在下面的代码片段中，变量`wolverine`是一个复合值(一个数组),在生命调用中，变量(函数参数)`x`通过引用被赋值。

通过将属性的值设置为`0`，可以使用属性`Array.prototype.length`创建一个空数组。因此，变量 wolverine 通过 JavaScript 引用被更改为变量`x`中设置的新值。

```
var wolverine = [8,7,8];
(function(x) {              //IIFE
    x.length = 0;           //make empty array object
    x.push(1,4,7,2);
    console.log(x);         //[1,4,7,2]
})(wolverine);
console.log(wolverine);     //[1,4,7,2] 
```

## 如何通过赋值来存储复合值

这里的解决方案是手动复制复合值，然后将复制的值赋给一个变量。因此，赋值的引用并不指回原始值。

创建复合值(数组对象)的(浅层)副本的推荐方法是对其调用`Array.prototype.slice`方法，不传递任何参数。

```
var cisco = [7,4,7];
var zoom = cisco.slice();  //create shallow copy
cisco.push(77,33);
console.log(zoom);         //[7,4,7]
console.log(cisco);        //[7,4,7,77,33] 
```

![Diagram4](img/db4c811b88af7125131a04e3a793041f.png)

## 如何通过按引用赋值来存储一个标量原始值？

这里的解决方案是将标量原始值包装在一个复合值(即一个对象或数组)中作为其属性值。因此，它可以通过引用来分配。在下面的代码片段中，变量`speed`中的标量原始值被设置为对象`flash`的属性。因此，它在对变量(函数参数)`x`的生命调用中被按引用赋值。

```
var flash = { speed: 88 };
(function (x) {             //IIFE
    x.speed = 55;
})(flash);
console.log(flash.speed);   //55 
```

## 摘要

![Neo stopping bullets in mid-air. Caption: References in JavaScript](img/a7a4faa0f207eae40d0a85babb4ef846.png)

很好地理解 JavaScript 中的引用可以帮助开发人员避免许多常见错误，编写更好的代码。

编码快乐！！

## 分享这篇文章