# jquery 同时动画显示多个 dom 元素

> 原文：<https://www.sitepoint.com/jquery-animate-mutiple-dom-elements-time/>

快速浏览一下在 jQuery 中使用多个选择器来同时激活多个 dom 元素。记住动画函数是异步的，事实上它可以同时在多个元素上执行。

[http://api.jquery.com/multiple-selector/](http://api.jquery.com/multiple-selector/)

多重选择器("选择器 1、选择器 2、选择器 3 ")

```
//does not work
$('#content', '#sidebar-grab').animate(

//works
$('#content,#sidebar-grab').animate(
```

## 分享这篇文章