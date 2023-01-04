# AtoZ CSS 快速提示:何时使用 ID 选择器代码

> 原文：<https://www.sitepoint.com/atoz-css-quick-tip-id-selector/>

购买 SitePoint Premium 会员资格可以让你访问完整的 AtoZ: CSS 系列。

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),每个值都以字母表中不同的字母开始。在本文中，我为您添加了一个关于`id`选择器的新的快速提示/课程。

![I2-01](img/f46298c44cd7af9bec688fd1d890d284.png)

## I 代表`id`选择器

在[字母 I](https://www.sitepoint.com/atoz-css-screencast-id-selector) 的视频中，我们看了一下`id`选择器以及它如何影响 CSS。我也对使用`id`而不是`class`的问题大加指责，无论是在可重用性还是特殊性方面。

为了平衡这个论点，我应该谈谈什么时候我们应该使用 ID 以及为什么。

### 为 Javascript 提供一个独特的挂钩

这有点超出了 CSS 的正常范围(尽管如果你相信[关于我们是否还需要 CSS 的持续争论](https://css-tricks.com/the-debate-around-do-we-even-need-css-anymore/)，这仍然是一条有用的建议。用 JavaScript 与元素交互的最快和最直接的方式是通过它的`id`属性来定位它。

### 为导航提供目标锚定标签

通过将锚标签中的`href`设置为`id`，可以在单个页面上使用 ID 进行导航。看看这个例子，它可以让你轻松地导航到页面上的不同章节。

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[或](http://codepen.io/SitePoint/pen/ORyGLa/)。

### 用于将表单输入与其标签相链接

在构建表单时，我们可以通过`label`上的`for`属性将特定`input`的`label`与输入的相应`id`属性链接起来。这意味着单击标签将聚焦文本输入字段或切换复选框或单选按钮的状态。

最后，这里是关于使用 ID 的最后一个提示:有效的`id`不能以数字开头，必须至少有一个字符长。