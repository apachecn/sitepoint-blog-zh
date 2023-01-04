# 改进 jQuery 的 10 个技巧

> 原文：<https://www.sitepoint.com/10-tips-improve-jquery/>

几乎到处都有 jQuery，你可以在当今著名的网站上找到它，包括社交网站、微博等。jQuery 确实很酷，也很有用，但是有时候最好还是看看它的另一面或者另一种味道。或者对其进行改进。

改进 jQuery 会让你的网站加载更快，并做类似的事情来帮助你的网站脱颖而出。这可能包括一个 jQuery 脚本或代码通过谷歌代码和类似的东西。就像我说的，最好改进或编辑一个 jQuery 插件或语法，让你的网站更加突出。

我这里有一个改进 jQuery 的 10 个技巧的列表。

## 1.“就绪”事件的快捷方式

打这个累不累？

```
$(document).ready(function (){
		// your code
	});
```

你可以做一个捷径，试试这个:

```
$(function (){
		// your code
	});
```

## 2.元素检查器

在操作该元素之前，一定要检查它是否真的存在于页面上。使用这个简单但不明显的代码:

```
if ($('#myDiv).length) {
		// your code
	}
```

## 3.使用另一个框架时，正确地重命名您的 jQuery 对象

使用 noconflict() jQuery 函数收回对$的控制权，并允许自己设置自己的变量名。

```
var $j = jQuery.noConflict();
	$j('#myDiv').hide();
```

## 4.压缩您的 jQuery 脚本

一个大项目可能也意味着大量 jQuery 插件的使用。但是请注意，这可能会降低页面速度，所以您应该使用狄恩·爱德华兹的 Packer 来压缩所有的 jQuery。这是一个基于网络的 javascript 压缩器。

## 5.最小化 DOM 操作

DOM 的插入操作。前置()。追加()。wrap()和。after()真的会减慢速度。你可以更快地编写代码；你所需要做的就是通过使用连接来建立列表，然后使用一个函数来将它们添加到你的列表中，就像无序的一样。html()要快得多。

*举例:*

```
var myList = $('#myList');

	for (i=0; i<1000; i++){
		myList.append('This is list item ' + i);
	}
	That example is relatively slow, but if you build the list item as a string and use the html method to do the insertion. You might want to try the following instead:
	Example:
	var myList = $('.myList');
	var myListItems = '';

	for (i = 0; i This is list item ' + i + '';
	}

	myList.html(myListItems);
```

这将使页面加载速度更快！

## 6.为你的选择器提供上下文

通常如果你使用一个像$('这样的选择器。my div’)DOM 肯定会被遍历，这取决于页面可能是昂贵的。执行选择会使 jQuery 接受第二个参数。
jQuery( expression，context )
通过将上下文放入一个选择器中，可以给这个选择器一个元素，这样它就不必遍历整个 DOM。

*之前:*

```
var selectedItem = $('#listItem' + i);
```

*之后:*

```
var selectedItem = $('#listItem' + i, $('.myList'));
```

那应该会加快速度！

## 7.动画的正确使用

动画是 jQuery 的核心力量。这真的很酷，并提供了非常吸引眼球的效果。
jQuery 的。animate()方法非常容易使用且功能强大。如果从内部看代码，这些方法只是被缩短了，并使用了。animate()函数。

*举例:*

```
slideDown: function(speed,callback){
		return this.animate({height: "show"}, speed, callback);
	},

	fadeIn: function(speed, callback){
		return this.animate({opacity: "show"}, speed, callback);
	}
```

animate()函数简单地改变元素的 CSS 属性，比如高度、宽度、颜色、不透明度、背景颜色等。

*举例:*

```
$('#myList li').mouseover(function() {
		$(this).animate({"height": 100}, "slow");
	});
```

与 jQuery 其他函数不同，动画是自动排队的。如果你想在第一个动画完成后执行另一个动画，只需调用 animate 方法两次。
举例:

```
$('#myBox').mouseover(function() {
		$(this).animate({ "width": 200 }, "slow");
		$(this).animate({"height": 200}, "slow");
	});
```

如果您想要多个动画，请这样做:

```
$('#myBox').mouseover(function() {
		$(this).animate({ "width": 200, "height": 200 }, "slow");
	});
```

## 8.做你自己的选择器

jQuery 有许多内置的选择器，可以通过 ID、标签、属性等选择元素。但是，如果您需要基于 jQuery 没有选择器的其他东西来选择元素，您会怎么做呢？很可能，你会从一开始就给元素添加类，并使用它们来选择它们。但是添加新的选择器违背了 jQuery 易于扩展的目的。

*举例:*

```
$.extend($.expr[':'], {
		over100pixels: function(a) {
			return $(a).height() > 100;
		}
	});
```

```
$('.box:over100pixels').click(function() {
		alert('The element you clicked is over 100 pixels high');
	});
```

自定义选择器的创建在第一个代码块中，它查找任何超过 100 像素高的元素。

## 9.加速内容加载以获得 SEO 好处

如果你认为整理你的 HTML 代码会让你的页面加载得更快。你是对的，搜索蜘蛛太懒了，在页面的其余部分加载完毕后，不会使用 AJAX 请求来加载整个代码。用户可以立即开始浏览，蜘蛛只能看到你希望他们索引的内容。

*举例:*

```
$('#forms').load('content/headerForms.html', function() {
		// Code here runs once the content has loaded
		// Put all your event handlers etc. here.
	});
```

## 10.使用备忘单

厌倦了 jQuery 技巧？这里有一些适用于大多数语言的很棒的备忘单！这是一个 A4 纸上可打印的 jQuery 函数，供您参考。

[http://www . gscottolson . com/weblog/2008/01/11/jquery-cheat-sheet/](http://www.gscottolson.com/weblog/2008/01/11/jquery-cheat-sheet/)
http://colorcharge.com/jquery/

## 分享这篇文章