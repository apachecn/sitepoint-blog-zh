# Internet Explorer 8 beta 1 中的标准支持

> 原文：<https://www.sitepoint.com/standards-support-in-internet-explorer-8-beta-1/>

上周，当微软最优秀、最聪明的人登上 MIX08 的舞台时，我们都知道我们会听到关于 Internet Explorer 8 的消息。如果我们*幸运的话*，我们甚至可以看到 Internet Explorer 8 的运行。但是微软让我们大吃一惊，[发布了新浏览器](http://www.microsoft.com/windows/products/winfamily/ie/ie8/)的公测版，我们可以立即下载并亲自试用！

尽管 IE8 beta 1 仍处于开发阶段，但它已经代表了标准支持方面的巨大飞跃。新的布局引擎在级联样式表(CSS)领域取得了令人难以置信的进步，对于 JavaScript 开发人员来说，在性能和与文档对象模型(DOM)标准的兼容性方面有了显著的提高。

自从 IE6 发布以来，微软第一次在某些领域超越了其他主流浏览器提供的标准支持！

感兴趣的开发人员可以获得大量的详细信息。在 Internet Explorer 8 中的 [**CSS 改进中，微软详细介绍了浏览器中的新样式特性:**](http://msdn2.microsoft.com/en-us/library/cc304082%28VS.85%29.aspx)

*   支持**数据 URIs** ，它允许你直接在你的样式表中嵌入小图片以提高性能
*   少得多的错误 **CSS 浮动**，这使得微软能够**废除以前用于解决这些错误的 hasLayout hack** 。
*   正确支持**边缘折叠**
*   支持**生成内容**，包括**自动计数器**，使用 **`:before`和`:after`伪元素**。
*   支持 **`:focus`伪类**
*   支持 **CSS 轮廓**(不影响布局的边框)
*   **打印输出中的孤儿、寡妇和分页符控制**
*   支持 **CSS 表格**，使用 CSS 实现更加简单和强大的页面布局
*   以及更多更小的改进！

正如我在上一期文章中预测的那样，CSS 表格支持的增加将极大地简化使用 CSS 的页面布局过程。我很高兴看到我的使用 CSS 表格的[三列布局示例](https://www.sitepoint.com/examples/3col.html)在 IE8 beta 1 中完美呈现。

除了 beta 1 中已经有的改进，微软已经宣布计划在 Internet Explorer 8 发布时完全支持 CSS 2.1！如果它在这方面取得成功，它将成为第一家这样做的浏览器厂商。

而这些 CSS 的改进只是冰山一角。IE8 beta 1 对开发者的改进几乎可以在浏览器的每个部分看到:

*   IE 开发者工具栏得到了极大的增强。它的 CSS 调试和实时编辑功能现在几乎与备受喜爱的火狐浏览器的 [Firebug](http://getfirebug.com/) 扩展不相上下。默认情况下，工具栏内置在浏览器中！
*   JavaScript 已经获得了巨大的速度提升，微软已经修复了臭名昭著的 T2 循环引用内存泄漏。
*   JavaScript 开发人员使用的主要 API**文档对象模型(DOM)** 现在更加符合标准，使其达到了其他主流浏览器的水平。
*   IE8 支持 W3C 新的**可访问的富互联网应用(ARIA)** 标准，使屏幕阅读器等可访问工具能够与光滑的、JavaScript 支持的用户界面元素一起工作。

在这个领域，没有足够的空间来展示 IE8 beta 1 中的所有改进。如果你想提高速度，最好从 MSDN 的[**Internet Explorer 8**中的新功能](http://msdn2.microsoft.com/en-us/library/cc288472%28VS.85%29.aspx)文章开始。

Internet Explorer 超出任何人的预期已经有很长一段时间了，但随着 IE8 beta 1 的推出，它无疑做到了这一点，甚至更多。现在轮到我们所有的网络开发人员开始尝试所有这些新功能，去发现一旦 Internet Explorer 8 上市将会发生什么！

## 分享这篇文章