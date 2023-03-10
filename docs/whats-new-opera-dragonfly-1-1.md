# Opera 蜻蜓 1.1 有什么新功能

> 原文：<https://www.sitepoint.com/whats-new-opera-dragonfly-1-1/>

Opera 11.51 于 2011 年 9 月 1 日发布。这是一个小更新，修复了一个已知的安全问题，提高了稳定性，并在 Mac Lion 中提供了全屏支持。

网络开发者更感兴趣的是蜻蜓 1.1——Opera 的类似 Firebug 的控制台。我在五月给了[1.0 版本一个热情洋溢的评论，但是有一些小问题引起了关注。最新版实现了 200 多个 bug 修复和界面更新，让我们来看看亮点…](https://www.sitepoint.com/opera-dragonfly-1-browser-development-tool/)

## DOM 检查器

也许蜻蜓 1.0 最大的问题是 CSS 文件中缺少样式的链接和行号。这个问题已经解决了，我想很多开发者会很感激。

检查器现在支持伪类和伪元素。这包括::before 和::after，它们现在作为节点出现在 DOM 树中。这是一个奇妙的功能，还没有出现在竞争工具中。

还提供了一个新按钮，允许您切换状态，包括:链接、访问、悬停、活动、聚焦和选择。虽然 Firebug 中也有类似的功能，但是很少有开发者知道它，蜻蜓的实现更胜一筹。

最后，还有一个新的“搜索”选项卡，允许您通过文本、正则表达式、CSS 选择器和 XPath 来定位元素(例如，输入//div 来查找页面中的所有`div`元素)。非常有用。

## JavaScript 调试器

脚本选项卡中还实现了改进的搜索功能。可以使用文本或正则表达式搜索单个文件或所有 JavaScript 文件。您还可以包含或省略动态注入的脚本。

## 网络和资源检查员

现在可以检查通过标准或多部分表单发送的 POST 数据。资源检查器中还提供了一个新的搜索栏。

对 1.0 的主要批评之一是 Ajax 监控。蜻蜓 1.1 更好，根据我有限的测试，它正确地报告了所有后台 XMLHttpRequest 调用。不幸的是，返回的消息体仍然显示为编码的 base64 字符串，因此不可能直接检查 JSON 数据。opera——拜托，请在下一个版本中解决这个问题！

## 存储检查员

小部件首选项、本地和会话存储现在使用与 cookies 相同的界面。更干净，更好用。

## 其他更新

蜻蜓 1.1 还提供:

*   一个改进的错误日志接口
*   实用程序中一个很棒的标尺工具——尽管很遗憾不能在主浏览器窗口中使用它
*   一个新的全屏控制台——尽管我喜欢浮动覆盖版本。

蜻蜓 1.1 修复了开发人员在第一版中遇到的大多数问题。一些问题仍然存在，但它的伟大 Opera 继续投资于一个最好的开发控制台。

如果你是蜻蜓用户，新版本应该可以在你的浏览器中找到。如果你想试试，[下载并安装 Opera](http://www.opera.com/) ，然后按 Ctrl+Shift+I 或者右键点击页面，选择“Inspect Element”。

如果你仍然不相信，蜻蜓团队正在[寻求反馈](http://www.opera.com/dragonfly/feedback/)。也许你最想要的特性会出现在 1.2 版中？

你使用蜻蜓吗？1.1 是一种进步吗？你把它作为你的主要开发工具了吗？欢迎评论。

## 分享这篇文章