# jQuery 捕获窗口调整片段大小

> 原文：<https://www.sitepoint.com/jquery-capture-window-resize/>

使用 jQuery 捕获浏览器窗口调整大小时的事件，然后做一些事情。在下面的例子中，它记录了窗口的新尺寸。

**2013 年 16 月 5 日更新:**请看下面的去抖动方法，更智能地调整窗口大小！

```
//capture window resize
$(window).bind('resize', function(e)
{
    var win = $(this),
        w = win.width(),
        h = win.height();

    console.log('window resized to: ' + w + ' by ' + h);
});

//output: window resized to: 1598 by 521
```

## 调整浏览器大小时刷新页面

一个相当粗糙的跨浏览器 IE8+解决方案。

```
//this is in a timeout so it works in IE8
setTimeout(function()
{
    $(window).bind('resize', function(e)
    {
        if(window.RT) clearTimeout(window.RT);
        window.RT = setTimeout(function()
        {
            this.location.reload(false); /* false to get page from cache */
        }, 300);        
    });
}, 1000);
```

## 调整窗口大小时重新定位导航条的示例

调整窗口大小时移动导航菜单栏。轻微的 300 毫秒延迟，但这是为了阻止它递归调用重新定位时，浏览器正在调整大小。

```
(function($,W)
{
    //DOM Ready
    $(function()
    {
        //responsive main nav absolute top position relative to window height
        function repositionMainNav()
        {
            var newT = W.innerHeight - 300;
            newT = (newT  550) ? 550 : newT; //max top
            // console.log(newT);
            $('#navbar').css('top', newT);
        }
        repositionMainNav();

        $(W).bind('resize', function(e)
        {
            if(W.RT) clearTimeout(W.RT);
            W.RT = setTimeout(function()
            {
                //recalculate the vertical position of the main nav
                repositionMainNav();
            }, 300);
        });
    });
})(jQuery, window);
```

## 消除“更智能”的窗口调整事件

承蒙杰出的保罗·爱尔兰先生在他的[去抖帖子](http://paulirish.com/2009/throttled-smartresize-jquery-event-handler/)中的好意，并观看[演示。](http://paulirish.com/demo/resize)

```
(function($,sr){

  // debouncing function from John Hann
  // http://unscriptable.com/index.php/2009/03/20/debouncing-javascript-methods/
  var debounce = function (func, threshold, execAsap) {
      var timeout;

      return function debounced () {
          var obj = this, args = arguments;
          function delayed () {
              if (!execAsap)
                  func.apply(obj, args);
              timeout = null;
          };

          if (timeout)
              clearTimeout(timeout);
          else if (execAsap)
              func.apply(obj, args);

          timeout = setTimeout(delayed, threshold || 100);
      };
  }
  // smartresize 
  jQuery.fn[sr] = function(fn){  return fn ? this.bind('resize', debounce(fn)) : this.trigger(sr); };

})(jQuery,'smartresize');

// usage:
$(window).smartresize(function(){
  // code that takes it easy...
});
```

## 分享这篇文章