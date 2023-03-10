# 如何在 2019 年让 JavaScript 超越 Web

> 原文：<https://www.sitepoint.com/how-to-take-javascript-beyond-the-web-in-2019/>

随着我们进入 2019 年，JavaScript 继续出现在网络以外的各种地方！我们已经在 SitePoint 上了解了 JavaScript 如何超越 web 的历史(参见 [JavaScript 超越 Web](https://www.sitepoint.com/javascript-beyond-web/) 、2014 年 [JavaScript 超越 Web](https://www.sitepoint.com/javascript-beyond-web-2014)和 2015 年 [JavaScript 超越 Web](https://www.sitepoint.com/javascript-beyond-the-web-2015))。今年，在探索 JavaScript 的地位之后，我们决定发表一篇前瞻性的文章，探讨 JavaScript 在 2019 年超越 web 的所有潜力。

## TensorFlow.js

![The Tensorflow.js logo](img/811dba440ee4c5e4b16c87cac4b89ded.png)

Tensorflow.js 徽标和网站描述

TensorFlow 是一个众所周知的机器学习框架，它可以做各种事情，从图像识别到为 90 年代热播系列“老友记”生成[新脚本。无论你往里面放什么，可能性都是相当大的！TensorFlow 最初在 Python 上运行，但现在也有了 JavaScript 库。JS 库将允许您在浏览器和 Node.js 上训练和部署机器学习模型。](https://www.theverge.com/2016/1/21/10805398/friends-neural-network-scripts)

还有 [ml5.js](https://ml5js.org) 建立在 TensorFlow 之上，如果 Tensorflow.js 有点太激烈，它的目标是更容易接近一点！

### 通过 JavaScript 参与进来

*   [tensor flow . js 网站](https://js.tensorflow.org/)
*   [tensor flow . js 入门](https://js.tensorflow.org/#getting-started)
*   [TensorFlow.js Codepen](https://codepen.io/pen?&editable=true&editors=0012) —无需设置即可试用！

## 粒子网格

![Particle Mesh's Argon, Boron and Xenon boards](img/4408de431a9db81adb9339fbce41f44c.png)

粒子网格的氩、硼和氙板

在过去的几年中，Particle 的团队一直在做着伟大的事情，并出现在我多年来的 JavaScript 和物联网兴奋列表中！2019 年是重要的一年，因为这是粒子*网格*之年。来自 Particle 的最新三款设备内置网状网络，这意味着一大堆设备可以在本地相互通信——不需要互联网。

虽然他们可以在没有互联网连接的情况下进行通信，但如果你需要，他们也可以连接到 Particle 自己的云服务。因此，通过网络发送和接收数据也非常容易。

最棒的是——你用 JavaScript 为这些编程！

他们的三个支持网格的微控制器如下:

*   **Argon** —他们的 Wi-Fi 连接网状网关。这是其他设备可以连接的设备。
*   **Boron** —他们的 LTE 或 2G/3G 连接网状网关。它可以像 Argon 一样工作，但更便于携带，因为它不需要 Wi-Fi，而是可以在全球范围内工作(Particle 提供了全球 SIM 卡选项！).
*   **氙** —这些是可以连接到氩和硼微控制器的网格端点。它们是低成本的传感器，你可以把它们放在任何地方(例如，放在你所有的工厂里来跟踪湿度)，然后让它们反馈回来。

### 通过 JavaScript 参与进来

*   [粒子网格网站](https://www.particle.io/mesh)
*   [粒子快速入门指南](https://docs.particle.io/quickstart)
*   [我在粒子网上采访了粒子 CEO](https://devdiner.com/internet-of-things/particle-mesh-iot-creation)

## 安静点

![Dialogflow logo, Google Home and Google Assistant](img/d8d7983b6c006b1c9e161b5a68fcb613.png)

Dialogflow 可以与 Google Home、Google Assistant 和一系列非 Google 服务协同工作

我是 Dialogflow 的忠实粉丝，这是谷歌为 Google Home 和 Google Assistant 等设备创建对话界面的平台。同样一套经过训练的反应和场景可以在各种地方跨平台使用，包括 [Facebook Messenger](https://dialogflow.com/docs/integrations/facebook) 、 [Skype](https://dialogflow.com/docs/integrations/skype) 、 [Telegram](https://dialogflow.com/docs/integrations/telegram) 、 [Twitter](https://dialogflow.com/docs/integrations/twitter) 、 [Slack](https://dialogflow.com/docs/integrations/slack) 甚至 [Twitch](https://github.com/patcat/DialogflowTwitchBotBridge) 。

虽然您的语音界面的许多设置将在他们的平台内进行，不需要任何编码，但他们的 [Node.js 客户端](https://github.com/googleapis/nodejs-dialogflow)扩展了更多的可能性！上面的 Twitch 集成实际上是我自己使用 Node.js 客户端构建的，作为 Dialogflow 和 Twitch 的 API 之间的桥梁。

不过有一点需要注意的是，这是我收到的一个常见问题——目前还没有办法将 Dialogflow 的新 V2 API 与客户端 JavaScript 直接集成——只能与 Node.js *( [更多关于将 Dialogflow V2 API 与 HTML 和 JavaScript](https://devdiner.com/virtual-assistants/how-do-i-integrate-dialogflow-v2-api-with-html-and-javascript) )* 集成的信息。

你甚至可以通过 Dialogflow 运行 Alexa 技能，它导出为 Alexa 可以导入的格式，但也有一种更彻底的方式使用 [Alexa Api.ai Bridge](https://github.com/Gnzlt/AlexaApiAiBridge) 将两者桥接在一起。

### 通过 JavaScript 参与进来

*   [dialog flow 网站](https://dialogflow.com/)
*   [用于对话流的 Node.js 客户端 V2](https://github.com/googleapis/nodejs-dialogflow)
*   [安静推特 Bot 桥](https://github.com/patcat/DialogflowTwitchBotBridge)
*   [使用 Dialogflow(早期访问)构建跨平台对话应用](https://devdiner.com/learn/building-cross-platform-conversational-apps-using-dialogflow) —我有一个关于使用 Dialogflow 的早期访问系列指南，我正在将其转化为电子书，你可以在它们以电子书形式结束之前在这里注册并提前访问它们

## 亚马逊 Lex

![Amazon Lex alongside an Amazon Echo Show](img/d9547afd695860dff6d2b1d8b3822ad5.png)

亚马逊 Lex 和亚马逊回声秀

对于那些喜欢使用与 AWS 和 Lambda 功能更接近的平台的人来说，Amazon Lex 是 Amazon 制作对话界面的方式之一。他们有一个类似于 Dialogflow 的界面，你可以在那里创建场景等供你的界面做出反应，但他们也有一个非常坚实的平台，可以在 Lambda 函数中做更高级的事情——在这些函数中，你可以用 JavaScript 的力量来[编码！](https://docs.aws.amazon.com/lex/latest/dg/getting-started-ex2.html)

也有内置选项挂钩到脸书，Kik，Slack 和 Twilio 短信。将 Amazon Lex 技能与 Google Home 操作挂钩是可能的，但是您必须自己解决两者之间的桥梁问题(没有官方方法可以将它们双向联系起来)。

### 通过 JavaScript 参与进来

*   [亚马逊 Lex 网站](https://aws.amazon.com/lex/)
*   [亚马逊 Lex 入门](https://docs.aws.amazon.com/lex/latest/dg/getting-started.html)
*   [使用 Lambda 函数和节点创建定制的 Amazon Lex Bot](https://docs.aws.amazon.com/lex/latest/dg/getting-started-ex2.html)

## Low.js

![The Low.js logo and its best pairing with the ESP32](img/8da5675811d817f5f1ec00b779d0adc1.png)

Low.js 标志及其与 ESP32 的最佳搭配

Low.js 是 Node.js 的一个较新的端口，似乎出现在 2018 年末。它的目标是做一个更适合物联网设备的 Node.js 的端口。

Low.js 对系统的要求比 Node 本身低。创作者说它可以立即启动，而 Node 可能需要一些时间来加载(在 Raspberry Pi 2 上大约 0.6 到 1.5 秒)。它还使用更少的磁盘空间和内存。

该端口允许开发人员利用整个 Node.js API，并且可以在 PC 以及物联网设备上运行。目前，该端口似乎专注于带 Wi-Fi 板载的 ESP32 微控制器(ESP32-WROVER)。对于那些想要以更低的成本尝试物联网的人来说，这些是一个很好的选择。ESP32 微控制器非常便宜，(他们的网站上说大约 3 美元，我假设是美元，我在澳大利亚见过大约 6 美元)。

### 通过 JavaScript 参与进来

*   [low . js 网站](https://www.lowjs.org/)
*   [low . js 入门](https://www.lowjs.org/examples/getting-started.html)
*   [The low.js GitHub 回购](https://github.com/neonious/lowjs)

## WebXR

![Mozilla's web-based A-Painter in WebXR](img/81d47e99cbae5312e0a87721600388bc.png)

Blair Macintyre 的 Mozilla 网络画师

WebXR 是基于浏览器的 WebVR 的下一步，WebXR 规范旨在涵盖更多设备，包括增强/混合现实耳机。这肯定仍是一项正在进行的工作，但自 2017 年底以来一直在讨论中。目前，这取决于 2019 年 2 月 6 日发布的编辑草案。这非常令人兴奋，我认为它将在 2019 年有很大发展！

如果你想做跨平台、更稳定的网络虚拟现实，请放心坚持使用 [A-Frame](https://aframe.io) 和 [React 360](https://facebook.github.io/react-360/) ，但是如果你热衷于探索即将到来的东西，并有可能成为帮助测试的一部分，并对新规范提供反馈，那么 WebXR 是值得一试的东西！

还有[一个 WebXR polyfill](https://github.com/immersive-web/webxr-polyfill) 提供回退到原生 WebVR 1.1 和 Google Cardboard。

### 通过 JavaScript 参与进来

*   [WebXR 设备 API](https://immersive-web.github.io/webxr/)
*   [WebXR Polyfill](https://github.com/immersive-web/webxr-polyfill)
*   [欢迎来到沉浸式网络](https://developers.google.com/web/updates/2018/05/welcome-to-immersive)——这是谷歌开发者博客上对 WebXR 的一个很好的概述！

## 结论

除了许多开发人员习惯的典型前端 web 开发之外，使用您现有的 JavaScript 知识还有很多可能性！

如果你在 2019 年用 JavaScript 做出了超越网络的酷东西，请在评论中留言或发推特给我(我是 [@thatpatrickguy](http://www.twitter.com/thatpatrickguy) )，我很乐意看到它！

## 分享这篇文章