# jQuery 获取当前页面 URL

> 原文：<https://www.sitepoint.com/jquery-current-page-url/>

jQuery 代码片段获取当前网页的完整 url，并将其存储在一个变量中，供其他脚本使用。这是您在地址栏中看到的 url。

```
$(document).ready(function() {
	//jquery
    $(location).attr('href');

	//pure javascript
	var pathname = window.location.pathname;

	// to show it in an alert window
    alert(window.location);
});
```

该功能也有助于确定绝对路径。

```
function getAbsolutePath() {
    var loc = window.location;
    var pathName = loc.pathname.substring(0, loc.pathname.lastIndexOf('/') + 1);
    return loc.href.substring(0, loc.href.length - ((loc.pathname + loc.search + loc.hash).length - pathName.length));
}
```

## 分享这篇文章