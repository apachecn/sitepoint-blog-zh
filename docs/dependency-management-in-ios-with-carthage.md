# iOS 与 Carthage 的依赖管理

> 原文：<https://www.sitepoint.com/dependency-management-in-ios-with-carthage/>

> 迦太基是古代迦太基文明的首都，位于突尼斯湖的东岸，也就是现在的突尼斯。公元前一千年，这座城市从腓尼基殖民地发展成为统治地中海的帝国中心。

尽管这是一个有趣的话题，但本文不会涉及古代历史，而是 iOS 的依赖管理。

Carthage 帮助您安装项目的依赖项以及这些依赖项的依赖项。如果您安装软件包 A，该软件包可能依赖于软件包 B，后者可能依赖于软件包 C。如果您安装软件包 D，它可能依赖于软件包 C，但却是不同的版本。寻找合适的版本是包管理器为您处理的工作。

iOS 中的包是‘框架’，有动态和静态两种。Carthage 只支持 iOS 8 中的动态框架。

## 装置

你可以用自制软件或安装包在 OS X 上安装 Carthage。

使用自制软件:

```
brew update
brew install carthage 
```

![Installing Carthage with Homebrew](img/22c4fc6865d9c35709623ad5829575b8.png)

如果你没有自制软件，从 Carthage 的[发布页面](https://github.com/Carthage/Carthage/releases)下载安装程序。

## 设置新项目

用您的项目设置 Carthage 很简单。启动一个新的 iOS 项目，在项目的根目录下创建一个名为 *Cartfile* 的文件。打开该文件并添加:

```
github "Alamofire/Alamofire" ~> 3.4 
```

在这个例子中，我使用了 [Alamofire](https://github.com/Alamofire/Alamofire) 作为项目的依赖项，github 作为包的源。Carthage 支持 github 和其他基于 git 的源代码。对于托管在另一个 git repo 上的包，用`git`代替`github`作为句子的前缀。顺便说一下，Carthage 是由一群来自 Github 的开发人员创建的。

![Installing dependencies using Carthage](img/e698872e44b7debc213235c76dd20284.png)

要安装或更新包，运行`carthage update`并等待 Carthage 下载并构建包。

![Cartfile location and libraries build directory](img/5225eda51cadac9167e4a41f499a6fd6.png)

在 *Carthage/Build* 文件夹中，Carthage 为它支持的每一个“部署”目标构建了一个框架，而 *Alamofire.framework* 是项目需要的文件。

![Link libraries to the project](img/c7fc6c5a76eb35bbc1820589b5e0d543.png)

使用项目设置中的*链接框架和库*窗格将包与项目链接，点击*加*按钮，选择*添加其他*添加 Alamofire。

现在，您已经准备好在您的项目中使用 Alamofire，并按照 sma eprocess 处理任何其他支持 Carthage 的包。

## 迦太基 vs 可可豆荚

很可能你已经在一个基于 XCode 的项目中使用了 [CocoaPods](https://cocoapods.org/) ，而且效果很好。为什么 XCode 需要另一个依赖管理器？

多个软件项目做同样的事情没有坏处。如果一个打包管理器出现故障，选件可以充当故障保险。过去，CocoaPods [在 github 上也有自己的问题。](http://thenextweb.com/dd/2016/03/08/cocoapods-popular-basically-took-five-github-servers/#gref)

Carthage 是一个分散的依赖管理器，而 CocoaPods 是集中的。有了 CocoaPods，你可以在他们的网站上搜索包，但是你不能用 Carthage，在 Carthage Github 将会是找到包的主要途径。这对某些人来说是个缺点，但是这样一来，迦太基就不能自己移除包了。

Carthage 是用 Swift 写的，CocoaPods 是用 Ruby 写的，这使它成为一个更“本土”的项目。

## 实证选择？

在本文中，我向您介绍了 Carthage，并展示了如何安装它，如何使用它，以及它与 CocoaPods 的主要区别。如果你想了解更多关于迦太基的事情，我推荐你去读读他们的[文档](https://github.com/Carthage/Carthage)。

在以后的文章中，我会介绍如何让你自己的包“兼容 Carthage ”,但同时我也希望在下面的评论中听到你使用它的经历。

## 分享这篇文章