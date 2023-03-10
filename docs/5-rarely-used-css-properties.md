# 5 个很少使用的 CSS 属性

> 原文：<https://www.sitepoint.com/5-rarely-used-css-properties/>

![rarely-used CSS properties](img/9cb5f0e3f4139692d67500973505e5e5.png "rarely-used CSS properties")CSS 属性的体积很容易让人忘记那些不是每天都用的。这里列出了 5 个有用但很少使用的 CSS 属性，它们适用于所有现代浏览器…

**1。大写`text-transform`**
这个属性改变了 HTML 元素的文本内容的大写方式。当你的设计部门在最后一刻改变了大写字母的时候，这是非常有用的，而且比重新编写 HTML 代码更容易。主要值有:

*   **大写:**使每个单词的第一个字符大写
*   **小写:**将所有字符变为小写
*   **大写:**将所有字符改为大写
*   **无:**无大写变化

大多数浏览器表现良好，尽管如果`font-variant`设置为“小型大写”，IE 可以设置为“无”。

**2。文本`letter-spacing`和`word-spacing`**
将你的页面文本保持为 HTML 使得维护更容易，对 SEO 也更好。简单的文本效果可以在没有图形的情况下使用`letter-spacing`来减少或增加固定数量的字符间距(px，em，ex，%，等等),例如

```
 h2 { letter-spacing: 0.1em; } 
```

## 宽间距 H2 标题(0.1 毫米)

## 密排 H2 片头(-0.1 毫米)

`word-spacing`是类似的，除了它影响单词之间的间距而不是字符之间的间距。

**3。使用`text-indent`**
`text-indent`缩进定义了文本块中第一行的左缩进(如果`direction`设置为“rtl”，则为右缩进)，例如

```
 p { text-indent: 10px; } 
```

这个属性经常显得多余，因为用`padding`可以实现类似的效果。然而，`text-indent`并不影响元素的宽度，所以在 IE 兼容的菜单中，整个块都是可点击的，例如

```
 ul#menu li a
{
	display: block;
	width: 100%; /* IE hasLayout applied */
	text-indent: 10px;
} 
```

**4。表格数据仍然需要使用`table-layout`**
表格来固定表格宽度，但是当浏览器对`table-layout`的默认设置为“自动”时，样式化列宽可能会很困难。如果单元格的文本需要更多空间，自动算法可能会覆盖您指定的表格宽度。

将`table-layout`设置为“固定”会强制浏览器遵循您指定的表格宽度。算法的全部细节可以在 [SitePoint CSS 参考表格格式页面](https://reference.sitepoint.com/css/tableformatting)上找到。

**5。`white-space`**
`white-space`的替代用法决定了浏览器如何在 HTML 中呈现空白。跨浏览器支持是不完整的，但是下列属性可以一致地工作:

*   正常:空白折叠成一个字符，并且在需要的地方出现换行符
*   **nowrap:** 空白折叠成一个字符，但是换行被禁止
*   前:空白不会折叠，在 HTML 中只在新行换行

在展示代码片段时,`white-space`是必不可少的，但在其他情况下，它也很有用。例如，我们有一个将`table-layout`设置为“固定”的表:

![Fixed-width table with differing row heights](img/7ca19af9765a27b54a864d0211e5db87.png "table with differing row heights")

行高不同的固定宽度表格

然而，我们更希望所有的行高保持一致，长文本被裁剪掉(可能是因为我们正在实现一个 JS 工具提示来完整地展示技能)。在`tr`或`td`元素上设置 CSS `height`不起作用，但是我们可以使用`white-space`来代替，例如

```
 td
{
	white-space: nowrap; /* suppress line breaks */
	overflow: hidden;    /* crop the text */
} 
```

![Table rows with consistent heights and cropped text](img/b746b4d22b5506f2c49d95ec4b5b6697.png "rows with consistent heights")

具有一致高度和裁剪文本的表格行

另请参见:

*   [5 个最不常用的 HTML 标签](https://www.sitepoint.com/five-under-used-html-tags/)
*   [解决 IE6 问题的 10 个修复](https://www.sitepoint.com/10-fixes-for-ie6-problems/)
*   [Opera MAMA 项目(网页结构搜索引擎)](http://dev.opera.com/articles/view/mama/)

我错过了你最喜欢的很少使用的 CSS 吗？

## 分享这篇文章