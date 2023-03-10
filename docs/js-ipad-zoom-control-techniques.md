# js ipad 缩放控制技巧

> 原文：<https://www.sitepoint.com/js-ipad-zoom-control-techniques/>

只是用 JavaScript 和 viewport meta 标签记下一些在 ipad 上控制缩放的技巧。请记住，对于 iPad，**设备宽度始终为 768 像素**，与方向
无关

```
<meta name="viewport" content="maximum-scale=1.6, minimum-scale=0.25" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<meta name="viewport" content="width=device-width" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1"/>
```

这一款针对 iPad 进行了优化(效果最佳):

```
<meta name="viewport" content="maximum-scale=1.6, minimum-scale=0.25" />
```

**使用这个脚本得到当前的视口比例**:http://menacingcloud.com/source/js/FlameViewportScale.js

代码:

```
$.getScript('http://menacingcloud.com/source/js/FlameViewportScale.js', function()
{
var viewScale = new FlameViewportScale();
var currentScale = viewScale.getScale();
viewScale.update(function() { alert('Scale measure complete...'+currentScale); });
});
```

该代码禁止用户使用收缩技术在页面上**重新缩放视窗**:

```
$('meta[name=viewport]').attr('content','width=1024, user-scalable=no');
```

或者使用普通的 JavaScript:

```
viewport = document.querySelector("meta[name=viewport]");
viewport.setAttribute('content', 'width=device-width; initial-scale=1.0; maximum-scale=1.0; user-scalable=0;');
```

**关键词:**
js ipad 自动缩放
js ipad 缩放按钮
js ipad 模拟捏出
js ipad 改变视口
ipad 改变视口比例

## 分享这篇文章