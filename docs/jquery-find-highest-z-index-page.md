# jQuery 查找页面上最高的 Z 索引

> 原文：<https://www.sitepoint.com/jquery-find-highest-z-index-page/>

jQuery 代码片段查找绝对 div 的最高 z 索引。对于让一个新元素出现在所有 html 元素的顶部非常有用。

```
//include jQuery.js --  visit: http://jquery.com/
$(function(){
    var maxZ = Math.max.apply(null,$.map($('body > *'), function(e,n){
           if($(e).css('position')=='absolute')
                return parseInt($(e).css('z-index'))||1 ;
           })
    );
    alert(maxZ);
});
```

[来源](http://abcoder.com/javascript/a-better-process-to-find-maximum-z-index-within-a-page/)

## 分享这篇文章