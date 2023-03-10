# 原生与混合应用程序开发

> 原文：<https://www.sitepoint.com/native-vs-hybrid-app-development/>

人们喜欢使用智能手机，因为他们可以在旅途中完成许多事情，如阅读电子邮件、社交网络、看电影和许多其他活动。我们喜欢智能手机，因为它们有许多应用程序，使日常活动变得更容易。

如果您正在考虑开发移动应用程序市场，一个重要的决定是决定是开发本地应用程序还是混合应用程序。

这是一个老问题，一直没有解决，我们认为是时候重新审视… **哪个是最好的？**

## 原生优于杂种

构建原生应用程序意味着使用平台的原生语言，在 iOS 上使用 Objective-C，在 Android 上使用 Java。原生应用的主要优势是它们的性能。原生应用程序被编译成机器码(Android 下的 Dalvik 字节码)，它可以提供您从手机中获得的最佳性能。

最佳性能包括快速流畅的动画，以及对手机硬件的完全访问，多点触摸支持和最新的 API。

原生开发远非易事。尽管可以找到大量的资源，但并不是每个人都能理解。由于必须为每个平台专门编写代码，相同的代码将不得不大量重写，很少能够被共享。逻辑可能是相同的，但是语言、API 和开发过程是不同的。对于复杂的应用程序，这个过程可能相对较长。

## 走向本土化

如果您是移动开发新手，并且想要构建性能关键的移动应用程序和/或利用本机 API，您将需要一个学习移动本机开发的好资源。

我们以 iOS 为例。如果你想成为一名原生的 iOS 开发者，首先给自己弄一台 Mac。你可以在任何地方编写代码，但需要一台 Mac 电脑才能将代码嵌入到应用程序中，iOS 开发者账户也是如此(每年 99 美元)。

通过在你的浏览器中创建你自己的 [Flappy Bird](https://www.makegameswith.us/build-an-ios-game-in-your-browser/) 游戏**，你可以很好地了解 Objective-C。虽然单一的在线教程并不能完全解决问题，但苹果提供了自己的[教程](https://developer.apple.com/library/iOS/referencelibrary/GettingStarted/RoadMapiOS/index.html)，这对初学者和有经验的开发者都很有帮助。本教程介绍了构建 ToDoList 应用程序时的应用程序设计、结构和代码实现移动开发的应用程序)。**

至于 Android 开发，我会推荐奥莱利的[学习 Android，第二版](http://shop.oreilly.com/product/0636920023456.do)。在构建一个类似 Twitter 的移动客户端时，它很好地洞察了 Android 开发。

当然，SitePoint 还有大量的 [iOS](https://www.sitepoint.com/mobile/ios/) 和 [Android](https://www.sitepoint.com/mobile/android/) 开发文章供您欣赏！

## 杂交优于原生

混合应用是原生浏览器中的 web 应用(或网页)，比如 iOS 中的`UIWebView`和 Android 中的`WebView`(不是 Safari 或 Chrome)。混合应用程序使用 HTML、CSS 和 Javascript 开发，然后使用像 [Cordova](http://cordova.apache.org/) 这样的平台包装在原生应用程序中。这允许你使用任何你想要的 web 原生框架，并且有很多这样的框架。

应用程序开发更快、**更简单、**、更**快速、**，应用程序更容易维护。你可以在任何你需要的时候改变平台，Cordova 让你只需要增加一行代码就可以为多个平台构建你的应用程序。至于手机硬件，如相机或蓝牙，Cordova 有一个大的[插件库](http://plugins.cordova.io/#/)供你使用。

混合应用的主要问题是它们仍然依赖于原生浏览器，这意味着它们没有原生应用快。

## 走向混合动力

如果您决定开发混合应用程序，那么您应该知道在这个领域有两个主要的“竞争对手”。一个是 [Cordova](http://cordova.apache.org/) (以及 PhoneGap 等基于 Cordova 的工具)，另一个是 [Appcelerator Titanium](http://www.appcelerator.com/titanium/) 。它们都以移动平台为目标，但工作方式截然不同。

用 Cordova 开发就像开发网页一样。您创建 HTML、CSS 和 JavaScript 本地文件，在浏览器中测试它们，然后用 Cordova 将它们包装在原生 web 视图中(这一步仍然需要原生 SDK 和开发工具)。

使用 Titanium 有一点不同，你不需要任何 HTML 和 CSS 文件，除非你想创建一个同时使用本地和基于 HTML 的用户界面的应用程序。Titanium 提供了一个非常有用的移动工具集，可以帮助您在真实的平台上模拟(或仿真)您的应用程序，而不是在浏览器中。当你的应用在设备上运行时，它不会被打包到网络视图中，而是由 Javascript 引擎解释(iOS 中的`JavaScriptCore`或 Android 中的`Rhino`)。

Appcelerator 提供了一个很好的[教程](http://docs.appcelerator.com/titanium/latest/#!/guide/Creating_Your_First_Titanium_App)(令人惊讶的是不是一个 ToDo 应用)。

还有其他几个不太为人所知的混合动力开发选项，如 [Xamarin](http://xamarin.com/) 、 [Rho](http://rhomobile.com/) 、 [Corona](http://coronalabs.com/) 和 [MoSync](http://www.mosync.com/) 。所有这些都以略微不同的方式工作，根据您当前的编程经验，可能对您更有好处。

## 结论

原生和混合都是满足用户和开发人员不同需求和偏好的方法，它们都不是完美的解决方案。它们各有优缺点，由您来决定哪一种更适合您，哪一种将用于您的应用程序中。

*你对混合与原生开发有什么经验和想法？*

## 分享这篇文章