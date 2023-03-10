# 使用 jQuery 清除 iPad Viewport Cookie

> 原文：<https://www.sitepoint.com/jquery-clear-ipad-viewport-cookie/>

最近在做一些关于 iPad 的工作，我想我应该发布一些关于如何使用 jQuery 清除 ipad viewport cookie 的技巧和想法。我对这种移动视口操作相当陌生，所以请随时纠正我，留下评论。


## 初始视口元标签设置

```
$('meta[name=viewport]').attr('content','width=device-width, user-scalable=yes, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0');
```

请注意，gesturestart 是捕获 iPad 屏幕调整缩放的事件的名称。

```
$(document).live('gesturestart', function()
{
    $('meta[name=viewport]').attr('content','width=device-width, user-scalable=yes, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0');
});
```

## 其他可行的方法

事件监听器检测 iPad 屏幕方向的变化。

```
(function(doc) {

  var addEvent = 'addEventListener',
      type = 'gesturestart',
      qsa = 'querySelectorAll',
      scales = [1, 1],
      meta = qsa in doc ? doc[qsa]('meta[name=viewport]') : [];

  function fix() {
    meta.content = 'width=device-width,minimum-scale=' + scales[0] + ',maximum-scale=' + scales[1];
    doc.removeEventListener(type, fix, true);
  }

  if ((meta = meta[meta.length - 1]) && addEvent in doc) {
    fix();
    scales = [.25, 1.6];
    doc[addEvent](type, fix, true);
  }

}(document));
```

基于 doorientationchange()函数更改 viewport 元标签设置，该函数检测到 iPhone/iPad 横向模式正在重新加载页面。

```
var viewportmeta = document.querySelector && document.querySelector('meta[name="viewport"]'),
ua = navigator.userAgent;

function allowscale()
{
    viewportmeta.content = "width=device-width, minimum-scale=0.25, maximum-scale=3.2";
}
var t=setTimeout("allowscale()",1000);

//and re-fix the orientation bug to clean up all problems:-

function doorientationchange()
{
    if (viewportmeta && /iPhone|iPad/.test(ua) && !/Opera Mini/.test(ua))
    {
        if(((window.orientation)&2)==2) {
            location.reload(false); // Safari messes up when changing into landscape mode… so reload to fix it.
        }
    }
}
```

您也可以像这样使用 CSS 属性:

```
body
  {
    -webkit-text-size-adjust:none;
    -webkit-transform: scale(1.1);
  }
```

## 分享这篇文章