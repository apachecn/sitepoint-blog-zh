# 快速提示:如何在 JavaScript 中转换字符串的字符大小写

> 原文：<https://www.sitepoint.com/transform-string-character-case-javascript/>

在本教程中，您将学习如何使用本地 JavaScript 方法将字符串的字符大小写转换为大写、小写和标题大小写。

JavaScript 提供了许多函数和方法，允许您为不同的目的操作数据。我们最近看到了将字符串转换成数字的方法，以及将 T2 数字转换成字符串或序数的方法，以及拆分字符串的方法。本文将介绍转换字符串的字符大小写的方法——这对于以某种格式表示字符串或进行可靠的字符串比较很有用。

## 将字符串转换成小写

如果需要小写的字符串，可以使用字符串上可用的`toLowerCase()`方法。此方法返回所有字符都是小写的字符串。

例如:

```
const str = 'HeLlO';
console.log(str.toLowerCase()); // "hello"
console.log(str); // "HeLlo" 
```

通过对`str`变量使用`toLowerCase()`方法，可以检索所有字符都是小写的同一个字符串。注意，一个新的字符串被返回，而不影响`str`的值。

## 将字符串转换为大写

如果需要大写的字符串，可以使用字符串上可用的`toUpperCase()`方法。此方法返回所有字符都是大写的字符串。

例如:

```
const str = 'HeLlO';
console.log(str.toUpperCase()); // "HELLO"
console.log(str); // "HeLlo" 
```

通过对`str`变量使用`toUpperCase()`方法，可以检索所有字符都是大写的同一个字符串。注意，一个新的字符串被返回，而不影响`str`的值。

## 将字符串转换为标题大小写

转换字符串大小写最常见的用例是将其转换为标题大小写。这可以用来显示姓名和标题。

做这件事有不同的方法。一种方法是对字符串的第一个字符使用方法`toUpperCase()`，然后将其连接到字符串的其余部分。例如:

```
const str = 'hello';
console.log(str[0].toUpperCase() + str.substring(1).toLowerCase()); // "Hello" 
```

在这个例子中，您使用`str`变量上的`0`索引来检索第一个字符。然后，使用`toUpperCase()`方法将其转换为大写。最后，使用`substr()`方法检索字符串的剩余部分，并将字符串的剩余部分连接到第一个字母。您对字符串的其余部分应用`toLowerCase()`,以确保它是小写的。

这只将单词的第一个字母转换成大写。然而，在某些情况下，如果你有一个句子，你可能想把句子中的每个单词都转换成大写。在这种情况下，最好使用这样的函数:

```
function toTitleCase (str) {
  if (!str) {
    return '';
  }
  const strArr = str.split(' ').map((word) => {
    return word[0].toUpperCase() + word.substring(1).toLowerCase();
  });
  return strArr.join(' ');
}

const str = 'hello world';
console.log(toTitleCase(str)); // "Hello World" 
```

`toTitleCase()`函数接受一个参数，该参数是要转换为标题大小写的字符串。

在函数中，首先检查字符串是否为空，如果为空，则返回一个空字符串。

然后，在空格分隔符上分割字符串，这将返回一个数组。之后，您在数组上使用 [map 方法](https://www.sitepoint.com/quick-tip-stop-writing-loops-start-thinking-with-maps/)对数组中的每一项应用您在上一个示例中看到的转换。这将每个单词转换为标题大小写。

最后，通过相同的空格分隔符将数组中的项目连接成一个字符串，并返回它。

## 活生生的例子

在下面的 CodePen 演示中，您可以尝试一下`toLowerCase()`和`toUpperCase()`的功能。当您在输入中输入一个字符串时，它会被转换为大写和小写并显示出来。您可以尝试在字符串中使用不同大小写的字符。

见笔 [在](https://codepen.io/SitePoint/pen/abGBBOX) [CodePen](https://codepen.io) 上通过 site point([@ site point](https://codepen.io/SitePoint))
变换 JavaScript 中字符串的字符大小写。