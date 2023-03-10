# 科技宝藏#1:感觉空虚

> 原文：<https://www.sitepoint.com/techy-treasures-1/>

Techy Treasures 是一个新的常规功能，我们将为 web 开发人员发布提示、技巧和代码片段。这些不是天马行空的幻想，它们是可靠的、经过验证的想法，你可以放心地在野外使用。

首先，一个检查变量是否为空的简洁的小函数，灵感来自于 PHP 的同名函数:

```
function empty(data)
{
	if(typeof data == 'undefined' || data === null) { return true; }
	else if(typeof data == 'string' && (data === '0' || data.replace(/^s+|s+$/g, '') === '')) { return true; }
	else if(typeof data == 'number' && data === 0) { return true; }
	else if(typeof data == 'boolean' && data === false) { return true; }
	else if(typeof data == 'object')
	{
		if(data instanceof Array && data.length == 0) { return true; }
		else
		{
			var n = 0;
			for(var i in data)
			{
				if(!data.hasOwnProperty(i)) { continue; }
				n++;
			}
			if(n == 0) { return true; }
		}
	}
	return false;
}
```

因此，如果一个变量满足以下条件，它就被认为是空的:

*   不明确的
*   `null`
*   一个字符串，它的值是`"0"`，或者一个空字符串，或者只有空格
*   一个数字，其值为`0`
*   一个布尔值，它的值是`false`
*   一个数组，它没有值
*   一个对象，它没有可枚举的属性

它适用于任何类型的变量，例如，用作条件:

```
if(!empty(data)) 
{
	//data is not empty
}
```

您甚至可以传递另一个进程的返回值，谢谢(一如既往！)到 JavaScript 将几乎任何东西作为参数处理的能力:

```
if(!empty(function()
{
	//do some process and return a value
}))
{
	//return value was non-empty
}
```

我发现它对验证函数参数特别有用，例如一个简单的快捷函数，用于获取元素引用:

```
function get(id)
{
	return document.getElementById(id);
}
```

但是如果`id`参数为空，或者`null`，或者根本不存在呢？我们可以用一条语句检查所有这些可能性，然后相应地处理这种情况:

```
function get(id)
{
	if(empty(id)) { return null; }
	return document.getElementById(id);
}
```

这就是你要做的——一个整洁、简单、优雅的验证任何类型变量的方法。

下一个科技宝藏再见！

## 分享这篇文章