# jQuery 检测并隐藏损坏的图像

> 原文：<https://www.sitepoint.com/jquery-detect-hide-broken-images/>

jQuery 代码片段要么为损坏的图像显示默认图像，要么检测并隐藏所有损坏的图像。把那些红叉都藏起来！；)

```
// Replace source
$('img').error(function(){
        $(this).attr('src', 'missing.png');
});

// Or, hide them
$("img").error(function(){
        $(this).hide();
});
```

## 分享这篇文章