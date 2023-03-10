# 快速提示:如何在 JavaScript 中使用三元运算符

> 原文：<https://www.sitepoint.com/javascript-ternary-operator/>

在本教程中，我们将探索 JavaScript 中三元运算符的语法及其一些常见用法。

可以使用**三元运算符**(也称为**条件运算符**)来执行内嵌条件检查，而不是使用`if...else`语句。它使代码更短，可读性更强。它可用于根据条件为变量赋值，或根据条件执行表达式。

## 句法

三元运算符接受三个操作数；这是 JavaScript 中唯一的[操作符。您提供一个要测试的条件，后面跟一个问号，后面跟两个用冒号分隔的表达式。如果条件被认为是真的(](https://www.sitepoint.com/javascript-operators-conditionals-functions/#javascriptoperators)[真值](https://www.sitepoint.com/javascript-truthy-falsy/)，则执行第一个表达式；如果它被认为是假的，最后的表达式被执行。

它的使用格式如下:

```
condition ? expr1 : expr2 
```

这里，`condition`是要测试的条件。如果其值被认为是`true`，则执行`expr1`。否则，如果其值被认为是`false`，则执行`expr2`。

`expr1`和`expr2`是任何一种表达式。它们可以是变量、函数调用，甚至是其他条件。

例如:

```
1 > 2 ? console.log("You are right") : console.log('You are wrong'); 
```

## 使用三元运算符赋值

三元运算符最常见的用法之一是决定将哪个值赋给一个变量。通常，一个变量的值可能取决于另一个变量或条件的值。

虽然这可以通过使用`if...else`语句来实现，但这会使代码变得更长，可读性更差。例如:

```
const numbers = [1,2,3];
let message;
if (numbers.length > 2) {
  message = 'The numbers array is too long';
} else {
  message = 'The numbers array is short';
}

console.log(message); // "The numbers array is too long" 
```

在这个代码示例中，首先定义变量`message`。然后，使用`if...else`语句来确定变量的值。

这可以在一行中使用三元运算符简单地完成:

```
const numbers = [1,2,3];
let message = numbers.length > 2 ? 'The numbers array is too long' : 'The numbers array is short';

console.log(message); // "The numbers array is too long" 
```

## 使用三元运算符执行表达式

三元运算符可用于执行任何类型的表达式。

例如，如果您想根据变量值决定运行哪个函数，您可以像这样使用`if...else`语句:

```
if (feedback === "yes") {
  sayThankYou();
} else {
  saySorry();
} 
```

这可以在一行中使用三元运算符完成:

```
feedback === "yes" ? sayThankYou() : saySorry(); 
```

如果`feedback`的值为`yes`，那么`sayThankYou`函数将被调用并执行。否则，将调用并执行`saySorry`函数。

## 使用三元运算符进行空值检查

在许多情况下，您可能要处理有或没有定义值的变量，例如，从用户输入中检索结果，或者从服务器中检索数据。

使用三元运算符，您可以通过在条件操作数的位置传递变量名来检查变量不是`null`或`undefined`。

当变量是一个[对象](https://www.sitepoint.com/back-to-basics-javascript-object-syntax/)时，这尤其有用。如果您试图访问一个实际上是`null`或`undefined`的对象的属性，就会发生错误。首先检查对象是否被真正设置，可以帮助您避免错误。

例如:

```
let book = { name: 'Emma', author: 'Jane Austen' };
console.log(book ? book.name : 'No book'); // "Emma"

book = null;
console.log(book ? book.name : 'No book'); // "No book" 
```

在这个代码块的第一部分，`book`是一个具有两个属性的对象——T1 和 T2。当在`book`上使用三进制运算符时，它检查它不是`null`或`undefined`。如果不是——意味着它有一个值——`name`属性被访问并记录到控制台中。否则，如果为空，则`No book`登录控制台。

由于`book`不是`null`，所以在控制台中记录的是书名。然而，在第二部分中，当应用相同的条件时，三元运算符中的条件将失败，因为`book`是`null`。因此，控制台中会记录“没有书”。

## 嵌套条件

虽然三进制操作符被内联使用，但是多个[条件](https://www.sitepoint.com/javascript-operators-conditionals-functions/#conditionalstatements)可以被用作三进制操作符表达式的一部分。您可以嵌套或链接多个条件来执行类似于`if...else if...else`语句的条件检查。

例如，变量的值可能依赖于多个条件。可以使用`if...else if...else`来实现:

```
let score = '67';
let grade;
if (score < 50) {
  grade = 'F';
} else if (score < 70) {
  grade = 'D'
} else if (score < 80) {
  grade = 'C'
} else if (score < 90) {
  grade = 'B'
} else {
  grade = 'A'
}

console.log(grade); // "D" 
```

在这个代码块中，您测试了`score`变量上的多个条件，以确定变量的字母分级。

可以使用三元运算符执行这些相同的条件，如下所示:

```
let score = '67';
let grade = score < 50 ? 'F'
  : score < 70 ? 'D'
  : score < 80 ? 'C'
  : score < 90 ? 'B'
  : 'A';

console.log(grade); // "D" 
```

第一个条件求值，是`score < 50`。如果是`true`，那么`grade`的值就是`F`。如果是`false`，那么计算第二个表达式，即`score < 70`。

这种情况会一直持续下去，直到所有条件都为`false`，这意味着等级的值将为`A`，或者直到其中一个条件被评估为`true`，并且其真值被分配给`grade`。

## CodePen 示例

在这个实例中，您可以测试三元运算符如何处理多个条件。

如果输入小于 100 的值，将显示消息“太低”。如果输入大于 100 的值，将显示消息“太高”。如果您输入 100，将显示消息“完美”。

在 [CodePen](https://codepen.io) 上通过 site point([@ site point](https://codepen.io/SitePoint))
查看 JS 中的笔 [三元运算符。](https://codepen.io/SitePoint/pen/bGMQxXa)