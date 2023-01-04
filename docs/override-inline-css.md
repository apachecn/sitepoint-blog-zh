# 如何重写内联 CSS 样式

> 原文：<https://www.sitepoint.com/override-inline-css/>

这篇文章写于 2009 年，至今仍是我们最受欢迎的帖子之一。如果你热衷于学习更多关于 CSS 的知识，你可能会对这篇[最近关于视网膜显示器](https://www.sitepoint.com/css-techniques-for-retina-displays/)CSS 技术的文章很感兴趣。

CSS 最强大的特性之一是层叠。了解浏览器如何选择和应用你的风格是无价的知识。新手可能会感到特别困惑，因为样式的使用会受到包含 CSS 的方法、规则的顺序、如何指定选择器以及特殊声明(如`!important`)的影响。

内联样式是在 HTML 本身中定义的样式，例如

```
 <p>This is an 
<strong style="color: red;">inline style that should be blue</strong>
.</p> 
```

内联样式在所有 CSS 中优先级最高。一般来说，我建议您避免使用它们，将 CSS 声明放在外部文件中。然而，如果您正在一个遗留系统上工作，或者不能直接访问 HTML 代码，您可能就没有这种奢侈了。

幸运的是，**有一种从外部样式表覆盖内联样式的方法:**

```
 strong[style] { color: blue !important; } 
```

在上面的例子中，这将强制`strong`标签中的文本变成蓝色。该方法似乎可以在所有主流浏览器中工作，包括:

*   Internet Explorer 8.0
*   Mozilla Firefox 2 和 3
*   歌剧 9
*   苹果 Safari，以及
*   谷歌浏览器

Internet Explorer 6 和 7 是明显的例外。然而，这种技术不是你每天都应该使用的。保持 CSS 的整洁，只有在别无选择的时候才重写内联样式。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如[实用 CSS](https://learnable.com/courses/practical-css-198?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。对 CSS 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章