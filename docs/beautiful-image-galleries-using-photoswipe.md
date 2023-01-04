# 使用照片擦拭的美丽图库

> 原文：<https://www.sitepoint.com/beautiful-image-galleries-using-photoswipe/>

PhotoSwipe 是一个开源库，它为您的移动网站访问者提供了一种近乎“原生的”基于手势的图像浏览体验，这与 iOS 设备预捆绑的图像查看器应用程序没有什么不同。在本文中，我将快速浏览一下如何开始使用 PhotoSwipe，并提供一些更深入的技术提示和技巧，这些都是我在开发这个库的过程中学到的。

在我们走得太远之前，如果你还没有看到 PhotoSwipe 的运行，那就一起去看一下 [PhotoSwipe 演示](http://www.photoswipe.com/latest/examples/04-jquery-mobile.html "PhotoSwipe Demo")。你回来时我们会等你的。

### 入门指南

开始使用 PhotoSwipe 很简单。访问 [PhotoSwipe](http://www.photoswipe.com) 并下载最新版本。

解压后，你会发现一个“示例”文件夹，其中包含许多 HTML 文件，向你展示了在你的手机网站上实现图片擦除的各种方法。您将找到许多场景的示例，包括基本设置、与 jQuery Mobile 集成以及定制它以适应您自己的需求和个人品味。查看这些例子无疑是开始使用 PhotoSwipe 的最佳方式。

### 支持哪些设备？

在撰写本文时，PhotoSwipe 可以在 iOS 设备、Android 以及黑莓 6 上运行，并且在 Windows Phone 7 Mango 上以有限但完美的方式使用。

此外，PhotoSwipe 在桌面浏览器上也能正常工作。它在 Firefox 上运行得很棒，而且由于这个库是 WebKit 优化的，它在 Chrome 和 Safari 上运行得一样好。最后，在 jQuery 的一点帮助下，PhotoSwipe 在 Internet Explorer 7 及以上版本上运行。

### 源代码、支持和参与

当然，PhotoSwipe 在麻省理工学院的许可下是开源的。你可以随意处置它！

如果你想进一步参与，或者需要支持你在库上发现的问题，你可以在 GitHub 上找到[computer love](https://github.com/codecomputerlove/)和 [PhotoSwipte Repo](http://github.com/codecomputerlove/PhotoSwipe) ，在那里你可以记录问题并提出请求。你也可以在 Twitter 上关注 [PhotoSwipe，了解最新的发布公告。](http://twitter.com/#!/PhotoSwipe)

### 变得技术化

在本文的其余部分，我将讨论 photo wipe 的一些基本原理，以及在编写自己的移动网站 JavaScript 库时可能会有所帮助的一些提示和技巧，而不是讨论所有各种各样的配置设置和实现 photo wipe 的方法，这些都在网站上有很好的记录，并在示例中进行了演示。

### 放开 jQuery，拥抱 jQuery

PhotoSwipe 是一个专为移动设备设计的 JavaScript 库。即使是高端设备，在处理能力和速度方面，台式机和移动设备之间仍然存在巨大差异。将移动设备作为主要平台进行设计，这带来了以下项目限制:

*   该库需要尽可能小，以便下载。不能保证移动网站的访问者在 WiFi 上很受欢迎，事实上更有可能是断断续续的 3G 连接或更低的连接。
*   该库需要尽可能精简，只包含移动浏览器所需的代码和逻辑。任何其他事情都会被认为是设备的膨胀和不必要的工作。

让事情变得棘手的是，我们还想支持桌面浏览器，尤其是 Internet Explorer，并且我们还想确保 PhotoSwipe 与移动网络图书馆的后起之秀 jQuery Mobile 配合良好。

为了解决这些限制，我们采用了界面导向的方法。在这里，我们同意了一个核心库接口(或定义的核心函数列表)，它将完成 PhotoSwipe 所需的底层辅助工作，如创建 DOM 元素、操纵样式、添加和监听事件、动画等。

一旦我们定义了这个核心库，我们就编写了一个针对 WebKit 浏览器的实现，并使用这些浏览器支持的内置 DOM 标准功能。这极大地解放了我们。我们不必担心对 IE 或其他浏览器的支持，这不是我们主要关心的。最终，这意味着我们不必使用 jQuery。

这是一个重大的决定！我喜欢 jQuery，它是流行的 JavaScript 开发中的一个常量，离开它感觉很奇怪。然而，这一决定是合理的:

*   首先，PhotoSwipe 的移动/标准优化版本将在核心库的实现上运行，该实现不包括 jQuery 为使跨平台 JavaScript 尽可能无缝而执行的大量不必要的变通方法。最终，这意味着移动设备要做的工作更少了。
*   其次，它让移动用户不必下载 jQuery 库，而理论上，PhotoSwipe 实际上只需要或使用其中的一小部分。

然后，在这个移动/标准优化实现之上，开发并测试了主要的 PhotoSwipe 代码。本质上，PhotoSwipe 并不关心核心库是如何实现的。它所需要知道的就是核心库支持所需的函数，比如`setStyle()`。

在这个阶段，这个世界一切都很好。然而，该项目的主要任务是支持 Internet Explorer 和 jQuery Mobile。

这个问题相对容易解决。我们只是开发了核心库的 jQuery 实现。这意味着，在相对较短的时间内，我们用最少的努力就让 PhotoSwipe 在 IE 上运行得非常好，在 jQuery Mobile 上也运行得非常好！

这种方法也意味着，如果我们需要，我们可以在其他 JavaScript 库中编写核心库实现，比如 Prototype、YUI、Dojo、MooTools、Zepto 等等。这有助于为实现 PhotoSwipe 的开发人员提供尽可能多的灵活性。

### 手机浏览器很疯狂

这一点在开发 PhotoSwipe 的早期就变得很明显。最初，瞄准 WebKit 似乎是一条简单的路线。iOS、Android 和黑莓都是基于 WebKit 的网络浏览器。乐观地说，可以预见的是，仅仅这个事实就可以消除许多潜在的跨平台问题。

事实并非如此。手机浏览器即使基于同一个核心渲染引擎也不尽相同。

在开发移动 JavaScript 组件时，这一点需要记住。不同操作系统的 WebKit 版本不同，浏览器使用的 JavaScript 解释器也不同。使问题更加复杂的是，同一操作系统的不同版本之间的浏览器有所不同。事实上，在早期版本的操作系统上运行良好的东西在新版本上崩溃了。这在 Android 从 1.5 升级到 2.1，以及 iOS 从 iPhone 升级到 iPad 时尤为明显。

这里要记住的主要事情是，你需要一套运行不同操作系统和不同版本操作系统的测试设备/模拟器。不要依赖于他们本质上都在运行同一个浏览器的事实。对于 Android 和 Blackberry，这相对简单，因为你可以根据需要通过官方模拟器运行多个版本。对于 iOS，让一些设备运行不同版本的 iOS 会很有帮助。

### 调试和获得帮助

为移动网络浏览器开发的另一个问题是缺乏像样的调试工具。这些浏览器没有像 Firefox 的 Firebug 或 Chrome 和 Safari 捆绑的 Web Inspector 这样的工具。甚至像获取基本的控制台日志这样的事情也会很麻烦。

总的来说，这并没有引起太大的问题。大部分开发是在桌面上的 Chrome 和 Firefox 中进行的，并在移动设备和模拟器上进行持续的完整性检查。这意味着我们可以使用 Firebug 和 Web Inspector 进行调试。

然而，有时移动浏览器的错误和怪癖意味着在桌面浏览器上调试是没有用的。你需要知道移动设备本身到底发生了什么。为此，至少 iOS 附带了一个开发人员控制台，因此您可以执行一些基本的跟踪。然而，这有点打扰，并占用宝贵的屏幕空间。此外，这在 Android 等其他设备上不可用。

幸运的是有帮助。如果你还没有，我推荐你看看 PhoneGap 的人开发的 Weinre 项目。这个项目允许你通过运行在桌面浏览器上的调试工具来远程调试移动网络浏览器上发生的事情！

该工具非常类似于 Firebug 和 Web Inspector，当您在移动 Web 浏览器中遇到一些奇怪的事情时，它可以为您节省大量时间。Weinre 可以免费下载，设置简单，可以在 Windows 或 Mac 上运行。或者，有一个在线版本可以让你不用安装任何东西！这可以在[http://debug.phonegap.com](http://debug.phonegap.com/)找到。

### 充分利用硬件加速

最后一个技巧是利用这些设备上的硬件加速，尤其是 iOS 设备。开发硬件对于实现“类似本地”的流畅动画和交互是至关重要的。

这真的很简单，有大量的文章介绍如何做到这一点。然而，基本要点是:

*   尽可能使用 CSS3 转场来实现动画，并避免基于 JavaScript 间隔的动画。通过这样做，你会发现速度有了很大的提高。
*   最后一个杀手级技巧，尤其是对于 iOS 设备，是在你制作动画的 HTML 元素上，如果你应用“-WebKit-transform:translate 3d(0px，0px，0px)；”这将触发 CSS3 动画被硬件加速。它可以很好地修复你在动画中发现的任何闪烁。然而，值得记住的是要有选择性，因为过度使用会产生负面影响。不应用这个先试试你的动画，看看你是不是真的需要。

### 包扎

PhotoSwipe 是免费的！来吧，[下载 PhotoSwipe](http://github.com/downloads/codecomputerlove/PhotoSwipe/code.photoswipe-2.1.6.zip) ，玩一玩，为您的手机网站添加一些精彩内容。如果你喜欢它，帮助传播这个词或者加入我们的 GitHub，为这个项目的未来做出贡献。

希望这篇文章给了你一些有用的技巧，让你在开发自己的基于移动设备的 JavaScript 库时可以使用，并且会启发你编写一些自己的非常棒的移动库。请在评论中告诉我们你创造了哪些令人敬畏的东西！

## 分享这篇文章