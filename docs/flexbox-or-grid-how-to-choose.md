# Flexbox 还是 CSS 网格？如何做出有意义的布局决策

> 原文：<https://www.sitepoint.com/flexbox-or-grid-how-to-choose/>

随着 CSS 网格布局的广泛实施，一个问题在前端 web 开发界变得突出:Flexbox 还有用吗？

现在所有主流浏览器都像 Flexbox 一样支持 Grid。但问题的答案是，是的，Flexbox 仍然是 CSS 的重要组成部分。

在本文中，我将提出一些方法来帮助您在处理 web 开发项目时决定何时使用 Flexbox，何时使用 Grid。

![Flexbox or CSS Grid? How to Make Layout Decisions that Make Sense](img/0922b51c5bc57772042d6f915b57461b.png)

*要掌握 Flexbox、Grid 和许多其他高级 CSS3 特性，请查看 **[CSS Master，第三版](https://www.sitepoint.com/premium/books/css-master-3rd-edition)** 。*

## 什么是 Flexbox？

CSS 灵活框布局，简称 Flexbox，在 2012 年左右开始出现在浏览器中。多年来，web 设计人员和开发人员对浮动和第三方布局库情有独钟，他们热情地欢迎这一新的 CSS 模块，尽管需要一段时间来适应其陌生的语法并完全掌握其内部工作方式。

可以在 CSS 文档中定义一个 flex 容器，比如:`.container { display: flex; }`。然后，正如 W3C 规范所说，它的孩子—

> 可以向任何方向布局，并且可以“伸缩”它们的大小，或者增长以填充未使用的空间，或者收缩以避免溢出父对象。可以容易地操纵孩子的水平和垂直对齐。这些盒子的嵌套(水平在垂直里面，或者垂直在水平里面)可以用来建立二维布局。

自从 Flexbox 的引入，像创建等长的列而不管它们的内容量，水平和垂直居中元素，沿着水平线排列和间隔导航链接等等，变得更加容易。

在 flex 容器中包装列足以实现等长列。看看吧！

参见 [CodePen](https://codepen.io) 上的笔 [Flexbox-Equal-Columns](https://codepen.io/SitePoint/pen/WNONXvN)by site point([@ site point](https://codepen.io/SitePoint))
。