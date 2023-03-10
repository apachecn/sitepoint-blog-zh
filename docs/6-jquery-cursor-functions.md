# 6 个 jQuery 游标函数

> 原文：<https://www.sitepoint.com/6-jquery-cursor-functions/>

jQuery 代码片段用鼠标光标创造奇迹！它们可用于设置和获取文本光标在输入和文本区域字段和选择范围内的位置。尽情享受吧！

```
//Example jQuery get cursor position function call
$("input[name='username']").getCursorPosition();
```

```
jQuery.fn.getCursorPosition = function(){
	if(this.lengh == 0) return -1;
	return $(this).getSelectionStart();
}
```

```
//Example jQuery set cursor position function call
$("input[name='username']").setCursorPosition(5);
```

```
jQuery.fn.setCursorPosition = function(position){
	if(this.lengh == 0) return this;
	return $(this).setSelection(position, position);
}
```

```
//Example jQuery get text selection function call
$("input[name='username']").getSelection();
```

```
jQuery.fn.getSelection = function(){
	if(this.lengh == 0) return -1;
	var s = $(this).getSelectionStart();
	var e = $(this).getSelectionEnd();
	return this[0].value.substring(s,e);
}
```

```
//Example jQuery get text selection start function call
$("input[name='username']").getSelectionStart();
```

```
jQuery.fn.getSelectionStart = function(){
	if(this.lengh == 0) return -1;
	input = this[0];

	var pos = input.value.length;

	if (input.createTextRange) {
		var r = document.selection.createRange().duplicate();
		r.moveEnd('character', input.value.length);
		if (r.text == '') 
		pos = input.value.length;
		pos = input.value.lastIndexOf(r.text);
	} else if(typeof(input.selectionStart)!="undefined")
	pos = input.selectionStart;

	return pos;
}
```

```
//Example jQuery get text selection end function call
$("input[name='username']").getSelectionEnd();
```

```
jQuery.fn.getSelectionEnd = function(){
	if(this.lengh == 0) return -1;
	input = this[0];

	var pos = input.value.length;

	if (input.createTextRange) {
		var r = document.selection.createRange().duplicate();
		r.moveStart('character', -input.value.length);
		if (r.text == '') 
		pos = input.value.length;
		pos = input.value.lastIndexOf(r.text);
	} else if(typeof(input.selectionEnd)!="undefined")
	pos = input.selectionEnd;

	return pos;
}
```

```
//Example jQuery set text selection function call
$("input[name='username']").setSelection(4, 20);
```

```
jQuery.fn.setSelection = function(selectionStart, selectionEnd) {
	if(this.lengh == 0) return this;
	input = this[0];

	if (input.createTextRange) {
		var range = input.createTextRange();
		range.collapse(true);
		range.moveEnd('character', selectionEnd);
		range.moveStart('character', selectionStart);
		range.select();
	} else if (input.setSelectionRange) {
		input.focus();
		input.setSelectionRange(selectionStart, selectionEnd);
	}

	return this;
}
```

## 分享这篇文章