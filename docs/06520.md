# 构建 Windows 应用程序:基本资源

> 原文：<https://www.sitepoint.com/building-a-windows-app-essential-resources/>

对于微软的 Windows 部门来说，过去的一年并不是最成功的一年。不仅 Surface 的表现不如预期，迫使该公司降价并进行股票减记，甚至有人认为 Windows 8 是 PC 销量持续下降的部分原因。

这话有点刺耳。科技和金融界的常识是，平板设备的崛起对个人电脑造成了沉重打击。这种情况只会持续下去，因为消费者更喜欢购买移动设备，而移动设备在互联网流量中占很大比例。

好消息是，Windows 8.1 将在今年晚些时候向供应商提供，开发者已经可以获得[预览版](http://windows.microsoft.com/en-us/windows-8/preview)。那么，还有什么更好的时机来考虑开发一个 Windows 应用程序呢？

## 竞争较少的市场

Google Play 和 iTunes 可以为开发应用程序的开发者带来巨大的收入流。两者都非常有竞争力，因此，开发一个 Windows 应用程序可能会给你一个更好的机会从应用程序开发中赚钱。

可以使用各种不同的技能来构建 Windows 应用程序，包括:

*   跨平台工具
*   C#和 Visual Basic 与 XAML
*   c++与 XAML 或 DirectX
*   HTML5 和 CSS3 的 JavaScript。

如果你还没有 8.1 预览版，并计划开发一个 Windows 应用程序，一个好的开始是下载它，以及一个虚拟机，如 VMWare 的。下载 Visual Studio 2013 预览版也是一个好主意，它有一个工具集，旨在支持 Windows 8.1 应用程序。

这包括:

*   改进了与 Windows 应用商店的集成
*   控件和模板
*   用户界面响应分析器
*   面向 XAML 和 HTML 应用的能耗分析器
*   增强的内存分析工具

下载用于 HTML、CSS 和 JavaScript 编程应用的 [SDK](http://msdn.microsoft.com/en-us/windows/apps/hh852650#Powerful_tools_and_all_the_resources_you_need) 和[开发指南](file:///C:/Users/Ricky/AppData/Local/Microsoft/Windows/Temporary%20Internet%20Files/Content.Outlook/X1TT1XS1/ligman.me/PnMWST) (PDF)也是明智的。除了 SDK 之外，您还会发现微软提供的许多旨在帮助开发和测试过程的工具，以及一个模板库和 Windows 运行时 API。

在 [Windows 开发中心](http://msdn.microsoft.com/en-us/library/windows/apps/hh700403.aspx)中，您还可以找到 Windows 应用的 PSD 设计资产。

## **规划 App**

和往常一样，规划阶段对于制作一个有效的应用程序很重要，所以确保你从草图、内容、目标受众和你希望应用程序实际做什么开始。看看市场，找出差距(Windows 应用商店里有很多)，首先考虑可用性和响应性。

这比创建一个需要用户执行大量任务的只会唱歌会跳舞的应用程序更重要。

## 资源和工具

Windows 开发人员网站确实是一个很好的资源，在这里您可以找到大量的教程、模板和工具。对于 XAML 应用， [XAML 间谍](http://xamlspy.com/)最近发布了一个针对 8.1 预览版的更新，允许你在运行时检查视觉元素。

这是一个很好的工具，可以在开发过程中进一步使用，因为它对测试网格覆盖的对齐很有用。值得指出的是，这是要付费的，尽管你最初可以注册 21 天的免费试用。之后，专业许可证的价格为 119 美元，个人许可证为 79 美元。

为 Windows 8.1 开发的问题之一是资源仍然相当稀少，所以值得看看 Windows Phone 和 8 的现有资源，如 [Telerik](http://www.telerik.com/developer-productivity-tools.aspx) 。

在这里，您可以找到使用开发的工具。NET，其中包括 350 多个 UI 控件和微软平台的报告，以及用于编码和调试的调试工具。它可以用于 web、移动或桌面应用程序(可能还有平板电脑，尽管没有指定)。

同样，这不是一个免费的资源，所以你应该在注册前检查价格选项。

## 可缩放的瓷砖

Windows 8 的开发应该考虑到触摸，以及该应用程序可能在许多不同大小的设备上使用的事实。这意味着瓷砖是使用图像创建的，这些应该是完全可扩展的。

这可以通过各种工具来实现，例如 [Inkscape](http://inkscape.org/) ，它允许在应用程序中使用矢量路径，可以自动缩放。这是一个开源资源，有一个非常有用的“保存到 XAML”工具。

PerfecTile 是另一个有用的工具，可以制作更宽的瓷砖，也可以缩放，你可以在 MSDN 网站[上找到一个瓷砖模板库](http://msdn.microsoft.com/en-us/library/windows/apps/hh761491.aspx)。然而，后者正在等待 8.1 的更新，但值得环顾四周并关注新的内容。

Json.Net**也非常有用，因为现在许多 API 都使用 JSON 作为传输格式，这是最容易使用的库之一。**

 **## RAM 和性能

Windows 应用程序一旦打开，就会在其他应用程序的后台运行，因此密切关注正在使用的内存量非常重要。这是因为占用资源的应用程序将被终止，因此，如果您使用 JavaScript，您可以使用异步调用来检索数据。

## 设计原则

微软建议推荐的应用程序设计应该“使用平衡、对称和层次来培养信任和正直感”，这可能会让许多人问:“嗯？”当涉及到设计时，这并没有给出任何明确的原则，但是你应该记住以下几点。

*   **保持简单**–应用程序通常被交付到更小的屏幕上，因此一个整洁的应用程序会更成功，并牢牢记住用户体验
*   保持触控功能并确保当用户触摸屏幕时，应用程序能迅速做出反应
*   **动画**–SDK 为此提供了一个库
*   **可扩展性**是必须的，在许多设备上测试应用程序以确保图像正确缩放而不像素化是至关重要的
*   开始屏幕标题很重要，因为这是潜在用户看到的第一样东西，所以要有吸引力

总而言之，有许多工具可以帮助你入门，其中许多可以在微软找到。如果你不想试用 Visual Basic 2013，那么 2012 版可以免费下载，尽管它可能没有那么多可以用于 8.1 应用的工具。

接下来就是使用 Windows 应用认证工具(SDK 自带)测试应用，建立一个帐户以获得支付并[提交给 Windows 商店](http://msdn.microsoft.com/en-us/library/windows/apps/br230835.aspx)。

祝你好运！

## 分享这篇文章**