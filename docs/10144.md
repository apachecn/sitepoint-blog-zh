# jQuery 加载新窗口

> 原文：<https://www.sitepoint.com/jquery-load-window/>

用于在新窗口中加载/打开链接的 jQuery 代码片段。这段代码向被赋予“new-window”类的锚标记添加一个事件，并强制它们在一个新窗口中打开。

```
$(function(){
 $('a.new-window').click(function(){
 window.open(this.href);
 return false;
 });
});
```

## 高级 jQuery 加载新窗口示例

这段代码获取容器 div 的 id，然后获取隐藏的 url div 元素，然后在新窗口中打开它。

```
function openblog(blog_id) {
	//alert(blog_id);
	$('#blog-wrap-'+blog_id).hide();
	var blogurl = $('#'+blog_id+'-url').text();
	var location = "http://domainname/index.php?blogurl="+blogurl;
	window.open(location);
}
```

## HTML 代码

```
 http://blogoola.com 
```

## 分享这篇文章