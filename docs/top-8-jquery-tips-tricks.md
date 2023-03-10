# 8 大 jQuery 技巧和窍门

> 原文：<https://www.sitepoint.com/top-8-jquery-tips-tricks/>

这里有一些很酷的 jQuery 技巧和窍门。有很多事情可以用这个聪明的 javascript 库来完成，比如调整字体大小，禁止右键点击等等。你也可以对 jQuery 进行定制编码，编写令人惊叹的脚本，就像我列出的这个列表一样。

## 1.字体大小调整

这将允许用户增加或减少网页的字体大小。您必须指定希望字体可调的 HTML 元素的 ID 或类别。

***代码:***

```
$(document).ready(function(){
	//ID, class and tag element that font size is adjustable in this array
	//Put in html or body if you want the font of the entire page adjustable
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

<a class="increaseFont">+</a> |
<a class="decreaseFont">-</a> |
<a class="resetFont">=</a*gt;
<span>Font size can be changed in this section</span>
<div class="section1">This won't be affected</div>
<div class="section2">This one is adjustable too!</div>
```

## 2.返回顶部按钮或链接

通过在链接或按钮上使用 jQuery 滚动效果，这是一种非常好的返回页面顶部的方式。

***代码:***

```
$('#top').click(function() {
	$(document).scrollTo(0,500);
}

<script type="text/javascript" src="js/jquery.scrollTo-min.js"></script>

<a id="top" style="cursor:hand;cursor:pointer">
Back to top
```

## 3.检测右键单击

考虑在网站上进行右键点击是很重要的。因为有时我们可能想禁用网站的右键功能。所以下面是我们如何使用 jQuery 来检测鼠标的右键单击。

***代码:***

```
$(document).bind("contextmenu",function(e){
	//you can enter your code here, e.g a menu list

	//cancel the default context menu
	return false;
});
```

## 4.在新窗口中打开

正如你可能知道的，html 中' a '标签的目标属性没有通过 W3C 验证，所以你会在这里得到一些验证错误。这个 jQuery 代码要做的是用能够通过 W3C 验证的东西替换目标属性。所以这里是 REL 和一些 jQuery 代码。

***代码:***

```
$('a[rel=external]').attr('target','_blank');
Queness in new window
```

## 5.切换到不同的 CSS 样式

如果你想让你的网站有多个样式表，这一个就是为你准备的。

***代码:***

```
$("a.cssSwitcher").click(function() {
	//swicth the LINK REL attribute with the value in A REL attribute
	$('link[rel=stylesheet]').attr('href' , $(this).attr('rel'));
});
<link rel="stylesheet" href="default.css" type="text/css"/>

<a href="#" class="cssSwitcher" rel="default.css">Default Theme</a>
<a href="#" class="cssSwitcher" rel="red.css">Red Theme</a>
<a href="#" class="cssSwitcher" rel="blue.css">Blue Theme</a>
```

## 6.获取鼠标指针的 X 轴和 Y 轴

这段代码将获得你的鼠标指针的坐标。

***代码:***

```
$().mousemove(function(e){
    //display the x and y axis values inside the P element
    $('p').html("X Axis : " + e.pageX + " | Y Axis " + e.pageY);
});
[/javascript]
7. MAKE WHOLE LI CLICKABLE
A very useful trick when you’re using UL list to make a navigational menu. When you click on the LI area (outside of the link) it will amazingly search for the url in the anchor tag and then execute it.

THE CODE:

[code language="javascript"]
$("ul li").click(function(){
  //get the url from href attribute and launch the url
  window.location=$(this).find("a").attr("href"); return false;
});
<ul>
    <li><a href="home">home</a></li>
    <li><a href="home">about</a></li>
    <li><a href="home">contac<></li>
</ul>
[/code]

        8. COLUMNS OF EQUAL HEIGHT
This is quite useful especially when you want the columns have the same height.

THE CODE:

[code language="javascript"]
$(document).ready(function() {
    setHeight('.col');
});

//global variable, this will store the highest height value
var maxHeight = 0;

function setHeight(col) {
    //Get all the element with class = col
    col = $(col);

    //Loop all the col
    col.each(function() {

        //Store the highest value
        if($(this).height() > maxHeight) {
            maxHeight = $(this).height();;
        }
    });

    //Set the height
    col.height(maxHeight);
}
<div class="col" style="border:1px solid">Column One<br />
With Two Line<br />
And the height is different<br /><br />
</div>
<div class="col" style="border:1px solid">Column Two<br /><br /></div>
```

## 分享这篇文章