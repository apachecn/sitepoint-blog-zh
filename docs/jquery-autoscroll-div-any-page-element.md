# jQuery 自动滚动到 Div(特定页面元素)

> 原文：<https://www.sitepoint.com/jquery-autoscroll-div-any-page-element/>

自动滚动到 div 或任何具有 id 的页面元素的 jQuery 代码片段。只需将 jQuery 选择器“mydiv”更改为您希望的任何元素 id。

```
function scroll_to(div){
	$('html, body').animate({
		scrollTop: $("mydiv").offset().top
	},1000);
}
```

## 分享这篇文章