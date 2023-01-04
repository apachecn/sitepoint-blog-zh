# 无 Java 的 Android

> 原文：<https://www.sitepoint.com/java-free-android/>

Android 发现自己处于有趣的时代。谷歌已经开始创建自己的“高级”操作系统版本， [Cyanogen](http://thenextweb.com/mobile/2016/10/11/cyanogen-wont-build-its-full-android-based-os-anymore/) 已经停止开发，许多人认为 Android 将在不久的将来改变名称或形状。

由于操作系统的未来仍然不确定，开发者为该平台开发的语言也是如此。传统上，开发人员用 Java 编写 Android 应用程序，Java 是一种有着悠久历史和生态系统的语言，但这总是感觉是强加给 Android 的，缺乏最新的功能，对于来自不同语言背景的开发人员来说很难操作。

在本文中，我将聚集一些可能的竞争者，看看他们取代 Java 这个庞然大物是多么容易、现实和可信。这些语言的额外好处是，你可以经常使用它们来定位其他移动平台。

## 不允许网络浏览

开发人员的长期解决方案是使用不同的技术将 JavaScript 和 HTML 转换成伪应用程序。我不会在这一轮中包括这些混合选项中的任何一个，如 [React Native](https://facebook.github.io/react-native/) 、 [Cordova](https://cordova.apache.org/) 或 [NativeScript](https://www.nativescript.org/) 。我不想就本地和混合应用程序开发展开激烈的争论，但是在本文中，我只对可能成为“本地”代码的语言感兴趣。

## 进入 NDK

谷歌做出了一个有趣的决定，决定用 Java 作为开发 Android 应用程序的编程语言。虽然它是可移植的和流行的，但它也限制了开发者，因为没有官方支持使用更有效的语言，如 C 或 C++。

本机开发工具包(NDK)是对此限制的一种变通方法，增加了对使用这些语言访问某些硬件功能的支持。这是开发人员重用使用 C/C++的遗留库的一种方式，也是在 Android 上使用其他语言的一种常见方式。我将展示的许多例子都采用了这种方法，使用 NDK 将代码编译成 C/C++。这不仅仅是一个实验的借口，还向开发者和库的更大世界开放了 Android。

要使用 Android APIs 和库，开发人员通常必须通过包装代码“绑定”C/C++，并通过这个中介来访问功能。这并不像听起来那么糟糕，只需要最少的 Java 代码。

过去最好避免使用 NDK，但近年来它变得更好用，性能也更高。在某些用例中(尤其是游戏),它比 Java 更有性能，但这仍然是一个高级领域，沿途有陷阱。

准备好冒险了吗？那我们开始吧。

## C、C++和 C

答:你可能已经猜到，如果你想为 Android 编写 C 和 C++代码，那么由于从 [CLion](https://www.jetbrains.com/clion/specials/clion/clion.html) 借来的工具， [Android Studio](http://tools.android.com/download/studio) 已经支持它，这是一个 IDE，也是由 Android Studio 核心 JetBrains 的制造商创建的。

如果你想了解更多关于在 Android 上使用 C/C++代码的知识，那么[我推荐这篇文章让你开始](https://www.sitepoint.com/using-c-and-c-code-in-an-android-app-with-the-ndk/)。

### Xamarin

最初名为 Mono，现在归微软所有， [Xamarin](https://www.xamarin.com) 允许你跨多个平台使用 C#，包括 Android。同样，在幕后，它使用 NDK 来编译 C#，但让您可以访问大多数本机组件和 API。定价模型可能会令人困惑，因为现在 Xamarin 是 Visual Studio 定价的一部分，这意味着您需要为支持和基础设施支持付费，但 IDE 是免费的。

如果你喜欢 C#(和我交谈过的每一个 C#开发人员都喜欢这种语言)，那么它对你来说是一个不错的选择。

## 我的锅

Kotlin 是一个有趣的命题，它是 Android 开发中最容易使用的语言之一，也是最有可能取代 Java 的语言。它是由 JetBrains 创建的，JetBrains 是 Android Studio 的基础，可以与 Java 互操作，这意味着你也可以访问官方的 Android 库。

它与 Android Studio 配合良好，旨在为 Java 开发人员所熟悉，同时带来其他现代语言的好处。您还可以将它编译成 JavaScript 代码，这对于创建多平台应用程序的人来说是一个更有趣的选择。

[在项目网站上了解更多信息](https://kotlinlang.org/)。

## 加油郎

Go 是相对较新的(2009 年)诞生于谷歌，所以许多人预计它会成为一个可行的 Android 开发选项。它拥有受现代应用程序开发人员欢迎的特性，比如能够同时运行应用程序的实例(以及的各个部分)。它很快，并且去掉了旧语言带来的许多额外负担。这个特性集使得它在像 [Docker](https://www.docker.com/) 这样的应用程序中很受欢迎，但是我想知道它们是否与移动应用程序开发相关。

尽管如此，仍然有支持 Android(和 iOS)开发的可用包，它们很容易集成到您的应用程序中，并且非常适合中间件或后端服务来服务应用程序接口。

首先[安装 GoLang](https://golang.org/doc/install) ，然后安装[移动工具](https://godoc.org/golang.org/x/mobile/cmd/gomobile)并初始化。之后，您可以选择构建一个适合我上面提到的目的的独立应用程序，或者将 Go 代码的某些部分绑定到本地 Java 代码。

想了解更多？然后[阅读项目文档](https://godoc.org/golang.org/x/mobile/cmd/gomobile)并确保你仔细遵循每一步，就像编译其他语言一样，Go 需要在你的系统上正确设置某些连接和工具。

## Dart.js

不要让名字欺骗了你，虽然 [Dart](https://www.dartlang.org/) 中有 JavaScript 的元素，但这种语言是编译的，并在它自己的虚拟机(VM)上运行，很像 Java。Dart 是谷歌最近的另一项发明(2011 年)，在淡出公众视线一段时间后，它作为 Java 的替代品重新浮出水面。它将吸引那些来自 JavaScript 背景、渴望进入 C 风格语言的人，因为它包含了两者的元素。

对于移动开发，有一组叫做 [Flutter](https://flutter.io/) 的扩展和工具，它们采用有趣的不同方法。它不使用本地部件和组件，也不使用 web 视图。相反，Flutter 使用自己的方法来呈现屏幕元素并运行您的代码。这给项目(不一定是语言)带来了复杂性，但也使它成为更明显的替代。

Flutter 的目标是通过为 [Atom](https://atom.io/packages/flutter) 和 [IntelliJ](https://flutter.io/intellij-ide/) 提供插件和工具，让你的生活尽可能的简单。首先，[安装 Dart.js](https://www.dartlang.org/install/) ，然后 [Flutter](https://flutter.io/setup/) ，然后[阅读项目指南](https://flutter.io/getting-started/)开始你的第一个项目。

## 迅速发生的

Swift 起源于苹果，现在是许多 iOS 开发者的首选语言，那么你为什么会考虑将它用于 Android 开发呢？该语言于 2015 年 12 月开源，并获得了坚实的 Linux 支持，因此开发人员想知道移植到具有 Linux 内核风格核心的 Android 上的可能性有多大。

Swift 项目中的拉取请求和文档以及大量的博客帖子激起了人们对这一潜力的兴奋。Swift 知识库中有一份[文件，它涉及到使用 NDK 将 Swift 代码编译成 Android 可以理解的字节码的传统步骤。通读该文档，您会发现有许多复杂和具体的步骤，并且根据我过去的经验，谷歌对 NDK 的更改经常会破坏这一过程。即使你真的做到了这一点，你还是会被一个不能与任何 Android 库交互的应用所困。](https://github.com/apple/swift/blob/master/docs/Android.md)

尽管这一过程令人担忧，但开发人员仍决心找到一种让 Swift 在 Android 上工作的方法，我建议您阅读[这一系列博客文章](https://medium.com/@ephemer/why-we-put-an-app-in-the-android-play-store-using-swift-96ac87c88dfc#.ngdv1oyco)，它们来自一群将 Swift 应用程序放入 Google play 商店的开发人员。

### 元素编译器

RemObjects 使 [elements](http://www.elementscompiler.com/) 系列工具成为 Xamarin 和 JetBrains 产品的混合体。他们有一套工具，允许你用多种语言开发跨平台的目标。从 Android 的角度来看，你可以用[银](http://www.elementscompiler.com/elements/silver/)来写 Swift(的确是的！)访问本机 API。IDE 不像其他解决方案那样完美，我有过让它工作的混合经历，但是如果它对你有用，它是一个令人信服的选择。

## 锈

Rust 源于 Mozilla，在过去的几年里越来越受欢迎(在 Stack Overflow 开发者调查中，它获得了 2016 年最受欢迎的编程语言)。它是为更复杂的系统同时运行多个进程而设计的，主要不适合移动应用程序，但这并没有阻止人们的尝试。

由于 Rust 是一种编译语言，让您的 Rust 应用程序在 Android 上运行与本文中的其他选项类似，但这条路径不太常用。我发现这个项目和指令[在这里](https://github.com/tomaka/android-rs-glue)是最有用的，这个过程对任何尝试过让其他编译语言在 Android 上运行的人来说都是熟悉的。

## 无咖啡因的未来

谁知道谷歌(和其他公司)对 Android 开发的未来有什么想法，但是在阅读了这篇文章和一些实验之后，我希望你们都为未来做好了更好的准备。

下面是我们为帮助你开始准备而写的教程！

*   [使用 Xamarin 表单构建跨平台 Android 和 iOS 用户界面](https://www.sitepoint.com/build-cross-platform-android-ios-uis-xamarin-forms/)
*   [使用 Kotlin 简化 Android Java 代码](https://www.sitepoint.com/streamline-android-java-code-with-kotlin/)
*   [iOS 和 Android 编程用 Go](https://www.sitepoint.com/mobile/ios/)
*   [Swift 简介](https://www.sitepoint.com/premium/courses/introduction-to-swift-2907)
*   有一点锈的 Ruby 可以更快

## 分享这篇文章