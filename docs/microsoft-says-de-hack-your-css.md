# 微软说:破解你的 CSS

> 原文：<https://www.sitepoint.com/microsoft-says-de-hack-your-css/>

ie blog[呼吁网页设计者](http://blogs.msdn.com/ie/archive/2005/10/12/480242.aspx)去除一些通常只在 ie 浏览器中应用 CSS 格式的技巧。以下是最常见的问题:

*   `html > body` [详情](http://css-discuss.incutio.com/?page=ChildHack)
*   `* html` [详情](http://css-discuss.incutio.com/?page=StarHtmlHack)
*   `head:first-child + body` [详情](http://centricle.com/ref/css/filters/tests/owen/)
*   `head + body` [详情](http://www.dithered.com/css_filters/css_only/head_adjacent_sibling_body.html)
*   `body > element` [详情](http://css-discuss.incutio.com/?page=ChildHack)

这些攻击所基于的许多 CSS 解析怪癖已经在 IE7 中得到修复，这意味着它们应用的特定于 IE 的格式将不会在 IE7 中应用。

现在，如果你的黑客设计的 IE 渲染怪癖也在 IE7 中被修复了，那就好了。但是如果没有，那么你的网站设计将会在新的浏览器中崩溃。

微软希望开发者能够使用更容易预测的 IE 浏览器 5 或更高版本的条件注释功能，将 IE 专用的 CSS 放在一个单独的样式表中，该样式表只能被该浏览器使用:

```
 <!--[if IE]><link rel="stylesheet" href="ieonly.css" type="text/css" /><![endif]--> 
```

但是正如我所说的，由于 IE7 中即将出现的许多渲染修复，你将只希望某些特定于 IE 的 CSS 应用于 IE7。因此，你需要进一步划分你的 IE 风格。使用更复杂的条件注释，您可以创建适用于 IE7 或更早版本、IE6 或更早版本、IE5.5 或更早版本等的样式表。

```
 <!--[if lte IE 7]><link rel="stylesheet" href="ie7.css" type="text/css" /><![endif]-->
<!--[if lte IE 6]><link rel="stylesheet" href="ie6.css" type="text/css" /><![endif]-->
<!--[if lte IE 5.5]><link rel="stylesheet" href="ie55.css" type="text/css" /><![endif]--> 
```

当然，在微软发布 Internet Explorer 的公开测试版之前，我们不会知道要保留哪些修复(在 ie7.css 中)，以及哪些只提供给旧浏览器(在 ie6.css 中)。

## 分享这篇文章