# 如何使用 PhoneGap 在 Windows Phone 上创建 HTML5 应用程序

> 原文：<https://www.sitepoint.com/how-to-create-html5-apps-on-windows-phone-with-phonegap/>

我们将在本文中首先了解 PhoneGap 为 HTML5 应用程序带来的附加值。然后，我们将了解如何创建我们的第一个项目，从 JavaScript 代码中检索加速度计的值。最后，我们将回顾一个完整的 HTML5 游戏示例，它几乎原样移植到 PhoneGap，以使用 Windows 手机上可用的加速度计。

1.  [简介](#introduction)
2.  PhoneGap:一个填补空白的框架
3.  [让我们创建我们的第一个 PhoneGap 项目](#firstproject)
4.  [从 JavaScript 获取加速度计的值](#accelero)
5.  [回顾 HTML5 平台游戏的完整示例](#html5platformer)

*   强制横向定位
*   处理各种决议
*   通过调用文件系统而不是使用 XHR 来加载级别
*   修改游戏来使用加速度计
*   一些手机上的结果和 FPS 的截图
*   下载完整的 Visual Studio 解决方案

*   [结论](#conclusion)

### 介绍

由于嵌入式 IE9 浏览器，Windows Phone 的芒果更新带有对 HTML5 的**支持。作为桌面版本，移动版本的 IE9 通过 Windows Phone 的 GPU 提供硬件加速。因此，结合 JavaScript，IE9 现在可以作为通常保留给“本地代码”的有趣用户体验的基础。**

使用 HTML5 作为开发平台的好处是可以在 Android 或 iOS 等其他兼容平台上轻松重用部分代码。在过去的几个月里，HTML5 引起了移动开发者生态系统的极大兴趣。

然而，即使 HTML5/CSS3/SVG & JavaScript 规范在过去几个月中有了很大的发展，它们仍然缺乏一些构建移动应用的主要特性。事实上，手机或平板电脑暴露了特定的功能，如:GPS、加速度计、摄像头、发送短信、访问联系人等。

为了从 JavaScript 代码中获得这些功能，W3C 已经研究了一段时间，我们称之为“[设备 API](https://www.w3.org/2009/dap/)或 **DAP** 。不幸的是，我们可以认为这些规范目前还没有实现，正如本文所证实的:[移动网络应用标准:2011 年 11 月当前状态和路线图](https://www.w3.org/2011/11/mobile-web-app-state.html "https://www.w3.org/2011/11/mobile-web-app-state.html")。Mozilla 已经开始了一项有趣的工作，通过他们所谓的[Web API](https://hacks.mozilla.org/2011/08/introducing-webapi/)或多或少地分叉那些规范，以支持他们的 [Boot To Gecko](https://hacks.mozilla.org/2011/07/announcing-boot-to-gecko-b2g-booting-to-the-web/) 项目。这是一个好消息，因为一种实现形式似乎是从与 W3C 的持续讨论开始的。然而，即使事情开始进展缓慢，我们也可能要等几年才能有一个稳定的官方 W3C 规范在所有平台上广泛实现。

所以问题是:在此期间我们应该做什么？HTML5 真的能解决这些问题吗？

### PhoneGap:填补空白的框架

在等待真正的标准化规范时，我们别无选择:我们需要在 JavaScript 和目标平台的本机代码之间建立一些**桥梁，以访问其功能。想法如下:采用每个平台的本地语言(C#、Objective-C 和 Java ),用这些语言创建一个框架，向 JavaScript 开发人员公开接口。**

这正是 [PhoneGap](http://phonegap.com/) 正在做的事情。让我们以 Windows Phone 为例，这是本文的主题。Windows Phone 的 PhoneGap 项目只是一个 Silverlight 应用程序，它托管 WebBrowser 控件(因此是 IE9)以及一个用 C#编写的 Silverlight 程序集，后者负责访问加速度计、GPS、联系人、摄像头等等。这样，作为一名 JavaScript 开发者，你将通过使用嵌入在 **phonegap-1.3.0.js** 文件中的代码，在不知道的情况下使用一个名为【WP7GapClassLib.dll】T2(PhoneGap 核心运行时)的 DLL。这个 DLL 包含一些 C#代码，这些代码调用电话上可用的 Silverlight 运行时。由于运行时可以访问手机的所有功能，JavaScript 也可以。JavaScript 库将充当两个世界之间的网关。此外，使用这个库的好处是你的代码大部分时间都可以在 Android 或 iOS 的 PhoneGap 版本上运行。PhoneGap 提供了一种有趣的便携性。

请顺便注意，自最近的 1.3.0 版本以来，PhoneGap 对 Windows Phone 的支持现已完全完成:

![Windows Phone support for PhoneGap](img/86d308b191f06c03546c02100c49cfbd.png "figure3")

最后，PhoneGap 还提供了另一项有趣的服务。它嵌入了你的。js，。css，。html，。png 资源打包成一个经典的应用程序。总之，您可以使用 PhoneGap 为各种应用程序商店打包您的 HTML5 应用程序。例如，使用这种方法构建的 SujiQ Windows Phone 应用程序就是这种情况。

### 让我们创建我们的第一个 PhoneGap 项目

#### 先决条件

以下是您需要遵循的最初步骤:

1.  下载 Windows Phone SDK:[Windows Phone SDK](http://aka.ms/hp-windows-phone)
2.  从他们的网站下载最新版本的手机(今天是 1.3.0 版):[http://phonegap.com/](http://phonegap.com/ "http://phonegap.com/")
3.  解压缩下载的文件
4.  将 **PhoneGapStarter.zip** 和 **PhoneGapCustom.zip** 文件复制到**documents visual Studio 2010 templates 项目模板**

#### 文件->新项目

一旦完成了前面的步骤，您就可以创建您的第一个 PhoneGap 项目了。启动 Visual Studio 2010，选择“Visual C#”模板，并通过“Gap”关键字过滤它们。然后，您应该会看到一个名为 **PhoneGapStarter** 的新项目类型:

![PhoneGap Starter](img/87caa24e2bfa857026444325f6c07d1b.png "figure4")

将您的项目命名为“ *MyFirstPhoneGapProject* ”。完成后，您将在解决方案资源管理器中找到我之前提到的文件:

![solution explorer](img/d134d84bb0910fdb35001d278ec7044f.png "figure5")

您现在只需将您的 HTML5 应用程序插入到“www”目录中。

关于这个默认的项目模板，我想与您分享几个技巧:

–**永远不要碰 *phonegap-1.3.0.js* 文件**如果您想在 PhoneGap
的其他版本上保留可移植代码——您将在“www”目录中添加的所有文件都必须在属性窗口
中设置为**内容**——而不是*WP7GapClassLib.dll*二进制文件，您可以添加对在如果需要，它将帮助您调试或发现 PhoneGap 库的本机代码。

好了，现在让我们开始做一些在 IE9 Mango 中默认情况下不可能完成的事情:从 JavaScript 访问加速度计的值。

### 从 JavaScript 获取加速度计的值

我们将在这里看到如何以一种非常简单的方式获得加速度计(仿真器或真实设备的)发回的值。

打开`index.html`页面，将其默认主体更改为:

```
<body>

    <h1>Accelerometer sample</h1>

    <div id="valueX"></div>

   <div id="valueY"></div>

    <div id="valueZ"></div>

</body>
```

我们将简单地使用 3 个`<div>`标签来显示加速度计的当前 X，Y & Z 值。

下一步是将最后一个默认`<script>`块改为这个:

```
<script type="text/javascript">

    document.addEventListener("deviceready", onDeviceReady, false);

    // variable to output the current x, y & z values of the accelerometer

    var valueX;

    var valueY;

    var valueZ;

    // when PhoneGap tells us everything is ready, start watching the accelerometer

    function onDeviceReady() {

        valueX = document.getElementById("valueX");

        valueY = document.getElementById("valueY");

        valueZ = document.getElementById("valueZ");

        startWatch();

}

        // start monitoring the state of the accelerometer

    function startWatch() {

        var options = { frequency: 500 };

        navigator.accelerometer.watchAcceleration(onSuccess, onError, options);

    }

    // if the z-axis has moved outside of our sensitivity threshold, move the aardvark's head in the appropriate direction

    function onSuccess(acceleration) {

        valueX.innerHTML = "X: " + acceleration.x;

        valueY.innerHTML = "Y: " + acceleration.y;

        valueZ.innerHTML = "Z: " + acceleration.z;

    }

    function onError() {

        alert('onError!');

    }

</script>
```

我认为代码是相对不言自明的。首先要注意的是，您需要等待 PhoneGap 引发的`deviceready`事件，以确保它处于稳定状态。然后，您需要订阅此活动。在我们的例子中，我们将被回调到`OnDeviceReady()`函数中。该函数获取对 3 个`<div>`标签的引用，然后每隔 500 毫秒通过`startWatch()`函数请求得到加速度计内部任何变化的通知。通知将被发送到`onSuccess()`函数，该函数将访问包含 x，y & z 值的`acceleration`对象。您可以在 PhoneGap 网站上找到完整的文档: [PhoneGap 文档–API 参考–加速度计](http://docs.phonegap.com/en/1.2.0/phonegap_accelerometer_accelerometer.md.html#Accelerometer "http://docs.phonegap.com/en/1.2.0/phonegap_accelerometer_accelerometer.md.html#Accelerometer")。

这就是你在 JavaScript 端需要做的一切。然而，要使它工作，你需要在项目的属性中指定**你想要请求访问设备的传感器**。正确执行我们的应用程序所需的功能在*属性*目录中的`WMAppManifest.xml`文件中列出。默认情况下，从 1.3.0 开始，PhoneGap 列出了严格的最低功能:

```
<Capabilities>

    <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    <Capability Name="ID_CAP_IDENTITY_USER" />

    <Capability Name="ID_CAP_LOCATION" />

    <Capability Name="ID_CAP_NETWORKING" />

    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

</Capabilities>
```

然后由您来添加 PhoneGap 应用程序所需的功能。在我们的例子中，我们需要添加这一行:

```
<Capability Name="ID_CAP_SENSORS" />
```

要访问加速度计，您可以在这里找到所有可用功能的完整列表:【Windows Phone 应用程序清单文件

好了，从逻辑上来说，我们已经准备好在模拟器中进行测试，作为第一阶段。按下神奇的“F5”键，让我们看看结果:

![emulator](img/dafe2484e98554df8120def8d8fb3262.png "figure6")

通过移动右边模拟器中的虚拟电话，您应该看到加速度计的值更新了。恭喜你！

你可以**在这里下载第一个解决方案的完整源代码**:[https://David . blob . core . windows . net/html 5/myfirstphonegapproject . zip](https://david.blob.core.windows.net/html5/MyFirstPhoneGapProject.zip "https://david.blob.core.windows.net/html5/MyFirstPhoneGapProject.zip")

#### 使用法语语言环境的手机的问题

如果您在配置为使用法语地区的手机上测试相同的代码(比如我的地区！)，你会有应用根本不行的感觉……确实是这样。我花了一些时间调试代码，发现在`phonegap-1.3.0.js`内部出现了以下异常:

“成功回调出错:加速度计=语法错误”

在转储值之后，我发现错误是由于以下错误格式的 JSON 字符串的尝试性反序列化造成的:

**" { " x ":0.00472，" y":-0.19879，" z ":-0.98115 } "**而合适的 EN-US 是下面这个: **"{"x":0.00472，" y ":-0.19879，" z":-0.98115}"**

是的，我们在法国使用逗号作为数字分隔符。

解决这个问题的 2 个方案:

1–懒的那个:把手机换成 EN-US(我知道，这不是办法！)
2–修复来自 C#代码的问题。为此，替换`WP7GalClassLib`库的 Accelometer.cs 文件中的以下代码:

```
/// <summary>

/// Formats current coordinates into JSON format

/// </summary>

/// <returns>Coordinates in JSON format</returns>

private string GetCurrentAccelerationFormatted()

{

    string resultCoordinates = String.Format(""x":{0},"y":{1},"z":{2}",

accelerometer.CurrentValue.Acceleration.X.ToString("0.00000"),

accelerometer.CurrentValue.Acceleration.Y.ToString("0.00000"),

accelerometer.CurrentValue.Acceleration.Z.ToString("0.00000"));

    resultCoordinates = "{" + resultCoordinates + "}";

    return resultCoordinates;

}
```

由这一个:

```
private string GetCurrentAccelerationFormatted()

{

    string resultCoordinates = String.Format(""x":{0},"y":{1},"z":{2}",

        accelerometer.CurrentValue.Acceleration.X.ToString("0.00000", CultureInfo.InvariantCulture),

        accelerometer.CurrentValue.Acceleration.Y.ToString("0.00000", CultureInfo.InvariantCulture),

        accelerometer.CurrentValue.Acceleration.Z.ToString("0.00000", CultureInfo.InvariantCulture));

    resultCoordinates = "{" + resultCoordinates + "}";

    return resultCoordinates;

}
```

该示例现在可以在所有语言环境下工作。我在这里提交了一个关于这个主题的 bug:[https://issues.apache.org/jira/browse/CB-141](https://issues.apache.org/jira/browse/CB-141 "https://issues.apache.org/jira/browse/CB-141")提出了下一版本(2.0.0)通常会提供的相同解决方案。

顺便说一下，你可能想知道我是如何调试 PhoneGap 项目的 JavaScript 部分的？嗯，你可以简单地使用美妙的 **jsConsole** 项目。如果你想了解更多，请阅读我同事的一篇文章: [jsConsole 远程调试在 IE9 上的应用](http://bristowe.com/blog/2011/6/8/jsconsole-remote-debugging-in-ie9-on-windows-phone-mango.html "http://bristowe.com/blog/2011/6/8/jsconsole-remote-debugging-in-ie9-on-windows-phone-mango.html")

### 回顾 HTML5 平台游戏的完整示例

现在让我们来看一个更复杂的例子。我的想法是从我以前写的游戏开始。本文曝光: [HTML5 Platformer:用 EaselJS](http://blogs.msdn.com/b/davrous/archive/2011/09/09/html5-platformer-the-complete-port-of-the-xna-game-to-lt-canvas-gt-with-easeljs.aspx "http://blogs.msdn.com/b/davrous/archive/2011/09/09/html5-platformer-the-complete-port-of-the-xna-game-to-lt-canvas-gt-with-easeljs.aspx") 将 XNA 游戏完整移植到<画布>。我想看看我该怎么做才能让它在电话上工作。

第一步是简单地复制/粘贴不同的。js，。png，。css 文件放入" www "目录并把它们标记为"**内容**"。下面是要遵循的其他步骤。

#### 强制横向定位

游戏必须在风景模式下进行。然后我强迫这个方向。我还删除了侧面的信息栏(系统托盘)。为此，您需要打开`MainPage.xaml`文件并更改这些属性:

```
SupportedOrientations="Landscape" Orientation="Landscape" shell:SystemTray.IsVisible="False"
```

#### 处理各种决议

因为我的主要目标是构建一个可以在最多数量的设备上运行的游戏，所以我需要处理很多不同的分辨率。

为此，我稍微修改了平台的初始代码，你可以在[其他文章](http://blogs.msdn.com/b/davrous/archive/2011/09/09/html5-platformer-the-complete-port-of-the-xna-game-to-lt-canvas-gt-with-easeljs.aspx)中下载。游戏现在可以在任何分辨率下运行，方法是对要绘制的图像和精灵应用缩放比例。一切都是基于 Windows Phone (800×480)的特定比例(100%)重新绘制的。你可以在你的桌面浏览器中测试这个版本: [HTML5 Platformer ReScale](https://david.blob.core.windows.net/html5platformerscale/index.html "https://david.blob.core.windows.net/html5platformerscale/index.html") 并尝试动态调整浏览器窗口的大小。此外，如果您的屏幕分辨率为 16/9 纵横比，请按 F11 键全屏播放！这种模式下的体验真的很酷，正如你在这张截图中看到的:

![fullscreen screenshot](img/524eda84d6fc55cbde119c67f1bde8bb.png "figure7")

我们让浏览器在缩放操作中负责抗锯齿。基于您将使用的浏览器，您还会注意到，性能可能会因窗口大小的不同而有很大差异。在我的机器上，IE9/IE10 通过保持稳定的 60 fps 帧率，似乎对全屏模式或小分辨率相对无所谓。

#### 通过调用文件系统而不是使用 XHR 来加载级别

在初始代码中。链接到每个级别的 TXT 文件存储在 web 服务器上，并通过 XmlHttpRequest 调用下载。由于我们现在在离线模式下运行客户端的一切，XHR 本地电话不是很合适。然后我用下面这段代码替换了`PlatformerGame.prototype.LoadNextLevel`函数的初始代码:

```
PlatformerGame.prototype.LoadNextLevel = function () {

    this.levelIndex = (this.levelIndex + 1) % numberOfLevels;

    // Searching where we are currently hosted

    var nextFileName = "app/www/assets/levels/" + this.levelIndex + ".txt";

    try {

        var instance = this;

        window.requestFileSystem(LocalFileSystem.PERSISTENT, 0, gotFS, fail);

        function gotFS(fileSystem) {

            fileSystem.root.getFile(nextFileName, null, gotFileEntry, fail);

        }

        function gotFileEntry(fileEntry) {

            fileEntry.file(gotFile, fail);

        }

        function gotFile(file) {

            readAsText(file);

        }

        function readAsText(file) {

            var reader = new FileReader();

            reader.onloadend = function (evt) {

instance.LoadThisTextLevel(evt.target.result.replace(/[nrt]/g, ''));

            };

            reader.readAsText(file);

        }

        function fail(evt) {

            console.log(evt.target.error.code);

        }

    }

    catch (e) {

        console.log("Error loading level: " + e.message);

        // Probably an access denied if you try to run from the file:// context

        // Loading the hard coded error level to have at least something to play with

        this.LoadThisTextLevel(hardcodedErrorTextLevel);

    }

};
```

我只是重用了 PhoneGap 文档中的代码: [FileReader](http://docs.phonegap.com/en/1.3.0/phonegap_file_file.md.html#FileReader "http://docs.phonegap.com/en/1.3.0/phonegap_file_file.md.html#FileReader") 。如您所见，您可以从 JavaScript with PhoneGap 完全访问 Windows Phone 文件系统。

**酷提示:** 为了帮你调试手机的隔离存储中到底存了什么，你应该看看这个牛逼的工具:[Samuel Blanchard 写的 IsoStoreSpy](http://isostorespy.codeplex.com/ "http://isostorespy.codeplex.com/") 。

#### 修改游戏来使用加速度计

好了，最后一部分只是混合本文的所有部分，以获得最终结果。为此，我在`Player.js`文件中的`Player`对象的构造函数中添加了以下代码:

```
var options = { frequency: 500 };

var that = this;

navigator.accelerometer.watchAcceleration(

      function (accelerometer) { that.moveDirectionAccel(accelerometer); },

      function () { console.log("Error with accelerometer"); },

      options);
```

以下是加速度计变化期间将被回调的函数:

```
Player.prototype.moveDirectionAccel = function(acceleration) {

    var accelValue = -acceleration.y;

    // Move the player with accelerometer

    if (Math.abs(accelValue) > 0.15) {

         // set our movement speed

         this.direction = Math.clamp(accelValue * this.AccelerometerScale, -1, 1);

    }

    else {

        this.direction = 0;

    }

};
```

我还在画布上的`onmousedown`事件上添加了一个处理程序，以便在用户点击屏幕时跳转。

#### 一些手机上的结果和 FPS 的截图

首先，让我们在 Windows Phone 模拟器中查看结果:

![review the emulator](img/70122349dd77af6e8ce0e7dbb8b5bb2a.png "figure8")

在我的机器上，我们的帧率从 54 到 60 fps 不等。在真实设备上，不同型号的帧速率在逻辑上是不同的。先说第一个游戏关卡。在 **LG E900** 上，帧率在 **22 fps** 左右。在 **HTC 雷达**上，在 **31 fps** 左右。在**诺基亚 Lumia 800** 上，在 **42 fps** 左右。

![framerates](img/f2bd83fcdc9413f9b9829b6ee1e8a8bd.png "figure9")

在大多数情况下，游戏可能不太令人信服。事实上，我正在使用全屏画布来绘制整个游戏。这对于移动有限功率 CPU 来说不是一个很好的主意，即使诺基亚看起来足够强大来处理这种方法。更好的方法是将屏幕舞台切割成各种小画布，然后通过修改它们的 CSS 属性来移动它们。例如，HTML5 版的《愤怒的小鸟》就是这么做的。一些 JS 游戏框架开始考虑为你处理这种复杂性。这个想法是用高级 API 对游戏进行一次编码，框架可以根据性能在全帧画布或通过 CSS 移动的各种小画布之间切换。

嗯，你会明白的:HTML5 手机游戏体验目前才刚刚开始。但是它的未来看起来很有希望！

#### 下载完整的 Visual Studio 解决方案

你可以在这里找到 PhoneGap 的 HTML5 平台的完整源代码:[html 5 gap Platformer . zip](https://david.blob.core.windows.net/html5/HTML5GapPlatformer.zip "https://david.blob.core.windows.net/html5/HTML5GapPlatformer.zip")

### 其他有用的资源

*   Windows Phone 上对 PhoneGap 的全面支持现已完成！
*   [PhoneGap on Windows Phone 提示](http://blogs.msdn.com/b/glengordon/archive/2011/11/15/phonegap-on-windows-phone-tips.aspx "http://blogs.msdn.com/b/glengordon/archive/2011/11/15/phonegap-on-windows-phone-tips.aspx")来自 Glen Gardon，他写了几个非常有趣的教程
*   PhoneGap 的维基部分有很多好的教程:[http://wiki.phonegap.com/w/page/35501397/Tutorials](http://wiki.phonegap.com/w/page/35501397/Tutorials "http://wiki.phonegap.com/w/page/35501397/Tutorials")

### 结论

PhoneGap 提供了有趣的视角来帮助你在手机上编写应用程序。它让你可以使用你现有的 JavaScript、HTML 和 CSS 技能。PhoneGap 没有必要覆盖所有当前本地开发栈(Silverlight 或 XNA)可用的场景。但是如果你想利用你的团队中的一员的 HTML 技能，这可能是值得一看的。你将不得不注意正确识别工作项目的类型。

您还可以考虑混合两种环境来创建混合体验:主分支将使用“HTML5”和一些特定部分编写成本地代码。利用这个想法，一些插件被创建出来，以进一步推动与 Metro UI 的集成:[PhoneGap Windows Phone 插件](https://github.com/purplecabbage/phonegap-plugins/tree/master/WindowsPhone "https://github.com/purplecabbage/phonegap-plugins/tree/master/WindowsPhone")。例如，你会发现其中一个允许 JavaScript 代码更新瓷砖。

最后，PhoneGap 和 HTML5 可以允许相对移植到其他平台…但有一些限制。但这是一个有着激烈辩论的庞大主题，需要一篇完整的专门文章。

*本文最初发表在作者的 [MSDN 博客](http://blogs.msdn.com/b/davrous/archive/2011/12/23/tutorial-how-to-create-html5-applications-on-windows-phone-thanks-to-phonegap.aspx)上。它被允许重新发布。*

## 分享这篇文章