# 火狐 3.6 现已发布

> 原文：<https://www.sitepoint.com/firefox-36-released/>

Mozilla 终于发布了 Firefox 3.6。您可能已经获得了更新；如果没有，选择帮助>检查更新…并按照向导进行操作。

## 升级安全吗？

主要问题是附加组件的兼容性。几个月来，Mozilla 一直在与开发者合作，以确保插件兼容，并且大多数流行的扩展已经可以工作。

但是，您可能会遇到失败的加载项。幸运的是，改进的升级过程将在附加组件不兼容时警告您，并提供中止安装或禁用附加组件，直到新版本发布。

## 对用户来说有什么新的？…

你会注意到的第一个改进是速度。Firefox 3.6 启动速度明显加快，性能有所提高，内存使用量减少，浏览器的响应速度更快。谷歌 Chrome 用户可能仍会嘲笑，但这是朝着正确方向迈出的一步。

火狐人物角色是轻量级主题，无需重启浏览器即可应用。本质上，人物角色是应用在浏览器控件后面的一些图像(类似于 Chrome 的主题)。就我个人而言，我认为它们是一个无聊的噱头，但我肯定许多用户会喜欢它们。

最后，由于组件目录锁定，稳定性应该会提高。它可以防止第三方开发者将扩展偷偷放入无法卸载的浏览器中……据说是为了*“你的”*的利益。

## …开发者呢？

我们有许多新玩具可以玩。更有趣的功能包括:

*   CSS 现在提供了[渐变](https://hacks.mozilla.org/2009/11/css-gradients-firefox-36/)、[多重背景](https://developer.mozilla.org/en/CSS/Multiple_backgrounds)和[可缩放图像](https://developer.mozilla.org/en/CSS/Scaling_background_images)。
*   支持新的 [Web 开放字体格式(WOFF)](https://hacks.mozilla.org/2009/10/woff/) 。
*   打开原生视频可以全屏显示。也支持标志帧(播放前代表视频的图像)。
*   还有进一步的工具用于选择和处理文件，比如从桌面拖拽文件到浏览器的 T2。
*   可以将 async="true "属性添加到 JavaScript `script`标签中，以指示代码可以异步加载和执行。
*   [方向事件](https://developer.mozilla.org/en/Detecting_device_orientation)可用于检测移动电话等设备何时旋转。这可以用来将页面从纵向视图切换到横向视图，反之亦然。
*   JavaScript 函数实例的原型属性不再是可枚举的。我需要对此进行进一步的测试，但它可以防止许多与脚本扩展本机对象功能相关的问题。
*   Firefox 的 JavaScript 引擎 TraceMonkey，[更快](https://hacks.mozilla.org/2010/01/javascript-speedups-in-firefox-3-6/)。

这些都是很棒的新增功能，但是要注意，许多功能只能在 Firefox 中使用，在其他浏览器中可能会被忽略或出错。

总的来说，Firefox 3.6 是一款很棒的浏览器。如果你是火狐用户，没有理由不升级。速度的提高也可能让一些不满的用户回到 Mozilla 的怀抱。

有用的链接:

*   [获得火狐 3.6](http://www.getfirefox.com/)
*   [发行说明](http://www.mozilla.com/firefox/3.6/releasenotes/)
*   Firefox 人
*   [如何创建人物角色](http://www.getpersonas.com/demo_create)
*   面向开发者的 Firefox 3.6

## 分享这篇文章