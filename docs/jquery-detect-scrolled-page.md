# jQuery 检测到页面上滚动了%

> 原文：<https://www.sitepoint.com/jquery-detect-scrolled-page/>

当用户向下滚动到网页的某个百分比时，创建事件动作的 jQuery 代码片段。我们已经测试过，当捕捉鼠标滚动时，最好使用 55%到 100%之间的值。

```
$(document).ready(function(){

//Example - show a div after 75% of the web page is scrolled.
var webpage = $("body");
var webpage_height = webpage.height();
//alert(webpage_height);
var trigger_height = webpage_height * 0.75;

if ($(window).scrollTop() > (webpage_height-trigger_height)) {
	//alert($(window).scrollTop()+" > "+(webpage_height-trigger_height));
	$("#divtoshow").show();
}

});
```

## 分享这篇文章