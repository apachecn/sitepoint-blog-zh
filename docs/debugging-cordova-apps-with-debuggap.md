# 用 DebugGap 调试 Cordova 应用程序

> 原文：<https://www.sitepoint.com/debugging-cordova-apps-with-debuggap/>

要在桌面浏览器中调试 web 应用程序，有无数的工具可用，比如 Chrome 的开发工具或 Firefox firebug 插件。本地应用程序开发具有提供集成调试工具的 ide。但是使用 web 视图的混合应用程序总是更难调试。

[Debuggap](http://www.debuggap.com/) 是一款调试跨平台移动应用的工具。很容易使用和上手。你需要做的就是下载这个包，包含一个 JavaScript 文件，然后在一个设备或者模拟器上运行这个应用。

它可以在不同的移动平台上调试应用程序，并在 Windows，Mac 和 Linux 上得到支持。

## 入门指南

要在桌面上开始使用 DebugGap，[下载](http://www.debuggap.com/)相关的包并解压。

导航到解压缩的文件夹，并更改访问权限。

```
sudo chmod -Rf 777 ./DebugGap-x.x.x
```

运行调试可执行文件。

```
cd DebugGap-x.x.x
./DebugGap
```

*在 MacOS 和 Windows 上，文件夹里有一个应用文件，双击它启动调试可执行文件*。

您现在应该会看到远程调试屏幕。

![Remote Debug Screen](img/6b0e29afe7abeb37857bdc3323416acd.png)

点击*连接*监听客户端。

## 将 DebugGap 添加到应用程序

为了演示 DebugGap，我将使用来自教程[使用 Apache Cordova](https://www.sitepoint.com/creating-an-expense-manager-app-with-apache-cordova/) 创建费用管理器应用程序的费用管理器应用程序。

首先，安装 cordova。

```
sudo npm install -g cordova
```

从 [GitHub](https://github.com/sitepoint-editors/ExpenseManager) 克隆源代码。

```
git clone https://github.com/sitepoint-editors/ExpenseManager.git
```

导航到项目目录并添加相应的移动平台。我用的是安卓。

```
cd ExpenseManager
cordova platform add android
```

在 DebugGap 解压后的文件夹中，将*客户端*文件夹中的 *debuggap.js* 复制粘贴到 *ExpenseManager/www/js* 中。在*expense manager/www/index . html*中包含 *debuggap.js* 作为脚本引用。

```
<script src="js/debuggap.js" type="text/javascript"></script>
```

建立科尔多瓦项目。

```
cordova build
```

构建完成后，在 android 模拟器上运行应用程序。

```
cordova emulate android
```

您应该能够查看模拟器中运行的带有蓝点的应用程序。

![Corodva App With Debuggap](img/4c06dcdbc8029bf46be748d6b9aee146.png)

点击蓝色点，你应该可以看到 4 个选项。

![DebugGap Options](img/de3aa31501f591c82e36d230a8749c63.png)

选择 *config* 并输入运行 DebugGap 的 IP 地址。在这种情况下，因为我们正在仿真器上运行应用程序，所以您需要输入 IP 地址 10.0.2.2，端口 8085，然后单击*连接*。它将连接到本地主机服务器上的 DebugGap 侦听端口 8085。

![Connect Details](img/a6092f56e5fa9882d5f704dbcefe1961.png)

一旦连接，您应该能够在 DebugGap 监听器屏幕中看到客户端。

![Listener Screen](img/d273c5d335cbd2318e1eaf68e2e3bf60.png)

您可以将多个客户端连接到 DebugGap。

要开始调试应用程序，双击调试监听器窗口中的客户端，您将看到一个类似于 chrome 开发者控制台的窗口。返回手机 app，点击蓝点，选择*检查*。单击移动应用程序中的任何元素，它应该会反映在 debugGap listener 窗口中的 elements 选项卡上。

![Element preview](img/1afd458068612ca73f53bcb64f714cf0.png)

单击蓝点提供了一个名为“节点”的选项，它将 DOM 元素显示为节点。DebugGap 有一个控制台，它提供对移动浏览器控制台的访问，以便进行错误消息调试。

差不多就是这样！DebugGap 很简单，但它提供了一些有用的特性，可以挖掘运行在真实(或仿真)设备上的混合应用程序的幕后，这些设备有时很难访问。

## 结论

在本教程中，我们看到了如何使用 [DebugGap](http://www.debuggap.com/) 来调试移动应用程序。这是一个快速检查移动应用程序元素的便捷工具。

您曾经使用 DebugGap 调试过您的移动应用程序吗？你的经历是怎样的？或者有没有使用过其他工具进行调试？我们希望了解您的想法、建议或体验。

## 分享这篇文章