# jQuery Konami 代码监听器

> 原文：<https://www.sitepoint.com/jquery-konami-code-listener/>

jQuery 代码片段监听日本 Konami 代码的输入，然后执行您想要的操作。

```
if ( window.addEventListener ) {
    var kkeys = [], konami = "38,38,40,40,37,39,37,39,66,65";
    window.addEventListener("keydown", function(e){
        kkeys.push( e.keyCode );
        if ( kkeys.toString().indexOf( konami ) >= 0 )
            // User entered Konami Code, do something cool!!!
            kkeys = [];
    }, true);
}
```

更多关于 Konami 插件的信息，请点击这里:[http://plugins.jquery.com/project/konamiCode](http://plugins.jquery.com/project/konamiCode)

## 分享这篇文章