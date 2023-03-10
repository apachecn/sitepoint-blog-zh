# 如何用 CSS 创建便于打印的页面

> 原文：<https://www.sitepoint.com/css-printer-friendly-pages/>

*本文于 2020 年更新，以反映 CSS 打印样式的最新最佳实践。*

在这篇文章中，我们回顾了用 CSS 创建易于打印的网页的艺术。

“谁打印网页？”我听到你哭了！相对来说，只有很少的页面会被复制到纸上。但是考虑一下:

*   打印旅行或音乐会门票
*   复制路线指示或时间表
*   保存副本以供脱机阅读
*   在连通性差的地区访问信息
*   在危险或肮脏的环境中使用数据，例如厨房或工厂
*   输出书面注释的草稿内容
*   出于记账目的打印 web 收据
*   为难以使用屏幕的残障人士提供文档
*   为你的同事打印一页，他拒绝使用这种新奇的互联网废话。

不幸的是，打印页面可能是一种令人沮丧的体验:

*   文本可能太小、太大或太模糊
*   列可能太窄、太宽或溢出页边距
*   部分可能被裁剪或完全消失
*   墨水浪费在不必要的彩色背景和图像上
*   看不到链接网址
*   图标、菜单和广告被打印出来，却永远不能被点击！

许多开发人员提倡网页可访问性，但是很少有人记得让打印的网页可访问！

将响应迅速的连续介质转换成任何尺寸和方向的页面纸张都是一项挑战。但是 CSS 打印控制已经可以实现很多年了，一个基本的样式表可以在几个小时内完成。以下部分描述了使页面易于打印的受支持的实用选项。

## 打印样式表

打印 CSS 可以是:

1.  **除了屏幕造型之外还适用。**以您的屏幕样式为基础，打印机样式会根据需要覆盖这些默认值。
2.  **作为单独的样式应用。**屏幕和打印风格完全分离；两者都从浏览器的默认样式开始。

选择将取决于您的网站/应用程序。就我个人而言，我大部分时间使用屏幕样式作为打印基础。然而，我对输出完全不同的应用程序使用了单独的样式表——比如一个会议预订系统，它在屏幕上显示时间表网格，但在纸上显示按时间顺序排列的时间表。

打印样式表可以添加到标准样式表之后的 HTML `<head>`:

```
<link rel="stylesheet" href="main.css" />
<link rel="stylesheet" media="print" href="print.css" /> 
```

打印页面时，`print.css`样式将在**添加**中应用于屏幕样式。

要分离屏幕和印刷媒体，`main.css`应仅针对屏幕:

```
<link rel="stylesheet" media="screen" href="main.css" />
<link rel="stylesheet" media="print" href="print.css" /> 
```

或者，可以使用`@media`规则将打印样式包含在现有的 CSS 文件中。例如:

```
/* main.css */
body {
  margin: 2em;
  color: #fff;
  background-color: #000;
}

/* override styles when printing */
@media print {

  body {
    margin: 0;
    color: #000;
    background-color: #fff;
  }

} 
```

可以添加任意数量的`@media print`规则，因此这对于将关联的样式放在一起可能是实用的。如有必要，屏幕和打印规则也可以分开:

```
/* main.css */

/* on-screen styles */
@media screen {

  body {
    margin: 2em;
    color: #fff;
    background-color: #000;
  }

}

/* print styles */
@media print {

  body {
    margin: 0;
    color: #000;
    background-color: #fff;
  }

} 
```

## 测试打印机输出

没有必要在每次你想测试你的打印布局的时候都杀死树木和使用昂贵的墨水！以下选项复制屏幕上的打印样式。

### 打印预览

最可靠的选项是浏览器中的打印预览选项。这显示了如何使用默认纸张大小处理分页符。

或者，您可以通过导出到 PDF 来保存或预览页面。

### 开发者工具

DevTools ( `F12`或`Cmd/Ctrl` + `Shift` + `I`)可以模拟打印样式，尽管不会显示分页符。

在 Chrome 中，打开开发者工具，从右上角的三点图标菜单中选择**更多工具**，然后选择**渲染**。将面板底部的**仿真 CSS 媒体**更改为**打印**。

在 Firefox 中，打开开发者工具，点击**检查器**标签的样式窗格上的**切换印刷媒体模拟**图标:

![Firefox print preview mode](img/3641a2a388d5348c5898bd106001f9e4.png)

