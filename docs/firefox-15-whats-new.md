# 火狐 15 的新功能

> 原文：<https://www.sitepoint.com/firefox-15-whats-new/>

又到那个时候了。[火狐 14 正式成为‘老’浏览器](https://www.sitepoint.com/firefox-14-whats-new/)；是时候欢迎 Firefox 15 进入您的电脑了。

我喜欢第 14 版。改进一直在稳步进行，但这个版本感觉比以前更稳定、更流畅。除了一些小的闪光灯故障，一切都很好。Mozilla 能在这个成功的基础上再接再厉吗？我认为他们已经做到了——尤其是如果你是一名网络开发人员…

## 响应设计视图

哦——一个很酷的新功能！虽然有几个在线和 [bookmarklet](https://www.sitepoint.com/responsive-web-design-tool/) 响应式网页设计工具，Mozilla 是第一个在浏览器中直接构建浏览器的供应商(菜单>网页开发者>响应式设计视图):

[![Responsive Design View](img/f0a4d42a949291eba8814d654ea549b9.png)](https://blogs.sitepointstatic.cimg/tech/726-firefox-15-rwd.png)

可以选择一系列典型的分辨率，或者您可以拖动框右下角的(很难看到的)调整大小控制。这很简单，但是很有效。您再也不需要调整整个浏览器窗口的大小。

## 检查器布局视图

页面检查器中添加了一个方框布局视图(“菜单”>“Web Developer”>“检查”)，然后点按右下角的蓝色栏:

[![Inspector layout view](img/ef978eb60c17dcc4833c98d9a39b5b59.png)](https://blogs.sitepointstatic.cimg/tech/726-firefox-15-inspector.png)

该图形指示了框的宽度和高度、填充、边框和边距。这是 Firebug 和其他几个插件中的一个特性，但是在裸 Firefox 安装中使用它很有用。

## JavaScript 调试器

Firefox 终于有了自己的 JavaScript 调试器！(菜单> Web 开发人员>调试器):

[![JavaScript Debugger](img/c0537439af778cad8ce299c33c5877ca.png)](https://blogs.sitepointstatic.cimg/tech/726-firefox-15-debugger.png)

为 Mozilla 说句公道话，他们的 [Venkman 调试器](https://developer.mozilla.org/en-US/docs/Venkman)已经问世十多年了，但几年前就停止了开发。同样，Firebug 中提供了 JavaScript 调试，但是本地替代方案可能更快——尤其是如果您只关心脚本问题的话。

不幸的是，调试器还不能正常工作。如果您将断点应用到计时器函数和事件处理程序，这是没问题的，但是一旦您刷新页面，它就会消失并清除您的设置。

我将再次讨论 v16 中的特性——特别是因为将实现远程移动调试。

## 静默更新

火狐 15 可能是你所知道的最后一次更新。从现在开始，所有的更新都将在后台进行。

这对开发者来说是个好消息:大多数 Firefox 用户将会快速、轻松地迁移到最新版本。系统管理员可能不太高兴；一个新版本可能会破坏一个任务关键型应用程序——尽管[自动更新可以被禁用](http://www.mozilla.org/en-US/firefox/organizations/faq/)。

我只希望它比谷歌的更新程序更高效一点，后者可以占用较慢的机器 10 分钟或更长时间。

## 杂项改进

还觉得无趣吗？也许这些改进中的一个会让你喜笑颜开:

*   CSS 属性:normal(缺省断字)、break-all(在任意字符之间断字)和 keep-all(不断字)
*   针对附加组件优化内存使用
*   一个新的 JavaScript 支持的 PDF 查看器(您需要在 about:config 中将 **pdfjs.disabled** 设置为 false)
*   web 控制台的性能得到了提升
*   支持 SPDY 网络协议 v3
*   一种新型高精度事件定时器
*   使用压缩纹理增强 WebGL 以提高性能
*   对 Opus `audio`编解码器的本机支持
*   `audio`和`video`中的`played`属性支持，表示已经播放的范围
*   `source`元素[支持媒体属性](https://developer.mozilla.org/en-US/docs/HTML/Element/source)
*   麦蒂里的本地化。

火狐 15 是一个伟大的更新。Mozilla 可能已经输给了 Chrome，但 Firefox 仍然是一个有能力的浏览器，它会随着每个版本的发布而改进。没有它，我不想开发 web 代码。

Firefox 15 适用于 Windows、Mac 和 Linux 电脑。你可能已经有了，但是如果没有，去 getfirefox.com 吧。

## 分享这篇文章