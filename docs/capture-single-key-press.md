# jQuery 捕获单次按键事件(键盘快捷键)

> 原文：<https://www.sitepoint.com/capture-single-key-press/>

有时候，为了提高我们网站的性能和可用性，我们可能想添加一些键盘快捷键，用来执行常见的网站任务比子弹还快！在这篇文章中，我将用简单易懂的 n00b 语言解释如何使用 jQuery 创建键盘事件。

*   [**找到按下**键的键码](http://www.jquery4u.com/events/find-keycode-keyboard-key-press/)

这是你如何做它。

```
// this is a generic setup to capture keyup events in jquery will console log for firebug

if(typeof console == 'undefined'){
	console = {};
	console.log = function(arg){return false;};
}

$(document).keyup(function(e){
	//find out which key was pressed
	switch(e.keyCode){
		case 65	:	console.log('a');	break;	// a
		case 66	:	console.log('b');	break;	// b
		case 67	:	console.log('c');	break;	// c
		case 68	:	console.log('d');	break;	// d
		case 69	:	console.log('e');	break;	// e
		case 70	:	console.log('f');	break;	// f
		case 71	:	console.log('g');	break;	// g
		case 72	:	console.log('h');	break;	// h
		case 73	:	console.log('i');	break;	// i
		case 74	:	console.log('j');	break;	// j
		case 75	:	console.log('k');	break;	// k
		case 76	:	console.log('l');	break;	// l
		case 77	:	console.log('m');	break;	// m
		case 78	:	console.log('n');	break;	// n
		case 79	:	console.log('o');	break;	// o
		case 80	:	console.log('p');	break;	// p
		case 81	:	console.log('q');	break;	// q
		case 82	:	console.log('r'); 	break;	// r
		case 83	:	console.log('s');	break;	// s
		case 84	:	console.log('t');	break;	// t
		case 85	:	console.log('u');	break;	// u
		case 86	:	console.log('v');	break;	// v
		case 87	:	console.log('w');	break;	// w
		case 88	:	console.log('x');	break;	// x
		case 89	:	console.log('y');	break;	// y
		case 90	:	console.log('z');	break;	// z
	}
});
```

## 分享这篇文章