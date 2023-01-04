# 使用 jQuery 为文本元素创建摘录

> 原文：<https://www.sitepoint.com/jquery-create-excerpts-text-elements/>

这就是如何使用 jQuery 来限制 textarea 中的字符。这是一个为任何页面元素设置最大字符长度的函数。例如，你可以用它来为你博客上的文章创建摘录。查看更多 [jQuery。每个例子]( http://www.jquery4u.com/jquery-functions/jquery-each-examples/)。

[演示](http://www.jquery4u.com/function-demos/extend/)


```
(function($) { 
	// jQuery function to set a maximum length or characters for a page element it can handle mutiple elements
        $.fn.createExcerpts = function(elems,length,more_txt) {
		$.each($(elems), function() { 
			var item_html = $(this).html(); //
			item_html = item_html.replace(/< /?[^>]+>/gi, ''); //replace html tags
			item_html = jQuery.trim(item_html);  //trim whitespace
			$(this).html(item_html.substring(0,length)+more_txt);  //update the html on page
		});
		return this; //allow jQuery chaining 
	}
})(jQuery);
```

这是你如何使用它:

```
//example call
$().createExcerpts('.blogpost',280,'...');
```

## 分享这篇文章