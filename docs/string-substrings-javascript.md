# 快速提示:如何在 JavaScript 中将一个字符串拆分成子字符串

> 原文：<https://www.sitepoint.com/string-substrings-javascript/>

在本教程中，你将学习将一个字符串拆分成子字符串的不同方法，以及每种方法的有用之处。

在 JavaScript 中，字符串可以很容易地用于不同的目的——使用语言中可用的本地方法。我们最近讨论了如何在 JavaScript 中将数字转换成字符串，以及如何将字符串转换成数字。

操纵字符串的另一种方法是提取一部分用于其他用途。例如，您可能有一个完整的 URL，但想只提取散列部分。或者，您可能有一个用逗号分隔的项目列表，并希望单独使用其中的每个项目。

## 使用 substring()将字符串拆分成子字符串

JavaScript 中的所有字符串都有一个`substring()`方法。此方法可用于检索特定索引处的子字符串。

`substring()`接受两个参数。第一个是必需的，表示子字符串开始的索引。第二个是可选的，表示子串结束的索引。如果省略第二个参数，子字符串将从作为第一个参数提供的索引开始，一直延续到字符串的末尾。

需要注意的是，第一个参数是基于 0 的索引，这意味着第一个字符在索引`0`处，第二个在索引`1`处，依此类推。另一个需要注意的重要事情是，第二个参数比希望子字符串结束的索引大一。例如，如果您希望字符串在索引`12`处结束，您可以为第二个参数提供`13`。

例如:

```
const a = 'Bytes and bits';
const b = a.substring(10, 13);
console.log(b); // "bit"
console.log(a); // "Bytes and bits" 
```

在这个例子中，`substring()`被用在变量`a`上来检索子串。子串从索引`10`开始，到索引`13`结束。返回值是`bit`。注意`substring()`返回子串，而不改变它所使用的变量的值。

见 [用子串拆分字符串](https://codepen.io/SitePoint/pen/OJZXEYB)的笔在 [CodePen](https://codepen.io) 上用 site point([@ site point](https://codepen.io/SitePoint))
)。