# 2015 年超越 Web 的 JavaScript

> 原文：<https://www.sitepoint.com/javascript-beyond-the-web-in-2015/>

2015 年是物联网的大年。我们已经看到设备的尺寸和功能取得了巨大进步，像微软和三星这样的大公司正在真正进入这一领域，物联网社区整体开始变得越来越大！在过去的两年里，在 SitePoint，对我来说回顾物联网和 JavaScript 的这一年已经成为一种传统(参见[Web 之外的 JavaScript](https://www.sitepoint.com/javascript-beyond-web/)和[2014 年 Web 之外的 JavaScript](https://www.sitepoint.com/javascript-beyond-web-2014))。虽然 JavaScript 作为物联网(IoT)语言的最初宣传和兴奋似乎在 2015 年有所平息，但 JavaScript 仍然继续成为一个非常强大的选项，在更多的物联网平台上实现魔力，这比人们意识到的要多。

在这个概述中，我们将看看物联网中的一些重大变化，这些变化将为 JavaScript 开发人员带来新的可能性，并将 JavaScript 的潜力扩展到 web 之外。

## Tessel 2

![The Tessel 2 (Photo credit: Tessel)](img/31bd19eda1910abd9917afafec1e0519.png)

Tessel 2(图片来源:Tessel)

Tessel 是一个微控制器(类似于 Arduino ),运行 JavaScript 而不是 c 等典型语言。它是帮助 JavaScript 爱好者跃入物联网的完美设备。去年，它被运往世界各地，人们用它制作了一些漂亮的 T2、整洁的 T4 和 T5 的东西。Tessel 2 今年发布了预购订单，并从第一代 Tessel 进行了一些非常令人兴奋的升级。

Tessel 能够运行各种 npm 包，但不能运行 Node.js 本身，因此 Tessel 经常需要专门为常用包构建兼容性。Tessel 2 通过开箱即用运行真正的 Node.js 对此进行了极大的改进。光是这个事实就让我瞬间预购了。对 npm 模块的访问给该微控制器带来了很大的潜力。

它还有两个 USB 端口，可以访问 USB 设备(如网络摄像头)以及现成的 Tessel 模块和 GPIO 端口(通过跳线将各种电子设备直接连接到引脚)。

### 通过 JavaScript 参与进来

*   [预购 Tessel 2](https://tessel.io)–从他们的网站预购 Tessel 2，并开始计划您的创作！Tessel 2 应该会在 2016 年 1 月到达。
*   官方 Tessel 文档–你已经可以开始阅读 Tessel 开发了(Tessel 1 开发应该和 Tessel 2 非常相似)。

## 开放式混合

![OpenHybrid in action (Photo credit: OpenHybrid)](img/e450a91e2eb8b4196a53df1c021d1724.png)

OpenHybrid 在行动(图片来源:OpenHybrid)

增强现实是控制我们周围物联网的一种有趣的替代方法。您可以通过智能手机应用程序等增强现实界面查看对象，并以直观和无限制的方式控制它们，而不是对对象进行物理控制！各种公司都在寻找实现这一点的方法，但在 2015 年，麻省理工学院媒体实验室揭示了(并开源了)一种令人印象深刻的方法，称为开放式混合。JavaScript 物联网开发人员可能会对该解决方案非常感兴趣，因为它允许通过包括 HTML 和 JavaScript 在内的 web 技术进行应用开发。虽然增强现实仍处于早期阶段，但现在是时候开始修补它与物联网的潜力了！

### 通过 JavaScript 参与进来

*   [官方开放混合页面](http://openhybrid.org/index.html)–这里有您下载平台和开始使用所需的一切。
*   [OpenHybrid 在 O'Reilly Solid 的演讲](http://openhybrid.org/solid.html)-在 O'Reilly Solid 大会上关于这一概念的精彩演讲，展示了它的潜力。

## 三星的 IoT.js 和 JerryScript

![The Samsung IoT.js and JerryScript pages](img/72fb283968546d537779fb8621230078.png)

Samsung IoT.js 和 JerryScript 页面

三星已经投入了大量资源来使 JavaScript 成为物联网的语言。2015 年，他们开源了物联网的 JavaScript 引擎 [JerryScript](https://samsung.github.io/jerryscript/) 。它允许 JavaScript 在小型、资源受限的设备上运行，如物联网中常用的微控制器。对我来说，这听起来类似于 Tessel 在 Tessel 的第一次迭代中试图整合的东西，但规模更大，对更多小型物联网设备开放。

[IoT.js](https://samsung.github.io/iotjs/) 是三星在物联网生态系统中启用 JavaScript 的又一努力。它和 JerryScript 差不多同时开源。IoT.js 是一个使用 JavaScript 为设备创建互操作平台的框架。它被描述为 Node.js 的一个轻量级版本，但是我还没有亲自使用它来真正感受这种描述有多准确。

JerryScript 和 IoT.js 都还处于早期阶段，因此看到它们在整个 2016 年的进展将是令人兴奋的。我热切希望在某个时候与三星 SmartThings 平台集成，但我还没有听到任何关于这一点的提及！

### 通过 JavaScript 参与进来

*   [Jerry script 官方页面](https://samsung.github.io/jerryscript/)–Jerry script 官方页面包含下载引擎和入门指南的链接。
*   [IOT . js 官方页面](https://samsung.github.io/iotjs/)–IOT . js 官方页面也有下载链接和入门指南。

## 火花变成粒子

![The Particle Electron (Photo credit: Particle)](img/844c754a79c417679034d33ee7c12cba.png)

粒子电子(照片来源:粒子)

2015 年，火花变成了粒子。Spark 提供了支持 Wi-Fi 的微控制器和他们自己的云服务，允许为物联网构建设备的简化方法。特别是从 JavaScript 的角度来看，他们有 JavaScript SDK 和各种其他兼容的第三方 JavaScript 插件。今年更名后，Particle 的设备范围扩大了。

他们开始推出一款速度更快、更可靠的微控制器 Photon，并将一款支持 2G/3G 的微控制器 Electron 放到 Kickstarter 上(无需 Wi-Fi 连接即可保持设备在线！).这款电子产品现在可以从他们的网站上预订，预计将于 2016 年 1 月发货。

最终，他们的设备系列中的这些发展继续支持 JavaScript API，所以 JavaScript 开发者在今年和以后有一系列新的可能性！围绕 Particle 的开发者社区也令人难以置信。

### 通过 JavaScript 参与进来

*   [粒子原型设备](https://www.particle.io/prototype)-提供每种设备功能概述和购买链接的官方页面。
*   [ParticleJS 文档](https://docs.particle.io/reference/javascript/)–官方 ParticleJS 文档。

## 微软 Windows 10 物联网核心

![Starter Pack for Windows 10 IoT Core with a Raspberry Pi (Photo credit: Microsoft and Adafruit)](img/2b1c58ef552f21821cb7e493cdfec53e.png)

Windows 10 物联网核心的入门包，带有一个树莓 Pi(图片来源:微软和 Adafruit)

微软今年向全球发布了 Windows 10，为包括物联网在内的多种设备提供了一个通用的 Windows 平台。他们将 JavaScript 开发人员放在心上，并通过 Chakra JavaScript 引擎在其 Windows 10 物联网生态系统[中提供 Node.js 访问。目前，我见过的与 Windows IoT 配合使用的主要设备是 Raspberry Pi 2。也支持 MinnowBoard Max 和 DragonBoard410c。随着时间的推移，随着生态系统的发展，我们可能会看到更多设备获得支持。Windows 10 物联网核心的一个重要区别是，它不是一个全功能的 Windows 10 操作系统。它是一个面向物联网设备的精简版操作系统。](http://blogs.windows.com/buildingapps/2015/05/12/bringing-node-js-to-windows-10-iot-core/)

### 通过 JavaScript 参与进来

*   [在 Raspberry Pi 2 上运行 Windows 10 物联网核心](https://www.sitepoint.com/running-windows-10-iot-core-on-a-raspberry-pi/)——这是我在 SitePoint 上整理的关于将 Windows 10 物联网核心安装到 Raspberry Pi 2 上的指南(没有提到 Node.js，但在做任何其他事情之前，您需要先浏览一下这个指南！).
*   [微软在 Windows 物联网平台上使用 Node.js 的指南](https://blogs.windows.com/buildingapps/2015/05/12/bringing-node-js-to-windows-10-iot-core/)——一篇关于 Windows 10 物联网核心和 Node.js 入门的官方博客文章。
*   [Windows 10 物联网核心文档和样本](http://ms-iot.github.io/content/en-US/win10/StartCoding.htm)——各种各样的例子，包括 Node.js 上的许多例子。

## 进化事物

![Evothings Studio running on Mac OSX and mobile](img/9d10bb742746097fccd4537d66c4dd84.png)

在 Mac OSX 和移动设备上运行的 Evothings Studio

Evothings，一套移动物联网 web 应用的开发工具，在 2015 年真正增长和扩展了它们的能力！我在下半年偶然发现了他们，他们的产品给我留下了深刻的印象。他们开发了一个名为 Evothings Studio 的应用程序，允许您开发具有实时重新加载和调试功能的移动 HTML5 和 JavaScript 应用程序。这在我[探索 Estimote 蓝牙信标](https://www.sitepoint.com/how-to-prototype-beacon-apps-with-estimote-and-evothings/)时尤其完美，因为桌面移动模拟器无法模拟到信标的蓝牙连接。在我的手机上运行 Evothings 并通过他们的软件进行测试，让这个过程变得异常简单。

对于 JavaScript 开发者来说，这个平台也是令人兴奋的，因为 Evothings 团队已经有了一系列教程来展示如何用 JavaScript 实现大量的物联网。

(声明一下，我现在正和 Evothings 团队合作一些潜在的项目，因为他们的东西给我留下了深刻的印象！)

### 通过 JavaScript 参与进来

*   [Evothings 官方“入门”页面](https://evothings.com/getting-started-with-evothings-studio-in-90-seconds/)–Evo things 官方 Evo things 入门指南。
*   [Evothings 教程](https://evothings.com/tutorials/)——前面提到的教程列表！
*   [如何使用 Estimote 和 Evothings 制作 beacon 应用原型](https://www.sitepoint.com/how-to-prototype-beacon-apps-with-estimote-and-evothings/)–我的 SitePoint 指南，介绍了我如何使用 Evothings Studio 制作 Beacon 移动应用原型。

## IFTTT 创客频道

![The IFTTT Maker Channel](img/3015eada9b4fc9f91c5c6cf7304fe94a.png)

IFTTT 创客频道

今年，IFTTT 发布了一个频道，提供了物联网社区梦寐以求的功能——在 IFTTT 内发出和接收自定义 HTTP 请求的能力。这开放了服务，因此任何定制原型，无论是发出还是接收 HTTP 请求，都可以与 IFTTT 和它的许多服务一起工作！

这对 Node.js 开发人员来说非常有用，因为 HTTP 请求可以在 Node.js 中轻松地发出/接收。现在，几乎任何用 Node.js 连接的物联网原型都可以连接到 IFTTT！

### 通过 JavaScript 参与进来

*   [if TTT 创客频道](https://ifttt.com/maker)——if TTT 创客频道官方页面。
*   [将物联网和 Node.js 连接到 IFTTT](https://www.sitepoint.com/connecting-the-iot-and-node-js-to-ifttt/)——这是我今年早些时候整理的一个 SitePoint 教程，旨在展示将 if TTT 与 Node.js 结合使用的基础知识。
*   [使用 LightBlue Bean 和 IFTTT 实现 LIFX 灯光自动化](https://www.sitepoint.com/automating-lifx-lights-with-the-lightblue-bean-and-ifttt/)–我的 SitePoint 指南，通过 Node.js、LightBlue Bean 和 IFTTT 实现 LIFX 灯光自动化。

## 强尼五号改版了！

![The new Johnny-Five website](img/b4df3792de3055164d6bd71fb731fd4a.png)

新的强尼五号网站

Johnny-Five 是一个最常用于 Arduino NodeBot 开发的 JavaScript 编程框架，它改进了他们的整个网站，并添加了一系列新文档。他们支持的设备大量增加，他们有帮助开发者开始使用 Johnny-Five 和那些平台的链接。

### 通过 JavaScript 参与进来

*   Johnny-Five 官网——Johnny-Five 改版后的网站。
*   JavaScript Robotics Book——由 Johnny-Five 的作者于 2015 年发布的一本书，NodeBots 社区对此做出了许多贡献。值得一读！

## 树莓派零度

![The Raspberry Pi Zero (Photo credit: Raspberry Pi Foundation)](img/2610dc9232419e5bd0a061f80aa61ea2.png)

树莓派 Zero(图片来源:树莓派基金会)

最后，最近的一项发展为 JavaScript 开发人员(以及其他各种语言的开发人员)提供了一些令人兴奋的可能性！)，树莓派零度。它只有 A+ Pi 型号的一半大小，但仍能以令人难以置信的速度和力量运行。由于 Raspberry Pi 设备通常运行 Linux，因此在这些设备上运行节点服务器非常简单。Raspberry Pi Zero 提供了一个更小的支持 JavaScript 的设备，我确信它将在未来几个月带来一些令人印象深刻的作品。

### 通过 JavaScript 参与进来

*   [树莓派零产品页面](https://www.raspberrypi.org/products/pi-zero/)——官方树莓派零产品页面，在这里你可以找到规格和购买链接！
*   [使用 JavaScript 将 Raspberry Pi 摄像头转换成 VR](https://www.sitepoint.com/streaming-a-raspberry-pi-camera-into-vr-with-javascript/) ——这是我整理的一个指南，介绍如何将 Raspberry Pi 和它的摄像头转换成谷歌 Cardboard 设备的流媒体 VR 摄像头。我还没有一个 Raspberry Pi Zero，但是从我目前所看到的来看，这个演示可能会在新的更小的设备上运行！
*   JavaScript 机器人学书籍–之前提到的关于 Johnny-Five 的 JavaScript 机器人学书籍有一些树莓 Pi 演示。

## 结论

2015 年，JavaScript 开发人员在物联网设备方面取得了令人难以置信的进步，直接体现在 Tessel 等真正专注于 JavaScript 的设备上，间接体现在 Raspberry Pi Zero 的进步上。进入 2016 年，JavaScript 开发者有很多选择。如果你是一名 JavaScript 开发人员，还没有开始研究如何将你的技能转移到物联网生态系统中——看看上面的技术和我在 2013 年[和 2014 年](https://www.sitepoint.com/javascript-beyond-web/)[的文章中的综述。JavaScript 开发者比他们意识到的更加强大！](https://www.sitepoint.com/javascript-beyond-web-2014)

## 分享这篇文章