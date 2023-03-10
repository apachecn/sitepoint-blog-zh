# jQuery 捕获窗口在事件结束时调整大小

> 原文：<https://www.sitepoint.com/jquery-capture-window-resize-event/>

有两种方法可以捕获窗口的 resize 事件，但是一旦用户完成了窗口的 resize，如何**捕获事件呢？一个小技巧是使用带有 clearTimeout()的 setTimeout()，最佳持续时间是 250，即使捕获 slowish 窗口大小调整平滑。参见[窗口大小调整示例](http://www.jquery4u.com/?p=12462)了解如何使用它们。** 

## 方法 1

```
$(window).bind('resize', function(e)
 {
     //do something
});
```

## 方法 2

```
$(window).resize( function(e)
{
    //do something
});
```

## 超时的方法 2

```
$(window).bind('resize', function(e)
{
    window.resizeEvt;
    $(window).resize(function()
    {
        clearTimeout(window.resizeEvt);
        window.resizeEvt = setTimeout(function()
        {
            //code to do after window is resized
        }, 250);
    });
});
```

## 分享这篇文章