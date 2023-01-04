# 使用普通 JavaScript 动态加载 jQuery 库

> 原文：<https://www.sitepoint.com/dynamically-load-jquery-library-javascript/>

这就是如何使用普通 JavaScript 来**加载 jQuery 库。由于加载是异步进行的，所以我也包含了一个回调版本，这样您就可以知道插入的脚本何时完成，您就可以开始使用 jQuery 了！我之前发布过关于[将脚本插入安全页面](http://www.jquery4u.com/javascript/inserting-script-secure-encrypted-pages/)的文章，所以那里也有更多的选择。** 

## 使用普通 JavaScript 加载 jQuery 库

```
//Load jQuery library using plain JavaScript
(function(){
  var newscript = document.createElement('script');
     newscript.type = 'text/javascript';
     newscript.async = true;
     newscript.src = 'https://ajax.googleapis.com/ajax/libs/jquery/1.6.1/jquery.min.js';
  (document.getElementsByTagName('head')[0]||document.getElementsByTagName('body')[0]).appendChild(newscript);
})();
```

## 带回拨

```
(function () {

    function loadScript(url, callback) {

        var script = document.createElement("script")
        script.type = "text/javascript";

        if (script.readyState) { //IE
            script.onreadystatechange = function () {
                if (script.readyState == "loaded" || script.readyState == "complete") {
                    script.onreadystatechange = null;
                    callback();
                }
            };
        } else { //Others
            script.onload = function () {
                callback();
            };
        }

        script.src = url;
        document.getElementsByTagName("head")[0].appendChild(script);
    }

    loadScript("https://ajax.googleapis.com/ajax/libs/jquery/1.6.1/jquery.min.js", function () {

         //jQuery loaded
         console.log('jquery loaded');

    });

})();
```

## 分享这篇文章