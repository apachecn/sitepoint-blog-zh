# 如何开发苹果应用程序:使用 Xcode & Swift 为 iOS & macOS 编程

> 原文：<https://www.sitepoint.com/develop-apple-apps/>

如果你是苹果生态系统的用户，你可能每天都会使用 Mac、iPhone 或 iPad。你可能不知道的是，为这些设备开发你自己的应用程序是可能的——而且没有你想象的那么难！在这篇文章中，我们将带你大致了解如何开发苹果应用程序。我们将为您提供更多资源，帮助您掌握在 macOS、iOS 和其他 Apple 平台上编程所需的技能。您将了解 Swift 和 Xcode，设置工具来创建、测试和调试 iOS 应用程序，并将其发布到 App Store 中。

到本文结束时，您将拥有开始开发自己的 Apple 生态系统应用程序所需的一切！

## 苹果开发者生态系统的组成部分

为了开发 macOS 和 iOS 应用程序，你需要了解一些关键组件。

熟悉有助于你制作第一个应用程序的工具。当然，像 MacBook 或 iPad 这样的苹果电脑应该是你的第一个工作工具。让我们深入探讨一下:

### Xcode IDE

![The Xcode 13 Integrated Development Environment interface](img/b22e019d95ff0ad124e18e273fe9d0f3.png)

