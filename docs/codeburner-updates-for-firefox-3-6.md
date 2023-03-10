# 火狐 3.6 的 CodeBurner 更新

> 原文：<https://www.sitepoint.com/codeburner-updates-for-firefox-3-6/>

![CodeBurner](img/fd0aa311624b6b0266bf20989c30de21.png)

我很高兴地宣布期待已久的 CodeBurner 的更新，这是我们为 Firefox 和 Firebug 开发的流行工具，使它与 Firefox 3.6 保持同步。

这个版本在 Firefox 3.6 发布后被推迟了，对此我很抱歉，因为在[addons.mozilla.org](https://addons.mozilla.org/)的公共目录中对插件脚本有新的严格限制。这使得有必要为搜索结果和代码示例实现一个全新的 <abbr title="HyperTextMarkup Language">HTML</abbr> 模板系统。

这种改变是必要的，因为我使用的 [domplate](http://code.google.com/p/domplate/) 库包含了几个`eval`语句的实例，这是不允许的。因此，我别无选择，只能废弃 domplate，并写一些新的东西来代替它。您实际上应该不会注意到任何差异，但是幕后的变化非常显著。

版本 1.5.2 中的其他新功能包括:

1.  Firefox 3.5 或更高版本中新的“最小化浏览器名称”用户选项，消除了搜索结果表中的混乱，因此浏览器名称在每个表中只显示一次，而不是每行
2.  改进了选项卡框中的焦点行为
3.  在 <abbr title="Cascading Style Sheets">CSS</abbr> 视图中更容易识别的样式表<abbr title="Uniform Resource Locators">URL</abbr>
4.  其他小错误修复

请通过以下链接安装最新版本的 CodeBurner:

*   **[火狐版本 1 . 5 . 2](https://getfirebug.com/)**
*   **[CodeBurner for Firebug v 1 . 5 . 2](https://getfirebug.com/)**

请继续关注这个空间，因为 [Air](https://getfirebug.com/) 、 [Opera](https://getfirebug.com/) 和 [Dashboard](https://getfirebug.com/) 版本即将更新，以及 [Firefox](https://getfirebug.com/) 和 [Firebug](https://getfirebug.com/) 版本的进一步更新。

我们从不停止！

## 分享这篇文章