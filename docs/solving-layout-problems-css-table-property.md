# 布局秘密武器# 1:CSS 表格属性

> 原文：<https://www.sitepoint.com/solving-layout-problems-css-table-property/>

[![Magnetic cube table](img/4f350860e5b500fb44c5658705180311.png)](https://www.home-designing.com/2010/10/10-strange-table-designs)

现在， [Flexbox](https://www.w3.org/TR/css3-flexbox/) 可以说是布局构建的热门新事物。Flexbox 几乎奇迹般的适应可用空间的能力让我们许多人睁大了眼睛看着可能性。

然而，它不能解决所有的布局问题，而且，它还带来了一些与传统浏览器的兼容性问题。对于 Flexbox 来说，根本没有防弹的“polyfill”(旧浏览器的后备)——目前我只知道 2009 版 IE 有一个 poly fill:[Flexie](https://flexiejs.com/)。

在许多情况下，我发现了更简单的解决方案，使用经常被忽略的 **CSS 表显示属性**。这些 CSS 属性[被所有相关浏览器](https://caniuse.com/#feat=css-table)广泛支持(注意这不包括 IE6 & 7)，并且可以优雅地解决几个大大小小的布局难题。

如果您不是 100%熟悉这项技术，更改 DIVs `display`属性可以使它*的行为*像一个表格或表格元素。

### 等等什么？用于布局的表格？这不是很糟糕吗？

也许 21 世纪初最热门的网页设计话题是关于使用 HTML 表格代码作为布局工具的争论。那是一种黑客行为，至今仍是一种糟糕的做法。

相反，我们在这里使用完全有意义的 HTML(即 div、SECTIONs、HEADERs 等)并简单地从 CSS 中借用一些有用的表格表示技术。这正是 CSS 的设计目的，所以不要觉得这是一个黑客或补丁。不是的。

### 使用“显示:表格单元格”

在下面的示例中，单击顶部的按钮可以将三种颜色的 div 的显示属性从`block`更改为`table-cell`:

参见 [CodePen](https://codepen.io) 上马西莫·卡珊德拉([@马西莫-卡珊德拉](https://codepen.io/massimo-cassandro))的钢笔[展示表#1](https://codepen.io/massimo-cassandro/pen/qEYZbw/) 。