### 黑掉你的媒体属性

假设您正在使用一个`<link>`标签来加载打印机 CSS，您可以暂时将`media`属性更改为`screen`:

```
<link rel="stylesheet" href="main.css" />
<link rel="stylesheet" media="screen" href="print.css" /> 
```

同样，这不会显示分页符。完成测试后，不要忘记将属性恢复到`media="print"`。

## 删除不必要的部分

在做任何其他事情之前，用`display: none;`删除并折叠多余的内容。纸上典型的不必要部分可能包括导航菜单、英雄图像、页眉、页脚、表单、侧栏、社交媒体小部件和广告块(通常是`iframe`中的任何内容):

```
/* print.css */
header, footer, aside, nav, form, iframe, .menu, .hero, .adslot {
  display: none;
} 
```

如果 CSS 或 JavaScript 功能根据特定的 UI 状态显示元素，可能有必要使用`display: none !important;`。通常不推荐使用`!important`,但是我们可以证明它在一组覆盖屏幕默认值的基本打印机样式中的使用是合理的。

## 使布局线性化

这么说让我很痛苦，但是 **Flexbox 和 Grid 很少能在任何浏览器中很好地配合打印机布局**。如果遇到问题，考虑在布局框上使用`display: block;`或类似的东西，并根据需要调整尺寸。例如，将`width: 100%;`设置为跨越整个页面宽度。

## 打印机样式

现在可以应用打印机友好的样式。建议:

*   确保在白色背景上使用深色文本
*   考虑使用衬线字体，这可能更容易阅读
*   将文本尺寸调整到`12pt`或更大
*   必要时修改填充和边距。标准的`cm`、`mm`或`in`单位可能更实用。

进一步的建议包括…

### 采用 CSS 列

