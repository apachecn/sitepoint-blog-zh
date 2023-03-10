# jquery mobile“加载页面时出错”解决方法

> 原文：<https://www.sitepoint.com/jquery-mobile-error-loading-page-workaround/>

如果你看到一个快速的错误信息弹出在屏幕上时，重定向到另一个页面(或者你可能会强制重新加载)。

![Image](img/451a288d57ec07a6b95985f2399d54ce.png)

不使用这个:

```
window.location.href = "/";
```

用这个:

```
$.mobile.changePage('/', { reloadPage: true, transition: "none"} );
```

或者如果那不起作用，试试这个:

```
$.mobile.changePage('./', { reloadPage: true });
```

这应该能解决问题。如果它不这样做，那么尝试覆盖 ajax 加载错误的默认设置(不推荐，但将修复它)。

```
$.mobile.pageLoadErrorMessage = "";
```

docs:[http://jquery mobile . com/demos/1 . 2 . 0/docs/API/global config . html](http://jquerymobile.com/demos/1.2.0/docs/api/globalconfig.html)

```
$(document).bind("mobileinit", function(){
  $.mobile.foo = bar;
});
```

如果这仍然不能解决问题，也许你正在使用文件协议？

ref:http://does what . com/2011/03/08/jquery-mobile-error-loading-page/

## 分享这篇文章