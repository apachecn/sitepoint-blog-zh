# jQuery 从 ALT 标签设置图像标题

> 原文：<https://www.sitepoint.com/jquery-set-image-title-alt-tag/>

jQuery 代码片段获取每个图像的 alt 标记，并设置 title 标记，使其与 alt 标记相同。可能有助于改善网页的 SEO，并增加对 Firefox 等使用图像标题标签的浏览器的支持。

```
jQuery(document).ready(function($) {
	$("img:not([title])").each(function() {
			if($(this).attr("alt") != '') $(this).parent().attr("title", $(this).attr("alt"))
			else $(this).parent().attr("title", $(this).attr("src"));
	});
});
```

## 分享这篇文章