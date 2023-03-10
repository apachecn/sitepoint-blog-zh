# jQuery Switch 语句

> 原文：<https://www.sitepoint.com/jquery-switch-statement/>

如何使用 JavaScript 声明 switch 语句？很容易知道，当根据变量的值执行不同的代码时，这会节省你很多时间。

```
var jsLang = 'jquery';
switch (jsLang) { 
	case 'jquery': 
		alert('jQuery Wins!');
		break;
	case 'prototype': 
		alert('prototype Wins!');
		break;
	case 'mootools': 
		alert('mootools Wins!');
		break;		
	case 'dojo': 
		alert('dojo Wins!');
		break;
	default:
		alert('Nobody Wins!');
}
//outputs "jQuery Wins!"
```

您还可以通过省略分隔符来匹配多个事例，如下所示:

```
var jsLang = 'prototype';
switch (jsLang) { 
	case 'jquery': 
		alert('jQuery sucks!');
		break;
	case 'prototype': 
		alert('prototype sucks!');
	case 'mootools': 
		alert('mootools sucks!');	
	case 'dojo': 
		alert('dojo sucks!');
		break;
	default:
		alert('Nobody sucks!');
}
//outputs "prototype sucks! mootools sucks! dojo sucks!"
```

## 分享这篇文章