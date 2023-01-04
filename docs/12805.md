# 挖掘站点点 CSS 引用

> 原文：<https://www.sitepoint.com/mining-the-sitepoint-css-reference/>

我们大多数知道 CSS 的人都是自学的。自学的问题是，你花了大部分时间只学习你每天需要的东西，在你的知识中留下了小漏洞——昏暗和布满灰尘的角落里居住着你很少使用但可能有用的模糊事实。

在编辑 [SitePoint CSS 参考文献](https://reference.sitepoint.com/css/)时，我发现了许多有价值的 CSS 知识；我只是部分理解或者完全不知道的事情。这里有几个例子。

## 术语:规则五声明

在 CSS 中，术语[规则](https://reference.sitepoint.com/css/rulesets)经常被误用。有时你会看到以下被称为规则的内容:

```
padding: 1em;
```

然而，这是一个声明。这里有一个 CSS 规则:

```
body {
  padding: 1em;
  background-color: #fff;
}
```

声明为单个属性指定一个值。一个规则可以在一个选择器前面的块中包含一个或多个声明。

## 顶部或底部的百分比值`padding`或`margin`

如果您为 top 或 bottom `[padding](https://reference.sitepoint.com/css/padding)`或`[margin](https://reference.sitepoint.com/css/margin)`指定一个百分比值，百分比值表示包含块的宽度的百分比，而不是它的高度，如您所料。

## `text-decoration`的多个值

您知道`[text-decoration](https://reference.sitepoint.com/css/text-decoration)`属性可以接受多个空格分隔的值吗？例如，您可以这样做:

```
a:hover, a:focus {
  text-decoration: underline overline;
}
```

## `vertical-align`的长度或百分比值

当用于内嵌框(不是表格单元格)时，`[vertical-align](https://reference.sitepoint.com/css/vertical-align)`属性可以采用长度或百分比值。效果是提高或降低行内元素相对于其行框的位置。百分比值表示元素的`line-height`的百分比。

## `background-position`的百分比值

如果你曾经想知道为什么你的背景图像在使用百分比值给`[background-position](https://reference.sitepoint.com/css/background-position)`时开始变得疯狂，那么你可能没有意识到百分比值实际上是如何处理属性的。百分比值既指元素内的位置，也指图像内的位置。设置为距离元素顶部 50%的`background-position`会将背景图像中图像高度 50%的点放置在元素高度 50%的位置。

这看起来很正常，因为它有效地使图像在元素中居中。但是，当你使用像 66%这样不寻常的百分比值时，图像的位置似乎会出乎意料地到处变动。亚历克斯写了一篇关于`background-position`属性的[优秀博客文章](https://www.sitepoint.com/css-using-percentages-in-background-image/)，解释了这种行为。

## `table-layout`和性能

属性允许您指定浏览器应该使用固定布局算法来计算列宽和表格宽。在默认的表格布局算法中，即使您指定了宽度，列宽也会受到单元格内容的影响。你可能会说，在这种情况下指定宽度更像是一个指导方针，而不是一个实际的规则。

您可以通过将`table-layout`设置为值:`fixed`来指定应该使用固定布局算法。使用该算法，浏览器将忽略单元格的内容，并使用可用的宽度规格来计算表格列的宽度，即使内容不适合。您可以在参考资料的[表格格式](https://reference.sitepoint.com/css/tableformatting)主题中了解更多信息。

固定表布局的好处是性能更好，尤其是对于包含许多列的大型表。如果您有一个大的表格，并且可以安全地指定列宽，使用`table-layout: fixed;`将使浏览器更快地显示表格。

## 分享这篇文章