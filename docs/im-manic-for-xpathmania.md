# 我对 XPathmania 很狂热

> 原文：<https://www.sitepoint.com/im-manic-for-xpathmania/>

可能没有什么比编写和调试复杂的 XPath 语句更烦人的了。有一些好的编辑，但充其量也是棘手的。在我的 XPath 之旅中，我最喜欢的辅助工具之一是来自 [TopXML](http://www.topxml.com) 的一个叫做 [XPath 可视化工具](http://www.topxml.com/xpathvisualizer/)的小工具。它允许用户针对样本文档编写特定的 XSL 查询，然后直观地突出显示结果。这是一个非常方便的技巧，当你看着 1500 行 XSL 并且想知道为什么一些查询会失败时。现在，这个工具确实有它的烦恼——主要是对 Internet Explorer 的依赖和相当原始的界面。我更愿意在漂亮、干净的 Visual Studio 2005 中使用我的 XPath。

进入 [Mvp。Xml 项目](http://www.xmlmvp.org/)——一组 Microsoft Xml MVPs 帮助补充。NET 框架。可能他们迄今为止做的最酷的东西是 [XPathmania](http://donxml.com/allthingstechie/archive/2006/07/07/2792.aspx) 。由[唐·德姆萨克创作，又名 DonXML](http://donxml.com) ，这是一个 Visual Studio 2005 扩展，允许用户编写特定的 XPath 查询并突出显示选定的节点，很像上面的工具。但是它存在于 Visual Studio 2005 中——这使得它更加方便，并留下了很大的发展空间。

[你可以从 CodePlex](http://www.codeplex.com/Release/ProjectReleases.aspx?ProjectName=MVPXML) 上的 MvpXml 工作区获得该工具的 Release Candidate 1.1(或者源代码)。请注意，它是一个 Visual Studio 外接程序，因此不能用于速成版。享受快乐的视觉体验。

## 分享这篇文章