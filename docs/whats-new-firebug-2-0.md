# Firebug 2.0 的新特性

> 原文：<https://www.sitepoint.com/whats-new-firebug-2-0/>

[火狐 30](http://www.mozilla.org/en-US/firefox/30.0/releasenotes/) 于 2014 年 6 月 10 日发布。这有点平淡无奇，但 Mozilla 有权在浏览器的[29 版](/firefox-29-mozilla-browser-upgrade/)重大检修后休息一下。最*有趣的*变化:

*   一个新的 [`background-blend-mode` CSS3 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/background-blend-mode)，它定义了背景颜色和图像如何混合在一起(叠加、相乘、变亮等)。)
*   开发人员控制台中的方框模型高亮显示和 [`console.count`](/three-little-known-development-console-api-methods/) 支持。
*   现在无法用`-moz-appearance: none`或`text-indent`黑客( [Bugzilla 649849](https://bugzilla.mozilla.org/show_bug.cgi?id=649849) )移除`select`框上的默认下拉箭头样式。谢谢 Mozilla——我的表格看起来又很糟糕，我现在不能写那篇文章了！

更重要的是， [Firebug 2.0](https://getfirebug.com/) 已经推出。考虑到这个项目已经有将近十年的历史，以及当前版本号快速增加的趋势，达到这个里程碑需要一段时间。随着供应商改进原生浏览器工具，世界上第一个(好的)开发者控制台开始落后于它的模仿者。

Firebug 2.0 实现了几个新特性和增强功能…

## 界面更新

界面已刷新。面板可以在 Firebug 主菜单中显示或隐藏:

![](img/240b32966a0afc72a5b8401514b664ad.png)

此外，我们最终获得了语法突出显示:

![](img/8526eece2a1fca9bc527d4c78c4eff36.png)

## 事件检查器

在 **HTML** 选项卡中新的**事件**面板列出了所有附加到所选 DOM 元素的事件，并链接到脚本处理程序。Chrome 的 Inspector 有这个功能已经有一段时间了，它非常有用:

![](img/e5dfd08236e16f9e48361d2b50da8917.png)

## JavaScript 表达式检查

通过将光标悬停在代码上，可以检查任何变量或表达式的值:

![](img/8de67ae65c64c72c88291abb28be9371.png)

## 改进的自动完成

代码自动完成在大多数面板上都可用—按 tab 打开建议下拉列表并选择一个项目。在编辑 HTML/SVG 属性时以及在断点条件下，也可以使用它:

![](img/f57a70a2ee6e07d2c33cf4b5dbb1d902.png)

## 杂项更新

除了常见的错误修复和速度改进，您还可以期待使用以下功能:

*   可以使用“{}”图标定义脚本。
*   通过单击开始标记的“>”，可以快速添加 HTML 属性。
*   来自同一行的控制台消息被分组(取消选择**控制台**选项卡下拉菜单上的**分组日志消息**以显示所有消息)。
*   搜索框允许 CSS 选择器和正则表达式。
*   CSS 颜色值现在可以在 **CSS** 面板下拉菜单中显示为“已创作”(以及十六进制、RGB 或 HSL)。
*   可以通过从控制台发出一个`getEventListeners(element)`命令来检查注册的突变观察者。

## 萤火虫的未来

Firefox 的原生开发工具很棒，但当我需要更深入的分析时，我还是会转向 Firebug。它仍然是我最喜欢的开发者控制台，但这可能是因为我从早期就开始使用它了。有些人会抱怨它没有其他地方的功能，尽管第三方扩展远远弥补了不足。

随着时间的推移，开发可能会减少，因为供应商们正在竞相添加更好的浏览器工具。然而，如果没有乔·休伊特革命性的附加功能，这可能永远不会发生。让非厂商创建不受商业、政治和技术压力阻碍的开发工具是有益的。

如果你是一名网络开发人员，你需要 Firefox，Firebug 应该是你安装的第一个插件。

## 分享这篇文章