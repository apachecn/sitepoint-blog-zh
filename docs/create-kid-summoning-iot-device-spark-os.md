# 使用 Spark OS 创建一个召唤孩子的物联网设备

> 原文：<https://www.sitepoint.com/create-kid-summoning-iot-device-spark-os/>

可用于与物联网接口的小工具的选择似乎每天都在增加，物联网是一种由互联设备组成的网络，允许开发人员编写代码与传感器进行交互。

如果你有任何喜鹊倾向，冲动购买一个树莓派可以很快变成一大堆小机器，包括 Arduinos 和所有的电缆，电路板，电阻，二极管，蜂鸣器，按钮和 LED 灯，让他们表演他们的把戏。

很快你就能直接从《MAKE》杂志中创造出引人入胜的项目，比如[树莓 Pi 驱动的气象气球携带一只玩具熊进入平流层](http://www.raspberrypi.org/tag/dave-akerman/)或者 [Arduino 驱动的智能手机车库门开启器](http://www.megunolink.com/garage-door-opener/)。

简单的项目是教孩子如何用 Python 编程的好方法(例如，用树莓 Pi 让 LEDS 在图案中闪烁),或者用像 Arduino 这样的小机器连接电路来创建有用的现实生活中的技巧。在本文中，我将介绍一个相对较新的物联网场景:Spark Core，一个*“微型 wi-fi 开发套件”*最初是一个成功的 Kickstarter 项目。套用 Spark 的营销广告，*“当互联网渗透到现实世界时，令人兴奋的事情就会发生”*。

![Spark Board](img/b43cdf5f8471481615624fa4d1c21951.png)

Spark Core 是一款支持 wifi 的 Arduino，自带试验板，旨在提供连接性和易用性。您可以使用 Spark 提供的云代码解决方案来更新藏在您的房子或办公室周围的 Spark，而不是在本地编写代码来为您的 Arduino 或 Pi 供电，只要它们连接到您的 wifi。

例如，你可能有几个火花连接到你的房子在一个黑客一起'巢状'系统。从 Spark.io 提供的 web 工具或从您的本地命令行，您可以将内容“刷新”到 Spark，使所有部分无缝地一起播放。

即使只有一个 Spark 内核，也可以承接很多好玩的项目。使用火花所在的试验板、几根连接电缆、一个电阻和一个按钮，你可以制作一个许多父母可以立即使用的工具:一个*“儿童召唤师】*。我将演示如何连接您的 Spark，使其与 Pushingbox 服务接口，以便在您希望您的孩子下楼时向他们发送推文，也许是去吃午饭。这比在楼梯上大喊大叫或者希望自己有对讲机要好！

## 你需要什么

*   Spark Core，售价 39 美元，来自 spark.io
*   一个 Arduino Sidekick 基本套件，用于公对公电缆、一个按钮和一个电阻器。你可以在 Radio Shack 以 34.99 美元的价格买到这个套件，这是一个手头上很有用的套件
*   一个 Twitter 账户，通过它你可以联系到你想要召唤的第三方
*   Pushingbox 帐户([pushingbox.com](http://www.pushingbox.com/dashboard.php))—为您的物联网设备创建通知“场景”的简单方法

## 步骤 1:在 Pushingbox 上创建一个场景

登录 Pushingbox，并确保您的 Spark Core 的 Twitter 帐户授权 Pushingbox 代表它发送推文。Pushingbox 集成了很多很棒的服务；您可以启用语音通知、电子邮件、浏览器和移动推送通知。出于我们的目的，我们只需要启用 Twitter。

接下来，创建一个名为“发送推文”的 Pushingbox“场景”。写一条你希望你的 Spark 核心发送的消息，引用你孩子的 Twitter 账户。

例如

> “@thing_one @thing_two 汤开始了，请到厨房来！."

通过点击场景屏幕上的*测试*来测试您的场景。您的仪表板将记录调用该场景的所有实例:

![Pushingbox dashboard](img/6452ff543652f727e62b9ce2ed6b8578.png)

## 第二步:让你的火花核心启动并运行

按照 Spark.io 上的说明，确保你的 Spark 在本地 wifi 上启用。使用 Spark 的移动应用程序有助于“申请”过程，因此您可以将 Spark Core 与您的 wifi 相关联。我发现我有时不稳定的家庭 wifi 引起了一些问题，但只要有点耐心，Spark Core 就会可靠地运行。安装命令行工具 *belt* 对你的内核进行细粒度控制:【docs.spark.io/cli/】T2。您可以通过 CLI“申请”或回收旧件。关于安装和故障排除的完整说明可以在 [docs.spark.io](http://docs.spark.io) 找到。

## 第三步:连接你的设备

现在，您需要配置您的电路。使用下图，使用 Spark Core 的试验板、一个按钮、六根公对公电缆和一个 10K 欧姆电阻器来制作一个开关，您的火花可以与之相互作用。

![Spark circuit diagram](img/e72acd3a59c920d0211a21ab41837415.png)

要测试您的电路是否正常工作，请打开您的终端，在 Core 仍然连接到您的计算机的情况下，确保 CLI tool belt 已安装在您的计算机上，然后键入:

```
spark cloud login
```

将您的凭据发送到 Spark.io。登录后，验证您的 Core 是否可用:

```
spark list
```

## 第 4 步:创作并刷新代码到核心

现在，导航到基于 web 的构建工具。在这里，您将在云端编写代码，这些代码可以刷新到您的内核。

在 Spark.io 网站的 Web IDE 或 Build 区域([spark.io/build](https://www.spark.io/build)，登录并创建一个新的应用。我把这个*叫做“儿童召唤师”*。如果你熟悉 Arduino，你将会在这里如鱼得水，因为这些设备使用的语言是相同的。因为我们的应用程序很简单，所以我们只需要一个`.ino`文件:

```
/*
  kid-summoner.ino

    On pressing a button, a tweet is sent via Pushingbox
    Written for the Spark Core
*/

int inputPin = D1; //push-button pin
int val = 0; //variable for push-button status
int state = 1; //variable for on/off state

const char * DEVID = “the-devid-created-in-pushingbox";         // Scenario: "Tweet"
const char * serverName = "[api.pushingbox.com](http://api.pushingbox.com)";   // Pushingbox API URL

TCPClient client;

void setup() {

    pinMode(inputPin, INPUT);
    Serial.begin(9600);
}

void loop() {
    val = digitalRead(inputPin); //read the state of the push-button

    if (val == LOW) { //if push-button pressed
        state = !state; //reverse on/off state
        delay(250); //primitive button debounce
        Serial.println("button pushed!");
        sendToPushingBox(DEVID);

    }
}

void sendToPushingBox(const char * devid)
{
    client.stop();
    if (client.connect(serverName, 80)) {
        client.print("GET /pushingbox?devid=");
        client.print(devid);
        client.println(" HTTP/1.1");
        client.print("Host: ");
        client.println(serverName);
        client.println("User-Agent: Spark");
        client.println();
        client.flush();

    }
    else{
        Serial.println("connection failed");
    }
}
```

在 IDE 中保存并“验证”您的代码。从您之前创建的 Pusherbox 场景中添加“DEVID”。

![Cloud code IDE](img/c905617cbb6318821801ecd915dc65f4.png)

确保您的核心是活的，连接到 wifi，并'呼吸'青色(核心上的中央灯有几个颜色状态；当它‘呼吸’青色时一切正常)。准备就绪后，单击 IDE 上的“闪电”图标。你的核心应该开始闪烁一系列的颜色变化。当它再次“呼吸”时，你就可以开始测试你的安装了。

去按那个按钮吧！你应该给你孩子的账户发一条推文。要排除故障，请检查 Pushingbox 推文应该会显示在其仪表板上。此外，您可以在终端中设置调试代码并监控其执行:

```
spark serial monitor
```

任何调试代码都将显示为打印行。一旦一切正常，你就可以解开你的核心，把它安装在你房子的某个地方。我把我的放在厨房里，这样当我准备好开饭时，我可以按下按钮，孩子们就会收到一条推特。由于设备支持 wifi，您可以对代码进行任何编辑，并再次刷新代码以刷新内核。这太神奇了。这里有一段视频展示了它的工作原理:

[//www.youtube.com/embed/rvmnezX1vio](//www.youtube.com/embed/rvmnezX1vio)

## 扩展此项目

当然，按下按钮发送推文是一个简单的初级项目。

扩展项目的选项包括:

*   传递变量以定制消息
*   添加位置坐标以邀请人们到您房子的不同区域
*   创建一个按钮阵列来召唤不同的第三方或发送不同类型的消息
*   添加运动传感器以支持其他类型的手势

Spark Core 允许您创建各种传感器支持的应用程序——我的下一个项目可能是一个运动传感器，允许我的衣物烘干机在烘干机循环完成时向我发送电子邮件。这将是有用的，对不对？

## 信用

我受字母机器人([hackster.io/jay_uk/letterbot](http://www.hackster.io/jay_uk/letterbot))和这个电路例子[hackster.io/maxeust/temperature-sensitive-led](https://www.hackster.io/maxeust/temperature-sensitive-led-2f1635)的启发，创作了这个儿童召唤师。

要制作漂亮的电路图，我推荐 Fritzing，它有一个很棒的 app:[fritzing.org/home/](http://fritzing.org/home/)。

make Magazine([makezine.com/](http://makezine.com/))是激发项目灵感的绝佳资源。

你可以在 Adafruit:[adafruit.com/](http://www.adafruit.com/)为你的设备购买零件。

## 分享这篇文章