借助 [Xcode IDE](https://developer.apple.com/xcode/ide/) (集成开发环境)，你将能够为 iPhone、iPad、Mac、Apple Watch 和 Apple TV 开发应用。Xcode 包含了创建令人惊叹的应用程序所需的一切，包括一套丰富的工具，用于设计用户界面、构建应用程序和测试代码。

Xcode 设备模拟器允许您在各种不同的设备上测试您的应用程序，无论您是在为 iPad、Apple Watch 和 Apple TV 构建 iPhone 应用程序还是其他应用程序。模拟器是测试你的应用的 UI 和功能的一个很好的方法，而不需要把它安装在物理设备上。Xcode 模拟器也是测试您的应用程序对不同屏幕大小和方向的响应的好方法。

### Swift 编程语言

[Swift](https://developer.apple.com/swift/) 是苹果公司创造的一种编程语言。这是一种强大而快速的编程语言，易于学习。如果你是编程新手，Swift 是一个很好的起点。

斯威夫特最棒的地方是什么？它允许您使用相同的工具和框架来创建各种平台的应用程序，如 iOS、tvOS、iPadOS、macOS 和 watchOS。

### 斯威夫特伊

SwiftUI 是一个用户界面框架，使得 UI 编码变得轻而易举。它还可以帮助你将用户界面与应用程序的功能代码联系起来。使用 SwiftUI，您可以快速轻松地创建复杂的界面，而不必担心底层代码。

SwiftUI 还包括许多内置组件，如按钮、标签和文本字段，您可以在应用程序的 UI 中使用它们。

### 软件开发工具包

Apple SDKs 是一组工具和 API，您可以使用它们来开发适用于 iOS、macOS 和 tvOS 的应用程序。每个 SDK 都包括许多系统 API 和框架，您可以使用它们来构建应用的 UI 和功能，并与操作系统功能进行交互。

*   [macOS SDK](https://developer.apple.com/macos/whats-new/)
*   [iOS SDK](https://developer.apple.com/ios/whats-new/)
*   [tvOS SDK](https://developer.apple.com/tvos/whats-new/)

### TestFlight 软件

TestFlight 对开发者来说是一个很有价值的工具，它允许你向有限数量的用户分发应用的测试版本。这是在向公众发布应用程序之前获得反馈的好方法。

### App Store 连接

通过 [App Store Connect](https://appstoreconnect.apple.com/login) ，您可以在 App Store 上无缝发布和管理您的应用。

你需要提供应用程序的基本信息，如名称、屏幕截图和描述。它还允许您直接从 Xcode 上传构建包。

列出你的应用了吗？直接从门户提交，等待审核。然后，当你等待应用程序在应用程序商店上线时，祈祷它能让审查者满意。

### 供应门户

签名工具在[供应门户](https://developer.apple.com/library/archive/recipes/ProvisioningPortal_Recipes/CreatingaDevelopmentProvisioningProfile/CreatingaDevelopmentProvisioningProfile.html)中，它允许您对代码进行签名。通过这种方式，苹果可以确定你是你的应用程序的原始作者。用户也能认出你的 app 是原版。这使他们的安全完好无损。

## 开发苹果应用程序需要什么

为了开发苹果应用程序，你需要一台运行最新版本 macOS 的 Mac 电脑。

你需要从 [Mac 应用商店](https://apps.apple.com/us/app/xcode/id497799835?mt=12)或者从[苹果开发者网站](https://developer.apple.com/download/more/)下载 Xcode。

一旦安装了 Xcode，就可以开始编写代码了。为了测试和调试你的应用，你需要一个运行 iOS 或 macOS 的设备。Xcode 附带了 iOS 模拟器和 macOS 模拟器，允许您在模拟设备上测试应用程序。

如果你想为 App Store 开发一款应用，你需要[注册苹果开发者计划](https://developer.apple.com/programs/)。通过苹果开发者计划，你将能够在真实设备上开发和测试你的应用程序，并将它们提交到应用程序商店。

苹果开发者项目每年花费 99 美元，你需要有一张有效的信用卡。当你开发完你的应用程序后，该计划的会员资格将使你能够将其提交到应用程序商店。App Store 模式有其缺陷，但为全球数百万消费者提供了内置的分销渠道。App Store 是销售您的应用程序、推广您的品牌和接触新用户的最佳场所。

如果你刚刚开始，我们建议你使用免费开发者帐户开发你的应用程序。有了免费的开发者账户，你可以在真实设备上开发和测试你的应用，但你不能将它提交到应用商店。

## 你需要学习的技能

如果你是编程新手，不要担心！有大量资源可以帮助您学习 Swift 的基础知识和开发 Apple 应用程序。

以下是开发苹果应用程序需要的一些技巧:

### 使用 Xcode

Xcode 包括代码编辑器、调试器和其他工具来帮助您开发应用程序。您需要学习 Xcode 的布局以及如何使用它的各种功能。

[https://www.youtube.com/embed/lhtx0NZ7hJk?feature=oembed](https://www.youtube.com/embed/lhtx0NZ7hJk?feature=oembed)

苹果开发者文档提供了对 Xcode 工作空间和功能的[全面介绍。](https://developer.apple.com/documentation/xcode)

### 学习 Swift

Swift 是用于开发苹果应用的主要编程语言。一旦你学会了 Swift 的基础知识，你将能够开始创建你自己的 iOS 和 macOS 应用程序！这是一种强大而快速的编程语言。

Swift 是一种编译型编程语言，针对性能和安全性进行了优化。它的代码在设计上是安全的，但也能产生运行速度极快的软件。Swift 将软件编译成本地二进制文件，可直接部署到设备和服务器上，或通过 JavaScript 部署到网络上。

苹果创造 Swift 是为了便于读写，便于初学者理解。如果你是编程新手，Swift 是一个很好的起点。

#### 学习 Swift 的资源

*   你可以通过跟随苹果官方教程来学习 Swift，这些教程可以在[苹果开发者网站](https://developer.apple.com/swift/resources/)上找到。
*   [Swift Playgrounds](https://developer.apple.com/swift-playgrounds/) 是一款 iPad 和 Mac 应用程序，帮助您学习使用 Swift 编码和构建应用程序。
*   苹果公司的 [Swift 编程语言](https://docs.swift.org/swift-book/index.html)参考书对 Swift 编程语言进行了完整的概述。

##### 创建您的首个 Swift 应用

[![Create Your First Swift App course](img/591e0f1b287aad19303d873a38baf8ce.png)](https://www.sitepoint.com/premium/courses/create-your-first-swift-app-2972/)

在 Swift 寻找快速基础？[创建您的第一个 Swift 应用程序](https://www.sitepoint.com/premium/courses/create-your-first-swift-app-2972/)是开始 Swift 编程的最佳方式。它从教你基础知识开始，然后继续介绍更高级的话题。

这些视频有一个引人入胜的主题，可以让你在每一堂课中都保持注意力。如果学习新东西听起来很有趣，看看这个——这根本不会花很长时间(不到两个小时)！

##### 云中的雨燕

[![Swift in the Cloud book cover](img/c369f6f63eaadd5f734f08a3dcfbd18f.png)](https://www.sitepoint.com/premium/books/swift-in-the-cloud/)

在 [Swift in the Cloud](https://www.sitepoint.com/premium/books/swift-in-the-cloud/) 中，您将看到创建您自己的可在云端运行的 Swift 应用程序的所有方面的全面报道。

您将从一个帮助将这种创新语言引入云环境的团队那里了解它是如何工作的。当您需要关于使用 Swift 专门为云开发应用程序的详细指南时，请阅读本书。

➤ [在云端免费阅读雨燕章节](https://www.sitepoint.com/premium/books/swift-in-the-cloud/read/)

### 学习 UIKit 和 SwiftUI

UIKit 是用于开发苹果应用程序用户界面的框架。使用 UIKit，您可以创建接口，而不必担心底层代码。

通过 [SwiftUI](https://developer.apple.com/xcode/swiftui/) ，可以快速创建复杂的界面。SwiftUI 是一个声明性框架，这意味着您用代码描述用户界面，而框架负责创建实际的界面。

SwiftUI 和 UIKit 是经常被比较的两种不同的框架。它们各有利弊，选择哪一个由你自己决定。这两个框架的心智模型不同，但是您可以使用其中任何一个。

两者都有很好的在线文档和支持，最低的 iOS 版本是相同的，开发速度是相当的，并且它们可以在各种各样的平台上运行。

#### 学习 UIKit 和 SwiftUI 的资源

Apple 有许多学习 UIKit 和 SwiftUI 的资源:

*   [UIKit 用户界面目录](https://developer.apple.com/documentation/uikit/views_and_controls/uikit_catalog_creating_and_customizing_views_and_controls)概述了 UIKit 中各种类型的用户界面元素。
*   [SwiftUI 框架参考](https://developer.apple.com/documentation/swiftui/)描述了组成 SwiftUI 框架的类、方法和属性。

还有很多优秀的第三方资源，比如:

*   Ray Wenderlich 的 UIKit 学徒:这些教程涵盖了 UIKit 开发中的广泛主题。
*   [AppCoda 的 SwiftUI 教程](https://www.appcoda.com/category/tutorials/swiftui/) : AppCoda 的 SwiftUI 教程很全面，很容易上手。
*   [用 Swift 进行黑客攻击](https://www.hackingwithswift.com/100/swiftui):用 Swift 进行黑客攻击有很多关于 SwiftUI 开发的优秀文章和教程。

## 了解更多信息的资源

如果你有兴趣学习如何开发苹果应用程序，请查看以下资源:

### Apple 开发者文档

无论您是苹果平台的编码或开发新手，还是经验丰富的 Swift 开发人员，Apple 开发文档都是一个很好的起点。该文档包括所有 Apple SDKs 和工具的指南和参考资料。

### WWDC 视频

每年，苹果都会举办全球开发者大会(WWDC)。在 WWDC，苹果开发人员可以参加研讨会和会议，了解新的功能和技术。苹果开发者网站上有免费的 WWDC 视频。

### 苹果开发者论坛

苹果开发者论坛是一个向其他开发者提问和寻求帮助的好地方。您还可以在论坛上找到有用的代码示例和示例项目。

### 用苹果学习编码

如果你有兴趣成为一名苹果开发者，你可以从参加免费的苹果课程开始。这些课程是为初学者设计的，涵盖了 Swift 编程的基础知识。完成这些课程后，你将拥有开发自己的应用程序所需的技能和知识。

### SitePoint 社区和移动开发文章

SitePoint 社区论坛是一个从其他开发者那里获得帮助和学习新技术的好地方。T2 的 SitePoint Discord 服务器 T3 也是一个和其他开发者聊天的好地方。

您还可以在 SitePoint 上找到关于[移动开发](https://www.sitepoint.com/mobile/)的有用文章。

### SitePoint 高级库

我们提到了一些 SitePoint 优质资源，比如我们的 [Swift 速成班](https://www.sitepoint.com/premium/courses/create-your-first-swift-app-2972/)。该库中还有数百种其他图书，包括许多对有抱负的移动开发人员有用的图书。你可以通过订阅[SitePoint Premium](https://www.sitepoint.com/premium/pricing/)来访问整个图书馆。

这里有几个例子，在你学习的不同阶段，你可能会感兴趣。

#### 初战告捷:应用程序开发实践指南

![](img/5294e6c9c3b1f6a30b87e441dc084f75.png)

如果你渴望创建自己的应用程序，但不知道从哪里开始， [Beginning Flutter](https://www.sitepoint.com/premium/books/beginning-flutter/) 是完美的指南。这个实践指南一步一步带你了解颤振开发的基础。Flutter 允许你为 Android 和 iOS 开发漂亮、强大的应用程序。

您将学习如何创建漂亮的用户界面，添加交互性和动画，以及在 Android 和 iOS 设备上部署您的应用程序。有了 Flutter，你就不需要学习多种编程语言或者修改多个代码库——你可以用这个强大的工具包完成所有的事情。

➤ [阅读免费章节](https://www.sitepoint.com/premium/books/beginning-flutter/read/1)

#### React Native 入门

![Getting Started with React Native](img/42842368817feeabb39bc20b34eca650.png)

如果你是一个想要创建移动应用的 web 开发者，React Native 绝对值得一试。使用 React Native，您可以使用您已经熟悉的相同语言(HTML、CSS 和 JavaScript)来创建原生移动应用程序。并且【React Native 入门是帮助您开始使用这种替代方法的完美资源。

这本书首先概述了 React Native 及其关键特性。然后，您将直接开始构建您的第一个 React 本机应用程序。在这个过程中，您将学习如何利用基本组件、使用列表和表单、合并特定于平台的代码、使用外部库等等。

➤ [阅读免费章节](https://www.sitepoint.com/premium/books/getting-started-with-react-native/)

#### 在 iOS 上构建 Node.js 应用程序

如果你想开始 iOS 开发，这个课程是为你准备的。在[为 iOS](https://www.sitepoint.com/premium/courses/building-a-node-js-application-on-ios-2999/) 构建 Node.js 应用程序中，我们将涵盖所有基础知识，从设计应用程序到与数据服务交互。

您将了解如何构建用于导航应用程序的 ViewControllers，以及如何为用户注册和登录添加身份验证。课程结束时，您将具备开始构建优秀的 Node.js 驱动的 iOS 应用程序所需的所有技能。

#### 初学者可用性:新手指南僵尸校对你的网站

![](img/fd88847dd4a5f58cd6d69b38616b14dd.png)

寻找僵尸证明你的网站？可用性是关键！

大多数人认为应用都是关于代码和设计的。但事实上，可用性同样重要——如果不是更重要的话。可用性是关于某人如何使用一个应用程序或网站，以及用户期望你的网站如何运行。

这就是这本书存在的原因——帮助你理解你需要知道的关于可用性的一切，包括前端和后端。[初级可用性](https://www.sitepoint.com/premium/books/beginner-usability/)将向你展示如何确保你的应用程序易于使用和导航，我们还会给你一些如何让它平稳运行的提示。

➤ [阅读免费章节](https://www.sitepoint.com/premium/books/beginner-usability/)

## 分享这篇文章