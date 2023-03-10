# 使用 jQuery 滚动到顶部(设置时间:2 分钟)

> 原文：<https://www.sitepoint.com/scroll-top-jquery-setup-time-2mins/>

这是一个快速的一步一步地为你的网站设置滚动到顶部。对于演示，只需向下滚动这个网站。

1.下载 [scrollTo 插件](https://github.com/flesler/jquery.scrollTo/blob/master/jquery.scrollTo.min.js)，包含它。
2。得到一个图像([箭头](http://www.iconarchive.com/search?q=up+arrow)之类的)。
3。下面包括一些 HTML。
4。包括一些 jQuery/JavaScript 来捕捉窗口滚动和处理图像显示。
5。就是这样！

## 超文本标记语言

## jQuery

只是一些小 jQuery 来显示当用户向下滚动时的图像，当用户向上滚动时隐藏图像，并处理点击事件。

```
$(document).ready(function()
{
    var $scrollTop = $('#scroll-to-top');

    $(window).scroll(function()
    {
        //scroll top top
        if ($(this).scrollTop() > 100)
        {
            $scrollTop.fadeIn(1000);
        }
        else
        {
            $scrollTop.hide();
        }
    });

    $scrollTop.bind('click', function(e)
    {
        e.preventDefault();
        $.scrollTo(0, 1000);
    });
});
```

**注意:**如果你想要一个跨浏览器的解决方案，只需使用这个:

```
window.scrollTo(0,0); //chrome scroll to top bugfix
```

## 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

只是一些简单的 CSS 来处理图像的显示和跨浏览器的不透明度。

```
#scroll-to-top {
    position: fixed;
    bottom: 10px;
    right: 10px;
    display: none;

    -ms-filter: "progid:DXImageTransform.Microsoft.Alpha(Opacity=50)";
    filter: alpha(opacity=50);
    -moz-opacity: 0.5;
    -khtml-opacity: 0.5;
    opacity: 0.5;
}
#scroll-to-top:hover {
    -ms-filter: "progid:DXImageTransform.Microsoft.Alpha(Opacity=100)";
    filter: alpha(opacity=100);
    -moz-opacity: 1.0;
    -khtml-opacity: 1.0;
    opacity: 1.0;
}
```

## 分享这篇文章