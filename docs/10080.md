# jQuery 后退按钮(转到上一页)

> 原文：<https://www.sitepoint.com/jquery-button-go-previous-page/>

jQuery/JavaScript 代码片段模拟基于用户最后一个网页的后退按钮。

```
$(document).ready(function(){
	$('a.back').click(function(){
		parent.history.back();
		return false;
	});
});
```

## 分享这篇文章