# 快速提示:在 JavaScript 中测试字符串是否匹配正则表达式

> 原文：<https://www.sitepoint.com/test-string-match-regex-javascript/>

**在这篇关于 JavaScript 正则表达式匹配的简短教程中，您将学习如何使用`test()`方法测试一个字符串是否匹配一个正则表达式。**

字符串是可以包含各种数据的文本片段，例如 URL、电话号码、姓名、数字等等。在许多情况下，您需要检查字符串是否包含一段文本或某些类型的字符。

当您测试一个字符串是否包含特定的子串时，您可能倾向于使用类似于`indexOf()`的方法。然而，对于更灵活的测试和条件，使用正则表达式是更好的选择。

JavaScript 正则表达式匹配允许您检查字符串是否包含特定的模式、子字符串或字符类型。[正则表达式](https://www.sitepoint.com/learn-regex/)对于检测字符串中可以用不同格式书写的信息很有用，比如日期。

## 根据正则表达式测试字符串

要测试字符串是否匹配正则表达式，必须首先创建一个正则表达式实例。然后，您可以使用正则表达式上可用的`test()`方法来检查字符串是否匹配正则表达式。

`test()`方法接受一个参数:测试模式的字符串。它返回一个布尔值，指示字符串是否匹配正则表达式。

例如:

```
const pattern = /test.*regular/;
const str = 'I want to test this string against a regular expression';
if (pattern.test(str)) {
  console.log('Matched');
} else {
  console.log('Not Matched');
} 
```

在本例中，您创建了模式`test.*regular`。这种模式意味着一个字符串必须依次包含单词`test`和`regular`，并且这些单词可以由零个或多个出现的任意字符分隔。

如果`test()`返回 true，则`Matched`被记录在控制台中。否则，`Not Matched`会登录到控制台。

因为`str`包含单词`test`和`regular`，并且`test`在字符串中位于`regular`之前，所以它将与模式匹配，并且`test()`将返回 true。

您还可以使用 [RegExp](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/RegExp) 构造函数来声明模式:

```
const pattern = new RegExp('test.*regular');
const str = 'I want to test this string against a regular expression';
if (pattern.test(str)) {
  console.log('Matched');
} else {
  console.log('Not Matched');
} 
```

您可以在下面的 CodePen 演示中测试这一点。

参见 [CodePen](https://codepen.io) 上的 site point([@ site point](https://codepen.io/SitePoint))
对正则表达式测试字符串的笔[。](https://codepen.io/SitePoint/pen/KKRvKzm)