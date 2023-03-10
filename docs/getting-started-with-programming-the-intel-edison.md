# 英特尔 Edison 编程入门

> 原文：<https://www.sitepoint.com/getting-started-with-programming-the-intel-edison/>

在我对英特尔 Edison 的简要介绍之后，是时候更熟悉该平台的软件方面了。

我将展示如何开始开发和部署您的想法，如何从传感器/致动器读取/写入，以及如何与云通信。为您提供开始修补和入侵物联网设备所需的一切。

## 安装和配置 SDK

第一件事是为项目选择你喜欢的语言。为了满足更多开发人员的需求，英特尔简化了许多不同编程语言的使用，并提供了几个 SDK。

你可以在本文的[中了解所有选项。](https://www.sitepoint.com/introduction-intel-edison-iot-developers/)

## 英特尔爱迪生主板安装程序

最新版本的英特尔 Edison SDK 可通过统一的安装程序获得，您可以在此处获得[。](https://software.intel.com/en-us/iot/downloads)

确保拥有最新版本的 Java JDK/JRE，并继续安装过程。

![Intel Edison Board Installer](img/59363bbd4cc55a92f00834ec94bc69b5.png)

这将为开发板安装合适的驱动程序，更新 Edison 上的 Yocto Linux 映像，并让您选择自己喜欢的 IDE。安装程序适用于 Windows 和 Mac OS，Linux 用户需要单独安装首选 IDE。

## 准备发展

[组装](https://software.intel.com/en-us/assembling-and-connecting-your-board)开发板，设置[串口](https://software.intel.com/en-us/setting-up-serial-terminal-intel-edison-board)和[连接 Edison 到 WiFi](https://software.intel.com/en-us/connecting-your-intel-edison-board-using-wifi) 。

记下主板 IP 地址，Edison 应该通过 [Zeroconf](https://en.wikipedia.org/wiki/Zero-configuration_networking) 暴露自己，但我们都知道技术并不总是有效。

现在我们可以配置我们的 IDE 了。

### 黯然失色

如果你要用 C++开发，打开 Eclipse，选择 *IoT DevKit - >创建目标连接*项。

![Eclipse Intel Edison Configuration](img/2ca1895f8089d3d18c284cc234627c2c.png)

您应该看到您的板列出，否则只需输入一个名称和 ip 地址之前提到的。

### 英特尔 XDK 公司

启动 XDK，看看屏幕的底部面板。

点击*物联网设备*下拉菜单，选择*重新扫描设备*或输入板卡 ip 地址，如下图所示。

![Intel XDK Edison Configuration](img/2bf983ebb4585198e8219306b099222c.png)

您应该会在控制台中看到一条成功消息。

### 外壳访问

板上启用了 SSH，所以如果您觉得更舒服的话，您可以跳过所有 IDE 的麻烦，从 shell 中做任何事情。

## 你好爱迪生

该打招呼了。

### C++

在 Eclipse 点击，*IoT dev kit—>创建 C++项目*并选择一个空白模板。

![Choose the blank template](img/07fb37e6380be290d5609d7e15e78a14.png)

并选择已经定义的目标。

添加以下代码:

```
#include <iostream>
using namespace std;

int main() {
    std::cout << "Hello, Edison!\n";
    return 0;
    }
```

单击绿色的播放按钮运行代码。Eclipse 将构建项目，部署到板上并运行它。在第一次运行时，Eclipse 将要求输入板密码。

![Eclipse asking password for Intel](img/45db8e0cf42ee035d26f2917128436ad.png)

您可以在屏幕底部的控制台中跟踪进度和应用程序输出。

### JavaScript/节点 JS

打开 XDK，点击*项目*选项卡，选择空白物联网模板开始一个新项目。

![Intel XDK Template](img/88bc06b469be77d3f5f49f05d2799fb7.png)

添加以下代码:

```
console.log("Hello, Edison!")
```

使用底部工具栏上的运行按钮。XDK 会问你是否要上传更新的项目，点击*是*并在底部控制台检查输出。

![Intel XDK App output](img/ce8df73e79c5bc0cc7e3f4b123831060.png)

### 计算机编程语言

在您喜欢的文本编辑器中，编写以下代码:

```
print "Hello, Edison!"
```

另存为 *hello.py* 并运行它:

```
python hello.py
```

### 摘要

使用爱迪生的一个好处是没有什么新东西要学。您可以用您当前喜欢的语言编写代码，使用您选择的库，并做您通常在 Linux 系统上做的任何事情。

主要区别在于，你可以在一个微型设备上运行你的项目，准备好制作可穿戴或互联网 *<q>东西</q>* 。

但是我们对制作更有趣的东西感兴趣，利用平台的 I/O 能力使<q>事物</q>变得智能。

## 处理传感器和执行器

我最喜欢爱迪生的一个方面是，即使像我这样的软件人员也可以处理硬件。英特尔为此提供了两个有用的库，`lib rmaa`和`lib upm`。

第一个为电路板提供了抽象，因此可以通过抽象类访问端口和其它硬件特性，而无需知道确切的型号和数据手册细节。

是时候做一些令人兴奋的事情了…闪烁一个 led！(好吧，没那么刺激)。

感谢`lib mraa`这很简单:

### C++

```
include <iostream>
#include <unistd.h>
#include <signal.h>
#include "mraa.hpp"

static int iopin = 13;
int running = 0;

void
sig_handler(int signo)
{
    if (signo == SIGINT) {
        printf("closing IO%d nicely\n", iopin);
        running = -1;
    }
}

int
main(int argc, char** argv)
{

    mraa::Gpio* gpio = new mraa::Gpio(iopin); // Select the pin where the led is connected
    if (gpio == NULL) { // Check for errors
        return MRAA_ERROR_UNSPECIFIED;
        }

    mraa_result_t response = gpio->dir(mraa::DIR_OUT); // Set "direction" of our operation, we use it as output here
    if (response != MRAA_SUCCESS) {
        mraa::printError(response);
        return 1;
    }

    while (running == 0) { // infinite loop just to test
        response = gpio->write(1); // set the output pin to "high" value, this will cause the led to turn on
        sleep(1);

        response = gpio->write(0); // set the output pin to "low" value, this will cause the led to turn on
        sleep(1);
    }

    delete gpio; // cleanups
    return 0;
    }
```

### java 描述语言

```
var m = require('mraa');

var gpio = new m.Gpio(13); // Select the pin where the led is connected
gpio.dir(m.DIR_OUT); // Set "direction" of our operation, we use it as output here
var ledState = true; // Led state

function blinkblink() // we define a function to call periodically
{
  gpio.write(ledState?1:0); // if ledState is true then write a '1' (high, led on) otherwise write a '0' (low, led off)
  ledState = !ledState; // invert the ledState
  setInterval(blinkblink,1000); // call this function every second
}

blinkblink(); // call our blink function
```

### 计算机编程语言

```
import mraa
import time

gpio = mraa.Gpio(13) # Select the pin where the led is connected
gpio.dir(mraa.DIR_OUT) # Set "direction" of our operation, we use it as output here

while True:
    gpio.write(1) # set the output pin to "high" value, this will cause the led to turn on
    time.sleep(0.2)
    gpio.write(0) # set the output pin to "low" value, this will cause the led to turn off
    time.sleep(0.2)
```

很简单，不是吗？

现在让我们看看如何从传感器读取值。在本例中，我将使用一个连接到引脚 *Aio 0* 的温度传感器。

通常，要从传感器中检索温度值，需要读取原始值，然后检查传感器数据手册，理解原始值的含义，并在使用前处理该值。

这里`Lib UPM`来帮忙了，我们可以使用库提供的类来抽象所有底层细节。我将使用 javascript，但是正如您之前看到的，同样的东西可以在任何语言中实现。

```
var groveSensor = require('jsupm_grove');
var tempSensor = null;
var currentTemperature = null;
var celsius = 0;

function init() {
    setup()
    readRoomTemperature();
    }

function setup() {
    // Create the temperature sensor object using AIO pin 0
    tempSensor = new groveSensor.GroveTemp(0);
}

function readRoomTemperature() {
        celsius = tempSensor.value();
        console.log("Temperature: "+ celsius + " degrees Celsius");
}

init();
```

现在，我们可以结合上述例子，只在达到预定温度时打开 led。

```
var m = require('mraa');
var MAX_TEMP = 30;
var groveSensor = require('jsupm_grove');
var tempSensor = null;
var currentTemperature = null;
var gpio = null;

function init() {
        setup()
    setInterval(checkTemperature, 1000);
        }

function setup() {
        // Create the temperature sensor object using AIO pin 0
        tempSensor = new groveSensor.GroveTemp(0);
        gpio = new m.Gpio(13); // Select the pin where the led is connected
        gpio.dir(m.DIR_OUT); // Set "direction" of our operation, we use it as output here

}

function readRoomTemperature() {
        var celsius = tempSensor.value();
        console.log("Temperature: "+ celsius + " degrees Celsius");
                return celsius;
}

function checkTemperature() {
        var temp = readRoomTemperature();
        if(temp>MAX_TEMP)
                gpio.write(1);
        else
                gpio.write(0);
                }

init();
```

我们可以使用`Lib UPM`提供的类，用几行代码在 LCD 显示屏上显示一条消息。

```
// LibUpm requires
var groveSensor = require('jsupm_grove');
var LCD = require('jsupm_i2clcd');

var myLcd;
var currentTemperature = null;

function init() {
        setup()
        setInterval(checkTemperature, 1000);
        }

function setup() {
        // Create the temperature sensor object using AIO pin 0
        tempSensor = new groveSensor.GroveTemp(0);
        myLcd = new LCD.Jhd1313m1 (6, 0x3E, 0x62); // setting up the grove lcd connected with i2c

}

function readRoomTemperature() {
        var celsius = tempSensor.value();
        console.log("Temperature: "+ celsius + " degrees Celsius");
        return celsius;
}

function checkTemperature() {
        var temp = readRoomTemperature();
        var lcdMessage = "Room temp:" +  temp + " C";
        myLcd.setCursor(1,1);
        myLcd.write(lcdMessage);
}

init();
```

浏览 [lib UPM 文档](http://iotdk.intel.com/docs/master/upm/modules.html)了解受支持的传感器和致动器，你会明白有多少*东西*你可以用同样简单的方式使用。

但是 IoT 是关于**互联网**的，所以让我们连接起来吧。

Edison 上的完整 Linux 栈的一个优点是，你可以使用任何现有的标准库来访问 web，并且使用 REST API、xml 和 json 等所需的所有工具在一个项目中都是可用的。

## 网络服务

在 JavaScript 中，我们可以使用`lib http`进行 API 调用。我将使用它来查询[打开的天气地图](http://openweathermap.org/) api，并在 LCD 上显示当前天气。

```
var myLcd;
var LCD = require('jsupm_i2clcd');
var http = require('http');

//  openweathermap apu uri
var owmUrl = "http://api.openweathermap.org";
// prepare the query
var owmPath = "/data/2.5/weather?unit=metric&q="
// My lovely city name
var yourCity = "Brescia,it";

function init() {
    setup()
    setInterval(checkWeather, 60000);
    }

function setup() {
    myLcd = new LCD.Jhd1313m1 (6, 0x3E, 0x62); // setting up the grove lcd connected with i2c, the address is in the doc
}

function checkWeather() {
    // url building
    var url = owmUrl + owmPath + yourCity

    try {
    // api docs : http://openweathermap.org/api

    // build the http request
    http.get(url, function(res) {
        var body = '';

        // read the response of the query
        res.on('data', function(chunk) {
        body += chunk;
        });

        res.on('end', function() {
        // now parse the json feed
        var weather = JSON.parse(body)

        // http://openweathermap.org/weather-conditions
        var id = weather.weather[0].id; // get the current weather code

        // show the message on the display
        lcdMessage = weather.weather[0].description;
        myLcd.setCursor(0,0);
        myLcd.write(lcdMessage);

        });
    }).on('error', function(e) {
        // check for errors and eventually show a message

        lcdMessage = "Weather: ERROR";
        myLcd.setCursor(0,0);
        myLcd.write(lcdMessage);

    });
    } catch(e) {
    lcdMessage = "Weather: ERROR";
    myLcd.setCursor(0,0);
    myLcd.write(lcdMessage);
    }
};

init();
```

## 结论

这些简单的例子可以作为集成传感器、执行器和互联网的更复杂应用的基础。

在下一篇文章中，我们将构建一个完整的项目来展示该平台的可能性，而不是大量的代码，让任何人都有能力加入物联网的宣传，并在这个过程中获得乐趣。

## 分享这篇文章