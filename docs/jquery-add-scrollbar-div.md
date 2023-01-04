# jquery 向 div 添加滚动条

> 原文：<https://www.sitepoint.com/jquery-add-scrollbar-div/>

当**在 div** 中添加滚动条时，如果内容超出了容器元素的区域，这是一个有用的 jquery 代码片段。抱歉没有演示，但只是粘贴到 Firebug。

```
//D = document
//W = window
//$ = jQuery

var contentArea = $(this),
    wintop = contentArea.scrollTop(),
    docheight = $(D).height(),
    winheight = $(W).height(),
    divheight = $('#content').height(),
    scrollheight = $('#content')[0].scrollHeight,
    scrolltrigger = 0.9;

console.log('wintop ' + wintop);
console.log('docheight ' + docheight);
console.log('winheight ' + winheight);
console.log('divheight ' + divheight);
console.log('scrollheight ' + scrollheight);

console.log((wintop + divheight)/scrollheight);

if  (((wintop + divheight)/scrollheight) > scrolltrigger) {
    // display scroll bar
}
```

**延伸阅读:**
[http://manos.malihu.gr/jquery-custom-content-scroller](http://manos.malihu.gr/jquery-custom-content-scroller)
[http://jscrollpane.kelvinluck.com/arrows.html](http://jscrollpane.kelvinluck.com/arrows.html)

## 分享这篇文章