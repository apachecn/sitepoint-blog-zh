# jQuery 设置/获取浏览器会话 Cookies

> 原文：<https://www.sitepoint.com/jquery-setget-browser-session-cookies/>

为用户会话设置/获取浏览器 cookies 的 jQuery 代码片段。这可以用来存储用户点击时的视图状态。下面的示例显示了保存 cookie 以存储元素的可见性。

```
//event to hide element
...
$("#element").hide();
$.cookie('cookie_name', 'not_in_view');

//event to show element
...
$("#element").show();
$.cookie('cookie_name', 'in_view');

//cookie
var cookie_name = $.cookie('cookie_name');

//initialise
if (cookie_name == 'in_view') {
	$("#element").hide();
};
```

## 分享这篇文章