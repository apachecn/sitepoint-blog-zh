# 用于加载图像的 ajaxSetup

> 原文：<https://www.sitepoint.com/ajaxsetup-loading-image/>

使用 ajaxSetup()设置加载图像的简单 jQuery 代码片段，以便在每次发送 AJAX 请求时显示加载图像，当它返回时隐藏加载图像。为 complete 和 success 包含相同代码的原因是。load()函数似乎忽略(或覆盖)了完整的函数。


## 演示

![ajaxsetup-demo](img/5ae690524be901505ceb7ad560cfd302.png "ajaxsetup-demo")

## 代码

```
$.ajaxSetup({
 beforeSend: function() {
    $('#general-ajax-load ').fadeIn();
 },
 complete: function() {
    $('#general-ajax-load ').fadeOut();
 }
 success: function() {
    $('#general-ajax-load ').fadeOut();
 }
});
```

## 分享这篇文章