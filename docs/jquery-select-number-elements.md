# jquery 选择前 x 个元素

> 原文：<https://www.sitepoint.com/jquery-select-number-elements/>

选择前 x 个元素的快速 jQuery 代码段。您也可以使用 jQuery。slice()函数，它可以将元素组分割成一个范围。get()；

```
//get first 20 anchor tags
$("a").slice(0,20)
```

[jQuery 切片函数示例演示](http://www.jquery4u.com/function-demos/slice/)

也可以使用 lt 伪选择器:它匹配第 n 个元素之前的元素(第 n 个元素除外)。编号从 0 开始。

 `$("a:lt(n)")

//而不是这样:

for(I = 0；我` 

## `分享这篇文章`