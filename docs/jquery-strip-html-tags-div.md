# jQuery 从一个 Div 中剥离所有 HTML 标签

> 原文：<https://www.sitepoint.com/jquery-strip-html-tags-div/>

简单的 jQuery 代码片段，使用 jQuery replace()函数从一个 div 中去除所有 html 标签(即只保留 html 标签中的文本)。另见[删除空白](http://www.jquery4u.com/snippets/jquery-strip-whitespace)。


```
var item_html = $(this).html();
item_html = item_html.replace(/]+>/gi, '');
```

## 可能更容易使用这个功能

```
jQuery.fn.stripTags = function () {
    return this.replaceWith(this.html().replace(/]+>/gi, ''));
};
```

**jquery 删除所有 html 标签，除了:**

```
rawHTML = textContainer.html().replace(//igm,'')
```

## 分享这篇文章