标准 A4 和美国信纸会导致文本行更长，可读性更差。考虑在打印布局中使用 [CSS 列](https://developer.mozilla.org/docs/Web/CSS/CSS_Columns)。例如:

```
/* print.css */
article {
  column-width: 17em;
  column-gap: 3em;
} 
```

在本例中，当至少有`37em`水平空间时，将创建列。不需要设置媒体查询；额外的栏目将被添加到更宽的纸上。

### 使用边框而不是背景色

您的模板可能包含由较暗或相反的配色方案表示的部分或标注框:

![callout box on-screen](img/e03e349f1d48d136e522f0c68173766c.png)

通过用边框表示这些元素来节省墨迹:

![](img/26f888b90401b86f5972c5098a670d2a.png)

### 移除或反转图像

用户不希望打印装饰性和非必要的图像和背景。您可以考虑默认情况下隐藏所有图像，除非它们有一个`print`类:

```
/* print.css */
* {
  background-image: none !important;
}

img, svg {
  display: none !important;
}

img.print, svg.print {
  display: block;
  max-width: 100%;
} 
```

理想情况下，打印图像应该在浅色背景上使用深色。在 CSS 中更改 HTML 嵌入的 SVG 颜色是可能的，但是会出现位图颜色较暗的情况:

![dark chart](img/2565a9bd3ca32269154d5f811609b457.png)

一个 [CSS 滤镜](https://developer.mozilla.org/docs/Web/CSS/filter)可以用来反转和调整打印样式表中的颜色。例如:

```
/* print.css */
img.dark {
  filter: invert(100%) hue-rotate(180deg) brightness(120%) contrast(150%);
} 
```

结果是:

![light chart](img/1fc223065cfc802087774088f079a23d.png)

## 添加补充内容

印刷媒体通常需要额外的信息，而这些信息在屏幕上是不必要的。CSS [`content`](https://developer.mozilla.org/docs/Web/CSS/content) 属性可以用来将文本添加到`::before`和`::after`伪元素中。例如，在文本后的括号中显示链接的 URL:

```
/* print.css */
a::after {
  content: " (" attr(href) ")";
} 
```

或者您可以添加仅供打印的邮件:

```
/* print.css */
main::after {
  content: "Copyright site.com";
  display: block;
  text-align: center;
} 
```

对于更复杂的情况，可以考虑在应该只在打印时可见的元素上使用类似于`print`的类。例如:

```
<p class="print">Article printed at 1:23pm 5 September 2020\. Please see https://site.com/page for the latest version.</p> 
```

CSS:

```
/* hidden on-screen */
.print {
  display: none;
}

@media print {

  /* visible when printed */
  .print {
    display: block;
  }

} 
```

注意:大多数浏览器在打印页面的页眉和/或页脚显示 URL 和当前日期/时间，所以很少需要用代码生成这些信息。

## 分页符

CSS3 属性 [`break-before`](https://developer.mozilla.org/docs/Web/CSS/page-break-before) 和 [`break-after`](https://developer.mozilla.org/docs/Web/CSS/break-after) 允许您控制页面、列或区域分隔符在元素前后的行为。[支持优秀的](https://caniuse.com/#search=break-before)，但较老的浏览器可能会使用类似的 [`page-break-before`](https://developer.mozilla.org/docs/Web/CSS/page-break-before) 和 [`page-break-after`](https://developer.mozilla.org/docs/Web/CSS/page-break-after) 属性。

可以使用以下`break-before`和`break-after`值:

*   `auto`:默认——允许休息，但不强制
*   `avoid`:避免页面、栏或区域的中断
*   避免分页
*   `page`:强制分页
*   `always`:是`page`的别名
*   `left`:强制分页，使下一页为左页
*   `right`:强制分页，使下一页为右页

示例:在任何`<h1>`标题之前强制分页:

```
/* print.css */
h1 {
  break-before: always;
} 
```

注意:警惕过度使用分页符。理想情况下，打印机输出应该使用尽可能少的页面。

[`break-inside`](https://developer.mozilla.org/docs/Web/CSS/break-inside) (以及更老的 [`page-break-inside`](https://developer.mozilla.org/docs/Web/CSS/page-break-inside) )属性指定元素内部是否允许分页符。通常支持的值:

*   `auto`:默认——允许休息，但不强制
*   尽可能避免内心的崩溃
*   尽可能避免内分页符

这可能比指定分页符更可取，因为您可以使用尽可能少的纸张，同时避免分组数据(如表格或图像)中的分页符:

```
/* print.css */
table, img, svg {
  break-inside: avoid;
} 
```

[`widows`](https://developer.mozilla.org/docs/Web/CSS/widows) 属性指定在页面的**顶部**必须显示的块中的最小行数。想象一个有五行文本的块。浏览器希望在第四行之后进行分页，这样最后一行就出现在下一页的顶部。设置`widows: 3;`在第二行或第二行之前中断，因此至少有三行延续到下一页。

[`orphans`](https://developer.mozilla.org/docs/Web/CSS/orphans) 属性类似于`widows`，除了它控制在页面的**底部**显示的最小行数。

[`box-decoration-break`](https://developer.mozilla.org/docs/Web/CSS/box-decoration-break) 属性控制跨页面的元素边框。当带有边框的元素有内部分页符时:

*   `slice`:默认，分割布局。上边框显示在第一页，下边框显示在第二页。
*   `clone`:复制边距、填充和边框。所有四个边框都显示在两个页面上。

最后， [CSS 分页媒体(`@page` )](https://developer.mozilla.org/docs/Web/CSS/@page) 有[有限的浏览器支持](https://caniuse.com/#feat=css-paged-media)，但提供了一种针对特定页面的方法，因此可以定义替代的边距或分隔符:

```
/* print.css */

/* target all pages */
@page {
  margin: 2cm;
}

/* target the first page only */
@page :first {
  margin-top: 6cm;
}

/* target left (even-numbered) pages only */
@page :left {
  margin-right: 4cm;
}

/* target right (odd-numbered) pages only */
@page :right {
  margin-left: 4cm;
} 
```

CSS 分页符属性可以放在您的`screen`或`print`样式中，因为它们只影响打印，但是为了清晰起见，我建议在打印 CSS 中使用它们。

请注意，分页符控件只不过是给浏览器的一个建议。因为版面被限制在纸张的范围内，所以不能保证一定会强制或避免换行。

## 印刷的烦恼

对印刷网络媒体的控制总是有限的，并且结果会因浏览器而异。也就是说:

*   易于打印的样式表可以更新到任何网站
*   大多数打印机样式可以在大多数浏览器中工作
*   打印样式不会影响或破坏现有功能
*   开发成本极低。

添加一些分页符和删除不必要的部分会让用户高兴，并使你的网站超越竞争对手。将其添加到您的待办事项列表中！

要了解更多的 CSS 知识，请阅读我们的书 [CSS Master，第二版](https://www.sitepoint.com/premium/books/css-master-2nd-edition?utm_source=blog&utm_medium=articles)。

## 分享这篇文章