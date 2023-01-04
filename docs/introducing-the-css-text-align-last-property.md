# CSS 文本对齐最后属性介绍

> 原文：<https://www.sitepoint.com/introducing-the-css-text-align-last-property/>

有时，通常不被注意的微小细节可能会影响或破坏用户体验。这可以包括应用于标题的细微文本阴影，或者网页上不同元素之间的适当间距。

在这方面，`text-align-last`属性很有用。它指定如何对齐块的最后一行或强制换行符之前的一行。这很重要，因为段落的最后一行通常没有足够的文本来填满整个空间。你可能会也可能不会注意到这些变化，但它会改善你的网站的整体外观。

本教程将涵盖`text-align-last`属性的所有方面，包括可接受的值、支持和特定于浏览器的行为。

## 用法和可能值

在你的项目中使用`text-align-last`非常简单。以下是将最后一行文本右对齐的基本代码片段:

```
.intro-graph {
  text-align: justify; // Required for IE and Edge 
  text-align-last: right;
} 
```

该属性有七个可能的值。你可能熟悉通常的`left`、`right`和`center`值。它们将最后一行中的文本与容器的左侧、右侧和中央对齐。
下面的演示展示了这三个值之间的差异:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔 [text-align-last —例 1](http://codepen.io/SitePoint/pen/KMNEBO/) 。