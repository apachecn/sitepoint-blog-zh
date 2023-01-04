# 如何不用编码制作一个有效的物联网仪表盘

> 原文：<https://www.sitepoint.com/how-to-make-an-effective-iot-dashboard-without-coding/>

不是每个人都想为他们的物联网生态系统编写一个完整的接口。有时候，你需要一个快速简单的界面，易于设置和运行。Blynk 就是那个接口！这是一款智能手机应用，可以与一系列系统集成，包括 Arduino、Particle、ESP8266、英特尔 Edison、英特尔 Galileo、LightBlue Bean、Raspberry Pi 等。它允许您显示来自传感器的值，并触发引脚响应来打开和关闭组件。这也是一种非常简单快捷的测试原型的方法。我没有找到一个关于如何将粒子设备与 Blynk 配对的很好的概述，所以这就是我将在本文中涵盖的内容。

如果你没有粒子设备，但有 Arduino 或其他微控制器，Blynk 的大部分过程应该和我在本文中解释的一样，你只需要将 Blynk 代码以不同的方式放到设备上[(对于 Arduino，上传这个库)](https://github.com/blynkkk/blynk-library)。

## 你需要什么

*   粒子核心或光子
*   试验板、电阻器和跳线
*   一些 led 和模拟传感器–Blynk 应用程序可以处理任何模拟和数字引脚信号，因此可以随意切换这些信号以使用您自己的组件。我用了一个电位器和一个光敏电阻。
*   Blynk 应用——你可以在 [iOS](https://itunes.apple.com/us/app/blynk-control-arduino-raspberry/id808760481?ls=1&mt=8) 和 [Android](https://play.google.com/store/apps/details?id=cc.blynk) 上找到它。
*   如何在你的粒子设备上获取代码的知识——如果你对这个或这个[官方粒子核心指南](https://docs.particle.io/guide/getting-started/start/core/)不熟悉，请参见[连接光子](https://www.sitepoint.com/getting-started-with-the-particle-photon/)文章。

## 我的素描

如果你想跟随我的精确设置(或接近它，取决于你有哪些部分)，这里是草图:

![Sketch of Particle and Blynk setup](img/1162968ffe759cf9598fcec68fc20a94.png)

## 设置 Blynk

首先，在你的智能手机上打开 Blynk(如果你还没有的话，这些是到 [iOS 下载](https://itunes.apple.com/us/app/blynk-control-arduino-raspberry/id808760481?ls=1&mt=8)和 [Android 下载](https://play.google.com/store/apps/details?id=cc.blynk)的链接)。

首次加载应用程序时，您需要创建一个帐户。这个过程非常简单，输入电子邮件和密码，然后点击“注册”:

![Blynk Login Screen](img/408d813eb82ad3b18d39ad86abde494f.png)

一旦你注册了，你会看到项目屏幕。单击“创建新项目”来设置您的第一个项目:

![Creating New Blynk Project](img/a98d4e228e3fd1fb2457ef3cbfcbec19.png)

在我们的新项目的设置中，我们给它一个名字，并选择“粒子核心”作为我们的硬件模型(这是一个选择，如果你也在使用光子)。我将我的项目命名为“物联网仪表板”，请尽情发挥创意吧！

![Initial New Blynk Project](img/8f938304a2a00c855079b88b7f3a9fab.png)

您还会在屏幕上看到一个 auth token。与其全部输入或以某种方式记住整件事，不如点击“电子邮件”按钮，让它通过电子邮件发送给你。这是一个巨大的时间节省！您收到的电子邮件将如下所示:

![The Blynk Auth Email](img/365ce8e7b81466127bfe62752a1a6fa8.png)

点击“创建”,你将会看到一个相当黑的空屏幕。这是你想放入所有组件的地方。单击右上角带加号的图标，打开“小部件框”:

![The Blynk Plus Icon](img/1d94e760de26a071085bbe76cd08d2fc.png)

“部件盒”是一个包含大量不同部件的菜单，您可以将这些部件添加到您的项目中，以控制硬件组件或从传感器读取数据:

![The Blynk Widget Box](img/16797a896b585a78b38c8d285063944a.png)

首先，让我们创建一个可以打开 LED 的按钮。点击“按钮”。这将在我们的项目中放置一个按钮小部件:

![Blynk Button Widget Placed](img/dff9916a7afb899b0b48578c1b3f29ed.png)

单击按钮 widge 更改其设置。我将我的按钮组件命名为“led ”,因为它将打开和关闭 LED。我已经将 pin 设置为 D2，并将模式设置为“推送”。这意味着当按钮被按下时，数字 pin 是打开的，但是当你放开按钮时，数字 pin 是关闭的。“开关”将它变成一个开关，当你点击它一次时，状态保持打开，当你再次点击时，状态关闭。彩色圆圈允许您更改按钮激活时的高亮颜色。

![Blynk LED Button Settings](img/4fc40f07cd5cca73bcdfdab1789404c6.png)

当您对这些设置满意时，单击返回箭头返回到项目屏幕。您将看到小部件现在显示 pin 号:

![Blynk Single LED Setup](img/8c0d2e63aaa9dc719f72214d8f870747.png)

我的设置中有两个 led，所以我添加了第二个，将第二个的颜色改为橙色。

![Blynk Two LED Setup](img/3408715c131eb5eae6b0e93a566b8d3e.png)

我还有一个光电传感器连接到引脚 A5 来检测房间内的光线水平。要显示此类模拟输入的值，最好用一个折线图来显示输入的电平。为此，创建一个“Graph”小部件。再次点击它，打开它的设置。

![Blynk Graph Widget Added](img/7081f81463389c59c48486c2172814a5.png)

我将我的命名为“LIGHT”，设置为 A5，设置值从 0 到 4095(您自己的模拟设置可能有不同的范围，可以随意更改以适应它)。我将读取频率设置为“1 秒”，但是您可以根据自己的喜好请求它更频繁或更不频繁地 ping 这些值。底部开关设置图形是条形图还是折线图。我更喜欢折线图:

![Editing the Blynk Light Widget](img/ff7f8ddecaa264b307571a68e050b9a9.png)

显示模拟值的另一种简洁方式是通过“Gauge”小部件。我在试验板上连接了一个电位计，用它来做实验。您可以将其连接到任何模拟输入。要尝试这一点，请添加一个“计量器”小部件:

![Blynk Gauge Widget Added](img/8dea572034ea0fa5266282f03b320d9e.png)

单击新的小部件打开设置，并根据您的组件进行调整。我将我的“仪表”部件命名为“表盘”，并将其连接到 A0 引脚。我将读取频率设置为 250 毫秒，以便在扭转电位计时获得良好而快速的响应时间:

![Our Dial Widget](img/24e32fca61368ec97bdf54b1a99a794d.png)

## 设置您的粒子设备

设置粒子设备来使用 Blynk 并不太难。幸运的是，大多数繁重的工作已经在一个预建的库中为您完成了！

首先，在[粒子构建界面](https://build.particle.io)中创建一个新项目。随便你怎么称呼它。我的名字很有想象力。我称之为“布林”。

在您的新项目中，单击左下方的库图标:

![Choosing Particle Libraries](img/0379172603046741051b9ed8f18cb64c.png)

你会在“社区图书馆”部分看到“Blynk”图书馆。选择那个:

![The Blynk Particle Library](img/c96bf2955aedf8cc0e184fd1a7002838.png)

它将加载组成 Blynk 库的许多文件。这些将提供我们所有的 Blynk 集成，而不需要我们从头开始硬编码。单击“包含在应用程序中”:

![Including the Library In App](img/a9a4c95a4a7ddda270d666b54a22bf11.png)

它会询问你想将它添加到哪个应用程序。选择您刚刚创建的新应用程序(我的应用程序名为“Blynk”):

![Adding to Particle App](img/b753428c2bb1d0a39433381e2837558f.png)

然后点击“添加到此应用程序”进行确认:

![Add to our app](img/44f0e5bed26bf6618b547b47b33cfccc.png)

它会将此添加到您的应用程序中。单击“返回应用程序”以返回设置您的代码的其余部分:

![Back to our Particle App](img/666642560d693208140d461b7f2ba2cd.png)

## 粒子代码

粒子设备的代码现在应该是这样的:

```
// This #include statement was automatically added by the Particle IDE.
#include "blynk/blynk.h"
```

更新代码，如下所示:

```
// This #include statement was automatically added by the Particle IDE.
#include "blynk/blynk.h"

char auth[] = "YOURAUTHCODE";

void setup()
{
  Serial.begin(9600);
  delay(5000);
  Blynk.begin(auth);
}

void loop()
{
  Blynk.run();
}
```

将`YOURAUTHCODE`更改为您之前收到的电子邮件中的代码。

代码本身非常简单。在我们的`setup()`函数中，我们初始化端口 9600 上的串行端口，然后等待五秒钟以确保粒子设备准备就绪:

```
Serial.begin(9600);
delay(5000);
```

然后，我们通过传入我们的授权代码来初始化对 Blynk 库的使用:

```
Blynk.begin(auth);
```

然后，在我们的`loop()`函数中，我们运行 Blynk 来检查粒子设备状态的任何变化，或者来自 Blynk 应用程序的请求的变化:

```
void loop()
{
  Blynk.run();
}
```

把代码闪到你的粒子设备上！

## 在活动

连接您的粒子设备，并且代码在设备上成功运行，单击右上角的播放按钮开始获取值。我的是这样的:

![Blynk IoT Dashboard In Action](img/378fbae3cddab0f8abcd215a48f53afe.png)

这里有一个视频，它为那些更倾向于视频的人工作:

[youtube mThrK2iy1fY]

## 结论

Blynk 是一个很好的工具，可以为家庭物联网项目组装一个简单的仪表板控件。在深入研究应用程序开发之前，测试原型是否传输了正确的值也是很棒的。希望这为开发人员提供了一个新的、非常有用的工具，让他们可以在物联网工具箱中拥有它！

如果你在某个真正有趣的地方看到了正在使用的 Blynk，或者以一种整洁的方式使用了它，请在评论中分享或者在 Twitter 上与我联系( [@thatpatrickguy](http://www.twitter.com/thatpatrickguy) )。我很想听听！

如果你正在寻找其他链接和示例项目来指导你的粒子开发，我有一套精选的链接，可能非常适合你！去 Dev Diner 看看我的[Dev Diner Particle Developer Guide](http://devdiner.com/guides/particle/)，里面有很多官方链接、其他优秀的 SitePoint 文章等等。如果你有我没有列出的其他好资源，也请告诉我！

## 分享这篇文章