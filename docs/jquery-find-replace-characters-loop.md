# jQuery 查找和替换字符循环

> 原文：<https://www.sitepoint.com/jquery-find-replace-characters-loop/>

使用网页中每个 html 元素的循环来查找和替换字符的 jQuery 代码片段。根据需要更改替换函数中的值。

```
jQuery('html').each(function(i){ jQuery(this).text(jQuery(this).text().replace('replacetext','withthistext'))})
```

## 分享这篇文章