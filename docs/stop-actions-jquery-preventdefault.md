# 用 jQuery 停止操作。预防默认()

> 原文：<https://www.sitepoint.com/stop-actions-jquery-preventdefault/>

因为动作没有被遵循，我们可能需要知道用户何时提示动作，并且可以使用`event.isDefaultPrevented()`来确定该方法是否已经被由该事件触发的事件处理程序调用。这可以用来触发函数调用。对于 jQuery 中带有 XHtmlRequest 的动态 URL 也很有用，可以用一个模块获取页面，例如 href="index.php？page =联系人而不是 contact.php。你可能也想看看国防部重写选项，也可以提供这种功能和搜索引擎优化的好处。 [jQuery 停止事件函数](http://www.jquery4u.com/jquery-functions/stop-jquery-event-functions/)。如果您正在使用$(document)，请小心。bind("keydown keypress "，function(event)then e . prevent default()不起作用..event.preventDefault()。有时候你有一个需要成为超链接的超链接，但是你不希望它处理和打开链接，而只是调用一个 javascript 函数。幸运的是，jQuery 中有一个函数可以停止超链接操作。

[jQuery Preventdefault 函数演示](http://www.jquery4u.com/function-demos/preventdefault/)

## 示例 1–阻止并捕捉超链接点击

## 示例 2–阻止表单提交按钮

```
$('#myform').submit(function(event) {
	event.preventDefault();
	var self = this;
	window.setTimeout(function() {
		self.submit();
	}, 2000);
});
```

## 示例 3–将超链接目标延迟到动画效果之后

```
$("#ELEMENT_WHICH_AFFECT_THE_SLIDEUP")
.click(function(event){
	event.preventDefault(); // prevents link to be redirected
	var time = 1000; // time for slideup effect
	var url = $(this).attr("href"); // pick url for redirecting via javascript

	$("#ELEMENT_TO_SLIDE_UP").slideUp(time); // effect
	window.setTimeout(function(){document.location.href=url;}, time); // timeout and waiting until effect is complete
	return -1;
});
```

## 示例 4–禁用按键滚动按钮

```
$(document).keydown(function(event){
	// Move Down
	if(event.keyCode == '40'){
		event.preventDefault();
		var posY = $('#'+selectedTxtID).css('top');
		posY = parseFloat(posY);
		var newPosY = posY + 1;
		$('#'+selectedTxtID).css('top', newPosY+'px');
	}
})
```

这个函数不仅可以用在超链接上，也可以用在任何你想阻止的默认动作上。

## 分享这篇文章