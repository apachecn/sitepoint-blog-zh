# 火狐 7 的新功能

> 原文：<https://www.sitepoint.com/whats-new-in-firefox-7/>

过去的六周去哪了？继 8 月 16 日推出 [Firefox 6 之后，Mozilla 于 2011 年 9 月 27 日发布了 Firefox 7。撰写本文时自动更新尚未启动，但你可以从 www.mozilla.org/firefox/](https://www.sitepoint.com/firefox-6-whats-new/)[下载最新版本。](http://www.mozilla.org/firefox/)

我体验了火狐 6 的几个问题，尤其是火狐 6 的 T2 资源对 OS X 的占用。和许多人一样，我考虑过全职转向 Chrome。Mozilla 承诺[Firefox 7 在所有平台上都有更好的性能和更低的内存使用量](http://blog.mozilla.com/blog/2011/09/27/mozilla-firefox-significantly-reduces-memory-use-to-make-web-browsing-faster/):

> Firefox 更有效地管理内存，提供灵活的网络浏览体验。用户会注意到 Firefox 在打开新标签、点击网站上的菜单项和按钮时速度更快。当大量标签打开时，以及在持续数小时甚至数天的长时间网页浏览会话期间，重度互联网用户将享受增强的性能。

我已经用了几个小时的浏览器，感觉确实更快了。Windows 7 的内存使用情况似乎更好，即 10 个标签需要 280Mb，而 Firefox 6 需要 380Mb。然而，需要更长时间的测试来真正评估进展。

Mozilla 正在收集性能数据，首次启动应用程序时，系统会询问您是否希望匿名加入。

## 对 Web 开发人员来说有什么新内容？

提供了一些有趣的增强功能:

*   CSS `text-overflow`属性现在支持`ellipsis`，因此当文本超出可用空间时会出现“…”。
*   画布渲染速度更快。
*   Firefox Mobile 上启用了 WebSockets。
*   [导航定时](https://dvcs.w3.org/hg/webperf/raw-file/tip/specs/NavigationTiming/Overview.html)已经实现。这允许 web 应用程序测量真实世界的性能因素并优化用户体验。

也不再可能用 JavaScript 代码调整窗口大小，除非它是用 window.open 创建的，并且没有两个或更多选项卡。开发者还在创建试图全屏打开的网站吗？我以为这种做法已经消失了，但 Mozilla 已经埋葬了它。

## 争论的版本

我已经多次讨论过火狐版本编号混乱的问题，但是——到目前为止——火狐 7 似乎比它的前辈问题更少。附加开发人员了解这些问题，我的扩展没有一个被禁用。我不能保证你会有同样的经历，但情况似乎正在改善。

Mozilla 正迅速输给谷歌 Chrome 浏览器。火狐 7 很好，我怀疑它会说服许多人坚持使用这款浏览器。这是否足以说服大失所望的用户回归，则是另一回事。

## 分享这篇文章