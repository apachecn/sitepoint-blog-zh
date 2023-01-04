# jQuery 获取当前页面标题

> 原文：<https://www.sitepoint.com/jquery-current-page-title/>

jQuery 代码片段获取当前网页的完整标题，并将其存储在一个变量中，供其他脚本使用。这是您在浏览器标题上看到的标题。

```
 当前页面标题: mytitle 。 
```

## 

安迪建议的另一种方法。

```
var current_href = $(location).attr('href');
var current_title = $(document).attr('title');
```

## 分享这篇文章