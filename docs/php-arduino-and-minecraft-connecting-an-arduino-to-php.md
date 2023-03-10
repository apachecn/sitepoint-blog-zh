# PHP，Arduino，还有…《我的世界》？将 Arduino 连接到 PHP！

> 原文：<https://www.sitepoint.com/php-arduino-and-minecraft-connecting-an-arduino-to-php/>

![IoTWeek_Gray](img/defe243339c5bf160966142955834754.png)

SitePoint 的第 10 周！整个星期，我们都在发布关于互联网和物理世界的交集的文章，所以请继续查看[物联网标签](https://www.sitepoint.com/blog/)以获取最新更新。

在这个系列的第一部分，我们学习了一点《我的世界》和我们可以在里面制作的电路。我们还制作了一个电路，当我们公寓的门被打开时会发出警报。

然后，我们将这个虚拟警报与一个监听 PHP 脚本挂钩，这样我们就可以在 PHP 脚本的上下文中知道门何时被打开。

在这一部分，我们将构建一个小型的[基于 Arduino 的](https://www.arduino.cc/)报警电路。我们将学习如何触发警报，使用官方 IDE 和编程语言，然后使用 Firmata。

我们将通过连接警报电路到《我的世界》电路来完成这个系列，所以我们听到了《我的世界》大厦的真实警报。

*你可以在[https://github . com/site point-editors/tutorial-PHP-arduino-and-minecraft/tree/connect-arduino](https://github.com/sitepoint-editors/tutorial-php-arduino-and-minecraft/tree/connect-arduino)T3 找到本教程的代码*

## Arduino 编程速成班

Arduino 是众多业余电路控制器中的一种。有一些不同的设计，像较大的 [Uno](https://www.arduino.cc/en/Main/ArduinoBoardUno) 或较小的 [Micro](https://www.arduino.cc/en/Main/ArduinoBoardMicro) 。

它们的共同点是都有用于连接不同组件(如按钮、传感器和 led)的端口/引脚，并且理解一种通用的编程语言。编程语言是 C 和 C++，这大概是关于 Arduino 最难学的东西。

你可以把 Arduino 板想象成更小、更简单的计算机，能够抽象出电路的基础，并为它们提供编程接口。

你需要一个 [Arduino](https://www.sparkfun.com/products/11021) (或者稍后讨论的替代品)和一个闹钟[蜂鸣器](https://www.sparkfun.com/products/7950)或者 [LED](https://www.sparkfun.com/products/12062) 。

蜂鸣器和/或 LED 有两个引脚:一个用于接地(有时标记为“gnd”或“-”)，另一个需要连接到 Arduino 端口/引脚:

![The illustration of a circuit](img/78656bab340fc0710e88a282ea16712a.png)

*我在 [Fritzing](http://fritzing.org/download) 做了这个模型。太棒了，你应该试一试！*

接下来，我们需要下载官方 IDE。前往[https://www.arduino.cc/en/Main/Software](https://www.arduino.cc/en/Main/Software)下载适合你的操作系统的版本。加载它，您应该会看到类似这样的内容:

![A screenshot of the Arduino IDE](img/90509ff83e0d3a8f7f6fcb638923078f.png)

主要的 Arduino 编程语言是 C 和 C++。函数看起来类似于 PHP 7 函数，其中返回类型代替了关键字`function`。所以我们看到一些方法应该返回`void`(意味着它们不应该返回任何东西)。

Arduino 启动时会调用`setup`函数。顾名思义，我们可以用它来进行开发板的初始设置。你看到 Arduino 上每个引脚/端口旁边的数字了吗？这些可用于输入或输出(数字和模拟)。事实上，在使用之前定义每个引脚的功能通常是个好主意。

许多 Arduino 板都带有已经连接到板上 LED 的第 13 针。我们可以使用下面的代码打开它:

```
void setup() {
    pinMode(13, OUTPUT);
}

void loop() {
    digitalWrite(13, HIGH);
} 
```

在我们上传代码到 Arduino 之前，我们需要找到它在哪里。打开终端窗口，尝试以下命令:

```
ls /dev | grep usbmodem 
```

这个命令列出了`/dev`中的所有内容(在 OSX 和大多数 Linux 系统中，我们可以在这里找到插入我们系统的 USB 设备的名称)。然后，它获取这个列表，并通过 Grep 命令来过滤掉所有包含单词`usbmodem`的条目。

记下归还的物品。如果您没有看到任何，可能是您的 Arduino 有问题，或者它可能没有正确插入。让我们知道，在评论中，如果你不能通过这一步…

返回 Arduino IDE，从“工具”→“端口”菜单中选择匹配的 Arduino 端口。知道实际的设备名称是很好的，即使没有终端命令您也能猜到，因为我们稍后需要确切的名称。

一旦您设置了正确的端口，并选择了适用的 Arduino 类型(从“工具”→“电路板”菜单)，您应该能够将示例上传到电路板。

![An animated image of the IDE in use](img/9ad7b3403982587143e5746c81453b4a.png)

Arduino IDE 获取 C 或 C++代码，并将其编译成主板可以理解的字节码。对我们 PHP 人来说，编写、编译和上传代码到 Arduino 的过程慢得令人沮丧。我们将很快使用更简单的方法。

通过“文件”→“示例”菜单，您可以找到许多 Arduino 程序示例。在掌握 C 语法和 board 功能时，我发现这些非常有价值！

事实上，检查 Arduino 是否正常工作以及连接是否正确的更简单的方法是上传“Blink”示例:

[https://www.youtube.com/embed/BhTMh2g7KMM](https://www.youtube.com/embed/BhTMh2g7KMM)

如果您已经正确连接了蜂鸣器或 LED，您甚至可以修改脚本，将适用的端口设置为`HIGH` / `Low`。但这是一个缓慢而令人沮丧的过程，必须学习 C 语言，并不断地将代码重新上传到 Arduino。

有时弄清楚不同组件是如何工作的是很棘手的。[这里有一个指导让你开始！](https://medium.com/@assertchris/electronic-components-20bfc59004bd)

## 熟悉的 PHP

让我们尝试一些不同的东西。在 Arduino 的世界里，有一种我们可以交流的通用语言。这是一个名为 Firmata 的协议，它使我们能够使用任何语言来使我们的技术运转。

转到“示例”→“Firmata”→“标准 Firmata”示例，并将其上传到您的 Arduino。它现在能够通过标准串行接口进行通信。

如果你还没有从[上一篇文章](#)中下载代码，现在是时候了。我们将在它的基础上构建…

我们需要在现有的基础上增加一个新的库:

```
composer require carica/firmata 
```

这个库充当我们刚刚上传到 Arduino 的标准 Firmata 代码的客户端。在`index.php`中我们可以添加以下代码，来连接 Arduino:

```
use Carica\Io;
use Carica\Firmata;

$board = new Firmata\Board(
    Io\Stream\Serial\Factory::create(
        "/dev/cu.usbmodem14141", 57600
    )
); 
```

在尝试运行这段代码之前，您肯定需要关闭 Arduino IDE。它打开了与 Arduino 的连接，这阻止了 PHP 脚本的后续连接。如果您在任何时候都无法连接到 Arduino，只需断开 Arduino 并将其重新连接到同一个 USB 端口。这将打破任何可能阻碍事情的开放连接。

您需要将`cu.usbmodem14141`替换为您从终端命令中获得的设备名称(或者在 IDE“工具”→“端口”菜单中找到)。

让我们添加一个事件监听器，当 PHP 脚本最终连接到 Arduino:

```
$board
    ->activate()
    ->done(
        function() use ($board, $loop, $watcher) {
            $pin = $board->pins[9];
            $pin->mode = Firmata\Pin::MODE_PWM;

            print "connected to Arduino";
        }
    );

$loop->run(); 
```

我们可以附加一个回调函数，在 PHP 脚本最终连接到电路板后运行。我们必须设置 Arduino 引脚/端口的模式，就像我们在 Arduino IDE C 代码中所做的那样。这次我们将引脚 9 设置为 [PWM(脉宽调制)](https://en.wikipedia.org/wiki/Pulse-width_modulation)模式。

*大多数 Arduino 引脚可以设置为`HIGH`或`LOW`。设置为`HIGH`的设备将打开(LED 将点亮)，设置为`LOW`的设备将关闭。PWM 是一种增量模式，0 到 255 之间的值为连接的设备提供颗粒电荷。从技术上讲，PWM 更像是在 x/255 的时间内在`HIGH`和`LOW`之间快速切换，否则它将被设置为`HIGH`，但可观察到的效果是相同的。*

注意到我们已经让`$watcher`和`$loop`通过了吗？`$loop`是事件循环的一个实例，这在 PHP 应用程序中并不常见。如果你不熟悉什么是事件循环，或者它们是如何工作的，看看[我们不久前给出的介绍](https://www.sitepoint.com/an-introduction-into-event-loops-in-php) …

我们可以使用类似于 JavaScript 的`setInterval`函数的概念，通过将回调附加到我们这里的事件循环:

```
print "connecting.";

$board
    ->activate()
    ->done(
        function() use ($board, $loop, $watcher) {
            print "connected.";

            $pin = $board->pins[9];
            $pin->mode = Firmata\Pin::MODE_PWM;

            $loop->setInterval(
                function() use ($pin, $watcher) {
                    // this is called about every second
                },
                1000
            );
        }
    ); 
```

因此，我们可以使用事件循环计时器，而不是使用无限循环来检查日志文件的更改。结合脚本的其余部分，我们可以将引脚 9 设置为一个介于`0`和`255`之间的值:

```
$loop->setInterval(
    function() use ($pin, $watcher) {
        print "check.";

        $watcher->findChanges();
        $changes = $watcher->getUpdatedResources();

        if (count($changes) > 0) {
            $first = $changes[0];

            $lines = file($first);

            for ($i = count($lines) - 1; $i > -1; $i--) {
                if (stristr($lines[$i], "CHAT")) {
                    if (stristr($lines[$i], "closed")) {
                        print "open.";

                        $pin->analog = 0; // 0/255
                    }

                    if (stristr($lines[$i], "open")) {
                        print "closed.";

                        $pin->analog = 0.5; // 127/255
                    }

                    break;
                }
            }
        }
    },
    1000
); 
```

我们使用了与上次大致相同的代码。这一次，我们不是将`open`或`closed`写入终端，而是设置引脚 9 的`analog`值。`analog`与我们之前进行的 PWM 设置相关。如果我们想使用更简单的`HIGH`或`LOW`值，我们可以将引脚模式设置为数字:

```
$pin->mode = Pin::MODE_OUTPUT; 
```

…并设置引脚的`digital`值:

```
if (stristr($lines[$i], "closed")) {
    $pin->digital = 0;
}

if (stristr($lines[$i], "open")) {
    $pin->digital = 1;
} 
```

[https://www.youtube.com/embed/aqFWijIcpII](https://www.youtube.com/embed/aqFWijIcpII)

## 把大猩猩带进来

在解决这个问题时，我发现 Arduino/Firmata/OSX 的链接有时不稳定。Carica Firmata 库使用几种不同的方法(取决于扩展的可用性)，默认方法是本地套接字流。不幸的是，Arduino I 版本在建立新连接后会自动重置，Firmata 在与 Arduino 通信前需要短暂的延迟。

本地套接字流实现(在 Carica Firmata 中)与板通信太快，这中断了引导周期。麻烦随之而来。

我可以通过安装由 Carica Firmata 支持的 Gorilla 扩展来避免这种情况。你可以在 Github 找到关于如何做这件事的说明。构建并启用 Gorilla 扩展，您就不会遇到我遇到的同样的稳定性问题…

尽管有许多不同的微控制器可以用于这个项目，但我将重点放在 Arduino 上，因为这是我所拥有的。如果你有其他的东西(与 Firmata 兼容),那么这部分的代码应该适合你。你必须弄清楚如何将蜂鸣器或 LED 连接到你的微控制器上…

我们已经成功地在《我的世界》内部实现了一半电路，另一半在 Arduino 上实现。此外，我们还发现了一种通过 PHP 将它们连接起来的方法。想想可能性吧！

## 分享这篇文章