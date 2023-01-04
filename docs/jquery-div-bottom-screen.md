# jQuery 保持 Div 在屏幕底部

> 原文：<https://www.sitepoint.com/jquery-div-bottom-screen/>

如何使用 jQuery 将 div 显示在屏幕上(底部或顶部)。

这可以通过基本的 CSS 来实现，但是如果这失败了，你可以使用一些 jQuery/JavaScript 来潜在地修复即使在滚动时在屏幕底部保持一个 div 的问题。

```
.bottom { position:fixed; position:absolute; right:0; bottom:0; padding:0; margin:0; }

/*IE FIX*/
.fixie {
  left: expression( ( - jsrp_related.offsetWidth + ( document.documentElement.clientWidth ? document.documentElement.clientWidth : document.body.clientWidth ) + ( ignoreMe2 = document.documentElement.scrollLeft ? document.documentElement.scrollLeft : document.body.scrollLeft ) ) + 'px' );
  top: expression( ( - jsrp_related.offsetHeight + ( document.documentElement.clientHeight ? document.documentElement.clientHeight : document.body.clientHeight ) + ( ignoreMe = document.documentElement.scrollTop ? document.documentElement.scrollTop : document.body.scrollTop ) ) + 'px' );
}
```

```
//note you MUST set a height property for it to work correctly
if ( $.browser.msie ) {
	div.css({position: "absolute", width: jslide_width, right: 0, height: 100});
	div.addClass('fixie');
} else {
	div.css({position: "fixed", width: jslide_width, right: 0, height: 100});
}
```

## 其他技术

还有一个 jQuery [插件](http://plugins.jquery.com/files/stickyfloat_0.htm)，它可以将侧边栏上的任何元素保持在视图中。

## 分享这篇文章