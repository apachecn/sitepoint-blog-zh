# 微软在 Outlook 2007 中破坏 HTML 电子邮件呈现

> 原文：<https://www.sitepoint.com/microsoft-breaks-html-email-rendering-in-outlook/>

以下内容转载自《科技时报》第 156 期。

如果浏览器对网络标准的支持进展缓慢，那么电子邮件客户端的支持则进展缓慢。试图在主要的电子邮件客户端中记录像 [CSS 支持这样的事情已经揭示了令人沮丧的事态，但是随着最近像](http://www.campaignmonitor.com/blog/2006/03/a_guide_to_css_support_in_emai.html)[雷鸟](http://www.mozilla.com/thunderbird/)这样的桌面客户端现在坐在可靠的渲染引擎上，事情已经有所好转。

当微软在年底向商业客户发布 Outlook 2007 和即将到来的消费者发布 Outlook 2007[时，这一切都改变了。](http://office.microsoft.com/outlook/)

冒着将这份时事通讯变成两周一次的微软聚会的风险，雷德蒙又做了一次。当 IE 团队用新的、更兼容的 Internet Explorer 7 抚慰各地 web 开发人员饱受折磨的灵魂时，Office 团队耍了一个花招，去掉了 Outlook 一直用于电子邮件的基于 IE 的渲染引擎，代之以…请敲鼓… Microsoft Word。

没错。Outlook 2007 没有利用 Internet Explorer 7，而是使用 Word 2007 内置的对 HTML 和 CSS 的非常有限的支持来显示 HTML 电子邮件。

去年底，我测试了 Outlook 2007 的两个公开测试版，我知道有些地方不对劲。我订阅的许多时事通讯已经变得无法阅读，SitePoint 自己的出版物(包括《科技时报》)看起来明显不健康。我尽职尽责地用测试版中提供的反馈机制报告了这些渲染问题，并将其作为将在最终版本中解决的 Internet Explorer 7 集成问题一笔勾销。

但是上个月末，SitePoint 论坛上的一个帖子引起了我的注意。微软[发表了两篇文章](http://msdn2.microsoft.com/en-us/library/aa338201.aspx)描述了 Outlook 2007 对 HTML 和 CSS 的支持，但消息并不好:

> Microsoft Office Outlook 2007 使用 Microsoft Office Word 2007 中的 HTML 分析和呈现引擎来显示 HTML 邮件正文。Outlook 2007 中提供了与 Word 2007 中相同的 HTML 和级联样式表(CSS)支持。

文章中详细描述了 Word 2007 带来的限制，但这里有一些亮点:

*   不支持背景图像(HTML 或 CSS)
*   不支持表单
*   不支持 Flash 或其他插件
*   不支持 CSS 浮动
*   不支持在无序列表中用图像替换项目符号
*   不支持 CSS 定位
*   不支持动态 gif

简而言之，除非您的 HTML 电子邮件非常非常简单，否则您会遇到 Outlook 2007 的问题，在大多数情况下，这些问题的唯一解决方案是降低 HTML 电子邮件设计的复杂性，以适应 Outlook 有限的功能集。

随着 Outlook 2007 的发布，微软有效地添加了一个全新的渲染引擎——一个制作 HTML 电子邮件的设计师无法忽视的引擎。

不仅如此，这个新的渲染引擎并不比 Outlook 以前使用的更好，事实上，要差得多。随着这一版本的发布，Outlook 从 HTML 电子邮件支持的最佳客户端之一下降到 Lotus Notes 和 Eudora 的水平，用 Campaign Monitor 的 David Grenier 的话说，“它们是让我们的电子邮件设计生活在地狱中的连环杀手。”

微软究竟为什么要做这样的事情？保安？微软一直在大肆宣扬 Internet Explorer 7 中的新安全模型，该模型可以防止过去困扰 Outlook 的讨厌的安全问题。然而，微软似乎并不相信自己的宣传，因为这一举动传达了一个信息，即 Internet Explorer 的安全模型是不可信的。

从这里去哪里？嗯，作为第一步，你会想使用微软的[便捷工具](http://msdn2.microsoft.com/en-us/library/aa338200.aspx)来告诉你，你的精简、卑鄙的 HTML 电子邮件的哪些部分需要被老式的 HTML 污泥所取代。第二步，你可以考虑给你的 Outlook 读者一个切换到纯文本邮件的简单方法。

带上 PDF 电子邮件。我准备好了。

## 分享这篇文章