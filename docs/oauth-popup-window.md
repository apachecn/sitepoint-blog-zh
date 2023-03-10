# JavaScript oAuth 弹出窗口处理程序代码

> 原文：<https://www.sitepoint.com/oauth-popup-window/>

这是一个有用的 JavaScript 函数，用于**创建一个 oAuth 弹出窗口**，它不会被 web 浏览器阻止(除非使用弹出窗口阻止程序)，并且可以通过回调进行监控，以通过 oAuth 进行身份验证，就像大多数流行的社交网络所允许的那样。

### 演示

[jQuery Twitter Widget](http://jquery4u.com/twitter/)

![oauth-login](img/0f264e4f864c2e9295dea48c191db6b7.png "oauth-login")


### 密码

```
//Authorization popup window code
$.oauthpopup = function(options)
{
    options.windowName = options.windowName ||  'ConnectWithOAuth'; // should not include space for IE
    options.windowOptions = options.windowOptions || 'location=0,status=0,width=800,height=400';
    options.callback = options.callback || function(){ window.location.reload(); };
    var that = this;
    log(options.path);
    that._oauthWindow = window.open(options.path, options.windowName, options.windowOptions);
    that._oauthInterval = window.setInterval(function(){
        if (that._oauthWindow.closed) {
            window.clearInterval(that._oauthInterval);
            options.callback();
        }
    }, 1000);
};
```

### 使用

```
//create new oAuth popup window and monitor it
$.oauthpopup({
    path: urltoopen,
    callback: function()
    {
        log('callback');
        //do callback stuff
    }
});
```

## 分享这篇文章