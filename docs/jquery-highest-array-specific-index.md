# jquery 获取数组中特定索引的最大值

> 原文：<https://www.sitepoint.com/jquery-highest-array-specific-index/>

简单的 jQuery 代码片段让**获得数组中特定索引**值的最大值。

**数据集。**

```
var data = Array();
data[0] = {"apples":1, "pears":2, "oranges":3};
data[1] = {"apples":3, "pears":3, "oranges":5};
data[2] = {"apples":4, "pears":1, "oranges":6};
```

**功能。**

 `//获取数组中特定索引的最大值
//用法:getHighestVal(data，index)
//data = array
//index =要分析的数组的索引
函数 getHighestVal(data，index)
{
$。each(data，function (i，v)
{
this val = v[index]；max = (max < thisVal)？this val:max；});返回 max} var 最高= getHighestVal(数据，'苹果')；console.log(最高)；[/js] **例子:
所以在这个例子中，我们想得到“苹果”的最高值。
![highest value in array for specific index](img/ff68ed1591dffb829a5d042f351875c0.png "highest value in array for specific index")**` 

## `分享这篇文章`