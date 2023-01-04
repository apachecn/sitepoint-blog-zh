# 在本机应用程序中设计 Web 代码

> 原文：<https://www.sitepoint.com/designing-web-code-native-app/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

这些年来，我听到开发者说，“你总能判断出它是一个 web 应用”，暗示 web 应用与本地应用相比有所欠缺。这些人会声称 web 应用程序性能差，设计差。虽然这在几年前可能是真的，但今天的网上交易会比过去的网上交易会好得多。我们的运行时间非常快，不会影响应用程序的性能。网络上的最佳用户体验比原生应用领先几代，响应式设计使网络应用在任何设备上都有很好的体验。

网络应用的好处还在继续。如果编写得好，任何拥有浏览器的人都可以与 web 应用程序进行交互，无论平台、版本或设备如何。它的单一代码库也使它成为那些不想或没有资源为每个平台构建和维护原生应用的开发者、品牌和公司的压倒性选择。

![Web app various screens](img/6eed097dc3d3c5afc0b0280dfd4ce0e4.png)

出色的网络应用就是出色的应用。无论是在浏览器中还是在应用商店中。

## 定义问题

每天我都和开发人员一起工作，他们使用 ManifoldJS 把优秀的网络应用转化成商店应用。ManifoldJS 让你通过构建托管的 web 应用程序来维护 web 的精神。托管 web 应用程序则不同，因为它们允许您的代码驻留在 web 服务器上，就像您在浏览器中访问它一样。这意味着代码库总是最新的，您可以随时部署更改，并且，通常，如果代码在浏览器中工作，那么它将作为托管的 web 应用程序工作。

优秀的 web 应用程序有很好的用户体验，一个被设计成可以在多种浏览器和设备上运行的 web 应用程序可以作为一个跨平台托管的 web 应用程序运行得很好。此类应用的一些关键特征是:

1.  **响应式布局。**无论屏幕大小或方向如何，该设计都能提供良好的阅读/观看体验。用户可以完成他们的目标，而无需向多个方向滚动或放大和缩小以与应用程序交互。
2.  大小合适的字体。你应该能够很容易地阅读屏幕上的字体。这是双向的——它应该足够大，可以在小屏幕上阅读，但不应该在大屏幕上大得离谱。平衡是关键。
3.  **灵活输入。**app 应支持触摸屏、键盘和鼠标；但是如果你想支持下一代界面，不要忘记像语音或手势这样的输入模式。
4.  快速交货。不是每个人都可以通过有线方式接入光纤连接和无限带宽。许多设备的带宽有限或处理器速度较慢。你的应用程序不应该花太长时间来加载或在交互过程中感觉迟钝。

考虑到所有这些，我最常被问到的问题是:*我应该如何设计我的应用程序？*(他们指的是审美方面的。)换句话说，视觉设计应该是什么样子？通常，选项有:

1.  为应用程序设计一个单一的界面，这样无论在什么平台上使用，它看起来都是一致的。
2.  为每个平台设计一个更加“原生”的界面，以体现其独特的外观和感觉。

