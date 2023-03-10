# 为 Microsoft Edge 构建扩展

> 原文：<https://www.sitepoint.com/building-extensions-for-microsoft-edge/>

[https://channel9.msdn.com/Events/WebPlatformSummit/edgesummit2016/ES1614/player](https://channel9.msdn.com/Events/WebPlatformSummit/edgesummit2016/ES1614/player)

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

自从我们发布以来，扩展一直是微软 Edge 的最终用户要求最高的功能，我们很高兴能够从 Windows Insider Preview build 1429 开始在 Edge 中预览扩展。在本次演讲中，我们将介绍我们如何努力构建一个值得信赖的扩展生态系统，同时让开发人员能够轻松编写可互操作的扩展来增强浏览体验。

这篇文章是微软技术倡导者和工程师关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?wt.mc_id=DX_838512) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?wt.mc_id=DX_838512)。

我们鼓励你在包括微软 Edge(Windows 10 的默认浏览器)在内的各种浏览器和设备上进行测试，在[dev.microsoftedge.com](https://dev.windows.com/en-us/?wt.mc_id=DX_838512)上使用免费工具，包括 [F12 开发者工具](https://developer.microsoft.com/en-us/microsoft-edge/platform/documentation/f12-devtools-guide/?wt.mc_id=DX_838512)——七种不同的、完整记录的工具，帮助你调试、测试和加速你的网页。此外，[访问 Edge 博客](https://blogs.windows.com/msedgedev/?wt.mc_id=DX_838512)，从微软开发者和专家那里获得最新信息。

## 分享这篇文章