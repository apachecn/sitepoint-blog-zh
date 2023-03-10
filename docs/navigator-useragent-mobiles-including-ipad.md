# navigator.useragent 手机，包括 ipad

> 原文：<https://www.sitepoint.com/navigator-useragent-mobiles-including-ipad/>

使用 navigator.useragent 检测包括 ipad 在内的手机的代码片段

```
function detectmob() {
     return !!navigator.userAgent.match(/iPad|iPhone|Android|BlackBerry|Windows Phone|webOS/i));
}
```

Kindle Fires 和 PlayBooks 在设计上不会被检测到。要添加对平板电脑的支持，请添加|playbook|silk

### 其他方式

```
var isMobile = {
    Android: function() {
        return navigator.userAgent.match(/Android/i);
    },
    BlackBerry: function() {
        return navigator.userAgent.match(/BlackBerry/i);
    },
    iOS: function() {
        return navigator.userAgent.match(/iPhone|iPad|iPod/i);
    },
    Opera: function() {
        return navigator.userAgent.match(/Opera Mini/i);
    },
    Windows: function() {
        return navigator.userAgent.match(/IEMobile/i);
    },
    any: function() {
        return (isMobile.Android() || isMobile.BlackBerry() || isMobile.iOS() || isMobile.Opera() || isMobile.Windows());
    }};
}
```

### 如何使用

```
if( isMobile.any() ) alert('Mobile');
```

要查看用户是否在特定的移动设备上:

```
if( isMobile.iOS() ) alert('iOS');
```

来源:
[http://stack overflow . com/questions/11381673/JavaScript-solution-to-detect-mobile-browser](http://stackoverflow.com/questions/11381673/javascript-solution-to-detect-mobile-browser)
[http://detectmobilebrowsers.com/](http://detectmobilebrowsers.com/)

## 分享这篇文章