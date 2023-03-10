# 通过有趣的实验提高您的 JavaScript 学习

> 原文：<https://www.sitepoint.com/improve-javascript-learning-experiments/>

这篇文章由[马克·布朗](https://www.sitepoint.com/author/mbrown)同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

学习永远不会停止——尤其是在 web 开发中。我们的行业在不断更新和完善自己，我们也应该如此！不幸的是，跟上进度可能会让人精疲力尽，但其实不必如此。在这篇文章中，我将向你展示小实验如何成为一种有趣而有效的方式来[学习新东西](https://www.sitepoint.com/how-to-learn-quickly/)，并确保学习和保持最新状态是有趣的。

## 保持实验的乐趣

你做副业吗？如果你这样做了，我敢肯定你曾经做过一个项目，一开始很有趣，但很快就变成了一团乱麻。陷入某件事可不是什么好玩的事，而且真的会损害你的工作和副业的积极性。这种情况的主要原因通常是范围蠕变或压力，而你应该在业余时间放松。通过实施一些规则，我们可以确保不会遇到不愉快的意外:

1.  准备你的工作。确保你确切知道一个实验有多大，包括哪些内容。如果太多，把它分成易于管理的小块，每个小块都有开始和结束，或者拔掉插头。
2.  **安排任务。**不要每晚熬夜。每周安排一到两个小时，一到两次。
3.  没有截止日期。包括你在内的每个人都需要放松。设定目标可以，错过目标也应该可以。
4.  给自己一些娱乐时间。虽然你不应该拖延，但生活不仅仅是待办事项清单上的一堆支票。花点时间享受你的劳动成果。

进行小实验并不意味着它们不能成为更大实验的一部分。以一个数据可视化项目为例。这可能会花费一些时间，但很容易分解成更小的任务:

*   设置构建工作流
*   找到并获得有趣的数据([这里有一个很酷的 API 列表，可以给你带来灵感](https://github.com/toddmotto/public-apis)
*   使用 Node.js 提取和处理数据，利用 ES2015 功能
*   比较并选择一种技术(canvas、WebGL、DOM/SVG)或库(d3.js、p5.js、THREE.js)来可视化数据
*   使用您选择的工具可视化数据

我用一块白板来记录我所有的实验。我在这里列出和计划它们，并添加相关信息，直到开始实验的时候。对于较大的项目，我倾向于专门为该项目创建一个新的板，并为每个任务添加卡片。任何替代方案都可以。记录事情让噪音远离我的头脑！

## 回顾实验

除了通过实际做新的东西来学习，实验也是提高自己的一个好机会。回顾你的实验，问自己以下问题:

1.  如果有更好的准备，执行过程中的问题是否可以避免？如果是，如何实现？
2.  已执行工作的质量如何，我能做些什么来改进？
3.  我能更有效地准备我的实验吗？

回顾和改进我们所做的事情会给我们带来经验，这在我们的行业中是非常宝贵的。沟通和记录也是如此。许多实验评论都是很棒的博客文章。

虽然与 JavaScript 不太相关，但我的一个特别有趣的实验是在 Sass 中创建[矢量图形，我将坐标图转换为路径，并将其栅格化为单像素的方框阴影。我不仅学习了以前不太熟悉的 Sass 功能，还必须研究一些算法。](https://www.sitepoint.com/vector-graphics-sass/)

![A boy doing experiments in the laboratory. Explosion in the laboratory.](img/ff231a90719919ba71debbd6ac1c9d22.png)

## 寻找灵感

如果你不确定下一步要学什么，手边有一些资源作为灵感总是好的。

如果你喜欢视觉和艺术， [CodePen](http://codepen.io/) 是一个很好的起点。这些拨片通常充满了令人惊奇的笔，值得拆开来看看它们是如何工作的。如果你更喜欢冒险，试试 [Dribbble](https://dribbble.com/) 或 [Behance](https://www.behance.net/) 。

SitePoint 由来自不同背景的许多作者提供支持。正因为如此， [JavaScript 频道](https://www.sitepoint.com/javascript/)充斥着各种 JavaScript 的美好。

### 运行时环境

您可能已经知道 JavaScript 可以在浏览器中运行，但是您知道您也可以在浏览器之外运行 JavaScript 吗？

*   Node.js 是目前最流行的非浏览器 JavaScript 运行时。使用它，您可以编写脚本、命令行界面或桌面应用程序等等。它可以在 Windows、OS X 和 Linux 上运行。
*   电子允许你使用传统的 HTML、CSS 和 JavaScript 来构建跨平台的桌面应用。
*   Cordova 允许你用 HTML、CSS 和 JavaScript 构建移动应用。
*   [React Native](https://facebook.github.io/react-native/) 允许你使用 React 框架构建移动应用。

### 浏览器 API

在过去的几年里，浏览器中引入了许多新的 API，允许你做各种有趣的事情。

*   服务工作者 API 是一个大型 API，它允许我们做各种事情，比如提供[离线支持](https://www.sitepoint.com/getting-started-with-service-workers/)。
*   [WebRTC](https://developer.mozilla.org/docs/Glossary/WebRTC) 是一个 API，它允许我们创建与另一台计算机的实时连接，并传递音频和视频数据。

一些 API 允许我们使用设备硬件来做一些事情，比如读取传感器或者让设备做一些事情(比如振动)。

*   [环境光传感器](https://developer.mozilla.org/docs/Web/API/Ambient_Light_Events)提供关于环境光亮度的信息，我们可以用它来调整我们的网站或应用程序。
*   [电池状态](https://www.sitepoint.com/introducing-the-battery-status-api/)相当自明；我们可以请求电池状态。较重的应用程序可以使用这个[来启用省电模式](https://www.sitepoint.com/using-the-battery-status-api-to-optimize-your-development/)。
*   [地理定位](https://www.sitepoint.com/html5-geolocation/)授予我们关于用户位置的信息。
*   我们可以利用[网络信息](https://www.sitepoint.com/use-network-information-api-improve-responsive-websites/)为客户端提供较小的文件。
*   [通知](https://www.sitepoint.com/introduction-web-notifications-api/)对于很多应用来说相当有用，比如聊天应用。
*   你在开发一个用鼠标输入的游戏吗？[指针锁](https://developer.mozilla.org/docs/Web/API/Pointer_Lock_API)是你的朋友。
*   [接近度](https://www.sitepoint.com/introducing-proximity-api/)让您了解用户离设备有多近。也许这可以用来检测拥抱？
*   [设备方向](https://www.sitepoint.com/using-device-orientation-html5/)允许我们检测方向何时改变。
*   [振动](https://www.sitepoint.com/use-html5-vibration-api/)允许我们振动一个设备，这对游戏来说很棒。

### 库和框架

大多数库的目的是让事情变得容易，所以有一些使用它们的经验不会有什么坏处。更流行的库和框架(比如 Angular 和 React)甚至可能是一项工作所需要的。一两个实验也是为面试做准备的好方法。

要知道库和框架只能存在这么久。虽然它们很有帮助，但是你应该能够理解它们所解决的问题，并且能够在没有它们的情况下解决那些问题。

#### 体系结构

*   [Angular](https://angularjs.org/) 是一个仍然被很多团队使用的 MVC 框架。
*   [React](https://facebook.github.io/react/) 是一个用来渲染视图的库。
*   [Polymer](https://www.polymer-project.org/) 是一个创建 Web 组件的库。
*   [Lodash.js](https://lodash.com/) 、[下划线. js](http://underscorejs.org/) 、[拉姆达. js](http://ramdajs.com/) 都是函数式编程库。一些团队喜欢使用它们，但许多团队不喜欢。不管怎样，很好地理解函数式编程是一项非常受欢迎的技能。

#### 视觉的

*   [D3.js](https://d3js.org/) 是一个图形和 DOM 操作库，如果你想用 SVG 或 HTML 渲染图形的话，它很好用。
*   [p5.js](http://p5js.org/) 是一个受[处理](https://processing.org/)启发的库(一个灵活的软件速写本和一种语言，用于[>学习如何在视觉艺术的背景下编码](http://<a%20href=))。它有丰富的绘图和计算功能。
*   Three.js 是一个高级 3D API，抽象出 WebGL 的难点。

### 物质世界

就我个人而言，当我可以编写软件在物理世界中做一些事情时，我真的很兴奋。如果你也这样做，考虑购买以下任何一套:

*   [树莓派](https://www.raspberrypi.org/)是一款信用卡大小的单板电脑，非常适合教学或学习。
*   Arduino 类似于 Raspberry Pi，但更侧重于电子方面。
*   [乐高头脑风暴](http://www.lego.com/mindstorms)是一台可以控制乐高硬件的迷你电脑。有什么不喜欢的？
*   [johny-Five](http://johnny-five.io/)是一个用于机器人和物联网的 JavaScript 框架，可以安装在 Arduino 或类似设备上。

所有这些都可以用 JavaScript 编程，或者能够运行 JavaScript。

## 结论

小实验是学习新事物的好方法。另一个好处是，他们可以帮助你改善工作方式，这在我们的行业中是最有价值的。通过执行一些规则，我们可以保持实验的乐趣，激励自己学习更多，变得更好。

对于那些墨守成规的人，我希望这篇文章能给你一些动力，让你开始一个新的有趣的实验。对于那些不确定下一步该做什么的人，我希望这篇文章能给你一些启发。或者你有一些很棒的实验想法？或者我错过了什么？不管怎样，我真的很想在下面的评论中听到你的意见。

## 分享这篇文章