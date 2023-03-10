# 快速提示:如何在 JavaScript 中将数字转换成字符串

> 原文：<https://www.sitepoint.com/convert-number-to-string-javascript/>

JavaScript 非常灵活，提供了许多不同的方式在数据类型[和数据类型](https://www.sitepoint.com/beginners-guide-javascript-variables-and-datatypes/#datatypes)之间转换。在这个简短的教程中，我们将看看如何在 JavaScript 中将数字转换成字符串。您可能希望这样做，以使数字数据对用户更具可读性，例如，将数字显示为句子的一部分。

本教程探索了在 JavaScript 中将数字转换成字符串的四种方法。我们根据您的具体需求和使用情形推荐不同的方法:

*   **[字符串插值](#convertanumbertoastringusinginterpolation) :** 在字符串内插入数值时。例如，在网页上显示类似“您已经使用了 **24** 的 **7** 信用点数”的文本。你也可以使用 [**串联**](#convertanumbertoastringusingstringconcatenation) 但是要小心。
*   **[字符串](#convertanumbertoastringusingstring)或 [toString()](#convertanumbertoastringusingtostring) :** 将数值的类型改为字符串时。例如，使用数字作为需要字符串的函数或 API 的输入。`String`和`toString()`基本相同，但对`undefined`和`null`变量的处理不同。

如果你想做相反的事情，你可能会对如何将一个字符串转换成一个数字感兴趣。

## 使用插值法将数字转换为字符串

[插值](https://en.wikipedia.org/wiki/String_interpolation)可能是在字符串中使用数字的可读性最强的方式。您可以使用此方法将数字插入到字符串中，而不是手动将数字转换为字符串。

要使用插值，请用反斜杠(`` ` ``)而不是引号(`"`或`'`)将字符串括起来。然后，在字符串中，您可以使用``${}``作为占位符插入任何变量。这被称为[模板字面量](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)，并且有许多其他的好处。

例如:

```
const number = 99;
console.log(`${number} percent of people love JavaScript`); // "99% of people love JavaScript" 
```

因为登录到控制台的字符串是用反斜线括起来的，所以可以使用`${}`将一个变量插入到字符串中。

你可以在下面的 CodePen 演示中看到这个例子。

参见 [CodePen](https://codepen.io) 上的 site point([@ site point](https://codepen.io/SitePoint))
在 JavaScript 中的 Pen [字符串插值。](https://codepen.io/SitePoint/pen/dyebbXP)