# 快速有效的 CSS 测试

> 原文：<https://www.sitepoint.com/cssrefresh-instant-css-testing/>

我们大多数人使用以下技术开发级联样式表:

1.  在浏览器中打开项目 URL。
2.  编辑一些 CSS 代码。
3.  点击浏览器刷新按钮。
4.  发誓。
5.  从第二步开始重复，直到完成/失败。

它可以工作，但是当你在 CSS 区域的时候，每隔几秒钟按一次 F5 键会降低你的速度！有各种解决方案可以在发生变化时自动刷新，但大多数都需要特定的 IDE 和/或浏览器插件。许多重载整个页面——不仅仅是 CSS。

[**CSSrefresh**](http://cssrefresh.frebsite.nl/) 是来自网络工程师[弗雷德·豪森](http://www.frebsite.nl/)的一个伟大解决方案。这是一个 [JavaScript 文件](http://cssrefresh.frebsite.nl/js/cssrefresh.js)，你可以下载并包含在你的页面中。或者，您可以将代码作为浏览器书签安装，并在您开始 CSS 开发时启动它。

假设您的页面从远程或本地 web 服务器运行，CSSrefresh 使用 Ajax 每秒检查一次每个样式表文件。当文件日期/时间被修改时，脚本会动态加载 CSS 文件并立即应用。如果你使用 [LESS](http://lesscss.org/) 、 [Sass](http://sass-lang.com/) 、手写笔或任何其他 CSS 预处理器，它也可以工作。

简单、聪明、有效，可能是我最近遇到的最有用的免费开发工具之一。给自己抢[**CSS refresh**](http://cssrefresh.frebsite.nl/)…

## 分享这篇文章