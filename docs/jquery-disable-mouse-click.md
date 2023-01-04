# jQuery 禁用鼠标右键

> 原文：<https://www.sitepoint.com/jquery-disable-mouse-click/>

jQuery 代码片段禁用鼠标右键，常用于希望增加一些低级别安全性的网站。jQuery 可以通过绑定到 contextmenu 事件来做到这一点。我建议只有在必要时才使用这个函数，因为这通常是不好的做法。

```
$(function() {
	$(this).bind("contextmenu", function(e) {
		e.preventDefault();
	});
});
```

## 分享这篇文章