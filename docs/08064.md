# jQuery 捕获弹出窗口的关闭

> 原文：<https://www.sitepoint.com/jquery-capture-close-popup-window/>

捕捉弹出窗口关闭的 jQuery 代码片段。

```
//monitor the closing of a popup window
var win = window.open("http://dev.com/index.php?m=social&a=testLinkedIn",'','height=500,width=500');
var winTimer = window.setInterval(function()
{
    if (win.closed !== false)
    {
        // !== is required for compatibility with Opera
        window.clearInterval(winTimer);
        log('win closed...');
    }
}, 200);
```

## 分享这篇文章