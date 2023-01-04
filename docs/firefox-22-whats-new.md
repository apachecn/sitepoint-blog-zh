# 火狐 22 的新功能

> 原文：<https://www.sitepoint.com/firefox-22-whats-new/>

Firefox 22 于 2013 年 6 月 25 日发布，比 Firefox 21 晚了不到六周。与以前的版本相比，这并不令人印象深刻，所以让我们看看第 22 版提供了什么…

## WebRTC 支持

Firefox 加入了 Chrome，并提供了本地 WebRTC 支持。

WebRTC 允许您在没有插件或第三方软件(如 Skype)的情况下进行视频通话、音频通话和共享文件。该 API 相对较新，还处于实验阶段——使用它需要您自担风险。

一旦 Opera 转向 Blink，IE 将是唯一一个没有 WebRTC 的主流浏览器。微软拥有 Skype，因此该公司不太迫切需要启用 WebRTC，但我怀疑它会在某个时候出现。

有用的 WebRTC 链接:

*   [在你的站点中嵌入 WebRTC](https://hacks.mozilla.org/2013/05/embedding-webrtc-video-chat-right-into-your-website/)
*   [Talky.io](http://talky.io/)
*   [钾远程面试](http://codassium.com/)
*   [Open.tok](http://www.tokbox.com/)

## 汇编. js

asm.js 是 JavaScript 的一个严格子集，允许动态编译和显著的性能改进。如果编译器能识别 asm.js 代码，它就能以接近本机的速度优化和运行它。然而，它仍然可以在不支持 asm.js 的标准 JavaScript 引擎中运行。

js 是一个有趣的想法，已经被几个游戏演示所采用。我怀疑这可能会盖过谷歌的 Dart 项目，该项目需要学习一种新的语言，并且只在 Chrome 中得到支持。

## CSS3 柔性盒

Flexbox 是一种 CSS3 布局模式，它允许您定义当页面适应不同的屏幕尺寸时元素的行为。这里有太多需要解释的内容，所以请尽快关注 SitePoint 上的 flexbox 文章。

Flexbox 在 Firefox、IE11 和 Opera 中不受前缀支持(不幸的是，在迁移到 Blink 渲染引擎后，Opera 似乎将恢复到-webkit 前缀)。Chrome 和 Safari 需要-webbit 前缀。IE10 需要-ms 前缀。

## 开发人员工具更新

想知道页面使用的是什么字体吗？打开 Firefox 的检查器(菜单> Web 开发者>检查器)，它会告诉你…

![Firefox font inspector](img/5c390ade64f1db56bd67ec023edb2f68.png)

在那里，点按“高亮着色区域”图标:

![Firefox inspector icons](img/64249d96f1ec79bc06dbc1676b83aee9.png)

重绘元素时，颜色将覆盖在页面上。这很有用；重画是最昂贵的浏览器活动之一，该工具将有助于识别性能问题。

您还会注意到一个新的“Dock to side”图标，它将工具停靠在浏览器窗口的右侧。

Firefox 开发工具并没有完全取代 Firebug，但是它们速度更快，我发现自己越来越经常地使用它们。

## HTML5 音频和视频播放速度

右键单击本机播放器中播放的任何媒体，您可以选择播放速度:慢动作、正常、高和滑稽。你可以用一半的时间听乏味的播客！

## 显示缩放

Firefox 22 支持缩放选项来调整高分辨率显示器上的文本大小。这目前只在 Windows 上可用(难道在 Mac 上不会更有用吗？)

缩放由 about:config 中的**layout . CSS . devpixelsperpx**控制。默认值为-1-您可以输入 0.05 之间的任何值；大于 1.0 的数字会增加文本大小。

## 杂项更新

一些微小的变化引起了我的注意…

*   支持 HTML5 `<time>`和`<data>`元素
*   默认情况下，CSS3 @supports 处于启用状态
*   实现了新的 [Web 通知 API](http://notifications.spec.whatwg.org/)
*   指针锁 API 可以在全屏之外使用
*   纯文本文件是自动换行的(这似乎是显而易见的——为什么在 1995 年没有在浏览器中实现呢！)
*   WebGL 性能改进
*   改善了渲染图像时的内存使用
*   社会服务经理
*   Mac OSX:文件下载进度显示在 dock 图标上
*   Firefox mobile:支持小屏幕平板电脑

Firefox 可能正在失去市场份额，但版本 22 令人印象深刻。即使你已经转向 Chrome，Firefox 仍然是网络开发者的基本应用。

## 分享这篇文章