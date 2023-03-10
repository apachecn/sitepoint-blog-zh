# 基本 JavaScript 正则表达式示例

> 原文：<https://www.sitepoint.com/basic-javascript-regular-expression/>

我写了一篇关于基本 JavaScript 正则表达式示例的帖子，让初学者体验一下在 jQuery/JavaScript 中使用[正则表达式](https://www.sitepoint.com/learn-regex/)的威力。

**示例:**想要从价格数据集中提取价格，可以是整数或浮点数。

**数据集:**

```
$55.99
$55
$55.00
etc...
```

**正则表达式模式:**

```
/[(0-9)+.?(0-9)*]+
```

你可以使用一个正则表达式工具，比如 Firefox Add-on:Regular Expressions Tester。

![regular-expressions-price-1](img/8b70ca74409d7d058ef1af5155345f04.png "regular-expressions-price-1")

如您在工具中所见，**匹配的表达式以黄色**突出显示。

```
var price = '$55.99';
var priceRegex = /[(0-9)+.?(0-9)*]+/igm;

console.log(priceRegex.test(price));
console.log(price.match(priceRegex));
console.dir(priceRegex.exec(price));
```

现在，如果我们使用。test()、match()和 exec() 这就是我们得到的。

![regular-expressions-price-2](img/80be18492c249af43a848e539e2c345c.png "regular-expressions-price-2")

在索引 0 处的 exec()函数**结果给出了我们的匹配，因此提取价格的完整代码。**

```
var price = '$55.99';
var priceRegex = /[(0-9)+.?(0-9)*]+/igm;

var price = parseFloat(priceRegex.exec(price));
console.log(price);
//output: 55.99
```

这只是一个非常基本的例子，但是应该可以让您了解如何在 JavaScript 中使用 Regex。欢迎评论。

## 分享这篇文章