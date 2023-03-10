# 用 PHP 支持 Raspberry Pi 项目

> 原文：<https://www.sitepoint.com/powering-raspberry-pi-projects-with-php/>

树莓派是一台非常棒的微型电脑，你可以用微型 USB 线给它断电。最新的型号有 WiFi、一个以太网端口、4 个 USB 端口和一个 HDMI 端口。还有一个微型 SD 卡插槽，这是“硬盘驱动器”的地方。

![Electronics stock image](img/aaa8c6bb80171a7c13386bbb162cc832.png)

它能够运行 Raspbian Linux，这是一个基于 Debian 的 Linux 发行版。这让 Ubuntu 用户很熟悉，然后他们就可以`sudo apt-get install`所有的东西。

就像任何 Linux 机器一样，你可以在上面安装 PHP 并创建一个网站——但是我们能做的远不止这些！

## 装备

首先，我们需要一个树莓派。最新的型号具有板载 WiFi，这对于轻松的 SSH 访问非常有用。我们还需要一些电子设备。获得一个包含试验板、跳线、电阻器、led 和按钮的初学者工具包是一个非常好的主意。你可以在 adafruit 网站上买到这种套装，在亚马逊和易贝上也可以找到便宜得多的类似套装。

## 设置

树莓派官网有一个极好的[快速入门指南](https://www.raspberrypi.org/help/quick-start-guide/)来帮助你入门和运行。一旦在您的机器上运行了 Raspbian Linux，您就可以使用这个命令来安装 PHP:

```
sudo apt-get install php5 
```

或者，安装 PHP7。它有点复杂，但性能更好。[这个指南解释的很好](https://www.symfony.fi/entry/install-php-7-on-raspbian-raspberry-pi)。

## 通用输入输出(GPIO)引脚

在树莓派电路板的一角有两排金属引脚，我们可以将电子电路连接到这些引脚上。这些管脚有的接地，有的 5V，有的 3.3V，大部分是 **GPIO 管脚**。

![GPIO pin map](img/86f5056a16832b648d53e1a51e590ed8.png)
*功劳:[www.raspberrypi.org](https://www.raspberrypi.org/documentation/usage/gpio/README.md)*

当 GPIO 引脚配置为输出引脚时，我们可以告诉 Pi 将其电压设置为高(3.3V)或低(0V)。这让我们可以打开和关闭东西。

当一个 GPIO 引脚配置为输入引脚时，我们可以检查该引脚上的电压，并检测该电压何时发生变化。这使得我们可以读取传感器，也可以检测像按钮按压这样的事情。

这些引脚还有很多用途，比如通过串行、i2c 或 SPI 进行通信，但目前我们只需要知道这些。

## 开始使用 [PiPHP](https://github.com/PiPHP/GPIO)

PiPHP: GPIO 是一个 PHP 库，可以通过 composer 安装，允许你控制这些 GPIO 管脚。下面是一个例子，说明当检测到按钮被按下时，我们如何使用库来使 LED 闪烁几次。下图显示了如何使用试验板将 LED 连接到 GPIO 引脚 2，将按钮连接到 GPIO 引脚 3。试验板是制作电子电路的可重复使用的基座。这些行是电子连接的，就像电路板两侧横跨电路板长度的两个轨道一样。我们将前两个轨连接到地和 Raspberry Pi 中的 3.3V 引脚。

这两个电阻很重要。第一个电阻(~ 220ω)限制流向 LED 的电流。第二个电阻(~ 10kω)是按钮的[上拉电阻](https://en.wikipedia.org/wiki/Pull-up_resistor)。

*注意:Raspberry Pi 上的一些引脚有内部上拉电阻，所以这并不总是**所要求的**(但这并没有坏处)。*
![Breadboard Diagram](img/74c4cb1f244738873f259d3a4f91f2bb.png)

回到 Raspberry Pi，创建一个项目目录，然后使用 [composer](https://getcomposer.org/) 安装`piphp/gpio`:

```
composer require piphp/gpio 
```

如果您将下面的文件放在项目目录中，然后执行它(`php led-blink.php`)，您应该会发现按下按钮会导致 LED 闪烁 5 次，就像视频中一样。

[https://www.youtube.com/embed/UVTgVapKifI](https://www.youtube.com/embed/UVTgVapKifI)

```
<?php // led-blink.php

require_once 'vendor/autoload.php';

use PiPHP\GPIO\GPIO;
use PiPHP\GPIO\Pin\InputPinInterface;
use PiPHP\GPIO\Pin\OutputPinInterface;

// This GPIO object can be used to retrieve pins and create interrupt watchers
$gpio = new GPIO();

// Configure pin 2 as an output pin and retrieve an object that we can use to change it
$ledPin = $gpio->getOutputPin(2);

// Configure pin 3 as an input pin and retrieve an object that we can use to observe it
$buttonPin = $gpio->getInputPin(3);

// Configure this pin to trigger interrupts when the voltage rises.
// ::EDGE_FALLING and ::EDGE_BOTH are also valid.
$buttonPin->setEdge(InputPinInterface::EDGE_RISING);

// Create an interrupt watcher (this is a type of event loop)
$interruptWatcher = $gpio->createWatcher();

// Register a callback for handling interrupts on the button pin
$interruptWatcher->register($buttonPin, function () use ($ledPin) {
    echo 'Blinking LED...' . PHP_EOL;

    // Toggle the value of the LED five times
    for ($i = 0; $i < 5; $i++) {
        $ledPin->setValue(OutputPinInterface::VALUE_HIGH);
        usleep(100000);
        $ledPin->setValue(OutputPinInterface::VALUE_LOW);
        usleep(100000);
    }

    // Returning false would cause the loop below to exit
    return true;
});

// Loop until an interrupt callback returns false, this code will iterate every 5 seconds
while ($interruptWatcher->watch(5000)); 
```

## 更进一步

我们已经看到了如何使用 PHP 来响应电子输入和控制电子输出。一旦我们有了这种理解，我们就可以在投入和产出上变得越来越大胆，从而创造出越来越多令人敬畏的项目。

就我个人而言，我用流量传感器和挡风玻璃清洗液泵制作了一台[饮料机](https://twitter.com/AndrewCarterUK/status/730940968820719616),玩得很开心。

> 为 [@phpday](https://twitter.com/phpday) 准备的谈话可能会进行得更好。pic.twitter.com/Z9IWGEQftq
> 
> —安德鲁·卡特(@ AndrewCarterUK)[2016 年 5 月 13 日](https://twitter.com/AndrewCarterUK/status/730940968820719616)