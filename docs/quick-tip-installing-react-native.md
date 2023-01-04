# 快速提示:安装 React Native

> 原文：<https://www.sitepoint.com/quick-tip-installing-react-native/>

React Native 已经迅速成为构建跨平台移动应用的最热门框架之一。基于 JavaScript 和脸书的 [React 库](http://facebook.github.io/react/)，它专注于性能和与本地平台支持的紧密集成。有了脸书投资的框架和 React 的<q>学一次，写随处</q>哲学，React 前途一片光明。

没有在 Windows 或 Linux 上构建 iOS 应用程序的官方方法，所以本指南主要关注在 OS X 上的安装。

## 步骤 1:依赖关系管理器

从安装 [Homebrew](http://brew.sh) 开始，这是一个在 OS X 上安装应用程序的便捷工具，而这些应用程序在 app store 中是买不到的。 [Chocolatey](https://chocolatey.org/) 是一个 Windows 等价物，在 Linux 上可以获得大多数必需的软件包。

## 步骤 2:安装可选的依赖项

### 巡夜者

Watchman 是一种服务，它监视文件的变化，并根据这些变化触发操作。React 原生团队建议立即显示代码中的变化。

#### Mac 安装

```
brew install watchman
```

#### Windows 安装

Windows 支持在 Alpha 中，但是通读[这个 GitHub 问题](https://github.com/facebook/watchman/issues/19)尝试在 Windows 上安装 Watchman。

#### Linux 安装

```
git clone https://github.com/facebook/watchman.git
cd watchman
./autogen.sh
./configure
make
sudo make install
```

### 流动

[Flow](http://flowtype.org/) 是一个[静态类型检查器](https://en.wikipedia.org/wiki/Type_system)，它将帮助你的 JavaScript 更加稳定。

#### Mac 安装

```
brew install flow
```

#### Windows 安装

Windows 支持在 Alpha 中，但是通读[这个 GitHub 问题](https://github.com/facebook/flow/issues/6)来尝试在 Windows 上安装 Flow。

#### Linux 安装

```
wget https://facebook.github.io/flow/downloads/flow-linux64-latest.zip
unzip flow-linux64-latest.zip
cd flow-linux64-latest
echo -e "\nPATH=\"\$PATH:$(pwd)/\"" >> ~/.bashrc && source ~/.bashrc
```

## 步骤 3:安装节点

使用 Homebrew 的缺点是让一个包管理器处理另一个包管理器会变得复杂，并且您的 NPM 安装可能有可靠性问题。如果你用的是 Windows，巧克力应该没有问题。

安装节点最可靠的方法是将其安装在 NVM(节点版本管理器)下。如果您已经安装了 NVM，或者通过其他方式安装了 Node 4+版本，您可以跳到步骤 4。

### 卸载以前安装的节点

按照本[要点](https://gist.github.com/DanHerbert/9520689)中的建议卸载 node 的现有版本，简要总结如下:

```
rm -rf /usr/local/lib/node_modules
brew uninstall node
```

### 安装 NVM

#### Linux 和 Mac OS

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
```

#### Windows 操作系统

在 Windows 上安装 NVM 有一些非官方的替代方法，[在这里找到它们](https://github.com/creationix/nvm#installation)。

## 步骤 4:安装最新的节点并本地反应

现在，您应该能够运行以下命令来安装 Node，为新终端设置默认节点版本，并安装 React Native:

```
nvm install node && nvm alias default node
npm install -g react-native-cli
```

现在您已经安装了 React Native 的命令行工具。接下来，让我们安装 iOS 和 Android 依赖项。

## 步骤 5:安装 Xcode

Xcode 只适用于 Mac，可以从这个链接直接安装[。](https://itunes.apple.com/us/app/xcode/id497799835?ls=1&mt=12)

## 第六步:安卓依赖

### Java 开发工具包

从这里安装最新的 JDK[。Mac 用户还需要安装 Java 运行时环境，](http://www.oracle.com/technetwork/java/javase/downloads/index.html)[在这里找到详细信息](https://java.com/en/download/)。

### Android SDK

Android Studio 是原生 Android 开发的官方 IDE，包含 Android SDK，[点击此处](http://developer.android.com/sdk/index.html)下载 Android Studio。

## 步骤 7:创建一个 React 本地项目

使用以下命令创建一个 React 本地项目:

```
react-native init projectname
```

您现在已经安装了 React Native 和您的第一个项目！

如果您有任何问题或意见，请告诉我。

## 分享这篇文章