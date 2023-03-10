# 混合字体时的注意事项

> 原文：<https://www.sitepoint.com/considerations-when-mixing-typefaces/>

排版和设计中的一种常见技术是混合字体。这需要做得优雅一点，以确保第二(或第三)种字体的添加不会与作品的主要面貌以及整个设计相冲突。

也许最常见的是衬线和 sanserif 的配对。一个用于设置标题，另一个用于设置正文；反之亦然。或者，一个可以设置内容，而另一个用于用户界面控制。

通常，首先要考虑的是字形之间的尺寸差异。为了描述这个问题是如何产生的，注意一下类型设计如何区分“字符”和“字形”是很有用的。

一个字符是语义表示，而一个字形是该字符的一个特定的风格图。所以，字母“a”是一个字符；你的电脑把 Lucida Grande roman 字体中的字母“a”变成了一个字形。Lucida Grande 有许多字母“a”的字形:斜体、罗马粗体、粗斜体等等。在每种情况下,“字体”样式对于字母具有不同绘图；每个都有不同的字形。

这导致了在混合多种字体时可能出现的问题:字体——即使在相同的设定点大小下——在许多方面都有光学差异。这意味着 Helvetica 96 磅的字体和泰晤士报同样大小的新罗马字体会有很大的不同。

## 对齐 x 高度

为了确保不冲突，*尤其是*在同一流动文本中设置两个面时，首先要对齐 x 高度:

[![Slide 88 from Standards friendly web type: x-heights](img/f6dcd11ebdbdc932ea953222b30c80f1.png)](https://www.sitepoint.com/wp-content/uploads/2011/04/setting-standards-friendly-web-type.088.png)

*x-height* 是从基线(不可见的行字母所在的位置)到没有*上升字母*的小写字母(例如 a、c、e、g、m、n、o、p、q、r、s、u、v、w、x、y、z，其中 g、p、q 和 y 有*下降字母*)的共同高度。这条标记 x 高度的看不见的线被称为*中间线*。

我们这样做是通过简单地把我们的第一个字体放在一个给定的大小和风格，然后增大或减小第二个字体的大小，直到 x 高度对齐。

## 文体冲突

对齐 x 高度只是第一步，如果我觉得字体在风格上有冲突，我通常不会费心去做。这里我们需要考虑一系列因素:

*   *笔画调制*(字形的不同粗细，在衬线斜体上尤其明显)。
*   轴的角度和面部的固有斜度——可能一切都很好，但是一个斜体向一个方向倾斜，而另一个向另一个方向倾斜。
*   衬线样式(细线或粗线，末端样式:泪珠形、鸟嘴形、椭圆形)，或缺少衬线样式。
*   *光圈*，*计数器的开口*(见下文)。

[![Slide 89 from Standards friendly web type: check for stylistic variation](img/660842b9f521f390e1051d6d2d93bee9.png)](https://www.sitepoint.com/wp-content/uploads/2011/04/setting-standards-friendly-web-type.089.png)

完成后，检查*负空格*——字母之间的空格:

[![Slide 90 from Standards friendly web type: negative space](img/a97faa62e602908ac7b159603199e772.png)](https://www.sitepoint.com/wp-content/uploads/2011/04/setting-standards-friendly-web-type.090.png)

[![Slide 91 from Standards friendly web type: negative space (2)](img/584ee1f6be837533a56e60a69c0786d3.png)](https://www.sitepoint.com/wp-content/uploads/2011/04/setting-standards-friendly-web-type.091.png)

对齐 x 高度并配对后，可能会有其他更直接可见的影响，例如重量:

[![Slide 92 from Standards friendly web type: differences in weight](img/f88e7c8cedcc06e9dfdd1c9d5414da48.png)](https://www.sitepoint.com/wp-content/uploads/2011/04/setting-standards-friendly-web-type.092.png)

再来看看几个:

[![Slide 93 from Standards friendly web type: differences in style](img/c5741ba193cebb44f88faeecf2bf2029.png)](https://www.sitepoint.com/wp-content/uploads/2011/04/setting-standards-friendly-web-type.093.png)

[![Slide 94 from Standards friendly web type: differences in style (2)](img/d95e15965ded10f06f6749c81b5c5623.png)](https://www.sitepoint.com/wp-content/uploads/2011/04/setting-standards-friendly-web-type.094.png)

注意到 Helvetica 和凡尔登之间的风格差异了吗？这主要是因为 Helvetica 是一张*现实主义*或*新怪诞*的脸，而弗达娜是一张*人文主义*的脸。(这些是字体设计者和印刷工人使用的风格分类。)

现在想象一下，把一张漂亮的人文主义衬线脸和一张现实主义或者更糟的几何图形 sanserif 配对？即使 x 高度对齐(1)，它们本身也会彼此不一致:

[![Slide 95 from Standards friendly web type: constrast serif and sanserif combinations](img/dcc30d210eae852ff787fdad9915d434.png)](https://www.sitepoint.com/wp-content/uploads/2011/04/setting-standards-friendly-web-type.095.png)

相反，为你的第一张脸选择一个合适的搭档，在这种情况下，古典的人文主义者 Palatino 和高度清晰易读的屏幕优化 Verdana (2)。

对于基于屏幕的排版项目来说，这两者将是一个理想的人文主义组合，因为两者都相当容易获得，并且在屏幕上光栅化时都非常易读。

你有最喜欢的组合和搭配吗？

## 分享这篇文章