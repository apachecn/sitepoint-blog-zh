# jQuery 向上滑动元素/向上切换

> 原文：<https://www.sitepoint.com/jquery-slide-element/>

使用 jquery.slideToggle()向上滑动或向上切换页面元素的简单 jQuery 代码片段。不同之处在于，jQuey 的 slideUp()函数主要用于将元素滑出视图，然后使用 slideDown()使它们重新出现，而 jQuery 的 toggleUp()函数在绑定到元素的同一个函数调用中提供了这两种功能。

## 幻灯片示例

```
$('#clickme').click(function() {
  $('#book').slideUp('slow', function() {
    // Animation complete.
  });
});
```

[http://api.jquery.com/slideUp/](http://api.jquery.com/slideUp/)

## SlideToggle 示例

```
$('#clickme').click(function() {
  $('#book').slideToggle('slow', function() {
    // Animation complete.
  });
});
```

[http://api.jquery.com/slideToggle/](http://api.jquery.com/slideToggle/)

## [http://www . learning jquery . com/2009/02/slide-elements-in-different-directions](http://www.learningjquery.com/2009/02/slide-elements-in-different-directions)

## 分享这篇文章