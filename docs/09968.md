# jQuery 动态调整文本大小

> 原文：<https://www.sitepoint.com/jquery-resizing-text-dynamically/>

jQuery 和 html 代码片段，用于在用户单击增加文本大小或减小文本大小按钮时动态调整网页上的文本大小。

## jQuery 代码

```
$(document).ready(function(){
	var section = new Array('span','.section2'); 
	section = section.join(',');

	// Reset Font Size
	var originalFontSize = $(section).css('font-size');
	$(".resetFont").click(function(){
		$(section).css('font-size', originalFontSize); 
	});

	// Increase Font Size
	$(".increaseFont").click(function(){
		var currentFontSize = $(section).css('font-size');
		var currentFontSizeNum = parseFloat(currentFontSize, 10);
		var newFontSize = currentFontSizeNum*1.2;
		$(section).css('font-size', newFontSize);
		return false;
	});

	// Decrease Font Size
	$(".decreaseFont").click(function(){
		var currentFontSize = $(section).css('font-size');
		var currentFontSizeNum = parseFloat(currentFontSize, 10);
		var newFontSize = currentFontSizeNum*0.8;
		$(section).css('font-size', newFontSize);
		return false;
	});
});
```

## HTML 代码

```
 | 
| 

Font size can be changed in this section
This won't be affected
This one is adjustable too!
```

[观看现场演示](http://wordpresserrorhelp.com)

## 分享这篇文章