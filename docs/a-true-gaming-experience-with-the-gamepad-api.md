# Gamepad API 带来真正的游戏体验

> 原文：<https://www.sitepoint.com/a-true-gaming-experience-with-the-gamepad-api/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

Web 上的游戏已经随着诸如 Canvas、WebGL 和 WebAudio 之类的 HTML5 技术取得了长足的进步。现在可以在浏览器中制作高保真的图形和声音。然而，要提供真正的游戏体验，您需要专为游戏设计的输入设备。Gamepad API 是 W3C 提出的标准，旨在提供跨浏览器的一致 API。

Gamepad API 允许用户[将 Xbox 控制器](http://support.xbox.com/en-US/xbox-one/accessories/controller-pc-compatibility?WT.mc_id=13409-DEV-sitepoint-article33)等设备连接到计算机，并使用它们获得基于浏览器的体验！如果你有游戏手柄，试着把它插到你的电脑上，然后按一个按钮。你会看到下面的 Xbox 控制器亮起，以反映你的每个动作！

![Xbox Controller](img/349c7c96846fe4045b292fd691a7dec4.png)

在这里互动地尝试一下。

本教程是 Flight Arcade 系列的第三篇，旨在展示在网络平台和新的[微软 Edge 浏览器和 EdgeHTML 渲染引擎](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article33&utm_campaign=SitePoint)中的可能性。您可以在 flightarcade.com 找到关于 WebGL 和 Web API 的前两篇文章，以及本文的交互式代码和示例。

[youtube xyaq9TPmXrA]

## 灵活的 API

Gamepad API 的智能设计考虑到了灵活性。在基本级别上，它提供对按钮和轴的访问。按钮值的范围从`[0 .. 1]`开始，而轴的范围从`[-1 .. 1]`开始。所有值都被标准化为这些范围，因此开发人员可以预期设备之间的一致行为。

`Gamepad`对象提供了所连接游戏手柄的制造商和型号的详细信息。更有用的是一个描述游戏手柄一般类型的`mapping`属性。目前唯一支持的映射是`standard`，它对应于许多流行游戏控制台使用的控制器布局，如 [Xbox](http://www.xbox.com/?WT.mc_id=13409-DEV-sitepoint-article33) 。

标准控制器映射有两个棒，每个棒由 2 个轴表示(`x`和`y`)。它还包括一个 D-pad、4 个游戏按钮、顶部按钮和触发器:所有这些都在 Gamepad API 中表示为按钮。

当前的 Xbox 控制器将按钮状态报告为 0(正常状态)或 1(按下)。然而，你可以想象未来的控制器可以报告施加到每个按钮上的力的大小。

Xbox D-pad 也报告离散值(`0`或`1`)，但棒提供整个轴范围内的连续值`[-1 .. 1]`。这种额外的精度使得在我们的飞行任务中驾驶飞机变得更加容易。

## PxGamepad

Gamepad API 提供的按钮和轴阵列具有前瞻性，是一个完美的低级 API。然而，当编写一个游戏时，有一个像 [Xbox One 控制器](http://www.xbox.com/en-us/xbox-one/accessories/controllers/wireless-controller?WT.mc_id=13409-DEV-sitepoint-article33)这样的标准游戏手柄的更高层次的表示是很好的。我们创建了一个名为 PxGamepad 的助手类，它将按钮和轴索引映射到 Xbox 控制器上更熟悉的名称。

我们将浏览这个库中一些有趣的部分，但是完整的源代码(麻省理工学院许可证)可以在这里找到:[https://github.com/thinkpixellab/PxGamepad](https://github.com/thinkpixellab/PxGamepad?WT.mc_id=13409-DEV-sitepoint-article33)

标准的游戏手柄 API 以按钮数组的形式提供按钮状态。同样，这个 API 是为灵活性而设计的，允许控制器具有不同的按钮数。然而，在编写游戏时，编写和阅读使用标准映射按钮名称的代码要容易得多。

例如，使用 HTML5 gamepad api，下面是检查左触发器当前是否被按下的代码:

![Code for Left Trigger](img/929b08ebe16917e72e6884f620a85cf6.png)

PxGamepad 类包含一个更新方法，该方法将收集所有标准映射按钮和轴的状态。因此确定`leftTrigger`是否被按下就像访问一个布尔属性一样简单:

![The Left Trigger is Pressed](img/8b7ecaff78390aca4f7a7471aba810bd.png)

标准游戏手柄 API 中的轴也以数值数组的形式提供。例如，下面是获取左摇杆的标准化 x 和 y 值的代码:

![Left Stick Function](img/6ba1b6d328560ada7d286a4081eebaf7.png)

D-pad 是一个特例，因为它考虑了 HTML5 Gamepad API 的一组四个按钮(索引 12、13、14 和 15)。然而，对于开发者来说，允许 dpad 以和一根棍子一样的方式使用是很常见的。PxGamepad 为 D-pad 提供按钮信息，但也合成轴信息，就像 D-pad 是一根棍子一样:

![D Pad Function](img/c637ba698375d7ca76e67c3e5dec8d69.png)

HTML5 游戏手柄 API 的另一个限制是它不提供按钮级别的事件。对于游戏开发者来说，想要通过一次按键激活一个事件是很常见的。在 flight arcade 中，点火和刹车按钮就是很好的例子。PxGamepad 监视按钮状态，并允许呼叫者注册按钮释放通知。

![Right Trigger Function](img/bf463363009231ad6c2ea9e97c05152b.png)

以下是 PxGamepad 支持的命名按钮的完整列表:

*   a
*   b
*   x
*   y
*   左上
*   右上方
*   左扳机
*   右触发器
*   挑选
*   开始
*   左钩拳
*   右棍
*   dpad 上升
*   dpadDown
*   dpad 左侧
*   padright

## 获取当前游戏手柄

有两种方法可以获取游戏手柄对象。Gamepad API 向名为`getGamepads()`的 navigator 对象添加了一个方法，该方法返回所有已连接游戏手柄的数组。还有新的`gamepadconnected`和`gamepaddisconnected`事件，每当一个新的游戏手柄被连接或断开时都会被触发。例如，下面是 PxGamepad 助手如何存储最后连接的游戏手柄:

![Listening for Gamepad Connection Events](img/7adb59c2083911864aa2c233c4f5f719.png)

这里是使用`navigator.getGamepads()` API 检索第一个标准游戏手柄的助手:

![Helper to Retrieve the Currently Connected Device](img/c3f1817ed401d60498be2631f5c5961b.png)

PxGamepad helper 类是为一个简单的场景设计的，在这个场景中，一个用户用一个标准的映射游戏手柄玩游戏。最新的浏览器，像[微软 Edge](http://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article33&utm_campaign=SitePoint) ，完全支持 W3C Gampepad API。然而，其他一些浏览器的旧版本只支持部分新兴规范。PxGamepad 监听 Gamepad 连接的事件，并在需要时返回查询所有游戏手柄的列表。

## 游戏手柄的未来

虽然 PxGamepad 专注于简单、最常见的场景，但 Gamepad API 完全能够支持多个玩家，每个人都有自己的游戏手柄。PxGamepad 的一个可能的改进是提供一个管理器风格的类，它跟踪多个游戏手柄的连接，并将它们映射到一个游戏中的多个玩家。另一个可能是允许用户重新映射或定制游戏手柄上的按钮功能。

我们也对游戏手柄在非游戏场景中的潜力感到兴奋。随着 WebGL 的兴起，我们看到了 3D 在网络上的各种创新用途。这可能意味着用 GlacierWorks 三维探索珠穆朗玛峰地区。或者参观大英博物馆的亚述人收藏，这要感谢 CyArk 对重要的世界遗址和文物进行数字化保护的努力。

在《飞行街机》的开发过程中，我们经常使用 [Blender](http://www.blender.org/?WT.mc_id=13409-DEV-sitepoint-article33) 和其他 3D 工具为 [Babylon 处理模型。JS](http://www.babylonjs.com/?WT.mc_id=13409-DEV-sitepoint-article33) 。一些开发人员和艺术家使用一种称为 3D 鼠标的设备来帮助操纵和导航 3D 模型。这些设备通过六个轴跟踪单个旋钮的运动！它们使得操纵模型变得非常容易和快速。除了游戏，它们还用于从工程到医学成像的各种有趣的应用中。在为 Flight Arcade 添加游戏手柄支持时，我们惊讶地得知游戏手柄 API 检测到了我们的 3D SpaceMouse，并提供了所有六个轴的运动数据！

想象一下新游戏手柄 API 提供的所有可能性是令人兴奋的。现在是试验新游戏手柄 API 的好时机，为你的下一个游戏或应用程序添加精确控制和许多乐趣！

## JavaScript 的更多实践

微软有许多关于开源 JavaScript 主题的免费学习，我们的任务是用 [Microsoft Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13409-DEV-sitepoint-article33) 创造更多。以下是一些要检查的:

*   【2015 年微软 Edge 网络峰会(关于新浏览器、新网络平台功能和社区演讲嘉宾的完整系列)
*   //BUILD/和 Windows 10 的构建(包括网站和应用的新 JavaScript 引擎)
*   [在不破坏网络的情况下推进 JavaScript](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web?WT.mc_id=13409-DEV-sitepoint-article33)(Christian heil Mann 最近的主题演讲)
*   [托管网络应用和网络平台创新](http://channel9.msdn.com/Events/Build/2015/2-665?WT.mc_id=13409-DEV-sitepoint-article33)(深入探讨流形等主题。JS)
*   [让你的 HTML/JavaScript 更快的实用性能技巧](http://channel9.msdn.com/Series/Practical-Performance-Tips-to-Make-Your-HTMLJavaScript-Faster/06?WT.mc_id=13409-DEV-sitepoint-article33)(从响应式设计到休闲游戏再到性能优化的 7 部分系列)
*   现代网络平台快速启动(HTML、CSS 和 JS 的基础)

以及一些免费的入门工具: [Visual Studio Code](https://code.visualstudio.com/?WT.mc_id=13409-DEV-sitepoint-article33) 、 [Azure 试用版](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13409-DEV-sitepoint-article33)和[跨浏览器测试工具](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article33&utm_campaign=SitePoint)——都适用于 Mac、Linux 或 Windows。

本文是微软网站开发技术系列的一部分。我们很高兴与您分享[微软 Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13409-DEV-sitepoint-article33) 和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13409-DEV-sitepoint-article33)。获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试@ [modern。即](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article33&utm_campaign=SitePoint)。

## 分享这篇文章