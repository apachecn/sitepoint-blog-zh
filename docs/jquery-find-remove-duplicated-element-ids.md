# jQuery 查找并删除重复的元素 id

> 原文：<https://www.sitepoint.com/jquery-find-remove-duplicated-element-ids/>

这个例子可以用来为你的博客修改一篇特色文章，这样就不会有一篇以上的文章出现在主页上。

```
// featured article fix
var myFeatured = $('div.featured').attr('id');
var theHidden;
$('div.article').each(function(){
	theHidden = $(this).attr('id');
	if(theHidden == myFeatured){ 
		$('div#'+theHidden).hide();	
	}
});
```

## 分享这篇文章