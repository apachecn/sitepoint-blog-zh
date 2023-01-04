# AtoZ CSS 快速提示:如何使用 Unicode 字符

> 原文：<https://www.sitepoint.com/atoz-css-quick-tip-unicode/>

本文是我们 AtoZ CSS 系列的一部分。你可以在这里找到[系列的其他条目。
你可以在这里](https://www.sitepoint.com/blog/)查看`unicode-range`和`@font-face`T5 的完整脚本和截屏。

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),每个值都以字母表中不同的字母开始。我们知道有时仅仅截屏是不够的，所以在本文中，我们添加了使用 unicode 字符的快速提示。

![U1_b-01](img/b73e943a57b0b584d1688dce1976a75c.png)

## u 代表 Unicode 字符

Unicode 字符对于通过伪元素的 content 属性在 HTML 或 CSS 中添加小符号和图标非常方便。这里有一些使用 unicode 字符的技巧。

### 在你的 CSS 中设置字符集

如果你想在你的 CSS 文件中使用 unicode 字符，除了在你的 HTML 文档中设置`charset` meta 标签之外，确保你为你的 CSS 设置了字符集。

```
<!-- in your HTML <head> --> 
<meta charset= "utf-8"/>
```

```
/* in your CSS at the top of the file*/
@charset 'utf-8';
```

### 使用代表天堂的三元图形作为一个简单的汉堡图标

当我需要快速创建一个演示网站或者一个视频截屏来制作一个有针对性的教程时，我经常使用“爱它或恨它”的汉堡图标作为移动菜单。有很多图标解决方案，也有很多非光栅的方法来制作汉堡图标，但对我来说，最快捷、最简单的方法是使用 unicode 字符“天堂三字图”，如下所示:`☰`。

它看起来就像一个汉堡图标。以下是我经常将它添加到我的导航中的方式:

```
<nav class="site-nav"> 
  <ul class="site-nav-menu"> 
    <li><a href= "#">lorem ipsum</a></li> 
    <li><a href= "#">lorem ipsum</a></li> 
    <li><a href= "#">lorem ipsum</a></li> 
    <li><a href= "#">lorem ipsum</a></li> 
    <li><a href= "#">lorem ipsum</a></li> 
  </ul> 
  <a href="#" class="site-nav-icon">☰</a> 
</nav>
```

### 搜索并快速复制和粘贴 unicode 字符

我在 copypastecharacter.com 搜索可用的 unicode 字符库时遇到了这个汉堡字符。

它们有一大堆的箭头、图形、数学符号、标点符号和一些古怪而奇妙的一次性字符供你搜索，并可以轻松地复制粘贴到你的代码中。非常方便的东西。

## 分享这篇文章