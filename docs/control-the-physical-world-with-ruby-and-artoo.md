# 用鲁比和 R2 控制物质世界

> 原文：<https://www.sitepoint.com/control-the-physical-world-with-ruby-and-artoo/>

![IoTWeek_Gray](img/defe243339c5bf160966142955834754.png)

SitePoint 的第 10 周！整个星期，我们都在发布关于互联网和物理世界的交集的文章，所以请继续查看[物联网标签](https://www.sitepoint.com/blog/)以获取最新更新。

[![artoo](img/40ad6c8739dfe51da18dfa1d9851c2c2.png)](http://artoo.io)

Ruby 是一种非常漂亮的语言。但是，在过去，每当有人提到“嵌入式”时，没有人会想到“滑头”这个词。取而代之的是，脑海中经常浮现出写得很糟糕的 C 语言的画面，其中有大量的比特混合，或者倒叙了在光线不足的情况下花费大量时间试图与实验板战斗的场景。幸运的是，这不再是 R2 的情况。R2 是一个很酷的库，它允许 Ruby 控制许多不同的硬件平台并与之交互(比如 Sphero，Arduino 等等)。).在本文中，我们将重点关注 R2 的启动和运行，并使用 [Arduino](https://www.arduino.cc/) 和标准键盘查看几个不同的 R2 运行示例。

## 设置软件

假设你有一个功能版的 Ruby(最好安装了 [RVM](https://rvm.io) 或者 [`rbenv`](https://rbenv.org) ，安装 R2 超级简单:

```
 gem install artoo 
```

此外，我们还需要为我们想要使用的每一个硬件单独准备宝石。对于本文，我们将使用 Arduino 作为我们的主要平台。要让它发挥作用:

```
 gem install artoo-arduino 
```

这就是我们需要的软件开始滴答！下一步是让阿杜伊诺人通过 R2 与我们对话。

## 设置硬件

您可能已经听说过，Arduino 基于一个名为 Atmel AVR 的微控制器平台。在 AVR 开发中曾经有一段时间，你通常要么从一个拼凑的并行端口程序员开始，要么买一个 STK500，然后开始用 c 编写一些位碰撞代码。然后，如果你在 Windows 上，你可以毫无问题地使用 AVRStudio。如果你在 Linux 上，你必须使用`gcc`工具链(它实际上工作得很好)来构建你的 C 代码，然后使用 [avrdude](http://www.nongnu.org/avrdude/) 将它推送到微控制器。

一般来说，这个过程工作得很好，但是当东西坏了，就很难搞清楚发生了什么。我清楚地记得，在让步并给自己买了一台 Arduino 之前，我花了大量的时间试图让并行端口程序工作。Arduino 的出现使整个过程变得简单了一百万倍，但它需要使用这种有点烦人的图形界面。

幸运的是，R2 让我们没有经历任何痛苦。相反，我们可以使用 [Gort](http://gort.io/) ,它有一个很好的[安装页面](http://gort.io/documentation/getting_started/downloads/),适用于各种平台。Gort 使得将固件上传到通过串行端口(如 USB)连接的设备变得更加容易。它并没有重新实现 avrdude 之类的软件所提供的功能，只是将这些功能包装在一个更易于使用的界面中。第一步是将 Arduino 插入机器的 USB 端口。然后，我们可以用`gort`扫描命令搜索它:

```
 gort scan serial 
```

这会给你一个串行端口列表，你可以找出哪个端口连接了你的 Arduino。Gort 可以连接各种设备。因为我们希望它特别与 Arduino 对话，所以我们必须为 gort 安装 Arduino 相关的包:

```
 gort arduino install 
```

而且，就是这样！稍后，我们将使用`gort arduino upload`命令将我们的代码推送到 Arduino。

## R2 建筑

R2 不仅仅是一个允许我们在 Arduino 上用 Ruby 写代码的库。更确切地说，这是一种以更加有效和干净的方式在“组”中构建设备的方法。R2 文档中的设备被称为“机器人”这些不一定具有我们一般与机器人联系在一起的特征(如运动)；它们应该是以某种方式与物理世界交互的设备(例如 Arduino、Sphero 等)。它使用“套接字”从这些设备接收事件，尽管这些可能是通过类似 Arduino 的串行连接。来自设备的这些事件由一段称为 Master 类的代码处理。基本上，这段代码允许这些设备一起工作，而不是作为完全独立的单元。您可以将其视为集群中的“主”节点。

所以，R2 的重点是让你能够协调多个设备(“机器人”)一起做事情，而不是单独做。

如果 R2 停在那里，这将是足够酷的调查。但是，事实并非如此。R2 允许你在 HTTP 和 WebSockets 上公开一个 API，这样你就可以构建一个 Web/iOS/Android/etc。这些设备上的应用程序。例如，如果你想用 Arduino 控制房间里的灯光，你就不必再自己构建所有的东西来与 Arduino 通信。相反，使用 R2，您已经有了一个允许您这样做的公开 API。你所要做的就是编写控制灯光的代码(使用 R2 的助手，等等)。用 Ruby)编写一个漂亮的面向用户的应用程序。R2 会处理剩下的。考虑到过去用硬件设备连接网络服务器是多么痛苦，这是一个巨大的进步。尤其是如果你使用类似于[粒子平台](https://www.particle.io/?redirected=true)的东西，你可以构建一些非常棒的、与云相关的东西。

## 发光二极管

现在我们知道了 R2 在更高层次上是如何工作的，让我们进入本质。我们将分解一个来自 R2 的例子，它允许我们构建一个响应按钮的 LED。直接从 R2 文档，我们有:

```
require 'artoo'

connection :arduino, adaptor: :firmata, port: '/dev/ttyACM0'

device :led, driver: :led, pin: 13
device :button, driver: :button, pin: 2, interval: 0.01

work do
  puts "Press the button connected on pin #{ button.pin }..."

  on button, :push    => proc { led.on }
  on button, :release => proc { led.off }
end 
```

你应该用你用`gort scan serial`找到的串口替换`/dev/ttyACM0`。虽然这段代码看起来很简单，但它在幕后做了很多工作，文档目前也没有很好地解释到底发生了什么。首先，我们通过以下调用建立到设备的连接:

```
connection :arduino, adaptor: :firmata, port: '/dev/ttyACM0' 
```

为了与 Arduino 对话，R2 使用了一种叫做 [Firmata](http://firmata.org/wiki/Main_Page) 的协议。Firmata 协议已经在各种设备上实现，并且允许 R2 构建 API 等。在 Arduino 的顶部。

```
device :led, driver: :led, pin: 13 
```

这里我们引入了一个全新的概念:设备驱动程序。R2 使用设备驱动程序的概念定义了 Arduino 可以与之交互的东西。这些函数根据设备的功能接受参数。Artoo Arduino 软件包中包含了几个设备驱动程序，还可以很容易地添加更多。驱动程序只接受一个参数:它所连接的引脚。此时，您应该将 LED 连接到 Arduino 的第 13 号引脚。

```
device :button, driver: :button, pin: 2, interval: 0.01 
```

驱动程序有点复杂。它需要一个`pin`，这是 Arduino 上的引脚，按钮在这里与一个`interval`相连。如果你以前读过 Arduino 代码，这看起来会很熟悉:事件被触发的时间间隔。

```
work do
  ...
end 
```

这是事件循环开始的地方。在这个代码块中，我们应该处理设备可以触发的事件(例如，按钮被按下)。我们用块中的两行代码来实现:

```
on button, :push    => proc { led.on }
on button, :release => proc { led.off } 
```

设备驱动程序公开了我们编码的两个事件:`release`和`push`。然后我们使用名为`led`的设备(我们之前在`device`的第一个参数中命名了它)，并为相应的动作打开和关闭它。为了“部署”这段代码，我们需要 Arduino 来学习如何与计算机对话:

```
gort arduino upload /dev/ttyACM0 
```

当然，你应该用你的 Arduino 所连接的设备来替换`/dev/ttyACM0`。这将允许 Arduino 在 Firmata 中说话，这样我们就可以告诉它通过 USB 做什么。假设我们将代码保存在`arduino_pushbutton.rb`中。然后，我们可以用标准的 Ruby 运行它:

```
ruby arduino_pushbutton.rb 
```

它应该连接到 Arduino，并根据按钮的状态打开和关闭 LED。

## 键盘

事实证明，我们并不局限于将微控制器板作为物理输入设备。相反，我们甚至可以使用键盘，因为它有一个设备驱动程序。要获得设备驱动程序，我们只需:

```
gem install artoo-keyboard 
```

现在，让我们来看一个使用键盘作为输入设备的例子:

```
require 'artoo'

connection :keyboard, adaptor: :keyboard
device :keyboard, driver: :keyboard, connection: :keyboard

work do
  on keyboard, :key => :got_key
end

def got_key(sender, key)
  puts sender
  puts key
end 
```

让我们来分解这个代码。

```
connection :keyboard, adaptor: :keyboard
device :keyboard, driver: :keyboard, connection: :keyboard 
```

就像我们告诉 R2 如何连接到 Arduino 一样，我们告诉它我们想连接到这里的键盘。请注意，键盘需要的只是适配器的名称，它会想出如何获得键盘输入(即不需要串行输入设备)。然后，我们注册我们想要在设备上使用的设备驱动程序，这样我们就可以得到我们想要的事件。

```
work do
  on keyboard, :key => :got_key
end 
```

`:keyboard`设备驱动程序公开了一个叫做`key`的事件。我们正在把它和一个特别的回调联系起来。

```
def got_key(sender, key)
  puts sender
  puts key
end 
```

对于回调，我们只需打印出发送事件给我们的设备(即键盘)和键值。

## 把它放在一起

到目前为止，我们一次只能使用一种设备。这有点无聊，也有点打击使用像 R2 这样的东西的意义。我们希望这些设备能够相互作用，以产生更有意义的东西，而不仅仅是由按钮打开的 LED。因此，我们将建立一个“键盘记录器”，并试图窃取人们的 Gmail 密码。

嗯，算是吧。我们要做的是使用键盘驱动程序来判断某人何时键入“gmail.com ”,一旦他们这样做了，就打开 Arduino 上的 LED，这样我们就知道要立即查找密码。不是特别有用，但应该允许我们一起使用键盘和 Arduino:

```
require 'artoo'

connection :keyboard, adaptor: :keyboard
device :keyboard, driver: :keyboard, connection: :keyboard

connection :arduino, adaptor: :firmata, port: '/dev/ttyACM0'
device :led, driver: :led, pin: 13

read_so_far = ""

work do
  on keyboard, :key => :got_key
end

def got_key(sender, key)
  read_so_far = read_so_far + key
  if read_so_far.end_with? "gmail.com"
    led.on
  end
end 
```

这应该会让我们有一些互动！让我们分解代码。

```
connection :keyboard, adaptor: :keyboard
device :keyboard, driver: :keyboard, connection: :keyboard

connection :arduino, adaptor: :firmata, port: '/dev/ttyACM0'
device :led, driver: :led, pin: 13 
```

同样，我们首先设置连接和设备驱动程序。然后，进入事件循环:

```
work do
  on keyboard, :key => :got_key
end 
```

这里唯一真正不同的是回调本身:

```
def got_key(sender, key)
  read_so_far = read_so_far + key
  if read_so_far.end_with? "gmail.com"
    led.on
  end
end 
```

当我们注意到用户输入了“gmail.com”时，我们会打开 LED！当然，这只是一个例子。当键盘焦点不在控制台窗口中时，键盘设备驱动程序将不起作用。

## 包装它

对于 R2 的可能性，我们只触及了皮毛。然而，R2 附带的文档有点缺乏，我们已经填补了空白，让它有可能做更多令人兴奋的事情。想获得一些灵感，请听听这个项目的创始人之一的演讲。很想看看人们会用 R2 建造什么！

## 分享这篇文章