# jQuery 动态改变 CSS 文件

> 原文：<https://www.sitepoint.com/jquery-change-css-file-2/>

如何使用老式的 jQuery 动态地改变一个 css 文件！(有点像 jQuery 样式表切换器！)

## jQuery

```
$(document).ready(function() {
	$("#nav li a").click(function() {
		$("link").attr("href",$(this).attr('rel'));
		return false;
	});
});
```

或者(动态交换 css 文件的更复杂版本)

```
$(document).ready(function() {
	if($.cookie("css")) {
		$("link").attr("href",$.cookie("css"));
	}
	$("#nav li a").click(function() {
		$("link").attr("href",$(this).attr('rel'));
		$.cookie("css",$(this).attr('rel'), {expires: 365, path: '/'});
		return false;
	});
});
```

或者(在使用 html 代码中的链接的相对属性交换 css 之后，减少当页面用新的 css 样式刷新时发生的闪烁的版本)。

```
if($.cookie("css")) {
	$("link").attr("href",$.cookie("css"));
}
$(document).ready(function() {
	$("#nav li a").click(function() {
		$("link").attr("href",$(this).attr('rel'));
		$.cookie("css",$(this).attr('rel'), {expires: 365, path: '/'});
		return false;
	});
});
```

## HTML

html 很简单，css 和 jQuery 文件像平常一样加载在 html 的头中。

```
<link rel="stylesheet" type="text/css" href="style1.css" />
<script type="text/javascript" language="javascript" src="jquery.js"></script>
<script type="text/javascript" language="javascript" src="jquery.cookie.js"></script>
<script>... your jQuery goes here...</script>
```

测试一下！这里有几个链接可以让你自己尝试一下。

```
<ul id="nav">
	<li><a href="#" rel="/path/to/style1.css">Default CSS</a></li>
	<li><a href="#" rel="/path/to/style2.css">Larger Text</a></li>
	<li><a href="#" rel="/path/to/style3.css">Something Different</a></li>
</ul>
```

[来源](http://www.cssnewbie.com/simple-jquery-stylesheet-switcher/)
[演示](http://www.cssnewbie.com/example/stylesheet-switcher/)

## 分享这篇文章