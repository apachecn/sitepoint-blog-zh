# 4 个 HTML5 和 jQuery 预加载插件/脚本

> 原文：<https://www.sitepoint.com/html5-jquery-preloading-plugins/>

这里有几个 **HTML5 & jQuery 预加载插件/脚本**为你的 HTML5 web 应用带来更好的用户体验。插件/脚本从静态资产和/或链接的 CSS 样式表中加载文本内容、图像、背景、视频、音频等。尽情享受吧！

**相关帖子:**

*   [**10 jQuery 预加载图片插件**](http://www.jquery4u.com/plugins/preload-image-plugins/)
*   [**2 分钟 WEB APP 图像预加载设置**](http://www.jquery4u.com/speed/web-app-image-preloading-setup-2mins/)

## 1.jQuery.html5Loader

它可以预加载图像，html5 视频和音频源，脚本和文本文件。

[![jQueryhtml5Loader-copy.jpg](img/230f90cbc02a1c5cd0d5ae18aa9a9b1b.png)](http://gianlucaguarini.com/canvas-experiments/jQuery-html5Loader/) 
[来源](http://gianlucaguarini.com/canvas-experiments/jQuery-html5Loader/) [演示](http://gianlucaguarini.com/canvas-experiments/jQuery-html5Loader/examples/demo-circular.html)

## 2.篮球. js

这是一个小型的 JavaScript 库，支持脚本的本地存储缓存。

示例:
单个脚本

```
basket.require({ url: 'jquery.js' });
```

多脚本

```
basket.require(
    { url: 'jquery.js' },
    { url: 'underscore.js' },
    { url: 'backbone.js' }
);
```

[来源](http://addyosmani.github.io/basket.js/)

## 3.jquery-ajax-html5-cache

ajax 请求的缓存，以减少请求。

```
$.ajax({
    url: 'test.json',
    dataType: 'json',
    localCache: true,       // enable localStorage
    forceCache: false,      // force to make an ajax request and cache it
    cacheKey: 'mydata',     // the item name in the localStorage
    cacheTTL: 3,            // the cache live for how long in seconds, default is 60 sec
    success: function(o){
        console.log(o); //see new ajax function .done()
    }
})
```

[来源](https://github.com/huboo/jquery-ajax-html5-cache)

## 4.CreateJS

一套 Javascript 库和工具，用于使用 HTML5 构建丰富的交互式体验。

预加载示例

```
var preload = new createjs.LoadQueue();
preload.addEventListener("fileload", handleFileComplete);
preload.loadFile('http://createjs.cimg/404/gBot-confused.jpg');
function handleFileComplete(event) {
    document.body.appendChild(event.result);
}
```

[来源](http://createjs.com)

## 分享这篇文章