# jQuery 字符串模板格式函数

> 原文：<https://www.sitepoint.com/jquery-string-template-format-function/>

非常有用的 jQuery 函数，我称之为“formatVarString”。Its 将一个字符串作为第一个参数，后面有 n 个参数，用于执行变量替换(使用括号将变量作为字符串的一部分返回)。

您可以简单地使用{1}、{2}、{3}等来引用字符串中的变量。

## 使用

```
formatVarString('we love {1}.', 'jQuery4u');
//output: "we love jQuery4u."

formatVarString('{1} is a {2} aimed to help you learn {3}.', 'jQuery4u', 'blog', 'jQuery');
//output: "jQuery4u is a blog aimed to help you learn jQuery."
```

## jQuery 格式函数

```
var JQUERY4U = {};
JQUERY4U.UTIL = {
formatVarString: function()
	{
		var args = [].slice.call(arguments);
		if(this.toString() != '[object Object]')
		{
			args.unshift(this.toString());
		}

		var pattern = new RegExp('{([1-' + args.length + '])}','g');
		return String(args[0]).replace(pattern, function(match, index) { return args[index]; });
	}
}
JQUERY4U.UTIL.formatVarString('{1} is a {2} aimed to help you learn {3}.', 'jQuery4u', 'blog', 'jQuery');
//output: "jQuery4u is a blog aimed to help you learn jQuery."
```

## 分享这篇文章