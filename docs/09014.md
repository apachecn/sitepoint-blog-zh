# jQuery 是否过于臃肿？试试 jquip。

> 原文：<https://www.sitepoint.com/jquip-lightweight-jquery/>

对于 jQuery 开发人员来说，这是忙碌的几周。我们已经收到了 [jQuery 1.7](https://www.sitepoint.com/jquery-17-whats-new/) 和 [jQuery Mobile](https://www.sitepoint.com/jquery-mobile-1-released/) ，但是第三个项目正在争夺我们的注意力…

对于 32KB 多一点的压缩下载，很少有人会指责 jQuery 过于肥胖。但是你很少需要它所有的能力。输入 [jquip —或 jQuery-In-Parts](https://github.com/mythz/jquip)；一个更小、更精简、更快速的库分支。它包含了 90%的最佳部分，而大小仅为 4.28KB(即使未压缩的源代码也只有 21.5KB)。

在这个小小的封装中，您可以获得许多功能:

*   主$( *选择器*)
*   DOM 遍历方法，比如 eq，first，last，slice，prev，next，siblings，children 等。
*   DOM 修改方法，如追加、前置和删除
*   CSS 类修改方法，如 hasClass、addClass 和 removeClass
*   事件方法，如与所有标准类型(单击、鼠标输入、鼠标离开、提交等)绑定和解除绑定。)
*   实用函数，如 each、trim、extend、merge、isArray 等。

这个库有一个模块化的架构，所以其他的 jQuery 工具可以作为插件使用，当你需要它们的时候可以包含它们:

*   **document ready**:$(function())和$。准备好的
*   **css** : css，宽度和高度方法
*   ajax : ajax、get 和 getJSON 方法
*   **自定义**:query string、事件对象 isTab、isShift、cancelEvent 等各种方法

[发行包](https://github.com/mythz/jquip/downloads)以独立脚本的形式在一个 20KB 的 jquip 文件中提供插件 jquip 压缩后只有 7.84KB。最终，jquip 的开发者 Demis Bellot 和 Jey Balachandran 希望将所有缺失的 jQuery 方法实现为插件。

我印象深刻。在一个单片数兆字节页面的时代，它证明了一些开发人员仍然关心敏捷的轻量级代码。鉴于性能适中的智能手机、电子阅读器和平板电脑越来越受欢迎，有充分的理由减肥。jquip 可能是你网站减肥方案的神奇减肥药。

## 分享这篇文章