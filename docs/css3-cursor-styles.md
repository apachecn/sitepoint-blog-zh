# CSS3 中新的 CSS 光标样式介绍

> 原文：<https://www.sitepoint.com/css3-cursor-styles/>

随着新技术和技巧的发现，web 开发的早期令人激动。在过去十年的中期，我们经历了几年的停滞不前，但是，由于 HTML5，web 开发再次变得令人兴奋。特别是，CSS3 发展迅速，您会在规范中发现一些有趣的精华。

在本文中，我们将研究 CSS cursor 属性，如您所料，该属性允许您在鼠标移动到某个元素上时改变光标的样式。对于交互式网络应用来说，它变得越来越重要…

## CSS2 光标样式

CSS2 提供了相对较少的选项*(悬停在任何元素上以查看光标如何变化)*:

```
cursor: auto
```

```
cursor: inherit
```

```
cursor: crosshair
```

```
cursor: default
```

```
cursor: help
```

```
cursor: move
```

```
cursor: pointer 
```

```
cursor: progress
```

```
cursor: text
```

```
cursor: wait
```

```
cursor: e-resize
```

```
cursor: ne-resize
```

```
cursor: nw-resize
```

```
cursor: n-resize
```

```
cursor: se-resize
```

```
cursor: sw-resize
```

```
cursor: s-resize
```

```
cursor: w-resize
```

## CSS3 光标样式

我们在 CSS3 中有更多的风格可供选择。除非另有说明，否则这些功能可以在 IE9 和最新版本的 Firefox、Chrome、Safari 和 Opera 中使用:

```
cursor: none (not IE, Safari, Opera)
```

```
cursor: context-menu (not Firefox, Chrome)
```

```
cursor: cell (not Safari)
```

```
cursor: vertical-text
```

```
cursor: alias (not Safari)
```

```
cursor: copy (not Safari)
```

```
cursor: no-drop
```

```
cursor: not-allowed
```

```
cursor: ew-resize
```

```
cursor: ns-resize
```

```
cursor: nesw-resize
```

```
cursor: nwse-resize
```

```
cursor: col-resize
```

```
cursor: row-resize
```

```
cursor: all-scroll
```

## 特定于浏览器的光标

Mozilla 以及 Chrome 和 Safari 的一些版本提供了许多厂商前缀的光标样式，这些样式很可能成为 CSS3 规范的一部分:

```
cursor: -webkit-grab; cursor: -moz-grab;
```

```
cursor: -webkit-grabbing; cursor: -moz-grabbing;
```

```
cursor: -webkit-zoom-in; cursor: -moz-zoom-in;
```

```
cursor: -webkit-zoom-out; cursor: -moz-zoom-out;
```

## 创建您自己的光标

最后，您可以创建自己的光标图形，例如

```
cursor: url(images/cursor.cur);
cursor: url(images/cursor.png) x y, auto;
```

注意:

1.  Internet Explorer 需要 Windows 光标文件(。cur)。
2.  Firefox、Chrome 和 Safari 需要图片——我推荐 24 位透明的 PNG。
3.  Firefox 还需要第二个非 URL 光标回退值。
4.  Opera 中不支持。
5.  x 和 y 是 Firefox、Chrome 和 Safari 中的可选属性，它们定义了从图形左上角开始的精确指针位置。如果省略，则假定为 0 0。

不错，但是对我来说听起来太费力了！我将坚持使用标准的光标样式…

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对这篇文章的评论已经关闭。对 CSS3 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章