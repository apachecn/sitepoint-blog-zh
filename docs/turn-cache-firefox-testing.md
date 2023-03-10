# 测试时关闭 FireFox 中的缓存

> 原文：<https://www.sitepoint.com/turn-cache-firefox-testing/>

给那些一直在做“清除浏览器 Cookies，缓存，一切…”的人一个小提示，你可以在做开发和测试的时候在 Firefox 中完全关闭它。

### 方法 1:在设置中

![turn-off-cache](img/2f27c1a4e720a39e1d8e79eeddde7268.png "turn-off-cache")

我补充一下如何完全禁用 FireFox 的浏览器缓存。

*   启动 FireFox
*   在地址栏中输入 **about:config** (点击我会小心的)
*   在搜索栏中键入“cache ”,查找 network.http.use-cache，双击它将其设置为 false。再次双击它会将其设置为真，并重新启用缓存…然后你就完成了。
*   要强制重新加载页面及其所有依赖项，请直接从源代码加载，忽略本地和代理缓存，按住 shift 键并点击 reload。
*   这不仅适用于 FireFox，也适用于 IE6/7 和 Safari(可能其他也是)。我有一种感觉，这可能是一个认可的标准。

### 方法 2:绕过

您可以通过以下方式重新加载页面并绕过缓存:

*   按住 Shift 并左键单击重新加载按钮。
*   按**“Ctrl+F5”**或“Ctrl+Shift+R”(Windows、Linux)
*   按下“Cmd+Shift+R”(MAC)
*   查看键盘快捷键–快速执行常见的 Firefox 任务和鼠标快捷键
*   Firefox 也有一个内存缓存，但不建议禁用内存缓存，这会导致问题。
*   如果你在隐私浏览模式下浏览网页而不保存你所访问网站的信息，那么你的磁盘缓存将被禁用，而永久 cookies 和历史记录等其他功能也将被禁用。

来源:http://support.mozilla.org/

## 分享这篇文章