# 使用应用中心为您的 iOS 应用提供持续学习

> 原文：<https://www.sitepoint.com/continuous-learning-ios-app-using-app-center/>

*本文是与[微软](https://www.visualstudio.com/app-center/)合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

应用程序开发是一个迭代的过程，你在应用程序版本 2 上所做的工作很大程度上是基于你从构建和发布版本 1 中学到的东西。为了让你的开发工作集中在正确的方向上，它们必须是*数据驱动的*:你的应用程序的使用数据，而不是你的个人直觉，应该通知你选择你决定投入有限的时间和精力的功能和缺陷。

Microsoft Visual Studio 应用中心的持续监控和学习功能是帮助您了解应用用户如何使用您的应用的最佳工具，将您所有的分析和崩溃报告需求无缝集中在一个位置，*支持您可能需要的任何平台和框架，包括 iOS、Android、Windows、macOS、Xamarin 和 React Native* 。

在本教程中，我们将看到它为您提供的不同的强大工具，以了解您的用户在 iOS 版本的应用程序上的行为。让我们开始吧！

**注意:**你的 app 必须针对 iOS 8.0 或以上版本。你还需要 Xcode 8+和 [CocoaPods](https://cocoapods.org/) 。如果你按照本教程将 App Center SDK 集成到现有的应用程序中，就不能使用任何其他的崩溃报告库。

## 在应用中心门户中创建您的应用

*(如果您已经在 App Center 中创建了应用，请跳过这一步。)*

App Center 需要了解您的应用程序，以便接收和处理其使用数据，因此我们将在 App Center 中为您的应用程序创建一个记录(与我们在 iTunes Connect 中的操作非常相似)。

1.  访问 [appcenter.ms](https://appcenter.ms/) 。
2.  注册一个新帐户或登录 GitHub、微软、脸书或谷歌。
3.  进入后，点击**添加新应用**，选择 iOS 作为操作系统，Objective-C/Swift 作为平台。
4.  现在，应用程序已在应用程序中心创建，点击它，然后点击它的**设置**部分。
5.  在屏幕右上方注明你的应用程序的应用程序秘密，你可能会需要它。

## 添加 App Center SDK

*(如果您还没有将 CocoaPods 集成到您的 Xcode 项目中，请关闭 Xcode 并在您的项目目录中运行`pod init`)*

App Center 具有模块化架构，这意味着您可以集成您感兴趣的功能。

1.  Open your project’s *Podfile* and add this dependency:

    ```
    pod 'AppCenter' 
    ```

    该行将应用中心分析和崩溃报告集成到您的应用中，因为本教程将涵盖这两者。如果您希望只指定其中一个服务，您可以根据需要添加`pod 'AppCenter/Analytics'`或`pod 'AppCenter/Crashes'`。

2.  Run `pod install`:

    ```
    $ pod install
    Analyzing dependencies
    Downloading dependencies
    Installing AppCenter (1.0.1)
    Generating Pods project
    Integrating client project

    [!] Please close any current Xcode sessions and use `MyAppName.xcworkspace` for this project from now on.
    Sending stats
    Pod installation complete! There is 1 dependency from the Podfile and 1 total pod installed. 
    ```

    如果 CocoaPods 抱怨找不到 AppCenter 的规范，那么运行`pod repo update`。

3.  打开你的应用程序的 *AppDelegate.swift* 文件(如果你刚刚集成了 CocoaPods，确保你打开了。xcworkspace 文件，而不是。xcodeproj，并在顶部添加这些行:

    ```
    import AppCenter
    import AppCenterAnalytics
    import AppCenterCrashes 
    ```

4.  Look for the `didFinishLaunchingWithOptions` method and add this line:

    ```
    MSAppCenter.start("{App Secret}", withServices: [MSAnalytics.self, MSCrashes.self]) 
    ```

    将`{App Secret}`替换为*你的*应用的应用秘密，这是你之前记下的。

这就是将 App Center 集成到您的应用程序中所需要做的一切。

## 应用中心分析

我们现在将快进到未来。你的用户使用你的应用已经有一段时间了，应用中心收集并处理了使用数据。让我们来看看应用中心报告了什么:

## 概览页面

![analytics_canned_combo](img/603c58a10293bcb5d8d51d26c6b9ba00.png)

这个控制面板让你一眼就能看到关于你的应用程序用户的最重要的数据。

**活跃用户**部分显示了在过去的一天、一周(7 个日历日)或一个月(30 个日历日)有多少独立应用程序安装启动了该应用程序。请注意，您有一个统计数据的图形表示，以及数字摘要。因此，在我们上面的例子中，图表似乎显示，在整个 9 月和 11 月，每日用户数保持不变，但事实上，该数字增加了 136.8%。您可以通过将鼠标悬停在线上并从弹出窗口中读取每个时间点的数字来验证这一点。

**每位用户的每日会话数**和**会话持续时间**为您提供用户在您的应用中进行了多少*会话*及其持续时间的信息。App Center 将“会话”定义为“从应用程序进入前台到进入后台之前的最后一个事件的时间”。

概览页面的其他三个部分为您提供了重要数据，包括您的用户将设备设置为的语言、他们使用应用程序的国家以及他们使用的设备和操作系统。这些数据有助于您回答一些重要问题，如您的用户是否正在采用您最新的应用程序版本，您的应用程序是否需要新的本地化，以及在开发过程中您通常可以安全地使用哪些设备功能。

## 自定义事件

分析的一个强大功能是跟踪应用程序特有的特定事件。这些通常(但不一定)是由用户触发的事件。例如，您可能想知道用户何时点击特定按钮。你可能还想知道更多关于事件发生的背景。

为了跟踪一个特定的事件，以这种方式使用`trackEvent:withProperties`方法:

```
MSAnalytics.trackEvent("Viewed Product", withProperties: ["ID": "23432", "Name": "Bacon Burger"]) 
```

*确保将`import AppCenterAnalytics`添加到任何调用该方法的源代码文件的顶部！*

### 字符串类型的警告

App Center SDK 根据您赋予事件的字符串来区分事件。在上面的例子中，事件是`"Viewed Product"`，但是注意这是一个不同于`"Product Viewed"`的事件。如果您要从代码中的多个位置跟踪同一个事件，最好将这些事件字符串定义为常量。

方法之一是通过一个简单的`AnalyticsConstants`类:

```
class AnalyticsConstants {
    static let ViewedProduct = "Viewed Product"
    static let SuccessfulOrder = "Order Successful"
    // ... etc
} 
```

你可以这样用:

```
MSAnalytics.trackEvent(AnalyticsConstants.ViewedProduct, withProperties: ["ID": "23432", "Name": "Bacon Burger"]) 
```

你还可以更进一步，在`AnalyticsConstants`中定义一个`trackProductView`方法来包装整个`MSAnalytics.trackEvent`方法调用。

## 在 App Center 中查看事件

在运行时调用该方法后不久，您将在 App Center 中看到显示的事件:

![event](img/0119b9ef054f15fc78c7cd171d77910f.png)

App Center 会跟踪特定事件被触发的次数，如果同一事件具有不同的上下文值，则会对其进行区分。

**趋势**是指与之前的时间范围相比，计数发生了多大的变化。在这种情况下，时间范围是“过去 30 天”(显示在屏幕的右上角)，因为我们刚刚第一次触发了事件，而在过去 30 天内从未触发过，所以趋势是+1。

我们还可以在我们的应用程序中看到所有活动的大图:

![analytsics_custom_events](img/365f7a5f951190712030bc45d3bac4cc.png)

这个概述将告诉您一个事件被触发了多少次，以及触发它的用户数量。与**趋势**，**用户变化**类似，是指触发事件的用户数量与上一个时间范围相比发生了多大的变化。**每用户**指用户平均触发该事件的次数。

## 导出到 Azure

App Center 让你更进一步。如果您想要从数据中获得更精细的见解，或者构建自己的自定义分析仪表板，应用中心允许您将所有分析数据导出到 Microsoft Azure 中。

微软 Azure 是一个企业级云计算平台，带有 **[应用洞察](https://azure.microsoft.com/en-us/services/application-insights/)** ，这是一个更强大的应用性能管理和监控解决方案。其功能包括详细的仪表盘和使用机器学习的性能监控解决方案。

要了解可能的结果，在应用中心，点击**设置**，然后点击**导出**。

## 应用中心崩溃报告

可悲的是，有时候你的应用程序并不像预期的那样运行，而且……它崩溃了。崩溃报告的全部目的是确保你知道你的应用程序何时崩溃，收集关于崩溃的数据，并最终修复其根本原因，以便它不会再次发生。应用中心的崩溃报告功能让你做到这一点。让我们来看看吧。

为了让崩溃报告功能正常工作，请确保您的应用程序没有使用任何其他崩溃报告库。

## 设置

1.  在 Xcode 中，选择你的 iOS 设备作为构建目标，然后执行*Project->Archive*。组织者应在成功完成后打开。
2.  在管理器的中，右键单击存档并选择**在 Finder** 中显示。
3.  右键单击。xcarchive 并选择**显示包内容**。
4.  You’ll find the dSYM in the dSYM directory:

    ![dsym](img/cf1a7bac000b2471f33774256afde9c9.png)

5.  点击右键，选择**压缩…**
6.  在应用中心的左栏，选择**崩溃**，然后选择**符号**，然后点击右上角的**上传符号**。
7.  上传您刚刚创建的 zip 文件。
8.  每当您的应用崩溃时，应用中心都会显示一份报告:

![](img/a5a919200f080bc1a90cfb078d3559f3.png)

请注意，有时会在应用程序打开的下一个时间*报告崩溃。*

## 崩溃数据概述

应用中心将为您提供应用中所有崩溃的概述。您将看到每天有多少用户经历崩溃，以及您的安装有多少总体崩溃。

![](img/a76bfab80bebc91082dc5cd5f3606146.png)

每当两个或更多崩溃报告共享一个根本原因时，App Center 将对它们进行分组:

![](img/8278cbfdcac8f59322bd35be827cd9c0.png)

您将看到堆栈跟踪的详细信息，以及哪些设备和操作系统受影响最大。

## 将应用中心崩溃与你的 bug 追踪器集成

我最喜欢的 App Center 特性之一是它提供了与你的团队正在使用的 bug 追踪器的紧密集成。App Center 与 Visual Studio Team Services 以及 GitHub 一起工作，我们现在将关注如何将 App Center 与后者集成。

1.  在应用中心的左侧栏中，点击**设置**，然后选择**服务**
2.  选择 **GitHub** 并授权 App Center 访问您的 GitHub 存储库。
3.  选择与您的应用程序对应的 GitHub 存储库。您可以使用屏幕右上角的文本字段来过滤列表。
4.  应用中心会自动为你的应用注册的每个崩溃实例创建一个 GitHub 问题。换句话说，同一个崩溃不会被报告超过一次。

![](img/692de9bfb8572d43381b5bbb4a117ab5.png)

## 这是一个总结！

你不能控制你不能测量的东西，你的 app 也不例外。正如我们所看到的，App Center 为您提供了可靠的工具来衡量您的应用程序在测试人员和最终用户手中的性能。它很容易集成，并且它为您提供了开箱即用的应用使用和用户的详细细分。定义您自己的自定义事件允许您测量对您重要的操作，崩溃报告功能帮助您在崩溃作为 GitHub 问题出现时立即了解并修复它！

我希望你喜欢使用最好的工具来了解你的用户和你的应用程序的健康状况。

## 分享这篇文章