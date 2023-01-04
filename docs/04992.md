# 正文(HTML 元素)

> 原文：<https://www.sitepoint.com/body-html-element/>

## 描述

`body`元素包含了将在屏幕上(或在其他媒体上，如印刷品上)显示的所有内容，如标题、段落、图像、表格等等。它有一些独特的属性，包括 [alink](https://reference.sitepoint.com/html/body/alink) 、 [link](https://reference.sitepoint.com/html/body/link) 和 [vlink](https://reference.sitepoint.com/html/body/vlink) ，所有这些属性现在都被弃用，还有许多其他属性没有在任何标准中定义，但遗憾的是，它们仍然被普遍使用。这些是:

### `marginheight`

设置文档内容的顶部和底部与视窗之间的间距(最初由 Netscape 4 定义)

### `marginwidth`

设置文档内容和视窗(最初由 Netscape 4 定义)左右之间的间距

### `topmargin/bottommargin`

相当于 Internet Explorer 的`marginheight`(允许我们分别设置上边距和下边距)

### `leftmargin/rightmargin`

相当于 Internet Explorer 的`marginwidth`(允许分别设置左边距和右边距)

请注意，不再需要这些属性来实现它们最初想要的视觉效果——这些属性的等价 CSS 是`margin`属性(或`margin-top`、`margin-right`等)，所有浏览器都支持它。

所有的 XHTML 网页都需要一个 body 元素，只有基于框架集的网页除外，对于基于框架集的网页，必须声明适当的框架集文档类型。使用 HTML 语法(即使使用严格的 doctype)，可以省略开始的和结束的标记，它仍然会被验证(浏览器会在 DOM 中创建它)，但是最好还是包含它。

在 HTML 和 XHTML 中，不应该将字符数据(文本)或内联元素(如 span、em 或 strong)直接放在正文中。相反，它们应该包含在块级元素中，比如 p 元素、标题 h1-h6 等等。但是，如果使用过渡的 HTML4 或 XHTML1 doctype，则允许字符数据和行内元素直接出现在正文中，但不推荐这样做。

## 例子

`body`包含所有对用户可见的内容:

```
*<body>*
  <h1>101 Ways to make a paper aeroplane</h1>
  <p>Let's start with the basics …</p>
  ⋮
*</body>*
```

## 将此用于…

使用`body`保存您希望读者看到的所有文档内容。

## 分享这篇文章