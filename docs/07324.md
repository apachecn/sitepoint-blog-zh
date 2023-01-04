# jQuery。每个()跳到下一个

> 原文：<https://www.sitepoint.com/jquery-each-skip/>

解决方法 jQuery 代码片段跳过循环中的下一次迭代。只是记录这个代码片段，没有演示抱歉。

```
//output keys=values. eg [1]=[2], [3]=[4] etc, skips undefined keys
var i = 0;
$.each(array, function(key, val)
{
	if (webTrendsParams[i]) {
		console.log(array[i] + ' = ' + array[i+1]);
	}
	i = i + 2;
});
```

## 分享这篇文章