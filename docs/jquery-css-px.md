# jQuery css 获取不带 px 的值

> 原文：<https://www.sitepoint.com/jquery-css-px/>

有时当你使用 jQuery 获取 CSS 值时，它们会有 px 这样的扩展名。这就是你在没有 px 的情况下获取 CSS 值的方式。如果你想得到一个 dom 元素的 css 属性值，比如 margin，那么它将返回 px 值。

例如:

```
$('#content').css('left');
```

会回说:“360px”。

要从 css 属性中获取数值，只需使用以下代码:

```
$('#content').css('left').replace(/[^-d.]/g, '');
```

## 分享这篇文章