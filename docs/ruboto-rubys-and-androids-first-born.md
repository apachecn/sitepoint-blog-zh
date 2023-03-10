# Ruboto: Ruby 和 Android 的第一次诞生

> 原文：<https://www.sitepoint.com/ruboto-rubys-and-androids-first-born/>

谷歌试图用 Android 平台与 iPhone 竞争。在过去的一两年里，它成长为一个值得尊敬的对手。其中一个原因是它提供了许多有趣的优点:开发人员享受各种可能性，当然还有可移植性，这让他们可以轻松地使用他们心爱的 Java 库，因为非本机 Android 应用程序运行在特殊的 Java 虚拟机或 JVM 上。

JRuby 是一款屡获殊荣的开源软件，它将我们的明珠 Ruby 带到了 JVM 上，就像我说过的，JVM 也支持 Android 应用程序。简单地说，最自然的事情发生了，[鲁博托](http://ruboto.org/)诞生了！确实是 Domo arigato。

因为我既喜欢 Ruby 又喜欢 Android，所以我不得不试驾了 Ruboto，上周我就这样做了。我想，为什么不把我的小冒险写成一篇短文呢？(如果你想自己写一篇关于一些很酷的 Ruby 东西的文章，请随时联系我们！)

让我先给你一个简单的概述，然后我会告诉你如何自己尝试。

![](img/8e79343eda544aa45013fe05cb12bd21.png)

正如你所看到的，Java 源文件和 JRuby JARs 一起被打包到一个 Android 应用程序中，这个应用程序能够运行你的定制 Ruby 脚本，同时让你能够访问 Android APIs。这是一个强大的方法，因为它使你能够在不需要重新编译整个应用程序的情况下修改 Ruby 脚本。这里的含义是你可以直接在你的设备上写 Ruby 代码并运行它。不可否认，这不是最友好的编码方式，但是在你的设备上有一个工作的 IRB 真的很好。

### 是怎么做到的？

为了简单起见，也为了我的精神健康，我将只向你展示我是如何在我的 Linux 机器上完成的。如果你使用的是 Mac 或 Windows，也有适合你的教程。

### 系统设置

首先你需要有一个 JDK (Java 开发工具包)和 Ant(想想 Java 的 Bundler)。
`sudo apt-get install default-jdk ant`

请验证您至少拥有 Ant 版本 1.8.1 或更高版本。如果没有，可以从[这里](http://ant.apache.org/bindownload.cgi)获取。

现在 Ruboto 教程通过 Aptitude 安装了所有与 Ruby 相关的东西，但是我倾向于支持 RVM T2(Ruby 版本管理器)。你可以简单地用`bash -s stable < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)`安装它，但是你应该先阅读他们的[安装指南](http://beginrescueend.com/rvm/install/)。

成功安装 RVM 后，您可以使用它自动为您安装一个 Ruby 环境。我通过运行`rvm install jruby`决定使用最新的 JRuby 版本。

现在，你可能知道也可能不知道，RVM 为你的 Ruby 版本透明地处理你的宝石，允许我们用简单的`gem install rake bundler ruboto jruby-jars`安装必要的宝石。

最后一步是安装 Android SDK。你所要做的就是从[这里](http://developer.android.com/sdk/index.html)下载 SDK，解压到你选择的位置(psst，`/opt/android-sdk/`很好！)并将这个位置添加到您的路径变量中(例如，通过将类似于
`export PATH=${PATH}:/opt/android-sdk/tools`的东西添加到您的`~/.bashrc`文件中)

要生成你的第一个 Ruboto 应用，只需在新创建的目录`quick_start`中键入`ruboto gen app --package org.ruboto.example.quick_start --target android-7`和
cd。

### 设备设置

我只是用了我实际的 Android 手机而不是模拟器，但是如果你更喜欢使用模拟器或者如果有任何问题，你需要知道安装过程的更多细节等等，只需访问官方 Ruboto 入门教程[T1。](https://github.com/ruboto/ruboto/wiki/Tutorial%3A-Getting-started-with-Ruboto)

为了使用您的 Android 设备试用您的应用程序，您需要在设置/应用程序中允许未知来源，并在设置/应用程序/开发中启用 USB 调试(从 Android 4.0 开始为设置/安全和设置/开发者 _ 选项)。

在我们可以将示例应用程序安装到设备之前，还需要做最后一件事。您需要通过添加行`SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", MODE="0666", GROUP="plugdev"`来创建文件`/etc/udev/rules.d/51 android.rules`,并在其上启动一个快速的`sudo chmod a+r /etc/udev/rules.d/51-android.rules`。

**请注意**您需要将“0bb4”与您的实际供应商 ID 进行交换。最常见的应该是

*   **HTC:** 0BB4
*   **三星:** 04E8
*   谷歌: 18D1
*   LG: 1004
*   摩托罗拉: 22B8
*   **华为:** 12D1

其余的可以在这里找到[。同样，如果你使用的是 Windows 或 Mac，你必须做不同的事情。请去阅读](http://developer.android.com/guide/developing/device.html#VendorIds)[这些](http://developer.android.com/guide/developing/device.html)说明。

### 有趣的部分

那很简单，不是吗？说真的，如果你还没有啤酒或类似的东西，那就来一杯吧，因为有趣的部分终于开始了:让我们写一些 Ruby 代码吧！假设您的设备通过 USB 连接，现在您应该能够构建并安装示例应用程序到您的设备上，只需简单的`rake install`。

既然示例应用程序真的很无聊，我们应该让它做一些更有趣的事情。当我开始玩的时候，我想先写一个小游戏，或者用设备摄像头写点东西，但事实是，这种项目要么是完全愚蠢的，要么是太复杂了，无法在像这篇文章这样的小文章中完成。然后我的一个朋友告诉我他正在做的一个项目，他想通过 Ruby 远程控制他的漂亮电视，这让我思考。为什么不构建一个连接到 TCP 服务器并发送消息的小脚本呢？我喜欢这个想法，因为它没有太多代码，完成很快，可以很容易地扩展到一些潜在的有用的东西，我只是喜欢网络的东西。

我们首先改变的是`res/values/strings.xml`中 app 的名称。紧接着，我们打开`AndroidManifest.xml`，在最后的`</manifest>`标签上方，我们添加`<uses-permission android:name="android.permission.INTERNET">`。显然，这给了我们的应用程序访问网络的权限。现在，我们终于可以在`src/quick_start_activity.rb`中添加 Ruby 代码了。用下面的代码替换这个文件中的所有内容