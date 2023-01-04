# 7 个惊人的 jQuery 技巧

> 原文：<https://www.sitepoint.com/7-amazing-jquery-tricks/>

我再次收集了一些有用的 jQuery 技巧和窍门，让您看看 jQuery 有多棒。

他们在这里:

## 1.预加载图像

有了这个技巧，你可以在使用图片之前在 JavaScript 中预加载图片。

```
jQuery.preloadImages = function()
{
 for(var i = 0; i ").attr("src", arguments[i]);
 }
};

// Usage
$.preloadImages("image1.gif", "/path/to/image2.png", "some/image3.jpg");
```

## 2.目标空白链接

避免 XHTML 1.0 限制你的 target =空白代码。使用这个 jQuery 技巧来做这样的事情。

```
$('a[@rel$='external']').click(function(){
 this.target = "_blank";
});

/*
Usage:
[lepinskidesign.com.br](http://www.lepinskidesign.com.br/)
*/
```

## 3.获取匹配元素的总数

```
$('element').size();
```

## 4.检测浏览器

css 的条件注释对于检测浏览器和应用一些 CSS 样式非常有用。

```
//A. Target Safari
if( $.browser.safari ) $("#menu li a").css("padding", "1em 1.2em" );

//B. Target anything above IE6
if ($.browser.msie && $.browser.version > 6 ) $("#menu li a").css("padding", "1em 1.8em" );

//C. Target IE6 and below
if ($.browser.msie && $.browser.version = "1.8" ) $("#menu li a").css("padding", "1em 1.8em" );
```

## 5.等高的柱子

是的，这可以通过下面的 jQuery 技巧实现。

```
function equalHeight(group) {
 tallest = 0;
 group.each(function() {
 thisHeight = $(this).height();
 if(thisHeight > tallest) {
 tallest = thisHeight;
 }
 });
 group.height(tallest);
}

/*
Usage:
$(document).ready(function() {
 equalHeight($(".recent-article"));
 equalHeight($(".footer-col"));
});
*/
```

## 6\. Font Resize

使用 jQuery 调整字体大小。

```
$(document).ready(function(){
 // Reset Font Size
 var originalFontSize = $('html').css('font-size');
 $(".resetFont").click(function(){
 $('html').css('font-size', originalFontSize);
 });
 // Increase Font Size
 $(".increaseFont").click(function(){
 var currentFontSize = $('html').css('font-size');
 var currentFontSizeNum = parseFloat(currentFontSize, 10);
 var newFontSize = currentFontSizeNum*1.2;
 $('html').css('font-size', newFontSize);
 return false;
 });
 // Decrease Font Size
 $(".decreaseFont").click(function(){
 var currentFontSize = $('html').css('font-size');
 var currentFontSizeNum = parseFloat(currentFontSize, 10);
 var newFontSize = currentFontSizeNum*0.8;
 $('html').css('font-size', newFontSize);
 return false;
 });
});
```

## 7.删除文本中的一个单词

使用这个 jQuery 技巧删除文本中的任何单词。

```
var el = $('#id');
el.html(el.html().replace(/word/ig, ""));
```

## 分享这篇文章