我的回答始终如一:*我不知道*。幸运的是，我的队友[亚伦·古斯塔夫森](https://twitter.com/AaronGustafson)对此有一些想法:

视情况而定。

我完全理解拥有一个和本地操作系统一样(或相似)的界面的愿望。它为你的用户创造了一个“无缝”的体验，让他们更容易理解如何使用你的应用。也就是说，如果你的应用是直观的，没有理由认为它看起来像底层操作系统的事实会让它更好地为他们工作。此外，一个“原生”的外观和感觉不会让一个蹩脚的应用程序立即可用。

最重要的是，追逐原生操作系统的设计可能不是你想下去的兔子洞。原因是:在像本机控件和 web 控件这样简单的东西之间实现精确的设计和功能对等通常需要额外的标记、一堆 CSS 和一点 JavaScript。没有时间和预算的限制，任何事情都是可以实现的，所以它是完全可行的，但是最好估算一下成本，看看你是否仍然认为这是一项值得的努力。

假设是这样的话，那么你就有了在哪个操作系统之后为控件建模的问题。或者，您可能希望根据用户使用的操作系统提供不同的控件。在这种情况下，您可能需要将最初的估计乘以您想要支持的操作系统的数量。值得注意的是，至少在 Android 世界中，不同的设备制造商通常会“修饰”操作系统，使其看起来与其他制造商的 Android 设备不同。您将需要找出您想要将哪些包括在您的支持矩阵中，并相应地乘以估计值。

然后就是 QA 和维护。您需要在相应的平台上测试每个类似本机的控件。您还需要测试选择将哪种体验交付给哪种设备的脚本，以确保您不会意外地发送错误的体验。您还需要在测试矩阵中的每个其他浏览器上测试交付脚本，以确保它不会在那里引起问题。

接下来的问题是，当一个新的操作系统版本推出时，你需要做什么。例如，iOS 在每个主要版本中都对其原生控件的设计进行了彻底的改变。您可能希望为您支持的每个操作系统的每个版本创建每个控件的唯一版本。你还需要关注升级，这样你就不会让你的用户困惑，如果他们在 iOS 8 中访问我们的网站，并且有一个看起来像是来自 iOS 6 的控件。您还需要将想要支持的操作系统版本数量添加到乘数中。

您还需要为不属于您的矩阵的每个人提供一个基线组件外观和感觉。

最后:你想对多少控件再次应用这种方法？

或者你可以拥抱网络，使用原生的表单控件，这样界面就可以工作了。网络的无处不在就是它的力量。像响应式网页设计和渐进式改进这样的方法接受了这一点，并在扩大覆盖面和降低设计、开发和维护成本方面支付了巨大的红利。一次构建，随处部署——这是网络的承诺，也是一个伟大的网络应用能够成为一个令人敬畏的已安装网络应用的原因。

亚伦

## 托管很容易

毫不奇怪，我认为托管 web 应用程序是构建商店应用程序的好方法。关于如何将一个网络应用变成一个托管应用，请查看我在[ManifoldJS](http://www.thishereweb.com/manifoldjs-building-simple-hosted-web-apps/)上的博文；这很容易做到，你甚至可以使用 [ManifoldJS 网站](http://www.manifoldjs.com/generator/?WT.mc_id=13422-DEV-sitepoint-article46)在你的浏览器中生成你的应用。

托管应用通过平台 API 为你提供了在浏览器中不可能实现的新的有趣的机会。iOS 和 Android 应用程序可以配置为访问 Cordova APIs，如 media capture 和 contacts，对于 Windows 10，您可以访问所有这些 API 以及整个 [Windows 通用 API 集](https://msdn.microsoft.com/en-us/library/windows/desktop/ff818516(v=vs.85).aspx/?WT.mc_id=13422-DEV-sitepoint-article46)。只需对 API 进行特性检测，然后执行额外的 JavaScript，就可以将新特性添加到您的应用中。

把你的网络应用带到应用商店并不需要太多额外的工作。到了那里，你将享受到基于商店的发现和真正的原生应用体验。那么为什么要等呢？如果你有一个很棒的网络应用程序，[花五分钟，让它成为一个托管的网络应用程序](http://www.manifoldjs.com/?WT.mc_id=13422-DEV-sitepoint-article46)。

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13422-DEV-sitepoint-article46) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13422-DEV-sitepoint-article46)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article46&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article46&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article46&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article46&utm_campaign=SitePoint)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development)

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=13422-DEV-sitepoint-article46)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=13422-DEV-sitepoint-article46)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com/) 和 [babylonJS](http://babylonjs.com/) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=13422-DEV-sitepoint-article46)(来自律师奶爸和基里尔·赛克谢诺夫，包括[流形。JS](http://manifold.js.com/) 项目)

更多面向网络平台的免费跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13422-DEV-sitepoint-article46)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=13422-DEV-sitepoint-article46) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13422-DEV-sitepoint-article46)

## 分享这篇文章