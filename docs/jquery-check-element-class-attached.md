# jQuery 检查元素是否附加了类

> 原文：<https://www.sitepoint.com/jquery-check-element-class-attached/>

jQuery 代码片段，用于检查网页元素是否附加了特定的 css 类。可用于检查该元素是否需要附加格式，或者您是否希望保护某些页面元素。为此，我们可以使用 jQuery hasClass()函数或 jQuery is()函数。

```
$("div").click(function(){
	if ( $(this).hasClass("protected") ) {
		//do something it does have the protected class!
		alert("i have the protected class");
	}
});
```

或

```
$("div").click(function(){
   if ( $('#myDiv').is('.pretty.awesome') ) {
	   //do something it does have the protected class!
	   alert("i have the pretty and awesome classes");
   }
});
```

**注意:**这个方法也允许你测试其他东西。检查一个元素是否被隐藏。

```
if ( $('#myDiv').is(':hidden') ) {
   //do something I am hidden!
   alert("i am hidden!");
}
```

## 分享这篇文章