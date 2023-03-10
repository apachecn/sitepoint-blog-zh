# 快速提示:如何在 JavaScript 中将字符串转换成数字

> 原文：<https://www.sitepoint.com/convert-string-to-number-javascript/>

**在 JavaScript 中，有很多方法可以在[数据类型](https://www.sitepoint.com/beginners-guide-javascript-variables-and-datatypes/#datatypes)之间进行转换。我们已经讲述了如何将一个数字转换成一个字符串。在这个简短的教程中，我们将看看如何在 JavaScript 中将字符串转换成数字。**

在许多情况下，数字可能会存储为字符串。例如，从表单元素接收的值总是字符串。

一般来说，您可以将包含一个数字(且仅包含一个数字)的 JavaScript 字符串视为一个数字，JavaScript 将自动为您执行字符串到数字的转换。但是，有时您需要从字符串中提取一个数字，或者对如何进行转换进行更多的控制。

在这个快速技巧中，我们将介绍三种将字符串转换为数字的方法。

## 使用 Number 将字符串转换为数字

在 JavaScript 中将字符串转换成数字的最直接的方法是使用内置的`Number`构造函数。例如:

```
const str = "10"
console.log(str); // "10"
console.log(typeof str); // "string"

const number = Number(str);
console.log(number); // 10
console.log(typeof number); // "number" 
```

在控制台中记录`str`的值及其类型时，结果分别为字符串形式的`10`和`string`。转换后，结果分别是作为数字的`10`和`number`。

你可以在下面的 CodePen 演示中看到这个例子。

看笔 [在](https://codepen.io/SitePoint/pen/oNdgGNP) [CodePen](https://codepen.io) 上通过 site point([@ site point](https://codepen.io/SitePoint))
将一个字符串转换成一个带有 Number() 的数字。