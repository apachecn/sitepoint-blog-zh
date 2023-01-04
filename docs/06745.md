# 对$的向后支持。live()和$。浏览器()

> 原文：<https://www.sitepoint.com/support-live-browser/>

来自 jQuery 迁移插件的快速代码片段。我建议使用完整的迁移插件，但如果你想为美元的细节。live()和$。浏览器()见下文。

```
var oldLive = jQuery.fn.live;
jQuery.fn.live = function( types, data, fn ) {
    // migrateWarn("jQuery.fn.live() is deprecated");
    if ( oldLive ) {
        return oldLive.apply( this, arguments );
    }
    jQuery( this.context ).on( types, this.selector, data, fn );
    return this;
};
```

来源:[https://github . com/jquery/jquery-migrate/blob/master/src/event . js](https://github.com/jquery/jquery-migrate/blob/master/src/event.js)

 `var 浏览器；

jquery . ua match = function(ua){
ua = ua . tolowercase()；

var match = /(chrome)[ /]([w.]+)/。exec(ua)| |
/(WebKit)[/]([w .]+)/。exec( ua ) ||
/(opera)(？:.*version|)[ /]([w.]+)/。exec( ua ) ||
/(msie) ([w.]+)/。exec(ua)| |
ua . index of(" compatible ")<0&&/(Mozilla)(？:.*?rv:([w.]+)|)/。exec(ua)| |[]；return { browser: match[ 1 ] || " "，version:match[2]| | " 0 " }；};//不要敲打任何现有的 jQuery.browser，以防不同 if(！jquery . browser){ matched = jquery . ua match(navigator . user agent)；browser = { }；if(matched . browser){ browser[matched . browser]= true；browser . version = matted . version；} // Chrome 是 Webkit，但 Webkit 也是 Safari。if(browser . chrome){ browser . WebKit = true；} else if(browser . WebKit){ browser . safari = true；} jQuery.browser =浏览器；} [/js]来源:[https://github . com/jquery/jquery-migrate/blob/master/src/core . js](https://github.com/jquery/jquery-migrate/blob/master/src/core.js)

[](https://github.com/jquery/jquery-migrate/blob/master/src/core.js)`

## `分享这篇文章`