# Internet Explorer 条件注释

> 原文：<https://www.sitepoint.com/internet-explorer-conditional-comments/>

[条件注释](http://msdn2.microsoft.com/en-us/library/ms537512.aspx)由微软对 Internet Explorer 的专有扩展组成，它提供了一种机制来针对 IE 的每个版本，或者是特定的，或者是作为一个组。这个扩展是在 IE5 中引入的，但在 IE10 中被删除了，所以它只能用于在 Windows 平台上从 IE5 到 IE9 的浏览器中呈现的文档。

条件注释使用特殊的语法——包装在条件语句中的 HTML 标记——并放置在 HTML 注释中。如果语句评估为`true`，包含的 HTML 将显示在 HTML 文档中。如果该语句的计算结果为 false，则包含的 HTML 保持隐藏状态。因为条件注释与 HTML 注释放在一起，所以包含的 HTML 对于所有不支持条件注释的浏览器都是隐藏的。

条件注释可以放在文档中普通注释所在的任何位置。因此，你不能把它们放在外部 CSS 文件中，或者在`之间`