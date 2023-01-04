# 布局好了吗？Internet Explorer 的页面布局秘密

> 原文：<https://www.sitepoint.com/got-layout-internet-explorers-page-layout-secrets/>

说到在 CSS 中布局一个页面，你知道一个元素在 Internet Explorer 中“有布局”是什么意思吗？如果没有，你可能会错过一个有用的工具来克服浏览器中的 CSS 错误。然后，你可能会错过对你正在处理的错误的解释。

在 Internet Explorer 的内部工作中，当涉及到布局时，页面上的每个元素都以两种模式之一运行。要么它允许其祖先影响其内容的定位，要么它简单地抓取页面的一个矩形区域，并在该矩形内完成其内容的所有布局。执行后者的元素被称为“有布局”，因为您可以通过使用 JavaScript 检查 IE 专用的 hasLayout 属性来检测它们。

事实证明，强制元素“具有布局”是克服 Internet Explorer 中 CSS 错误的一种方法。例如，Internet Explorer 有许多错误，这些错误会导致浮动元素及其后代由于其父元素或其他祖先元素的影响而被错误地定位。通过将浮动元素的父元素引入“having layout ”,您可以消除这种不必要的影响，这通常会允许浮动元素被正确定位。

不幸的是，因为“有布局”的元素忽略了它们的祖先，它们并不总是以你期望的方式表现。例如，它们不能有溢出的内容，也不能环绕浮动的元素。本质上，“拥有布局”允许 Internet Explorer 用更简单的代码绕过一些有问题的 CSS 代码，这些代码通常可以通过忽略页面布局标准来完成工作。

强制一个元素“有布局”可以简单到给它分配一个固定的宽度。这种技术最著名的应用是 Holly Hack(T1 ),尽管当它在 2003 年发表时，没有人知道“having layout”……这只是一个看起来有效的 Hack。现在，Internet Explorer 开发已经全面展开，IE 团队正在[与标准组织](http://webstandards.org/press/releases/archive/2005/07/05/index.html)进行对话，我们开始深入了解浏览器的这些模糊细节，这些细节解释了浏览器行为中更令人精神分裂的方面。

微软新发布的一篇文章对“拥有布局”做了一个简短但有用的总结——哪些元素拥有它，其他元素如何获得它，以及一旦它们拥有了，它们会如何表现。这篇文章对浏览器的漏洞直言不讳，令人耳目一新。如果你想要更多的细节，你可以花一个安静的下午和[讨论 having layout](http://www.satzansatz.de/cssd/onhavinglayout.html) ，这是一篇关于“having layout”主题的冗长而详细的论文，以及它可以帮助或阻碍你在 Internet Explorer 中进行 CSS 布局的许多方式。

## 分享这篇文章