# 快速提示:今天就在您的浏览器中尝试 WebAssembly 吧

> 原文：<https://www.sitepoint.com/try-webassembly-today/>

2015 年 6 月，JavaScript 的发明者和 Mozilla 的联合创始人 [Brendan Eich 宣布了一件非常令人兴奋的事情:WebAssembly。](https://brendaneich.com/2015/06/from-asm-js-to-webassembly/)

Eich 解释说 JavaScript 被称为 Web 的汇编语言[，他不同意这种说法，并继续介绍 WebAssembly，“*是 Web*上安全代码的一种新的中间表示”，正如他所描述的。在 Eich 宣布之前，谷歌、微软、Mozilla、苹果和其他一些公司已经在进行实验。](http://www.hanselman.com/blog/JavaScriptIsWebAssemblyLanguageAndThatsOK.aspx)

> WebAssembly，简称“wasm”。wasm 文件名后缀是一个新兴的标准，其目标是定义一个安全的、可移植的、大小和加载时间有效的二进制编译器目标，提供接近本机的性能 Web 的虚拟 CPU。

为什么需要 WebAssembly？嗯， [asm.js](http://asmjs.org/) 需要引擎为其进行优化，这使得解析器成为热点(字面意思——移动设备可以变得非常热)。这是因为需要传输压缩，这也节省了带宽，但在解析之前解压缩可能会很痛苦。此外，一旦浏览器本机支持 WebAssembly 格式，JavaScript 和 wasm 就可以分离，而不会在 JavaScript 中引入不安全或不适当的功能，只供开发一些完全不同的编程语言的编译器使用。

如果你需要一个更好的概述，Auth0 在这篇文章中很好地解释了 WebAssembly。

## WebAssembly 一年后

WebAssembly 的设计考虑了浏览器内外的几个用例。正如你所猜测的，wasm 可以用于图像/视频编辑、浏览器中的 AAA 游戏、实时增强、虚拟现实等等。几乎所有在网络上已经可以实现的东西，但是有可能更快更有效。但是 WebAssembly 在浏览器之外也很有用:服务器端应用程序、混合本地应用程序、不受信任代码的服务器端计算只是一些潜在的应用程序。

路线图也进展顺利。在过去的一年中，WebAssembly 社区小组取得了巨大的进步，产生了:

*   初始功能集和计划的未来功能的描述和基本原理；
*   规范和参考解释程序；
*   13，000 行测试用于验证 spec 解释器和浏览器；
*   二进制格式的初稿。

有了这些令人兴奋的承诺，浏览器供应商已经开始在他们的浏览器引擎中提供早期的 WebAssembly 实现。现在可以构建在多个实验性实现上运行的 WebAssembly 演示。

为什么是实验性的？这是因为二进制格式和 JavaScript 对 WebAssembly 的绑定可能会在接下来的几个月里发生不兼容的变化，直到第一个稳定的版本。对于压力测试或基准测试来说，实现在未来一段时间内还不够成熟。

然而，这是一个巨大的飞跃，因为所有的浏览器都在同一个页面上，当你想到 Internet Explorer 在过去一直在做的事情时，这是很棒的。

请通读 [Mozilla Hacks WebAssembly 文章](https://hacks.mozilla.org/2016/03/a-webassembly-milestone/)了解更多详情。

## WebAssembly 演示

让我们通过 [Firefox Nightly](https://nightly.mozilla.org/) 来尝试一下 WebAssembly 演示(它应该也能在 Chrome Canary 上工作)。确保你已经安装了最新版本的[夜间](https://nightly.mozilla.org/)，打开`about:config`并将`javascript.options.wasm`设置为`true`。

![Screenshot of the Angry Bots game](img/14775d3262f366f0b71e47b774dd420d.png)

看看这个[愤怒机器人](http://webassembly.github.io/demo/)的实验演示，这是一个已经移植到 WebAssembly 的 Unity 游戏。在这款自上而下的射击游戏中，你可以与机器人战斗，探索 3D 空间站。移动由 W、A、S、D 或方向键控制，瞄准由鼠标控制&射击。

## 接下来

在发布第一个稳定版本之前，WebAssembly 仍有一些主要任务要做:

*   需要定义正式的 WebAssembly 文本格式。
*   进一步减小二进制格式大小。
*   迭代 WebAssembly JavaScript API。
*   对编译器作者、工具作者、黑客和学生来说更容易理解的文档。
*   测试套件中增加了更多的测试。

## 韦伯，集合！

如果你想参与，你可以加入 W3C 上的社区组。实际的 WebAssembly 规范正在 GitHub 上的[规范库](https://github.com/WebAssembly/spec/)中开发。小组建议高级设计讨论应该继续在[设计库](https://github.com/WebAssembly/design/)通过问题和拉式请求进行。在你开始之前，确保通读[道德和职业行为准则](https://github.com/WebAssembly/design/blob/master/CodeOfConduct.md)。

兴奋了吗？你对 WebAssembly 的最新消息怎么看？

## 分享这篇文章