# 电子邮件中的 CSS 支持

> 原文：<https://www.sitepoint.com/css-support-in-email/>

不久前，Alex 花了一些时间[研究 Lotus Notes](https://www.sitepoint.com/html-email-and-lotus-notes/) 的 CSS 秘密，以帮助我们的电子邮件简讯的 HTML 版本在该客户端中正确显示。但是在 HTML 电子邮件这个疯狂而复杂的世界里，Lotus Notes 只是死囚区里最糟糕的罪犯。还有很多其他表现不佳的电子邮件客户端，它们中的大多数都没有 Eudora 考虑周到(它根本不支持 CSS)。

在 [Campaign Monitor 博客](http://www.campaignmonitor.com/blog/)上，David Greiner 发布了一份完整的[CSS 支持指南](http://www.campaignmonitor.com/blog/archives/2006/03/a_guide_to_css_1.html)在所有流行的电子邮件客户端，包括基于桌面和网络的。

这个指南将为你节省大量的时间，即使它只是让你相信电子邮件中的 CSS 是一个失败的事业。

考虑一下:如果您想避免将`style`属性应用到您的 HTML 标签，您将不得不完全牺牲对 GMail 的支持，并且您将需要在`<body>`中使用一个`<style>`标签来支持 web 电子邮件客户端，在`<head>`中使用一个`<link>`标签来支持 Lotus Notes。

听起来很荒谬？恐怕只是冰山一角…

## 分享这篇文章