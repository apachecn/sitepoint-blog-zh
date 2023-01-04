# javascript dom

> 原文：<https://www.sitepoint.com/javascript-doom/>

请原谅明目张胆的链接诱饵标题。JavaScript 就可以了。世界上最常用的编程语言有一个安全的未来——特别是现在 id Software 的厄运已经被转换为在浏览器中运行！

如果你已经被过度兴奋所征服，停止继续阅读，点击链接:
[developer.mozilla.org/demos/detail/doom-on-the-web/](https://developer.mozilla.org/demos/detail/doom-on-the-web/)

这款游戏可以从 Mozilla 的演示工作室获得。展示 Firefox 和其他浏览器中 HTML5、CSS3 和 JavaScript 技术的资源。

**note:** Where’s it gone?

这篇文章一出现，Mozilla 就撤下了 Doom 演示。我不知道为什么，这可能只是暂时的——我怀疑是他们的服务器过载了。在我们等待 JavaScript 末日的时候，可以在 YouTube 上观看它的视频。

我意识到 SitePoint 吸引了远比我年轻的读者。如果你从未听说过《毁灭战士》，这是一款第一人称射击游戏，于 1993 年在 PC 上发布。虽然这不是第一个 FPS-id 软件的 Wolfenstein 3D 可以声称皇冠 Doom 彻底改变了这一类型。它开创性的 3D 图形、多人游戏和图形电锯暴力是当今许多大片的灵感来源。

以现代标准来看，末日正在显示它的年龄。它有过时的块状 VGA 图形，2D 地图(走道不能通过另一个)和有限的控制(没有跳跃或垂直瞄准)。但游戏仍然令人震惊，厄运已经被转换为一系列游戏机和手持设备。现在它已经被移植到 JavaScript，可以在没有插件的浏览器中播放。

不幸的是，JavaScript Doom 在 Chrome 中非常慢，并且不能在 IE 中运行。据报道，Safari 的一些版本可以工作，但我在 5.0.5 版本中的体验并非如此。不过在火狐 4 和 Opera 上效果不错；一台中档电脑应该达到每秒 20-30 帧——可能比我 1993 年用来玩《毁灭战士》的老款 486 还要好！有一些图形故障，但它是可玩的。

令人惊讶的是，这款游戏使用 [Emscripten](http://emscripten.org/) 和 [Clang](http://clang.llvm.org/) 从 C 编译成 JavaScript，然后用[谷歌的闭包编译器](http://code.google.com/p/closure-compiler/)进行优化。视频输出在标准 HTML5 `canvas`元素上呈现。声音是用 Mozilla 的非标准音频数据 API 处理的，但是效果太糟糕了，你不会想要的！如果你感兴趣，可以下载源代码，尽管 JavaScript 被缩小了，无法阅读。

## 如果你认为那令人印象深刻…

法布里斯·贝拉用 JavaScript 开发了一个 x86 PC 模拟器。为了好玩。

它可以在所有最新的浏览器中运行，所以他安装了 2.6.20 Linux 内核并发布了基于浏览器的模拟器。是的，它在网页中运行 Linux。

该演示仅限于在 HTML 表中呈现终端输出，但其含义是惊人的。我唯一担心的是有人会添加 X11，安装一个浏览器并递归地内爆网络！

你还见过其他优秀的前沿 JavaScript 例子吗？

## 分享这篇文章