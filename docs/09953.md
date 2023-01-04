# jQuery 获取相对鼠标位置

> 原文：<https://www.sitepoint.com/jquery-relative-mouse-position/>

获取鼠标指针相对位置的 jQuery 代码片段。该函数将元素 id 作为参数，并将鼠标指针的当前 x 和 y 坐标作为参数。然后，它返回鼠标光标当前位置和指定元素之间的相对距离。

```
function rPosition(elementID, mouseX, mouseY) {
  var offset = $('#'+elementID).offset();
  var x = mouseX - offset.left;
  var y = mouseY - offset.top;
  return {'x': x, 'y': y};
}
```

## 用法示例

```
jQuery(document).ready(function($) {

	//get the current x and y of the mouse pointer
	var X = $('body').offset().left;
	var Y = $('body').offset().top;
	mouseX = ev.pageX - X;
	mouseY = ev.pageY - Y;

	//get the relative position to the #eid element on the page
	alert(rPosition('eid',x,y));

});
```

## 分享这篇文章