# jQuery 动态改变 CSS 很简单！

> 原文：<https://www.sitepoint.com/change-css-jquery/>

![change-css-with-jquery](img/6a59e6f1fc9b4f34c237a7ca9b5ab300.png "change-css-with-jquery")

[CSS 功能演示](http://www.jquery4u.com/function-demos/css/)

动态地改变你的网站风格现在正风靡网络！在这篇简短而甜蜜的帖子中，我将解释如何使用 jQuery 来做一些简单而有效的 CSS 技巧。这是所有狂热的 jQuery 开发人员必须知道的！


## 更改特定的 CSS 元素

用 jQuery 修改 CSS 真的很容易。CSS()函数。

```
$('jQuery selector').css({"css property name":"css property value"});
```

以下是一些常见的例子:

```
//change paragraph text colour to green 
$('p').css({"color":"green"});

//float all divs with class .left
$('div.left').css('float');

//change all elements with class .bg-red to have a red background
$('.bg-red').css({"background-color":"red"});
```

## 嵌套 jQuery CSS 命令

知道 jQuery 同样可以解释多单词属性的 CSS 和 DOM 格式是很方便的。这不仅会节省你很多时间，而且看起来更漂亮！

```
newimg.css({'background-image': 'url('+newimgsrc+')'});
newimg.css({'position': 'absolute'});
newimg.css({'height': '70px'});
newimg.css({'width': '200px'});
newimg.css({'top': '68px'});
newimg.css({'right': '2px'});
```

与以下内容完全相同:

```
newimg.css({'background-image': 'url('+newimgsrc+')', 'position': 'absolute', 'height': '70px', 'width': '200px', 'top': '68px', 'right': '2px'});
```

## 移除 CSS 样式

有两种主要的方法来删除 CSS 样式，它们之间没有太大的区别。

1.您可以移除与页面或元素关联的类

```
//remove text color from a div
$('#mydiv').removeClass('colors');
```

2.您也可以直接移除某些元素上的 CSS 样式

```
//remove text color from a div
$('#mydiv').css('color', '');
```

这也是在同一个调用中删除和添加一个类的 jQuery CSS 技巧。

```
//change text color from red to green (classes specified in stylesheet)
$('#div').removeClass('red').addClass('green');
```

## 扩展现有样式值

您可能希望只扩展基于其当前值的样式。例如，如果一个元素的左填充是 10px，那么下面的代码将产生 25px 的总左填充。

```
.css( "padding-left", "+=15" )
```

## jQuery。CSS()函数属性

正如大多数狂热的 jQuery 开发人员所知，从 jQuery 1.4 开始。css()允许我们传递一个函数作为属性值。这对于返回当前 CSS 值以确定更改非常方便。

```
$('div.example').css('width', function(index) {
  return index * 50;
});
```

## 常见背景 CSS 更改

下面是一些改变背景 CSS 的例子。

```
$('#myDiv').css('background-image', 'my_image.jpg');
// OR
$('#myDiv').css('background', 'path/to/image.jpg');
// OR
$('#myDiv').css("background-image", "url(/myimage.jpg)");  

<br /><br />
<h2>A Full Code Example - Set Div Background Image</h2>
This is a full example of jQuery Code to set a background image into a div dynamically when the page is loaded.

[code lang="js"]
<script type='text/javascript'>
$(document).ready(function() {
	//preload image (add timestamp to prevent cache)
	var newimgsrc = 'https://www.sitepoint.com/wp-content/uploads/jquery4u/2011/03/jquery-plugins2.jpg?' + (new Date().getTime());
	var newimg = $('#header');
    //replace the image
	$('#header').css("background-image", "url("+newimgsrc+")");
	newimg.css({'background-image': 'url('+newimgsrc+')', 'position': 'absolute', 'height': '70px', 'width': '200px', 'top': '68px', 'right': '2px'});
	newimg.show();
});
</script>
```

## 分享这篇文章