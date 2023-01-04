# 快速提示:安装 Android SDK

> 原文：<https://www.sitepoint.com/quick-tip-installing-the-android-sdk/>

*这篇文章由[阿德里安·桑杜](http://www.adriansandu.com/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

要开发 Android 应用程序，您需要在机器上安装 Android SDK(软件开发工具包)。因为有几种不同的方法可以做到这一点，所以在这篇快速技巧文章中，我将介绍可用的选项，以及为什么您可能想要选择其中的一个。

## 安卓工作室

安装 Android SDK 最简单的方法是从 Google 和 JetBrains 下载 Android Studio，这是官方的 Android IDE(集成开发环境)。如果你打算用 Cordova、Ionic 或 React Native 等跨平台工具创建 Android 应用，Android Studio 远远超过你的需求。或者你可能需要 Android SDK 来构建和测试其他人构建的应用程序。在这些情况下，安装独立的 SDK 就是您所需要的，同样，有几个选项。

## 爪哇 JDK

要在任何平台上安装 Android SDK，您需要安装 Java JDK。从适用于所有主要平台的 [Oracle 网站](http://www.oracle.com/technetwork/java/javase/downloads/index.html)下载。

### Linux 操作系统

```
apt-get install sun-java6-jdk
```

如果您的发行版是 64 位的，请首先运行此命令来安装所需的 x86 库:

```
sudo dpkg --add-architecture i386
sudo apt-get update
sudo apt-get install libncurses5:i386 libstdc++6:i386 zlib1g:i386
```

## 安装和配置 SDK

### Windows 操作系统

从下载页面中选择第一个选项[，将会下载一个*。exe* 文件。下载完成后，双击并开始安装。](http://developer.android.com/sdk/index.html#Other)

您可以将安装位置更改为您想要的任何位置，但是为了简单起见，请保持原样。

安装完成后，您需要更改 Windows 环境变量，以便 adb(定义)服务器可以在任何地方启动。

1.  打开*控制面板*
2.  点击*系统*，在左侧选择*高级系统设置*。
3.  在*高级*选项卡中，点击底部的*环境变量*。
4.  在*系统变量*部分选择*路径*变量。
5.  选择*编辑*，点击*新建*。
6.  add _；c:\ Users \ % username % \ AppData \ Local \ Android \ Android-SDK \ tools \；c:\ Users \ % username % \ AppData \ Local \ Android \ Android-SDK \ platform-tools _ 如果您没有在安装窗口中更改位置。如果您更改了位置，那么将*C:\ Users \ % username % \ AppData \ Local*更新到新位置。

**注意**:用你的账号用户名替换`%username%`。

确认更改，您就可以使用 SDK 了。

## Linux 操作系统

下载*。tgz* 文件来自 Linux 部分[这里](http://developer.android.com/sdk/index.html#Other)。

下载完成后，将内容解压到您的*主目录*中。

打开您的`~/.bashrc file`,如果它不存在，请创建它。这是您主目录中的一个隐藏文件。启用*查看菜单- >在文件导航器中显示隐藏文件*或从命令行打开。

在文件末尾添加以下几行:

```
export ANDROID_HOME=/home/<yourusername>/Android/Sdk
export PATH=$PATH:/home/<yourusername>/Android/Sdk/tools
```

用您的用户名替换`<yourusername>`。

保存文件并重新启动计算机，或者注销并登录。

## 苹果个人计算机

下载*。来自 Mac 部分[的 zip](http://developer.android.com/sdk/index.html#Other)* 文件在这里。将 *android-sdk-mac* x86_ 文件夹解压到您的主目录中，以便于访问。

在*中设置`PATH`变量。bash* profile_ file 位于您的主目录中，添加以下几行:

```
export PATH=/Users/me/android-sdk-mac_86/platform-tools:/Users/me/android-sdk-mac_86/tools:$PATH
```

用您的用户名替换`/Users/me`。

保存并重新启动计算机，以使更改生效。

### 公司自产自用

Mac 用户的另一个很好的选择是使用[自制软件](http://brew.sh/)来安装和更新 Android SDK。用标准的`brew`命令安装:

```
brew install android-sdk
```

就这样，你现在可以使用 Android SDK 了。*如果您有任何问题或意见，请在下面*告诉我。

## 分享这篇文章