# Canvas 与 SVG:为工作选择合适的工具

> 原文：<https://www.sitepoint.com/canvas-vs-svg/>

**HTML5 Canvas 和 [SVG](https://www.sitepoint.com/svg-101-what-is-svg/) 都是基于标准的 HTML5 技术，你可以用它们来创建令人惊叹的图形和视觉体验。我在本文中提出的问题如下:在项目中使用哪一个重要吗？换句话说，有没有比 SVG 更喜欢 HTML5 Canvas 的用例？**

首先，我们花点时间介绍一下 HTML5 Canvas 和 SVG。

## HTML5 画布是什么？

下面是 [WHATWG 规范如何引入`canvas`元素](https://html.spec.whatwg.org/multipage/scripting.html#the-canvas-element):

> 元素为脚本提供了一个依赖于分辨率的位图画布，可用于动态渲染图形、游戏图形、艺术或其他可视图像。

换句话说，`<canvas>`标签暴露了一个表面，您可以在其中使用 JavaScript 可编程接口逐个像素地创建和操作栅格化图像。

下面是一个基本代码示例:

参见 [CodePen](https://codepen.io) 上 SitePoint([@ SitePoint](https://codepen.io/SitePoint))
的笔 [基本画布形状演示](https://codepen.io/SitePoint/pen/LYbOBgm)。