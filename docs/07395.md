# 创建 ISO 日戳

> 原文：<https://www.sitepoint.com/creating-an-iso-datestamp/>

小而甜的函数系列的第四篇文章介绍了一个名为`datestamp()`的函数，它使用各种 JavaScript `Date`方法产生一个 [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) 格式的 <abbr title="Universal Co-ordinated Time">UTC</abbr> 日期字符串，比如“1991-08-06T08:30:00Z”。

<abbr title="International Standards Organisation, Standard 8601">ISO 8601</abbr> 格式是一种非常健壮且无处不在的表示日期的方式。它是许多其他标准和规范的首选格式，因为它符合许多条件:

*   它存储完整的日期和时间，为创建任何其他格式提供所有必要的原始信息。
*   它有明确的分隔符，没有内部空白，并且总是一个固定的长度——所有这些都使得解析起来容易、便宜、明确。
*   它可以用在许多特定于语言的日期结构中，比如 JavaScript `Date`对象的输入，或者 <abbr title="Structured Query Language">SQL</abbr> `DATESTAMP`列中的值。
*   如果按字母顺序排列，它就按时间顺序排列。
*   当一个 JavaScript `Date`对象被传递给`JSON.stringify()`时，它会自动生成。
*   是由 <abbr title="Word Wide Web Consortium">W3C</abbr> 推荐的格式和概要文件[。](https://www.w3.org/TR/NOTE-datetime "W3C Date and Time Formats")
*   它用于<abbr title="HyperText Markup Language Version 5">html 5</abbr>的输入。

`datestamp`函数是创建这种格式的便利抽象，甚至可以在最基本的 JavaScript 浏览器中工作。代码如下:

```
function datestamp(date)
{
  if(isNaN((date = typeof(date) !== 'undefined' ? new Date(date) : new Date()).getTime()))
  {
    return null;
  }

  var pad2 = function(n) {
    return (n < 10 ? '0' : '') + n;
  },
  tokens = [[['FullYear'], ['Month', 1], ['Date']], [['Hours'], ['Minutes'], ['Seconds']]];

  for(var a = tokens.length, i = 0; i < a; i ++)
  {
    for(var b = tokens[i].length, j = 0; j < b; j ++)
    {
      tokens[i][j] = pad2(date['getUTC' + tokens[i][j][0]]() + (tokens[i][j][1] || 0));
    }
  }

  return tokens[0].join('-') + 'T' + tokens[1].join(':') + 'Z';
}
```

## 该功能如何工作

该函数可以创建一个全新的日期，也可以从 JavaScript 的`Date()`构造函数支持的任何输入日期开始。支持的格式包括 Unix 时间戳、ISO 时间戳和现有的`Date`对象。下面的例子展示了`datestamp()`的作用。请注意，应该避免使用部分日期，如`"1991-08-06"`，因为浏览器对时间的假设会有所不同。

```
datestamp();
datestamp(1333880482000);
datestamp("Mon, 07 Feb 2000 09:22:04 GMT");
datestamp("Sun, 06 Feb 2000 23:22:04 +10:00");
datestamp("1973-05-29T03:03:45Z");
datestamp(new Date(1349646120000));
```

如果输入无效，那么函数返回`null`。否则，使用各种特定于 UTC 的方法创建并格式化一个`Date`对象，比如`getUTCFullYear()`和`getUTCDate()`。你可以看到`tokens`数组最初是如何存储一组对这些函数的引用，迭代代码使用这些引用来创建每个方法名，比如`Minutes`代表`getUTCMinutes()`；调用方法，然后它返回的值覆盖原始引用。

`Month`的定义也指定了一个数字增量–`["Month", 1]`,而不是简单的`["Month"]`。这是因为`getUTCMonth()`方法返回介于`0`和`11`之间的数字，而 ISO 格式的月份必须是从`1`到`12`(或者更确切地说，是从`"01"`到`"12"`)。

将令牌保存在数组中并不是特别必要，只是非常方便。这样做意味着我们可以通过动态构建方法调用来减少代码量，然后只需几个`join()`调用就可以编译大部分 datestamp。

## 万能最好！

`datestamp()`函数只创建 UTC 格式的日期，因为它们是供程序使用的，而不是人类可读的输出(尽管，就程序格式而言，它非常可读！).将日期和时间存储在不同的时区也是不必要的，而且可能会造成混乱——最简单的方法是*总是*将它们存储为 UTC。这样，您就有了一个比较和评估它们的通用参考框架，而不会丢失国际化所需的任何信息。

当需要在用户浏览器中实际显示日期和时间时，将它们转换成更友好的本地格式。JavaScript 在这方面特别方便，因为它使用客户端的时钟在客户端进行评估。因此，通过`Date()`构造函数传递的任何时间戳都会自动转换成用户的地区(除非您使用了`getUTC`方法，就像我们在这个函数中所做的那样)。采用 ISO 格式的 UTC 日期戳，并将其转换为人类友好且特定于地区的日期，可以像下面这样简单:

```
new Date("1991-08-06T08:30:00Z").toString();
```

## 结论

可以说，*ISO 8601 格式是*存储日期最有用的格式。但是，它有一个小小的缺点，事实上一些老的浏览器不支持它作为`Date()`构造函数的输入。这些浏览器包括 Safari 3 和 Internet Explorer 8 或更早版本。因此，在这个简洁明了的函数系列的下一篇文章中，我将向您展示一个倒数函数，它解析 ISO 日期戳来生成 Unix 时间戳——甚至 IE6 都可以理解！

## 分享这篇文章