# 使用 jQuery 制作彩色过渡动画的尝试

> 原文：<https://www.sitepoint.com/attempt-animating-colour-transitions-jquery/>

我已经提到了[。之前的 setInterval()函数](http://www.jquery4u.com/jquery-functions/setinterval-example/)。我在创建一个脚本时使用了这个函数，这个脚本将(理论上)使用 jQuery 制作颜色过渡的动画。例如，从白色到浅红色到红色到暗红色，如不透明过渡。


## 动画颜色过渡的初步尝试

这是我的尝试。请注意，最初的结果不是很好，所以我没有浪费更多的时间来编码它，而是张贴在这里，主要供我参考。

```
$('#input-page-url').css('border-color','rgb(255, 255, 255)');

var r = 0, g = 0, b = 0;
var interval = 25;
var borderWidth = 0;

var animateLoop  = setInterval(function() {

/* exit loop if out of colour range */
if ((r+interval) > 255) { r = 255; clearInterval(animateLoop); }
else { i++; r += interval; borderWidth += 0.2; }

$('#input-page-url').css(
      { 
         'border-color': 'rgb('+r+','+g+','+b+')',
         'border-width': borderWidth
      });

console.log('rgb('+r+','+g+','+b+')');

}, 100);
```

这是一种。使用 jQuery 函数的 setinterval()循环示例。clear interval()(clear setinterval)当颜色达到 255 RGB 时重置颜色。然后它使用。CSS()来改变元素边框的 RGB 颜色。

这也是一个很酷的插件，它做了一些类似的事情:高亮渐变，为事件创建了高亮背景色渐变。

## 常见错误:引用错误:未定义 rgb

这是因为你需要用引号将 rgb 括起来(记住 rgb 是有效的 css！不需要转换成十六进制)。

```
$('#input-page-url').css('border-color','rgb(redShade, 0, 0)');
```

## 分享这篇文章