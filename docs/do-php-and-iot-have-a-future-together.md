# PHP 和 IoT 在一起有前途吗？

> 原文：<https://www.sitepoint.com/do-php-and-iot-have-a-future-together/>

![IoTWeek_Gray](img/defe243339c5bf160966142955834754.png)
*这是 SitePoint 的 IoT 周！整个星期，我们都在发布关于互联网和物理世界的交集的文章，所以请继续查看[物联网标签](https://www.sitepoint.com/blog/)的最新更新。*

整整一周，每个频道都是物联网资源，是的，包括 PHP。什么？“PHP 和 IoT！？你肯定弄错了！”，你一定在想。不，亲爱的读者。PHP 和 IoT 很好地结合在一起——比人们想象的要好。

![Robot elephant](img/4a28fb874fd0d92e4e1441e5bc1c3dad.png)

在本帖中，我们将列出一些入门资源。在本周余下的时间里，我们将为你提供一些令人惊叹的帖子——从用 PHP 创建饮料机，到当《我的世界》的一扇门打开时的真实生活警报(是的，真的！)–这只是我们的频道。一定要看看其他人！

事不宜迟，如果您想在我们正式开始之前涉猎一下，这里有一些介绍性的材料！

## 五金器具

*   [电子元件](https://medium.com/@assertchris/electronic-components-20bfc59004bd):刚开始时，有时很难知道如何使用电子元件，或者如何将它们插入电路。这个由优秀的[克里斯多佛·皮特](https://www.sitepoint.com/author/assertchris/)提供的小指南有助于找出一些常见的组件。
*   Arduino:一个整洁的业余爱好者开发平台。它抽象了从电阻和电线到功能计算的步骤。您使用 C/C++对 Arduino 板进行编程，本指南将详细介绍您的入门…
*   洋葱物联网:一个类似 Arduino 的硬件平台，但据称更灵活，更[语言不可知](https://tldrify.com/i9v)，因为它也支持 PHP。这些设备和插件相当便宜，并且非常容易与其他硬件混合和匹配，从通用电子设备到 Arduinos。
*   [英特尔开发人员专区硬件商店](https://software.intel.com/en-us/iot/hardware/sensors):英特尔开发人员专区包含专为物联网设计的精美硬件目录。不完全是，它只是你的通用电子产品，但所有产品都捆绑到一个专注于物联网的商店中。因此，如果网上订购更是你的强项，这是获得装备的地方。
*   气体传感器:当我还在上小学的时候，我制作了一个带有发光二极管的酒精测试仪，用来显示比赛中的酒精含量。可以说，那时候建造东西比较简单，但找到合适的硬件要复杂得多。如今，情况正好相反——最难找到的是基于气体的电阻传感器，而这正是这些网站所能提供的。

## 软件和教程

*   [Firmata](https://medium.com/@assertchris/firmata-a375e714c974) (基于 JavaScript):当你开始使用 Arduino 时，你可能没有时间或愿望去学习 C/C++，仅仅是为了能够编写一个电路板。幸运的是你不必。本指南介绍了 Firmata，它是众多可以与之交流的语言之一。
*   对孩子们来说，电子产品可能非常有趣！让他们开始使用 MakeyMakey。它模拟了一个键盘/操纵杆，你可以用它来玩你最喜欢的游戏。
*   如果你有一个 Arduino，并且想把它变成一个 MakeyMakey，这个库就是你需要的。它包括转换套件(你可以把它还原成 Arduino)和例子。
*   [Arduino +串口 PHP 通讯](http://www.instructables.com/id/Control-an-Arduino-with-PHP):你不必使用 Firmata 来控制你的 Arduino。本指南演示了如何在 PHP 和 Arduino 之间创建一个自定义的串行通信通道。当您需要在 PHP 应用程序中使用特殊的 C/C++控制的组件时，这是一个很好的起点。
*   [Arduino + Firmata PHP 通信](https://github.com/ThomasWeinert/carica-firmata)
*   [使用 PHP 和 Arduino 为 LED 灯泡供电](https://www.lassiemarlowe.com/tutorials/power-led-bulbs-php-arduino)
*   [Gorilla PHP 扩展](https://github.com/oasynnoum/phpmake_serialport):一旦你在 Arduino 上运行了 Firmata，你将需要一个可爱的通信库来连接你的 PHP 应用程序。就是这个！
*   [洋葱物联网云](https://onion.io/cloud/):真正的“云”和真正的*互联网的入口*在物联网中，前面提到的洋葱硬件平台拥有专用的应用云，用于部署洋葱应用并连接您的各种设备和电子设备
*   [构建使用物联网设备 GPS 数据的 PHP 应用](https://www.ibm.com/developerworks/library/iot-php-app-iot-foundation-bluemix/):利用 IBM Watson 物联网平台、Bluemix 和 MQTT 开发物联网 PHP 应用。把你的 Android 手机变成一个 GPS 传感器，不断地向 Bluemix 云发布它的位置。然后，用这个数据流连接一个 PHP 应用程序，用它在你的 web 浏览器中实时跟踪 Android 手机的位置。
*   与 Azure IoT hub 通信的简单 PHP 库:PHP 库的介绍性教程，可用于与 Azure IoT Hub 通信。

## 接下来呢？

现在你已经准备好开始了，继续尝试吧。思考想法，想象场景，然后去你的电器商店买一把 10 美元的硬件——等你回来的时候，我们会为你准备大量的实践教程。

有没有更多适合这个列表的资源？请在评论中告诉我们，祝你好运！

## 分享这篇文章