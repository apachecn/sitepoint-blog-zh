# jQuery 自动滚动到页面顶部

> 原文：<https://www.sitepoint.com/jquery-auto-scroll-top-page/>

您可以使用 jQuery 自动缓慢滚动到网页顶部，只需使用下面的一段简单代码。为什么？

1.  如果你的页面很长，可以节省用户滚动到顶部的时间
2.  看起来很酷。

[见现场演示](http://blogoola.com/#bottombar2)

## jQuery 滚动到顶部

```
jQuery(document).ready(function($) {
	$('a[href=#topbar]').click(function(){
		$('html, body').animate({scrollTop:0}, 'slow');
		return false;
	});
});
```

## 分享这篇文章