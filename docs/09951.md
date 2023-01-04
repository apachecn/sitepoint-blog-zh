# jQuery 在弹出窗口中干净地打开链接

> 原文：<https://www.sitepoint.com/jquery-cleanly-open-links-popup-windows/>

简单的 jQuery 代码片段将一个事件添加到**类的所有链接。popup 在弹出窗口**中打开，而不是当前页面或新标签页/窗口。请随意指定高度和宽度。

```
jQuery(document).ready(function($) {
	jQuery('a.popup').live('click', function(){
		newwindow=window.open($(this).attr('href'),'','height=200,width=150');
		if (window.focus) {newwindow.focus()}
		return false;
	});
});
```

## 分享这篇文章