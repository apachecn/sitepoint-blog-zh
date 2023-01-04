# CSS“位置:粘性”-简介和多填充

> 原文：<https://www.sitepoint.com/css-position-sticky-introduction-polyfills/>

如果你读过 Annarita Tranfici 的文章[明显的设计总是赢](https://www.sitepoint.com/obvious-design-always-wins/)，你可能会同意她的说法:

> 人们希望看到常见的模式:在页面顶部找到主菜单，在右上角找到搜索框，在底部找到页脚，等等。

我同意人们期望网站的某些部分被放在一个特定的位置，在我看来，对于主菜单来说更是如此。

有时，由于客户的要求，或者因为我们认为这是最好的方法，我们可能要求主导航在页面上始终可见，而不是固定在某个位置，本质上是跟随页面内容。近年来，许多基于 JavaScript 的解决方案已经问世，因为单靠 CSS 无法完成这项任务。

在本文中，我们将讨论`position: sticky`，这个问题的新 CSS 解决方案。

## 我们在解决什么问题？

在讨论`position`属性的这个新值之前，让我们更好地理解我们试图解决的问题是什么。

让我们假设这个令人惊叹的网站的主菜单就在标题之后，但仍然在页面的顶部(而不是在侧边栏中)，并且它占据了所有可用的宽度。这可能看起来像这样:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的无粘性菜单的笔[示例。](http://codepen.io/SitePoint/pen/LBHvI/)