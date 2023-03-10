# 雅虎！UI 和 DP 库已更新

> 原文：<https://www.sitepoint.com/yahoo-ui-and-dp-libraries-updated/>

雅虎！[宣布了](http://feeds.yuiblog.com/YahooUserInterfaceBlog?m=14)雅虎的第一次更新！用户界面库和雅虎！设计模式库(参见 [JavaScript 库和模式:雅虎！※AJAX](https://www.sitepoint.com/javascript-libraries-and-patterns-yahoo-does-ajax/)。

不出所料，更新到[雅虎！UI 库](http://developer.yahoo.com/yui/)引入了一些新的 JavaScript 支持的用户界面组件。最值得注意的是，这个库现在包括了基于 HTML 代码中语义正确的无序列表来生成鼠标和键盘可访问的[层次菜单系统](http://developer.yahoo.com/yui/menu/)的组件。

更出乎意料的是，UI 库现在除了 JavaScript 魔法之外，还包括了一些有价值的 CSS 代码。CSS 重置消除了当前浏览器中应用于 HTML 元素的默认样式之间的许多差异，从而为您应用自己的样式提供了一个通用的基线。 [CSS 字体](http://developer.yahoo.com/yui/fonts/)提供了预定义的字体系列组和字体大小，能够在当前平台上稳定可靠地工作。

最后，也是重要的一点， [CSS 页面网格](http://developer.yahoo.com/yui/grids/)通过提供一套七个可重用的、可伸缩的、带页眉和页脚的两列页面布局模板，极大地简化了 CSS 页面布局的任务。使用`<div>`可以将每个列进一步划分为垂直堆叠的子列集合，这些子列具有一组针对不同百分比宽度的预定义网格类。

虽然雅虎！不提供任何功能，但如果能生成可插入的样式表，将可视化格式应用于使用 CSS 页面网格生成的页面布局，那将是非常实用的。如果这种布局技术获得牵引力，我希望围绕这种风格会出现一个站点皮肤社区(如果不是商业市场的话)。这种布局页面的方法也易于工具化，支持它们是像 Dreamweaver 这样的 WYSIWYG 设计工具的下一个合乎逻辑的步骤。

甚至*更*没想到，雅虎！开源了 Yahoo！通过[将 UI 库放在 SourceForge](http://sourceforge.net/projects/yui) 上，开发者可以在那里报告错误，并在库的主要版本之间获得较小的更新和修复。雅虎！暂时不会接受社区贡献的补丁或者通过 SourceForge 管理邮件列表，但是会继续使用 [ydn-javascript](http://groups.yahoo.com/group/ydn-javascript/) Yahoo！出于这些目的进行分组。

[雅虎的更新！设计模式库](http://developer.yahoo.com/ypatterns/)极大地扩展了它，它包含的设计模式数量大约翻了一番。大多数新模式被归入[邀请](http://developer.yahoo.com/ypatterns/parent.php?pattern=invitation)(通知交互特性存在的方式)和[转换](http://developer.yahoo.com/ypatterns/parent.php?pattern=transition)(通知应用程序状态变化的方式)。

像 [WebPatterns](http://webpatterns.org/) 这样的社区项目进展缓慢，以确保每个人在定义网页设计语言时都有发言权。同时，雅虎！和雅虎一起豁出去了！设计模式库，并且在 Web patterns 和它的同类产品问世之前，就已经开始定义 Web 模式中事实上的标准。

## 分享这篇文章