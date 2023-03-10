# 触发集会:释放 HTML5 游戏的潜力

> 原文：<https://www.sitepoint.com/trigger-rally-html5-game/>

毫无疑问，你玩过一些 HTML5 游戏。虽然它们在技术上令人印象深刻，但许多都是有限的演示或提供简单的游戏体验，让人想起我们在 80 年代和 90 年代玩的平台游戏、谜题和枪战。很少有游戏开发者认为网络浏览器是原生操作系统或 [Flash](https://www.sitepoint.com/adobe-shifts-flash-focus/) 代码的良好替代品。

我也这么想。直到我打了 [**触发拉力赛 Online**](https://triggerrally.com/drive?track=preview.json&car=car1.json) 。

![Trigger Rally Online](img/d9f449253bad67860d1d12106ec92d7f.png)

游戏是 [*触发拉力赛*](https://trigger-rally.sourceforge.net/) 的 JavaScript 端口，用 C++编写的一款面向 Linux 的开源单人 3D 赛车游戏。它使用了 canvas、WebGL 和 [three.js](https://mrdoob.github.com/three.js/) 库。

在线版可以在支持 WebGL 的浏览器中运行。如果你的显卡启用了 [WebGL，并且你可以在没有声音的情况下生活，那么 Chrome 和 Firefox 10 也是如此。即使在普通的硬件上，你也应该达到每秒 20 帧。如果你不能播放它，看一看](https://www.sitepoint.com/firefox-enable-webgl-blacklisted-graphics-card/)[的 YouTube 视频](https://www.youtube.com/watch?v=M31heMqQ0Ug)。

从技术上来说，Trigger Rally 使用 JSON 定义的高度地图来创建关卡几何图形，使用彩色地图结合图像来绘制几何图形，使用树叶地图来放置植物，使用检查点坐标来定义比赛路径。应该可以创建自己的地图，所以如果有人在使用在线编辑器，我不会感到惊讶。声音是使用网络音频 API 生成的。当你加速和减速时，一个标准的引擎循环会实时改变音调。很简单但是很有效。

很简单，这个游戏令人震惊。虽然它目前只限于一辆车和一个课程，但它展示了 HTML5 游戏的潜力。如果有什么不同的话，它比我见过的用 Flash 制作的任何 3D 游戏和例子都要好。

帮自己一个忙【调查】 [**触发拉力赛上线**](https://triggerrally.com/) 。毕竟今天是星期五。

## 分享这篇文章