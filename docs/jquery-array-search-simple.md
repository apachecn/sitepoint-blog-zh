# jQuery 数组搜索简单示例

> 原文：<https://www.sitepoint.com/jquery-array-search-simple/>

如何使用 jQuery 的$的快速示例。map()函数从 JS 对象的数组中获取一个值。**在示例**中，我有一个包含日期和价格的 JS 对象数组，我们希望提取特定日期的价格。

```
var dayArr = Array( { "day" : "day01", "price" : "$210" }, { "day" : "day02", "price" : "$220" }, { "day" : "day03", "price" : "$230" } );

var findDay = 'day02'; //find price for day 1

var price = $.map(dayArr, function(value, key) {
     if (value.day == findDay)
     {
        return value.price;
     }
});
console.log(price);
//output: $220
```

## 分享这篇文章