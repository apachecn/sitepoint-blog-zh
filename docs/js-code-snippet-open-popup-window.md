# 打开弹出窗口的 js 代码片段

> 原文：<https://www.sitepoint.com/js-code-snippet-open-popup-window/>

没有什么特别的只是一些 JavaScript 代码片段打开一个弹出窗口。

```
W = window,
    D = document;

    //set the window to widgets dimensions
    var winH = 400, //allow for window padding and header
        winW = 400,
        winT = 400,
        winL = 400;

    //set the content
    var windowName = 'Widget', // should not include space for IE
        path = W.location.path,
        windowOptions = 'location=0,status=0,toolbar=0,dependent=1,resizable=1,menubar=0,screenX=' + winL + ',screenY=' + winT + ',width=' + winW + ',height=' + winH,
        that = this;

    //note path needs to be in the same domain...

    //open the window
    that._Window = window.open(path, windowName, windowOptions);
    that._Interval = window.setInterval(function () {
        if (that._Window.closed) {
            window.clearInterval(that._Interval);
            //code to run when window is closed

        }
    }, 1000);
```

## 分享这篇文章