# Swift 的过去、现在和未来

> 原文：<https://www.sitepoint.com/the-past-present-and-future-of-swift/>

*这篇文章由[阿德里安·桑杜](http://www.adriansandu.com/)和[马克·托勒](https://twitter.com/MarcTowler)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

我对 Swift 感到兴奋，原因我将在后面详述，但主要是它所吸引的热情。在不到六个月的时间里，以下是该项目的 GitHub 统计数据。

![Swift Repo](img/3861bbcbcceb3239937ff8e6d67366fd.png)

我知道 GitHub 的统计数据并不总是能说明什么，但这些数字仍然令人印象深刻。

上周，我[在阿姆斯特丹精彩的](http://chrischinchilla.github.io/Presentations/future_swift/mdevcon.html#/1) [MDevCon](http://mdevcon.com) 上发表了一篇关于 Swift 的过去、现在和未来的演讲。尽管有一些技术问题，但它受到了好评，似乎是一个很好的主题，可以变成一篇文章，所以在这里！

我将从回到 30 年前的 20 世纪 80 年代开始。

## 过去-目标-C，Swift 的遗产

布拉德·考克斯和汤姆·洛夫是 20 世纪 80 年代初 Objective-C 的最初创造者。他们想在 [C](https://en.wikipedia.org/wiki/C_(programming_language)) 中加入[面向对象编程](https://en.wikipedia.org/wiki/Object-oriented_programming)的概念，并受到了当时流行语言 [Smalltalk](https://en.wikipedia.org/wiki/Smalltalk) 的启发。

1988 年， [NeXT](https://en.wikipedia.org/wiki/NeXT) 决定授权 Objective-C 构建他们的高级操作系统， [NeXTSTEP](https://en.wikipedia.org/wiki/NeXTSTEP) 。

![NeXTSTEP](img/a074a131c687ecc7c1a4de3500500023.png)

NeXT 并不成功，但他们的软件工具足够受欢迎，他们的遗产以创始人史蒂夫·乔布斯和他创办的另一家公司的形式保留了下来。

1997 年，苹果收购了 NeXT，作为协议的一部分，史蒂夫·乔布斯重返公司担任首席执行官，凭借 iMac、iPod，当然还有 iPhone 等产品，开始了这家(当时)苦苦挣扎的公司的第二次生命。

2001 年，苹果发布了第一个版本的 Mac OS X T1，与之前的 Mac OS 版本大相径庭。那时，我在 Mac 支持部门工作(啊，OS 7-9 的令人兴奋的日子)，相信我，OS X 是**可怕的**。

![Mac OS X 10.0](img/6dac4cc5114d703d3c2822a1aa68dbc2.png)

NeXTSTEP 的残余在 OS X 随处可见，官方的开发语言是 Objective-C。我们仍在使用的一些常用库，如 Cocoa、App Kit 和 Foundation Kit 都来自 NeXTSTEP，它们已经有 30 年的历史了。

当 2007 年 iOS 出现时(当时是 iPhone OS ),它也使用 Objective-C 并与 OS X 共享一些相同的库。

时间快进到 2014 年的 WWDC，苹果公司宣布，经过 30 多年的发展，Objective-C 是时候慢慢退休了，取而代之的是一种新的、更现代、更合适的语言。那当然是斯威夫特。

对于这位苹果的长期用户来说，Swift 代表了苹果第一次倾听人们的需求，从而提供比其他工具更有吸引力的东西。开源和创建该语言的其他平台版本是另一个非苹果的步骤。加上活跃的社区，我认为这是 Swift 的优势所在。

## 现在 Swift 有什么可能

从 Swift 开源的第一天起，Linux 支持就已经可用，根据我的实验，它工作得很好。

目前还没有发行包，但是安装很容易:

```
wget https://swift.org/builds/swift-2.2-branch/ubuntu1404/xxx.tar.gz
tar -xvf xxx.tar.gz
sudo apt-get install clang
export PATH=/home/vagrant/xxx/usr/bin:"${PATH}"
```

*用适当的文件名和路径替换值。Swift 在不断发展，版本和文件名也是如此。*

编译并运行任何 Swift 文件，例如:

```
swiftc helloworld.swift
./helloworld
```

Linux 编译器提供了对大多数相关功能的支持(记住，这是一个服务器，而不是一个移动设备),并且到目前为止看起来性能良好且稳定。

在服务器端 Swift 的基础上，已经出现了两个框架。Perfect ( [已经在 SitePoint](https://www.sitepoint.com/server-side-swift-with-perfect/) 上介绍过)提供简单的服务器/客户端交换，而 [Vapor](https://github.com/qutheory/vapor) 是一个 Rails / Laravel 启发的用于 Swift web 应用的 MVC 框架。

出现了一些工具来更好地支持跨平台 Swift，其中包括:

*   Swift env:Swift 环境管理器，如 [pyenv](https://github.com/yyuu/pyenv) 。
*   [swifter](https://github.com/httpswift/swifter) :一个用 Swift 写的基本 HTTP 服务器。

### iOS vs Swift

需要注意的是，Swift 不等于 iOS。Swift 包括:

*   编译程序
*   标准程序库
*   SDK 覆盖
*   调试器

它不包括(也永远不会包括)任何特定于 iOS 的库。首先因为它们是专有知识产权，其次因为它们与跨平台语言无关。iOS 库主要与 iOS 设备相关，虽然我是开源支持者，但我明白为什么它们不值得开源。

Swift 得到了苹果的大力支持，但却是一个外部的基金会和社区。swift.org 网站包含信息、邮件列表和社区指南，供有兴趣了解更多的人参考。

## 未来

### 版本 3 及更高版本

Swift 的未来目标是围绕稳定性和跨平台一致性，对于版本 3，这包括:

*   核心库改进
*   再见`NS`名称空间，一个最终会消失的 Objective-C 遗产。
*   对应用程序二进制接口(ABI)的改进，该接口是库和主机操作系统之间的接口。
*   弹性和便携性
*   代码和标准的一致性和整合。

在未来的某个时刻:

*   测试模块，因此开发人员可以将测试文件夹放入 Swift 项目中。
*   C++互操作性，允许使用遗留代码和本地代码。
*   Swift 目前依赖操作系统级库进行并发处理，并需要自己的本地并发。

Swift 功能的计划和提案都是公开的，因此，有关这些问题和其他可能出现的问题的更多详情，请访问 [Swift Evolution repository](https://github.com/apple/swift-evolution) 。

### Windows 和 Android

最大的问题是 Swift 还将支持哪些平台？我发现自己在想，既然 Android 有一个 Linux 内核，那么运行 Swift 应用程序就不会太难了？

第一个问题是一个商业问题。让 Swift 应用原生运行在 Android 和 Windows 上，谷歌、微软和苹果能得到什么？我觉得 Windows 更有可能，因为微软最近出人意料地对想法持开放态度。

如果你现在乐于忽略“原生”方面，那么有一些选项可以用来编写一次可以在 Mac OS X、Windows、iOS 和 Android 上运行的 Swift。这些是来自 Elements Compiler 的 [Fire / Silver](http://www.elementscompiler.com/elements/fire/) 和来自 JetBrains 的 [CLion](http://www.elementscompiler.com/elements/fire/) ，支持 Swift(带插件)和 C++ / C 代码。这些 ide 将代码编译成目标平台的本地语言，这从来都不是最有效的策略，但这是一个可行的选择，我已经测试过了。

如果您想采取不同的方法，那么 Swift 的 C/C++基础可以提供帮助。如果你的平台有一个 C 编译器(包括[安卓 NDK](http://developer.android.com/tools/sdk/ndk/index.html) )，那么理论上有可能强行将一个 Swift 项目编译到任何平台上。我偶然看到[这篇很有前途的博文](http://romain.goyet.com/articles/running_swift_code_on_android/)，它涵盖了在 Android 上运行 Swift，看起来很合理，但这个过程在进行到一半时就停止了。这是由于 NDK 和 Swift 不断发展的性质，但步骤大致遵循以下流程:

1.  从 Swift 代码编译一个目标文件。
2.  将对象与适当的处理器体系结构和库定义相链接。
3.  从步骤 2 创建机器码。
4.  使用共享库、NDK 和 C 代码，将结果文件打包成一个 Android 应用程序。
5.  利润？

这同样适用于为 Windows 编译 Swift 代码，但我能找到的唯一一篇涵盖这种可能性的帖子是一篇 [Stack Overflow](http://sweettutos.com/2012/08/11/objective-c-on-windows-yes-you-can/) 评论，该评论称，因为你可以在 Windows 上运行 Objective-C，Swift 可能成为可能。

这一切都牢牢地在里面<q>何必呢？</q>领域，但作为开发人员，我们经常喜欢尝试和突破界限，看看是否可行。Windows 和 Android 是否得到“官方”支持还有待观察，但与此同时，疯狂一下，看看你能得到什么。

## 结论

我希望你喜欢这次旅行，并沿着记忆中的小路蹒跚而行，去审视当前快速的景观，并展望暴风雨的地平线。在讨论了一些边缘话题之后，这是一个很好的时机来回顾为什么我认为 Swift 很棒，为什么你应该尝试它。Swift 有一个财力雄厚的热心支持者和一个热情响应的社区。Swift 对开发人员友好，具有现代范例和考虑因素，并且部分是跨平台的。

如果你是 Linux 或 Mac 用户，无论是否在开发 iOS，请访问[swift.org](http://swift.org)开始吧。

## 分享这篇文章