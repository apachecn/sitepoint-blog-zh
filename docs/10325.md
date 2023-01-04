# 15 个精选的 jQuery 代码片段

> 原文：<https://www.sitepoint.com/15-selected-jquery-snippets/>

用这些很酷的 jQuery 片段改进您的 JavaScript 项目。jQuery 1 . 4 . 2 版支持其中的一些。这些都是非常有用的功能或方法，可以帮助你更好更快地工作。

## 1.正确的 ToggleClass 用法

```
//Toggle class allows you to add or remove a class
	 //from an element depending on the presence of that
	 //class. Where some developers would use:

	 a.hasClass('blueButton') ? a.removeClass('blueButton') : a.addClass('blueButton');

	 //toggleClass allows you to easily do this using

	 a.toggleClass('blueButton');
```

## 2.页面部分自动滚动

```
jQuery.fn.autoscroll = function(selector) {
  $('html,body').animate(
    {scrollTop: $(selector).offset().top},
    500
  );
}

//Then to scroll to the class/area you wish to get to like this:
$('.area_name').autoscroll();
```

## 3.自动隐藏或关闭元素

```
//Here's how we used to do it in 1.3.2 using setTimeout
    setTimeout(function() {
  $('.mydiv').hide('blind', {}, 500)
}, 5000);

//And here's how you can do it with 1.4 using the delay() feature (this is a lot like sleep)
$(".mydiv").delay(5000).hide('blind', {}, 500);
```

## 4.在屏幕上居中元素

```
jQuery.fn.center = function () {
	this.css('position','absolute');
	this.css('top', ( $(window).height() - this.height() ) / +$(window).scrollTop() + 'px');
	this.css('left', ( $(window).width() - this.width() ) / 2+$(window).scrollLeft() + 'px');return this;}
	//Use the above function as: $(element).center();
```

## 5.从元素中剥离 HTML

```
(function($) {
    $.fn.stripHtml = function() {
        var regexp = /< ("[^"]*"|'[^']*'|[^'">])*>/gi;
        this.each(function() {
            $(this).html(
                $(this).html().replace(regexp,”")
            );
        });
        return $(this);
    }
})(jQuery);

	//usage:
	$('p').stripHtml();
```

## 6.强制链接在弹出窗口中打开

```
jQuery('a.popup').live('click', function(){
	newwindow=window.open($(this).attr('href'),'','height=200,width=150');
	if (window.focus) {newwindow.focus()}
	return false;
});
```

## 7.强制链接在新标签中打开

```
jQuery('a.newTab').live('click', function(){
	newwindow=window.open($(this).href);
	jQuery(this).target = "_blank";
	return false;
});
```

## 8.向元素添加 HTML

```
$('#lal').append('sometext');
```

## 9.检测浏览器

```
Detect Safari (if( $.browser.safari)),
	Detect IE6 and over (if ($.browser.msie && $.browser.version > 6 )),
	Detect IE6 and below (if ($.browser.msie && $.browser.version < = 6 )),
	Detect FireFox 2 and above (if ($.browser.mozilla && $.browser.version >= '1.8' ))
```

## 10.定义自定义选择器

```
$.expr[':'].mycustomselector = function(element, index, meta, stack){
    // element- is a DOM element
    // index - the current loop index in stack
    // meta - meta data about your selector
    // stack - stack of all elements to loop

    // Return true to include current element
    // Return false to explude current element
};

// Custom Selector usage:
$('.someClasses:test').doSomething();
```

## 11.显示或删除输入字段中的默认值

```
//This snippet will show you how to keep a default value
//in a text input field for when a user hasn't entered in
//a value to replace it

swap_val = [];
$(".swap").each(function(i){
    swap_val[i] = $(this).val();
    $(this).focusin(function(){
        if ($(this).val() == swap_val[i]) {
            $(this).val("");
        }
    }).focusout(function(){
        if ($.trim($(this).val()) == "") {
            $(this).val(swap_val[i]);
        }
    });
});
```

## 12.用 jQuery 解析 XML

```
function parseXml(xml) {
	//find every Tutorial and print the author
	$(xml).find("Tutorial").each(function()
	{
	$("#output").append($(this).attr("author") + "");
	});
}
```

## 13.检查图像是否已完全加载

```
$('#theImage').attr('src', 'image.jpg').load(function() {
alert('This Image Has Been Loaded');
});
```

## 14.检查 Cookies 是否已启用

```
var dt = new Date();
    dt.setSeconds(dt.getSeconds() + 60);
    document.cookie = "cookietest=1; expires=" + dt.toGMTString();
    var cookiesEnabled = document.cookie.indexOf("cookietest=") != -1;
    if(!cookiesEnabled)
	{
        //cookies have not been enabled
    }
```

## 15.用可点击的链接替换任何 URL

```
$.fn.replaceUrl = function() {
        var regexp = /((ftp|http|https)://(w+:{0,1}w*@)?(S+)(:[0-9]+)?(/|/([w#!:.?+=&%@!-/]))?)/gi;
        this.each(function() {
            $(this).html(
                $(this).html().replace(regexp,'<a href="$1">$1</a>‘)
            );
        });
        return $(this);
    }

	//usage

	$('p').replaceUrl();
```

## 分享这篇文章