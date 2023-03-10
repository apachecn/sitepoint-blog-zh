# 在 JavaScript 中修剪字符串

> 原文：<https://www.sitepoint.com/trimming-strings-in-javascript/>

字符串修整是程序员处理的最常见的任务之一。修剪函数从字符串的开头和/或结尾删除空白。JavaScript 1.8.1 中引入了对裁剪的本机支持，这意味着在 IE9 之前，它们不是 Internet Explorer 的一部分。本文将向您展示如何修剪，以及如何构建更加灵活和强大的修剪函数。

JavaScript 提供了三个函数来执行各种类型的字符串修整。第一个是`trimLeft()`，从字符串的开头开始删除字符。第二个是`trimRight()`，删除字符串末尾的字符。最后一个函数，`trim()`，删除两端的字符。像许多其他语言一样，JavaScript 的本地函数只删除空白字符。相反，PHP 的函数可以删除任意字符。本文将介绍类似 PHP 的修剪函数。

## `trimLeft()`

我们来看看原生`trimLeft()`是如何工作的。

```
var string = " Hello world";
console.debug(string.trimLeft());
// prints "Hello world"
```

请注意，字符串开头的空格被删除了。我们可以通过指定要删除的字符列表来增强这个功能。增强版如下所示。

```
String.prototype.trimLeft = function(charlist) {
if (charlist === undefined)
charlist = "\s";

return this.replace(new RegExp("^[" + charlist + "]+"), "");
};
```

这个函数的关键点是`RegExp`对象和特殊的`^`字符。`RegExp`创建一个正则表达式对象，它用给定的模式匹配文本。在正则表达式中，`^`字符表示字符串的开始。请注意，输入字符列表区分大小写。

下面的例子向您展示了`trimLeft()`如何删除字符串开头的空格。这种行为类似于等效的本机函数。

```
var string = " Hello world";
console.debug(string.trimLeft());
// prints "Hello world"
```

以下示例显示了如何从字符串的开头删除一组给定的字符。第一个示例修剪小写字符“o”、“e”和“l”，以及大写字符“H”。第二个示例修剪小写字母“h”。由于字符串以大写字母“H”开头，因此不会发生修剪。

```
var string = "Hello world";
console.debug(string.trimLeft("Hoel"));
// prints " world"

string = "Hi mate!";
console.debug(string.trimLeft("h"));
// prints "Hi mate!"
```

## `trimRight()`

增强的`trimRight()`函数也接受一个代表要被修剪的字符的参数。新的`trimRight()`功能如下所示。

```
String.prototype.trimRight = function(charlist) {
if (charlist === undefined)
charlist = "\s";

return this.replace(new RegExp("[" + charlist + "]+$"), "");
};
```

功能和上一个挺像的。唯一的区别是正则表达式模式。我们不使用`^`字符来表示字符串的开始，而是使用`$`字符来表示字符串的结束。

接下来的例子展示了如何使用`trimRight()`函数。第一个很简单，和前面几个差不多。第二个非常有趣，因为它不仅使用了字符列表，还使用了一个 [POSIX 字符类](http://en.wikipedia.org/wiki/Regular_expression#POSIX_character_classes "POSIX character class")。字符类是指定字符集的速记符号。该示例使用了`W`代码。`W`代表非文字字符，或除字母、数字和下划线字符以外的所有字符。

```
var string = "Hello world";
console.debug(string.trimRight("odl"));
// prints "Hello wor"

string = "Hi mate!";
console.debug(string.trimRight("\W"));
// prints "Hi mate"
```

## `trim()`

最后一个函数非常简单，依赖于前两个。它会修剪字符串的开头和结尾。它的实现也很简单，因为它包括在同一个字符串上调用`trimLeft()`和`trimRight()`。

```
String.prototype.trim = function(charlist) {
return this.trimLeft(charlist).trimRight(charlist);
};
```

使用`trim()`的一些例子如下所示。

```
var string = "he loves she";
console.debug(string.trim("hes "));
// prints "lov"

string = " I write for JSPro.com ";
console.debug(string.trim());
// prints "I write for JSPro.com"
```

## 结论

修剪功能对于删除用户键入的多余空格很有用。很多时候，用户甚至没有意识到他们输入了额外的空格。这个事实也可能导致登录问题，例如，如果一个用户用尾随空格注册自己。然而，这并不是修剪的唯一用例。使用本文中介绍的 trim 函数的增强版本，您还将能够解决比原生版本更广泛的问题。

## 分享这篇文章