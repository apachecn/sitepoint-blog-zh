# 停止残害身体:像素字体大小的危险

> 原文：<https://www.sitepoint.com/stop-maiming-bodies-the-perils-of-pixel-font-size/>

![An eye test for pixel size](img/e860c056e1d9e0d4560940b322ac825d.png)

SitePoint/ [Natalia Balska 的作品](http://sf-lab.net/)

想象你有视力障碍或阅读障碍。浏览器带有内置的字体大小设置，因此您可以增加默认字体大小并开始浏览。奇怪的是，大多数网站上的文字看起来仍然很小，所以你回到浏览器设置，把字体增大到很大。你回到你原来的网站，什么都没有。该设置不起作用。

![Font size setting in Google Chrome](img/695981899d41c338c9b0a9a0a5cb3d23.png)

在过去的几年里，我们已经学会了如何使用并逐渐接受印刷内容的相关单位。像`font-size`这样的属性不用像素，大多数值是`rem`或`em` s

许多开发者仍然在`html`或`body`标签上设置一个‘初始’`font-size`，经常使用`px`作为单位。如果你不完全理解(并欣赏)相对单位，这对你可能很方便，因为所有的`em`和`rem`现在都是相对于初始值的。

```
/* A body containing a font-size with absolute value */
body { font-size: 14px; }

/* The h1 will be 2em relative to 14px */
h1 { font-size: 2em; } 
```

不幸的是，这个初始值对于一些用户来说并不方便。你看，我前面提到的浏览器设置只设置了基本字体大小。每个绝对单位(`px`、`pt`、`inch`等)。)使用，覆盖它。

```
/* Fictional browser setting */
html { font-size: 18px; }
/* The absolute unit in the body overwrites the browser setting */
body { font-size: 14px; } 
```

因此，改变浏览器字体大小设置的用户将会*仍然*看到`14px`中的文本，即使他们明确地将字体大小设置得更大。

许多网站，甚至是知名度很高的网站，都犯了这种罪:

*   [Google.com](https://google.com)
*   [Twitter.com](https://twitter.com/)
*   [Facebook.com](https://www.facebook.com/)
*   [Github.com](https://github.com/)
*   [Codepen.io](http://codepen.io/)

## 缩放和字体大小的区别

我得到的一个论点是:“缩放有效，那不是一回事吗？”

几乎所有的浏览器都有放大功能，可以放大整个页面。从技术上讲，它放大了每一个单位，除了百分比。这对视力受损的人来说非常好，比如视力(清晰度)有问题的用户。使用眼镜可以矫正大部分视力，但在太多的情况下，它不能。

但是，更改浏览器的字体大小只会更改基本字体大小。有些人视力很好，但是有阅读障碍，比如诵读困难。事实上，3-7%的人口患有阅读障碍，但高达 20%的人会经历其症状。增加字体大小可以降低症状的严重性。当他们只能改变字体大小时，为什么要放大页面呢？

## 正确的方式

绝对单位在这里是坏人。如果我们使用相对单位，比如`%`和`em`，浏览器的设置会受到尊重。默认情况下，`1em`约为`16px`。所以，如果你*真的*想使用非默认字体大小，你可以把`font-size`设置为`.875em`或者`87.5%`为`14px`。

```
/*
These are equivalent:
- font-size: 100%;
- font-size: 1em;
- absent font-size property
*/
body { font-size: 1em; }

/* If the users changes the browser setting, both the body text and h1 text will scale accordingly */
h1 { font-size: 2em; } 
```

做得好的网站:

*   [Sitepoint.com](https://www.sitepoint.com/)
*   [Smashingmagazine.com](https://www.smashingmagazine.com/)

## 结论

印刷属性的绝对单位是行业中的常见做法。可能是因为它是显式的，而相对单元需要一些挖掘来找出为什么深度嵌套的元素可能与主体中的元素有不同的计算`font-size`。

问题是这是一个主要的可访问性缺陷。为了让视觉障碍者或阅读障碍者的网站变得更好，我们必须使用相对单位。

## 分享这篇文章