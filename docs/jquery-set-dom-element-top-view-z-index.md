# jQuery 将 DOM 元素设置为俯视图 Z 索引

> 原文：<https://www.sitepoint.com/jquery-set-dom-element-top-view-z-index/>

jQuery 函数使用 CSS Z-Index 属性将任意 DOM 元素设置为顶视图(**置于最前面**)。


```
jQuery.fn.mb_bringToFront= function(zIndexContext){
    var zi=1;
    var els= zIndexContext && zIndexContext!="auto" ? $(zIndexContext): $("*");
    els.not(".alwaysOnTop").each(function() {
      if($(this).css("position")!="static"){
        var cur = parseInt($(this).css('zIndex'));
        zi = cur > zi ? parseInt($(this).css('zIndex')) : zi;
      }
    });
    $(this).not(".alwaysOnTop").css('zIndex',zi+=1);
    return zi;
  };
```

## 分享这篇文章