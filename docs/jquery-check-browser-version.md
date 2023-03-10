# jQuery 检查浏览器版本

> 原文：<https://www.sitepoint.com/jquery-check-browser-version/>

使用 jQuery 检查您的浏览器版本，只需几行代码，您就可以针对不同的浏览器优化代码，例如 Firefox、IE、Safari、Chrome 等等。jQuery 函数**。浏览器()**和 **jQuery。Browser.Version()** 为我们做所有辛苦的工作！

这是你怎么做的。

**//示例:输出当前浏览器的所有版本信息**

```
jQuery.each(jQuery.browser, function(i, val) {
  $(" `" + i + " : " + val + "")
			.appendTo( document.body );
});`
```

**示例输出:**

*   Internet Explorer: 6.0、7.0、8.0
*   Mozilla/Firefox/Flock/Camino:1 . 7 . 12，1.8.1.3，1.9
*   歌剧:10.06，11.01
*   Safari/Webkit: 312.8，418.9

**//示例:仅适用于 firefox 3 浏览器的提醒“为 firefox 3 做些事情”。**

```
var ua = $.browser;
if ( ua.mozilla && ua.version.slice(0,3) == "1.9" ) {
	alert( "Do stuff for firefox 3" );
}
```

**//示例:为特定浏览器设置 CSS 属性。**

```
if ( $.browser.msie ) {
	$("#div ul li").css( "display","inline" );
} else {
	$("#div ul li").css( "display","inline-table" );
}
```

## 分享这篇文章