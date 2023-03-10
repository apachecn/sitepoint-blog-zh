# 微软在 Whidbey 中取消了 XHTML 支持

> 原文：<https://www.sitepoint.com/microsoft-backs-off-xhtml-support-in-whidbey/>

(Via [InfoWorld](http://www.infoworld.com/cgi-bin/redirect?source=rss&url=http://www.infoworld.com/article/05/08/29/HNvschanges_1.html) )在[ASP.NET 开发者中心](http://msdn.microsoft.com/asp.net/default.aspx)的一篇[文章](http://msdn.microsoft.com/asp.net/beta2/beta2rtmchanges/default.aspx#standards)中，微软概述了当前 ASP.NET 2.0 Beta 2 版本(代号为 Whidbey)和将于 11 月发布的最终生产版本(RTM)之间的重大变化。

尽管微软淡化了这些变化的重要性，但真正吸引我眼球的是取消了对 XHTML 1.1 的支持。目前，Beta 2 默认呈现符合 XHTML 1.1 的[标记，并带有恢复为不符合 XHTML 的输出的选项(例如，< br >而不是< br / >)。相反，ASP.NET 2.0 的最终版本将默认输出](https://www.w3.org/TR/xhtml11/) [XHTML 1.0](https://www.w3.org/TR/xhtml1/) 过渡标记，并可以选择呈现 XHTML 1.0 严格或不符合 XHTML 的标记。

为什么要改变？微软在这方面有点含糊，列举了许多依赖过时的客户端脚本的开发人员的需求，这些脚本要求表单标签上有 name 属性。在 HTML 4 中[不鼓励使用这个属性，在 XHTML 1.0](https://www.w3.org/TR/html401/interact/forms.html#adef-name-FORM) 中[被正式否决。在 XHTML 1.0 Strict 或 XHTML 1.1 中根本不允许使用该属性，因此在默认情况下，将 XHTML 1.0 转换为 Transitional 会提供最现代的默认输出，同时仍然允许使用该属性。](https://www.w3.org/TR/xhtml1/#C_8)

但是如上所述，微软仍然计划在 ASP.NET 2.0 中支持一个“严格”的呈现选项，产生 XHTML 1.0 严格输出。为什么不在这种渲染模式下继续支持 XHTML 1.1？

答案尚未揭晓，但我认为可以用两个词来概括:Internet Explorer。W3C [推荐](https://www.w3.org/TR/xhtml-media-types/)使用 application/xhtml+xml MIME 类型提供 XHTML 1.1 内容，这是 Internet Explorer 6 不支持的。在这个阶段，也没有理由相信 Internet Explorer 7 会支持它。

在另一篇关于 ASP.NET 开发者中心的文章中，SuperExpert.com 的 Stephen Walther 对这个主题有如下看法:

> W3C 的推荐标准有一个明显的问题:不是所有的浏览器都能识别 application/xhtml+xml。特别是 Internet Explorer(世界历史上最流行的 Web 浏览器)不识别 application/xhtml+xml MIME 类型。因此，使用推荐的 application/xhtml+xml MIME 类型来提供 XHTML 页面不是一个可行的选择。

“突出的问题”是 W3C 的还是微软的，这是一个有争议的问题，但是通过从 ASP.NET 2.0 中移除 XHTML 1.1 支持，微软似乎正在向 W3C 的智慧低头。如果 ASP.NET 2.0 要输出 XHTML 1.1，它需要使用 Internet Explorer 支持的旧文本/html mime 类型，这将直接违反 W3C 的推荐标准。

虽然从表面上看，微软似乎在标准遵从性方面后退了一步，但事实上，它承认在 Web 应用程序平台中采用 XHTML 1.1 之前，它必须使其“世界历史上最流行的浏览器”达到标准，从而展示了对 Web 标准的健康尊重。

Tom 在 hist 评论中正确地指出，IE 的问题不仅仅是缺乏对新 MIME 类型的支持。事实上，微软没有天真地急于实现这种支持是另一个好迹象。微软似乎明白，application/xhtml+xml MIME 类型应该与 geniune 对 xhtml 的支持一起实现——这也是 Internet Explorer 所缺乏的。

## 分享这篇文章