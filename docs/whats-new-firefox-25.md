# 火狐 25 的新功能

> 原文：<https://www.sitepoint.com/whats-new-firefox-25/>

Firefox 24 发布于 2013 年 9 月中旬，有点平淡无奇。除非你是一名 Java 开发人员，对 Mozilla 取消插件“总是激活”的决定不感兴趣。*(说真的，现在还有人在创建 Java 小程序吗？)*

火狐 25 于 10 月 29 日发布。新功能列表可能比我们过去看到的要小，但浏览器中潜伏着一些宝石。像往常一样，你可以通过等待自动更新，从**帮助**菜单中点击**关于火狐**，或者访问【firefox.com】的[来安装。](https://www.firefox.com/)

## 网络音频 API

[网络音频 API](https://www.w3.org/TR/webaudio/) 允许用 JavaScript 进行音频处理和合成。换句话说，您可以创建自己的声音效果并将滤镜添加到音频剪辑中。我甚至不打算解释它；API 的选项会挑战最有知识的音响工程师。

网络音频最明显的用途是游戏，尽管声音效果可以增强许多应用程序的用户界面——特别是在移动设备上，在那里你不需要一直看着屏幕(参见[网络应该连接声音吗？](https://sitepoint.com/website-sound-effects/))

Mozilla 创造了一个伟大的《我的世界》风格的演示，名为[Diridum 之歌](https://labs.gooengine.com/mozlod/) …

[![Songs of Diridum](img/0f437a0db0b15e665856d1015fcd0434.png)](https://labs.gooengine.com/mozlod/)

移动使用 W，A，S，D/光标键，鼠标和空间跳转。探索地图，你会注意到声音和音乐变得越来越不清晰。您还可以配置正在演奏的乐器。

Web Audio API 现在在 Gecko 和 Webkit/Blink 浏览器中得到支持(带有 Webkit 前缀)——只有 IE 不支持。我怀疑我们大多数人都需要一个像样的库才能实现广泛部署。

## 特定于选项卡的查找栏

“在页面中查找”栏界面已经简化。此外，它不再在选项卡之间显示或共享。这似乎是一个进步，因为你现在可以在不同的页面上搜索不同的字符串——代价是必须重新打开这个栏。

## CSS3 背景-附件:本地

在 CSS2.1 中，`background-attachment`属性可以设置为:

*   `scroll` —背景图像随页面滚动
*   `fixed` —当页面滚动时，背景图像在视窗内保持静止，或
*   `inherit` —使用应用于父元素的任何属性。

当应用于`body`时，这些工作如你所料，但是没有考虑到单个元素也可以滚动。

从 CSS3 开始，你现在可以使用`local`。这相对于元素的内容定位背景。因此，如果该元素使用了`overflow: scroll`或`overflow: auto`，那么当元素滚动时，背景也会滚动。但是当页面滚动时，它不会移动。

Firefox 是最后一个加入对`local`支持的浏览器；已经有 IE9+、Chrome 4+、Safari 5+和 Opera 10.5+版本。

## 杂项更新

还进行了一些小的改动:

*   探查器工具可以保存和导入测试结果。
*   Iframes 可以使用内嵌内容。
*   额外的 ECMAScript 6 函数可用。
*   Android 上的访客浏览允许你共享你的手机，而那个人看不到你打开的标签页，历史记录等等。
*   移动加载项现在可以在地址栏中添加指示器。
*   Android 上有联系人 API。
*   常见的过多错误和安全问题已经得到解决。

新的 Australis 主题带有类似 Chrome 的圆形标签和更简单的图标，已经被放回到 2014 年 3 月 4 日发布的 Firefox 28 中。一些人推测它会出现在 Firefox 25 中，所以不要期望浏览器在几个月内会是这个样子…

[![Firefox Australis](img/805839a079c5ce6d5e633a883ba84e32.png)](https://www.getaustralis.com/)

Firefox 26 应该会在 2013 年 12 月中旬发布。

## 分享这篇文章