# Spanky Corners 1.0:圆角+干净的 HTML +无 JavaScript

> 原文：<https://www.sitepoint.com/spanky-corners-10-rounded-corners-clean-html-no-javascript/>

在我们最近围绕 [Greg 漂亮的边角和一般的标记](https://www.sitepoint.com/youve-come-a-long-way-baby-not/)的讨论中，提出了许多有趣的论点，其中有一点很难争辩——*如果你说这不是答案，你有什么选择？*'

也有道理。

当时，我成功地绕过了这个问题的一个真正的答案，但是，由于有一个设计视图，又没有什么可写的，我决定尝试一个答案。我上个月发表的替代方案相当原始，但在 Lox、Matt、Kevin、Tom 和 Craig 的大量帮助下， [Spanky Corners](https://www.sitepoint.com/premium/library/) 现在处于这样一个阶段，我们认为它对广泛的 web 开发者来说是相对健壮、有用和可用的。

这些优点很容易识别。

[![Screenshot of Spanky Corners](img/4a825d318745d43c062dd14da314e845.png)](https://www.sitepoint.com/premium/library/)

*   超低脂肪 HTML
*   不需要 JavaScript
*   对拐角半径没有实际限制
*   有效的 XHTML(严格)和 CSS
*   易接近的
*   微小图形要求
*   我们为您生成图像和 CSS！

**我们看到的唯一可预见的缺点是:**

*   对一个*最小* HTML 结构的依赖(即单个段落无法工作)。
*   已解决:~~一个 IE6 用户放大他/她的字体大小会在左上角看到一个小缺口~~

我仍然不相信 IE6 的问题没有解决的办法，所以如果你找到了什么，请告诉我。

Spanky 在 IE6、IE7、Firefox 1.5、Safari 1.0 和 Opera 8.53 中进行了测试。

我们决定完全忽略 IE5+5.5 支持，因为这些浏览器对列表图像的支持不够好。在实践中，我们建议使用条件注释将 IE5 中的圆形图形一起去掉。

我们已经在和 [FAQ](https://www.sitepoint.com/community/latest) 中为[提供了一个关于它如何工作的相当深入的解释，但是你可能只是想玩玩生成器和代码来感受一下。](https://www.sitepoint.com/community/latest)

希望你觉得它很方便，如果你偶然发现任何改进，让我们知道。这当然是一项正在进行的工作。

我们重新调整了 IE 渲染左上角的方式(基本上只是将列表图像换成了 IE 中的第一个字母),允许它在两个方向上平滑缩放。如果您想更新您之前下载的任何代码，您只需要更新条件注释。

我们还将 IE 特有的代码分解成它自己的样式表，并以 ZIP 文件的形式提供下载。

 **[带 Spanky 试驾这里](https://www.sitepoint.com/premium/library/)**

## 分享这篇文章