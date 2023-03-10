# 使用 JavaScript/jQuery 检查 IE 版本的 5 种方法

> 原文：<https://www.sitepoint.com/check-ie-version/>

好的，我想我会把我所知道的每一种使用 JavaScript 和 jQuery 检查 Internet Explorer 版本的方法结合起来。我们都喜欢迎合 IE 的需求，所以如果任何人知道任何其他方式使 IE 版本的支持更容易，请分享，我会包括在列表中。Hawt-sniff…

## 使用 JavaScript 对 IE 进行基本检查

```
//check for IE7
if(navigator.appVersion.indexOf("MSIE 7.")!=-1)
```

## 使用 modernizr 检查 ie 版本

Moderizer 为我们提供了一个很好的方法来检测支持的浏览器功能，正如你在下面看到的，它增加了 IE 上的版本。

```
//check for IE8 or less
if ($('html').hasClass('lt-ie8');

//example of HTML tag populated by modernizer
<html class=" lt-ie9 lt-ie8 js no-flexbox no-canvas no-canvastext no-webgl no-touch no-geolocation postmessage no-websqldatabase no-indexeddb no-hashchange no-history draganddrop no-websockets no-rgba no-hsla no-multiplebgs no-backgroundsize no-borderimage no-borderradius no-boxshadow no-textshadow no-opacity no-cssanimations no-csscolumns no-cssgradients no-cssreflections no-csstransforms no-csstransforms3d no-csstransitions fontface no-generatedcontent no-video no-audio no-localstorage no-sessionstorage no-webworkers no-applicationcache no-svg no-inlinesvg no-smil no-svgclippaths" sizset="false" sizcache032866541369794594="704 34 0">
```

## 使用 jQuery 检查 IE

[$。从 jQuery 1.9 开始，浏览器不再被推荐使用](http://api.jquery.com/jQuery.browser/)，因为这个方法不再有效。

```
//check for IE8 or less
if($.browser.msie && parseFloat($.browser.version)&lt;8){//do other stuffreturn;}
```

## CSS 条件注入脚本

一小段代码，用于检测 JavaScript 中的 IE 版本，而不需要借助用户代理嗅探。酷毙了。

```
var ie = (function(){

    var undef,
        v = 3,
        div = document.createElement('div'),
        all = div.getElementsByTagName('i');

    while (
        div.innerHTML = '<!--[if gt IE ' + (++v) + ']><i></i>< ![endif]-->',
        all[0]
    );

    return v > 4 ? v : undef;

}());
```

来源:https://gist.github.com/padolsey/527683

## 使用 JavaScript 检查 IE10

浏览器代理嗅探器。

```
(function() {
  "use strict";
  var tmp = (document["documentMode"] || document.attachEvent) && "ev"
       , msie = tmp 
                  && (tmp = window[tmp + "al"])
                  && tmp("/*@cc_on 1;@*/")
                  && +((/msie (d+)/i.exec(navigator.userAgent) || [])[1] || 0)
  ;
  return msie || void 0;})();
```

## 基本 HTML 条件

通常的方法你可能已经见过了。

```
<!--[if IE 7 ]> <div id="system" class="ie7"> < ![endif]-->
<!--[if IE 7]> <script type="text/javascript"> $('#system').addClass('ie7'); </script> < ![endif]-->
```

如果你知道更多的方法、陷阱和其他观察结果来帮助我们对抗 IE，请分享。

## 分享这篇文章