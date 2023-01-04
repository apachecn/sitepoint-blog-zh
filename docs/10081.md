# jQuery 更改超链接属性值

> 原文：<https://www.sitepoint.com/jquery-change-hyperlink-attribute-values/>

有时，更改网页上的超链接会很耗时。不要害怕！jQuery 来救援了！下面是一些 jQuery 代码，用于更改网页上超链接的属性值。

链接属性:“名称”、“id”、“类”、“目标”、“href”。

```
$(document).ready(function(){
	$(function(){
		$("a[href^='http']").attr('target','_blank');
		$("a[href^='.pdf']").attr('target','_blank');
		$("a[href^='http']").attr('class','linkclass');
		$("a[href^='http']").attr('id','linkid');
		$("a[href^='http']").attr('name','linkname');
		$("a[href^='http://jquery4u.com']").attr('target','_blank');
	});
});
```

## 分享这篇文章