# 使用 Genymotion 改进了 Android 仿真

> 原文：<https://www.sitepoint.com/improved-android-emulation-genymotion/>

如果你开发 Android 应用已经有一段时间了，并且一直使用默认的模拟器，那么你就会知道它有多慢。即使在强大的机器上，它也是可管理的。模拟器不仅启动缓慢，而且在应用程序的所有后续运行中也是如此。一个解决方案是为 [VirtualBox](https://www.virtualbox.org/) 提供安卓虚拟机客户端的*安卓虚拟机*。这导致了一个明显比默认 Android 模拟器更快的模拟器。

Android 被 Genymobile 收购，他们发布了 Genymotion，这是一款改进 Android 的商业产品。

Genymotion 仿真器使用 x86 架构虚拟化和 OpenGL 硬件加速，使您的应用程序运行更加高效。它的工作原理是通过 VirtualBox 创建一个虚拟机，以提供一个支持 GPS、加速度计和电池等硬件传感器的 Android 仿真器。它适用于 Windows、Mac 和 Linux。你可以免费得到它，它有一个额外的许可证，为模拟器增加了更多的功能。关于这些的列表，[看看这个列表](https://shop.genymotion.com/index.php?controller=order-opc)。对于一般的测试，免费版就足够了。

要下载，请在 [Genymotion 网站](http://www.genymotion.com/)上创建一个帐户。当您登录时，您将被定向到一个下载页面，在那里您可以为您的机器下载一个安装程序。对于 Mac 和 Linux，您将需要首先安装 [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) 。Windows installer 包含 VirtualBox 依赖项，因此您无需手动安装。对于您的特定开发环境的安装说明，[查看此页面](https://cloud.genymotion.com/page/doc/#collapse4)。安装 VirtualBox 时，确保您已经启用了 [**VirtualBox 网络**](https://cloud.genymotion.com/page/faq/#collapse-nostart)。对于 Mac 安装，确保将 Genymotion 和 Genymotion Shell 都拖到 Applications 文件夹中。

![genymotion installer](img/ca79c23146ea127ba8fb676d94799258.png)

安装完成后，启动 Genymotion 应用程序以打开 Genymotion 主窗口，您可以在其中查看虚拟设备列表、添加新设备和配置设置。

![genymotion main screen](img/5a632e51ccc0ea9da42e3e61b4dec74e.png)

因为这是您的第一次安装，所以不会列出任何虚拟设备或登录，如上图所示。

要添加设备，请单击添加按钮。系统将提示您登录以发现可用的虚拟设备。登录后，您将看到一个可用设备列表，您可以根据 Android 版本或设备型号进行筛选。在撰写本文时，最高版本号是运行在 Nexus 5 上的 Android 5.0 预览版。

选择要添加的设备，然后单击下一步。我们将添加一款运行 Android 4.4.4 的三星 Galaxy S5。

![adding a device to genymotion](img/84a3e0ee84faf203649d7c71e9f5e7b1.png)

虚拟设备将被下载，下载完成后，该设备将在主 Genymotion 窗口中列出。下面是我下载的虚拟设备列表(我在列表中添加了一些)。

![starting a genymotion device](img/7e128da85f21c426e4d05c63383febd5.png)

要启动设备，请选择它并按开始。请注意启动所需的时间，并将其与默认仿真器进行比较。

除了右边的控件之外，虚拟设备看起来与默认模拟器相似。这些用于 Genymotion 提供的各种传感器。下图显示了设备免费版和付费版的区别。在免费版本中，一些传感器控件被禁用。

![comparing emulators](img/a33f59ad5faf4281f5938c547dceb6da.png)

从顶部开始，控件包括:

*   **电池**–能够在不同的电池条件下测试您的应用。
*   **GPS**–使用它为您的设备设置不同的位置，并测试您的应用程序的地理定位功能。
*   **摄像头**–设置虚拟设备的前后摄像头。如果您的电脑有网络摄像头，您可以使用它。您还可以设置一个虚拟网络摄像头，显示一个带有移动 Genymotion 图标的空白视图。
*   **捕获**–使您能够对虚拟设备进行截图或截屏。这对于记录正在使用的应用程序非常有用。
*   **远程控制**–使您能够从另一台物理设备(运行 Android 2.2 或更高版本)控制虚拟设备。连接的物理设备会将触摸输入和加速度计事件转发给 Genymotion。因此，您可以测试您的应用程序，就像您手中拿着一个设备一样。
*   **标识符**–显示设备 ID 和 Android ID 号。您可以随时查看和编辑这些值，而无需重新启动虚拟设备。
*   **网络**–让您能够测试应用如何应对不同类型的网络质量。
*   **增加音量**
*   **降低音量**
*   **旋转屏幕**
*   **完美像素**–实现用户界面的高精度，并确保应用程序的每个像素看起来都符合要求。虚拟设备的一个像素使用计算机屏幕的一个像素来显示。

## Eclipse 和 Android Studio 集成

要在开发过程中使用 Genymotion 虚拟设备，请启动该设备，当您在所选 IDE 中运行应用程序时，该虚拟设备将列在可用设备列表中。

如果你不想每次使用 Genymotion 的设备时都启动它，那么可以安装适用于 Eclipse 和 Android Studio 的 Genymotion 插件。

对于 Eclipse，打开*帮助- >安装新软件*，点击添加按钮添加新的更新站点。给它起个名字 Genymotion 和 http://plugins.genymotion.com/eclipse/的 url。单击确定，然后在可用软件窗口中，从列表中选择 Genymobile，并单击下一步。按照提示操作，直到安装完成。Eclipse 将需要重启。

在重新启动 Eclipse 时，您会注意到一个用于 Genymotion 虚拟设备管理器的图标。

![genymotion icon in IDE toolbar](img/26f4edf82d5ce825ee4b7ae3127c3bda.png)

启动 Genymotion 虚拟设备管理器。这类似于默认的设备管理器，您可以在其中查看设备列表并启动它们。如果您从管理器中选择“新建”,它将打开 Genymotion，因为您无法从这里创建设备。要运行设备，请在从列表中选择设备后单击“开始”,设备将启动，无需运行 Genymotion。

![genymotion devices](img/9071b4d9044a73da0e9da9cac9baf301.png)

当 Genymotion 虚拟设备正在运行时，它将出现在您运行应用程序时可以在其上运行应用程序的设备列表中。

![selecting emulator in IDE](img/4243e25386e5149d301e510b2d1193a5.png)

*注意*:这是一个开始出现在 Mac OSX 约塞米蒂上的 bug。当你选择在一个设备上运行你的应用，从而启动 Android 设备选择器，如上所示。您可能看不到正在运行的 Genymotion 设备列为可用。一个简单的修复方法是调整序列号选项卡的大小，您将看到正在运行的 genymotion 模拟器。

对于 Android Studio，打开设置。这对于不同的操作系统是不同的。对于 Mac 来说，是 *Android Studio - >偏好*。在出现的窗口中，在 IDE 设置下，选择插件。单击“浏览存储库”按钮并搜索 Genymotion。从搜索结果中选择 Genymotion，然后单击 Install Plugin 按钮。安装完成后，Android Studio 会重启。

重新启动时，Genymotion 虚拟设备管理器图标将被添加到工具栏中。

![genymotion icon in eclipse](img/0be0574f6bbec783f529b079ca444ba0.png)

单击图标时，系统会提示您设置 Genymotion 应用程序的路径。对于 Mac 来说，这个应该是`/Applications/Genymotion.app`。设置路径后，您现在可以启动设备管理器了。

![list of devices](img/4f37af93e7dc1e937750d2d2328b341e.png)

在运行您的应用程序时，正在运行的 Genymotion 虚拟设备将是您可以运行应用程序的设备选择之一。

![selecting device](img/6ee471900203c5ad3c139c19936d8fdf.png)

## 值得一提的额外功能

就个人而言，因为它的速度，我更喜欢使用 Genymotion 而不是默认的模拟器。但是它还提供了一些我非常喜欢的功能。首先是安装 apk 文件的便利性。使用默认仿真器时，要在设备上运行 apk 文件需要经过几个步骤，而使用 Genymotion 时，只需将文件拖放到虚拟设备上。

第二个特性实际上是 Genymotion 不再支持的[特性](https://plus.google.com/u/0/+GenymotionEmulator/posts/jNF8Kwu5p1c)。之前的 Genymotion 版本支持 Google apps，这意味着你可以在虚拟设备上安装 Google play 商店的应用程序，就像在真实设备上一样。通过查看各种应用程序是如何实现这些功能的，这在研究要包含在应用程序中的功能时会很方便。你可以在不同的虚拟设备上安装应用程序，从而在手机和平板电脑上测试应用程序，而无需购买各种真实的测试设备。

通过在虚拟设备上安装兼容的 [Google apps 包](http://wiki.rootzwiki.com/Google_Apps#Universal_Packages_2)，您仍然可以在 Genymotion 上启用 Google app 支持。但是正如[基因运动团队所说](https://plus.google.com/u/0/+GenymotionEmulator/posts/jNF8Kwu5p1c)。

> 如果您决定自行添加所需的包装，风险和责任由您自行承担，我们对此不承担任何责任。

如果你不想走这条路，但仍然想在你的电脑上运行谷歌 Play 商店的 Android 应用程序，那么两个仿真器:[安迪](http://www.andyroid.net/)和 [BlueStacks](http://www.bluestacks.com/) 可能是更好的选择。

## 分享这篇文章