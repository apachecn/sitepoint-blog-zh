# iScroll 4 助手代码片段

> 原文：<https://www.sitepoint.com/iscroll-helper-code-snippets/>

我已经用了 [iScroll 4 插件](http://cubiq.org/iscroll-4)有一段时间了，它非常漂亮。我用它来制作触摸友好的滚动条——很棒的插件。如果我有时间，我会为它编写一个扩展 API&代码一些演示，如果有足够的兴趣。但是现在这里有一些代码片段。

```
//settings for snippets below
var scrollName = 'myScroll',
     scrollDuration = 1000,
    scrollAmount = 200;
```

带有结束滚动回调的设置滚动。

```
//setup scroll with end scroll callback
window[scrollName] = new iScroll(scrollName, {
    onScrollEnd: function() {
        console.log('onScrollEnd');
        //do something on end of scrolling

    }
});
```

检查滚动是否在内容的顶部。

 `//检查滚动是否在内容顶部
window . scroll name . y<0[/js]检查滚动是否在内容底部。[js]//检查滚动到内容窗口的底部。Scroll name . y = = window . maxscrolly[/js]滚动到内容的顶部。[js]//滚动到内容窗口顶部. scroll name . scroll to(scroll duration)；//或者 window.scrollName.scrollTo(0，0) [/js]相对向上滚动。[js]//相对向上滚动 window.scrollName.scrollTo(0，'-'+scrollAmount，scrollDuration，true)；[/js]相对向下滚动。[js]//相对向下滚动 window.scrollName.scrollTo(0，scrollAmount，scrollDuration，true)；[/js]//初始化一个引导模式窗口上的滚动，如果它不存在[js] if(！窗口[scrollName]。wrapper){//它需要可见才能工作...setTimeout(function(){ console . log(' modal is scroll ... '));console . log(scroll name)；window[scroll name]= new is scroll(scroll name)；}, 10);} [/js]` 

## `分享这篇文章`