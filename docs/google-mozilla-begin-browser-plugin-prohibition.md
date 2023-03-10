# 谷歌和 Mozilla 开始禁止浏览器插件

> 原文：<https://www.sitepoint.com/google-mozilla-begin-browser-plugin-prohibition/>

您的 web 应用程序依赖于 Java、Silverlight 还是其他浏览器插件？为一些快速发展做好准备；插件的末日即将来临。更具体地说，谷歌将于 2014 年 1 月从 Chrome 中删除网景插件 API (NPAPI)。Mozilla 稍微温和一些，但是从 2013 年 12 月起自动加载将被禁用——插件仍然有效，但是用户必须点击才能玩。

早在 1996 年，NPAPI 就在 Netscape 2.0 中首次引入。Web 开发充其量只是初级阶段；web 标准还处于婴儿期，CSS 支持还不存在，JavaScript 刚刚出现在同一个浏览器中。甚至表格布局和间隔 gif 也还没有成为好的实践。NPAPI 使供应商能够创建可以部署到浏览器的特定于任务的代码。因此，您的鼠标悬停效果可以在 Java applet 或 Macromedia Flash(在被 Adobe 收购之前)中实现。插件给网络带来了多媒体。即使在今天，如果您想要一致的跨浏览器音频或视频，Flash 仍然是一个很好的解决方案，特别是如果您需要支持旧的浏览器。

## 插件问题

不幸的是，插件也是浏览器变慢和崩溃的最常见原因… *或者它们肯定为厂商提供了方便的借口*。智能手机和平板电脑的兴起也导致了进一步的复杂化；插件可能是大的可执行文件，不一定适合较小的触摸屏。T2 公开批评 Flash 并在 iOS 上禁止了它。不久之后，Adobe 放弃了 Flash Android 插件，开始专注于 HTML5。

## 标准解决方案

HTML5 否定了浏览器插件的大部分需求。网络标准允许本地动画、音频、视频、3D、游戏和硬件集成。该平台可能不像依赖插件那样稳定，但它正在迅速改进，而且供应商们(大部分)正在合作创建适用于所有浏览器的同质 API。

## 死亡插件…或者只是休息

在恐慌出现之前，谷歌和 Mozilla 的声明并没有完全禁止插件。

Chrome 还有第二个插件 API，名为 Pepper (PPAPI ),没有被移除。Adobe Flash Player 使用 PPAPI，因此它的未来在一段时间内是有保证的。此外，几个 NPAPI 插件将被列入白名单，以避免中断。其中包括:

*   银光
*   一致
*   谷歌地球
*   Java *(尽管目前因为安全原因被屏蔽)*
*   Google Talk
*   脸书视频

现在，包含基于 NPAPI 的插件的应用和扩展将从谷歌的网络商店中移除。

Mozilla 的点击播放功能已经在 Firefox 的桌面版和移动版中出现了一段时间。从 2013 年 12 月起，只有最新版本的 Flash 会自动启动。

我怀疑插件会在几年内变得无关紧要。特别是，在一段时间内，闪存仍将是桌面上无处不在的技术。然而，如果你仍然在开发基于插件的应用程序，是时候开始研究 HTML5 的替代品了。

另请参见:

*   Chromium 博客:告别我们的老朋友 NPAPI
*   [Mozilla 安全博客:让用户控制插件](https://blog.mozilla.org/security/2013/01/29/putting-users-in-control-of-plugins/)

## 分享这篇文章