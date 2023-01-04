# 使用 jQuery。使用 Live 或 Bind 切换()

> 原文：<https://www.sitepoint.com/jquery-toggle-live-bind/>

简而言之，你可以一起使用 Live 和 Toggle，但是对于编码最佳实践，你不应该这样做。都是 jQuery。Live()和 jQuery。Toggle()自动为元素创建自己的绑定事件。当它们一起使用时，这将导致问题，因为您需要单击按钮两次才能使切换工作。

解决这一问题的一种方法是使用带有内部切换事件的实时(“click”)事件。触发(' click ')命令结束函数调用如下:

```
// Add sort functions on dynamic elements inserted into DOM
$('.sort').live('click',JQUERY4U.sortClickListener);

[code lang="js"]
//function inside JQUERY4U namespace
sortClickListener: function(){

	// Find second class name
	var button = $(this).attr('class').split(' ');
	// Sort table
	$(this).toggle(function() {
		$('.item').tsort('.'+button[1],{order:'desc'});
	}, function() {
		$('.item').tsort('.'+button[1]);
	}).trigger('click'); /*force the button to work with 1 click*/
},
```

另一种方法是使用。数据方法，但这将是矫枉过正和冗长的方式来解决这个难题。

```
$(".reply").live('click', function () {
    var toggled = $(this).data('toggled');
    $(this).data('toggled', !toggled);
    if (!toggled) {
        x1();
    }
    else {
        x2();
    }
});
```

## 分享这篇文章