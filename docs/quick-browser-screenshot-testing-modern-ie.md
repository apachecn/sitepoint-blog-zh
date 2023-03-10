# 现代的快速浏览器截图测试。工业管理学(Industrial Engineering)

> 原文：<https://www.sitepoint.com/quick-browser-screenshot-testing-modern-ie/>

*本文由 [modern 赞助。即](http://www.modern.ie/)。感谢您对使 SitePoint 成为可能的赞助商的支持！*

Web 开发人员明白在多种移动、平板和桌面浏览器上进行严格测试的重要性。我们有。说实话。简直就是耗时太久！一天只有那么几个小时，测试经常远远落在我们优先考虑的事情之外。一旦我们实现了一个更重要的特性，阅读了最新的 SitePoint HTML5 文章，喝了杯咖啡，现在是下午 5 点，我们就会这么做。

跨多个设备进行测试可能会很乏味，但是，你拖得越久，就越难解决不可避免的问题。你可能有一个很棒的设备实验室，或者使用现代的虚拟机。但是从开发人员到测试人员的心理跳跃是令人畏惧的。

## 简单的多设备测试

幸运的是，[现代。IE](http://www.modern.ie/) 有另一个快速肮脏测试的解决方案。由 [BrowserStack](http://www.browserstack.com/) 支持的免费[截图自动化工具](http://modern.ie/screenshots)将你的网站加载到一系列移动和桌面设备上，并在几分钟内呈现截图。这个过程很简单，没有痛苦…

在浏览器中打开[modern.ie/screenshots](http://modern.ie/screenshots):

[![test website](img/4aadbb9a5edb2ff3862930d3269dc842.png)](https://blogs.sitepointstatic.cimg/tech/944-modern.ie-screenshot-start.jpg)

输入要测试的页面的地址，然后按 Enter 键:

[![test website](img/173647da628b0d28385998228e149606.png)](https://blogs.sitepointstatic.cimg/tech/944-modern.ie-screenshot-end.jpg)

生成并显示结果—单击任意缩略图查看全尺寸图像。选项按钮允许您:

*   下载包含所有截图的 PDF 文件；
*   通过电子邮件、脸书或推特分享结果；和
*   为你的网站制作一张海报！

如果您需要，还可以找到更多工具和帮助资源的链接。

## 要记住的事情

但是，要警惕局限性。您的网站必须位于一个实时的、可访问的 URL 上，该工具无法访问从“本地主机”或您的内部服务器运行的网站。

提供了多种设备和浏览器，例如:

*   Window 8 上的 IE10、Firefox 20、Chrome 27 和 Opera 12
*   iPhone、iPad 和 Mac OS 上的 Safari，以及
*   谷歌 Nexus 和三星 Galaxy 上的安卓浏览器。

…但是该工具不可能考虑到所有的组合。在撰写本文时，您不能选择自己的设备、操作系统、浏览器、窗口大小或方向。

最后，截图是静态的。它们是检查字体、样式、设计和布局的理想选择，但它们不会突出 CSS3 动画、JavaScript 交互性或视频等媒体的错误。

对于更复杂的测试和调试，你需要直接控制，所以它回到真实的设备或虚拟机。或者，考虑使用 [BrowserStack](http://www.browserstack.com/) ，它在几十种设备上提供一系列实时和自动化的浏览器测试服务。[现代。IE](http://www.modern.ie/) 为你的网站提供三个月的无限制测试——从[虚拟化工具页面](http://modern.ie/en-us/virtualization-tools)注册。

也就是说，[现代。IE](http://www.modern.ie/) 截屏捕获工具在您实施快速修复并且不想花整个上午测试它时提供即时保证。一个伟大的除了你的质量保证工具包！

## 分享这篇文章