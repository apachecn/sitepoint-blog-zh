# $.获取脚本多个脚本

> 原文：<https://www.sitepoint.com/getscript-mutiple-scripts/>

这对于**加载带有回调函数**的多个脚本非常有用，回调函数包含只有在加载完所有脚本后才运行的代码。要加载多个脚本，您需要增强 AJAX $。getScript()函数来处理多个脚本，然后简单地将它们添加到一个数组中作为第一个参数，将回调函数作为第二个参数。


## 单个 jQuery Get 脚本

```
$.getScript('script1.js', function(data, textStatus) {
    //do something after script has loaded
});
```

## 多个 jQuery Get 脚本

```
/* enhance $.getSctipt to handle mutiple scripts */
var getScript = jQuery.getScript;
jQuery.getScript = function( resources, callback ) {

    var // reference declaration & localization
    length = resources.length,
    handler = function() { counter++; },
    deferreds = [],
    counter = 0,
    idx = 0;

    for ( ; idx Any way I discovered:

You could just do it this way, say you had to load 3 scripts and the third has a callback.

[js]
$.get("js/ext/flowplayer-3.2.8.min.js")
.pipe($.get("js/eviflowplayer.js"))
.pipe($.get("js/evi.flowplayer.js", {}, function()
{
    W.EVI.FLOWPLAYER.init(elem.attr('id'));
});
```

## 分享这篇文章