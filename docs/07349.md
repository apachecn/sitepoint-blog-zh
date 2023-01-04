# 解析 ISO 日期戳

> 原文：<https://www.sitepoint.com/parsing-an-iso-datestamp/>

小而甜的函数系列的第五篇文章是上一篇文章的后续，上一篇文章向您展示了一个简单的函数，用于[创建 ISO 日戳](https://www.sitepoint.com/creating-an-iso-datestamp/ "Creating an ISO datestamp")。前一篇文章指出，一些较旧的浏览器不支持将这种格式作为`Date`构造函数的输入。本文将向您展示一个倒数函数，该函数解析 ISO 日期戳以产生一个 Unix 时间戳，它甚至可以被 IE6 理解！

`timestamp()`的代码如下所示。

```
function timestamp(datestamp)
{
  var pattern = /^([\d]{4})\-([\d]{2})\-([\d]{2})T([\d]{2}):([\d]{2}):([\d]{2})(Z|(?:[+\-][\d]{2}[:]?[\d]{2}))$/;
  if(!pattern.test(datestamp)) 
  { 
    return null; 
  }

  var 
  components = [], 
  zoneoffset = 0;
  datestamp.replace(pattern, function(a,y,m,d,h,i,s,z)
  {
    for(var bits = [y,m,d,h,i,s], i = 0; i < 6; i ++)
    {
      components[i] = parseInt(bits[i], 10);
    }
    components[1]--;

    if(z !== 'Z')
    {
      zoneoffset = 
      (
        (
          (parseInt((z = z.replace(':', '')).substr(1,2), 10) * 3600) 
          + 
          (parseInt(z.substr(3,4), 10) * 60)
        ) 
        * 
        (z.charAt(0) == '-' ? 1000 : -1000)
      );
    }
  });

  return Date.UTC.apply(Date, components) + zoneoffset;
}
```

## 该函数的作用

`timestamp()`函数获取一个 [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601 "ISO 8601 - Wikipedia") 日期戳，比如`"2012-12-06T04:19:27+00:00"`，并将其转换成一个 Unix 时间戳——自 UTC 纪元以来的毫秒数，在本例中为`1354767567000`。时间戳整数与`Date.getTime()`方法产生的整数完全相同，在大多数现代浏览器中，我们可以像这样从一个到另一个:

```
new Date("2012-12-06T04:19:27+00:00").getTime();
```

然而，一些较老的浏览器不支持这一功能——最重要的是 IE8 或更早版本，还有 Safari 3。`timestamp()`函数填补了这一空白，它提供了在旧浏览器中工作的中间转换。每当需要将 ISO 日期戳解析为`Date`时，只需首先通过`timestamp()`函数传递它，如下所示。

```
new Date(timestamp("2012-12-06T04:19:27+00:00"));
```

## 该功能如何工作

`timestamp()`函数利用了静态`Date.UTC()`方法，该方法将一组数字日期组件作为其输入，如下所示:

```
Date.UTC(2012, 11, 6, 4, 19, 27);
```

本质上，我们所要做的就是将日期戳分成这些部分，然后将它们全部传递给`Date.UTC()`，我们将获得一个 Unix 时间戳。然而，事情并没有*相当*那么简单！

首先，使用简单的正则表达式验证日期戳。它本来可以更精确，但是更简单的表达式解析起来更便宜，我们通常可以假设输入格式要么完全正确，要么根本不是 ISO 日期戳。尽管如此，如果您确实传递了一个带有非常不准确的值的日期戳，浏览器仍然会泰然自若地处理它们。例如，如果您指定日期为`"2012-26-00"`，它将被视为 2014 年 1 月 31 日——为月份`"26"`加上一年零两个月，然后为日期`"00"`减去一天。

如果日期戳验证失败，那么`timestamp()`函数返回`null`。否则，它继续将日期戳拆分成其组成整数。这是通过使用带有回调的字符串替换来完成的，这是一种解析字符串的强大方法。回调函数被传递一组与正则表达式匹配相对应的参数——一个用于整体匹配，一个用于每个反向引用。在回调中，我们将这些值解析为整数，并保存到一个数组中。对于月份值，我们还必须将该值减 1，因为 JavaScript 月份数字的范围是从`0`到`11`，其中我们的输入是`"01"`到`"12"`。

接下来，我们解析时区指示符，它可能是 UTC 日期戳的`"Z"`，也可能是像`"+10:00"`或`"-0600"`这样的偏移量。偏移量被转换为以秒为单位的整数，然后再次转换为正或负毫秒，这取决于偏移量的走向。

最后，我们将组成整数传递给`Date.UTC()`，然后将时区偏移量添加到返回值中。`UTC()`方法假设其输入组件已经是 UTC 格式，所以我们必须添加时区偏移量来补偿该值。`apply()`用于调用`UTC()`方法，因为它允许将`components`数组作为单个参数传递。

## 结论

`timestamp()`和 [`datestamp()`](https://www.sitepoint.com/creating-an-iso-datestamp/ "Creating an ISO datestamp") 函数都是 JavaScripter 工具箱的宝贵补充，提供了创建和解析无处不在的 ISO 格式的能力。这些功能被证明是有用的，尤其是在那些我们宁愿忘记的陈旧和古怪的浏览器中！

## 分享这篇文章