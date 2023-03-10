# JavaScript 显示日期的年份错误

> 原文：<https://www.sitepoint.com/javascript-showing-wrong-year-date/>

快速张贴，让您 JavaScript 新手在那里，当宣布一个日期，它可能会显示错误的日期年。你可能会想“javascript 日期返回到下一年，我看到的是 2012 年而不是 2011 年，wtf？”。考虑下面的例子:

因此，我们希望看到的日期是 2011 年 7 月 15 日星期五。

```
var todaysDate = new Date();
console.log(todaysDate);
//output: Date {Fri Aug 12 2011 18:45:53 GMT+1000}

var expiryDate = new Date('15/07/2011');
console.log(expiryDate);
//output: Date {Wed Mar 07 2012 00:00:00 GMT+1000}

var expiryDate = new Date('07/15/2011');
console.log(expiryDate);
//output: Date {Fri Jul 15 2011 00:00:00 GMT+1000}
```

你能弄清楚发生了什么事吗？嗯，JavaScript 的 getMonth()函数从 0 开始代表一月，1 代表二月，以此类推……(你得加一个！)但这是错误的一年，一天一切！

如果我们尝试将日期解析为单个参数，也不会产生正确的结果。

```
var expiryDate = new Date(2011, 15, 07);
console.log(expiryDate);
//output: Date {Sat Apr 07 2012 00:00:00 GMT+1000}
```

但是，如果我们解析为文本日期，我们会得到正确的结果！:)

```
var expiryDate = new Date('July 15, 2011');
console.log(expiryDate);
//output: Date {Fri Jul 15 2011 00:00:00 GMT+1000}
```

日期可能是一件痛苦的事情，所以如果你做了很多关于日期的工作，我建议你使用一个 [JavaScript 日期库](http://www.jquery4u.com/jquery-functions/datetime-functions-complete-listing/)来让用 JavaScript 管理日期变得更有效率！

这也是一个[很棒的日期资源](http://www.hunlock.com/blogs/Javascript_Dates-The_Complete_Reference)(非常全面！).

## 分享这篇文章