# jQuery 禁用 Onclick 事件

> 原文：<https://www.sitepoint.com/jquery-disable-onclick-event/>

简单的 jQuery 代码片段，通过禁用与按钮关联的 on click 事件来停止点击时的输入清除。这只是下降和肮脏的代码抱歉，没有适当的例子。

```
$('.del').click(function(){
   onclick = $(this).attr('onclick');
   $(this).attr('onclick','');
   showConfirm(onclick);
        return false;
});

$('input').click(function(){
	console.log("clicked");
	onclick = $(this).attr('onclick');
	$(this).attr('onclick','');
	showConfirm(onclick);
	return false;
});

$('input').each(function () {
this.onclick = undefined;
});
$('.foo').removeAttr('onclick').click(function(){
// Do something
});
var original_onclick = $('.del').attr('onclick');
$('.del').removeAttr('onclick').click(function(){
showConfirm(original_onclick);
});
```

## 分享这篇文章