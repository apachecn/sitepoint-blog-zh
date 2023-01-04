# jQuery 检查是否启用了 Flash

> 原文：<https://www.sitepoint.com/jquery-check-flash-enabled/>

检查您的浏览器是否启用了 flash 的简单 jQuery 代码片段。我还找到了一篇关于如何[打开和关闭 flash 进行浏览器测试](http://blog.lroot.com/articles/techniques-to-easily-disable-flash-in-firefox-and-ie/)的有用文章。


**相关帖子:**

*   [**有史以来最令人印象深刻的 5 个 Flash 式导航菜单**](http://www.jquery4u.com/menus/5-impressive-flash-like-navigation-menus-time/)

```
//checks if flash is installed/enabled on the browser
function isFlashEnabled()
{
    var hasFlash = false;
    try
    {
        var fo = new ActiveXObject('ShockwaveFlash.ShockwaveFlash');
        if(fo) hasFlash = true;
    }
    catch(e)
    {
        if(navigator.mimeTypes ["application/x-shockwave-flash"] != undefined) hasFlash = true;
    }
    return hasFlash;
}
```

## 分享这篇文章