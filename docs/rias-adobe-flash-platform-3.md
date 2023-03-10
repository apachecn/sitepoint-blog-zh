# 使用 Adobe Flash 平台构建 RIA

> 原文：<https://www.sitepoint.com/rias-adobe-flash-platform-3/>

****你以为自己认识的闪光灯变了。它长大了。****

 **在本文中，我将解释构成 Flash 平台的一些技术，它们是如何结合在一起的，以及如何使用它们来构建 RIA。注意，因为[最后会有一个小测验！](https://www.sitepoint.com/quiz/adobe/building-rias-with-flash-platform/)前 200 名提交答案的人将免费获得一本*Adobe Flash Platform action script Reference for Rich Internet Application Development*(这是印刷版)，感谢 Adobe。

Flash 经历的转变已经有一段时间了，许多开发人员都意识到了这一点。每天我都听说有开发人员为 Flash 运行时构建强大的新应用，执行连接到由 SAP 或 Oracle 支持的企业解决方案等任务，所有这些都得到这些供应商的全力支持。Flash 已经超越了我们过去讨厌的 *Skip Intro* 按钮，并且已经变得无处不在——帮助浏览器执行远远超出它最初为之构建的文本渲染。

是 Macromedia(早在 2002 年，在 Adobe 收购之前)创造了“富互联网应用程序”(RIA)这个术语。这个术语的引入与 Flash MX 的一组新的自定义组件的发布相一致，从而为 Flash 开发人员提供了一组 UI 组件，并为他们提供了构建自己的组件的方法。Flash 已经包含了在不强制页面刷新的情况下调用远程服务器的能力，但随着同年 ColdFusion MX 的发布，出现了 Flash Remoting，它允许用 Flash 构建的应用程序通过 AMF(动作消息格式)将序列化数据传递给服务器。

这两种产品——用于 Flash 和 Flash Remoting 的定制组件——以及将它们结合在一起所能体验到的协同作用，在它们首次发布以来的六年半时间里已经扩展了许多倍。最近，这些技术被称为 Adobe Flash 平台。这个总括术语描述了 Adobe 产品的集合，包括多个运行时、开发工具和服务器。

![Adobe Flash Platform](img/b8a358d59f7de0bf5b115a6a82de3388.png)

##### Flash 运行时

Flash Player 是 Adobe Flash 平台的基石；它为开发人员提供了一个单一的、跨平台的运行时，具有当今互联网的所有功能。它不仅仅支持矢量动画，它还是一个多媒体播放器。事实上，如今大多数在线视频都是通过 Flash 传输的，能够通过蓝光中使用的相同 H.264 编解码器提供真正的高清视频。

运行时可以通过 web 服务、HTTP 和远程对象调用与应用服务器通信，而无需执行浏览器刷新。此外，该播放器已经有许多代的 VoIP 能力，它可以使用 Flash 构建和部署协作应用程序，包括文本聊天、白板和跨操作系统共享桌面。

为了确保基于 Flash 的 RIA 能够在各种设备和操作系统上成功工作，已经投入了大量的精力。例如，Adobe 和 Google 最近宣布成功实现和开发了一个虚拟用户，Google 用它从 Flash 内容中检索文本。这使得内容能够出现在谷歌的搜索结果中。Adobe 不断改进 Flash Player 中的可访问性性能及其与针对视觉障碍者的屏幕阅读器的兼容性。深度链接和浏览器历史兼容性是 Flash 应用程序的开源 Flex 框架中的特性；这些旨在消除基于 Ajax 的 RIA 中动态内容遇到的导航问题。

Adobe 在推出 Flash 运行时产品的短短时间内，已经成功地使其成熟。每一次迭代，特别是玩家 9 和 10，性能都有显著的提高；Player 10 现在为高级图形效果提供硬件加速。Flash Player 10 还引入了新功能，包括 3D 效果、自定义滤镜和效果、高级文本支持以及用于提高视频性能的动态流。

Flash Player 10 是浏览器插件的最新版本；它适用于 Windows、OS X、Linux 和 Solaris。Flash Lite 3 是运行时的缩小版本，适用于移动和手持设备。

##### 抬头仰望天空！

然后是 Adobe AIR——一种新的运行时类别，它将 Flash Player 10 和 WebKit(Safari 和 Chrome 背后的网页渲染引擎)结合到一个独立的运行时中，能够运行离线应用程序。该运行时可用于 Windows、OS X 和 Linux。

Adobe AIR 是 Adobe 在经过漫长的公开测试周期后于 2008 年年中推出的。开发人员可以使用他们的 web 应用程序构建技能来创建桌面应用程序，并通过 AIR 部署它们。AIR 运行时合并了浏览器中缺少的功能，例如访问文件系统以创建、删除和维护文件和文件夹。

Adobe AIR 支持与操作系统的拖放交互、网络连接检测以及用于数据本地存储的 SQLite 后者可以很容易地用标准的 SQL 查询进行搜索。如果客户端计算机上安装了 Acrobat Reader，AIR 将利用该软件包的功能来呈现 pdf。Adobe 已经暗示了一个用于移动和手持设备的 AIR 运行时版本，尽管还没有宣布发布日期。

当然，[所有这些工具都可以免费下载](https://www.sitepoint.com/blog/)，Flash Player 作为 Windows 和 OS X 发行版的一部分捆绑在一起。

##### 区域一体化协定的开发工具

Flash CS4 Professional 是 Flash 创作环境的最新版本。最初是一个动画工具，Flash IDE 也可以用于使用 [ActionScript](http://www.adobe.com/devnet/actionscript/) 编写 Flash 电影，这是一种基于 [ECMAScript](http://en.wikipedia.org/wiki/ECMAScript) 的面向对象编程语言。它拥有创作 ActionScript 所需的工具，包括 ActionScript 编辑器和高级调试器。Flash CS4 既是设计者也是开发者的工具，虽然可以用 Flash 创建完整的应用程序，但 Adobe 已经为 Flash 应用程序创建了一个名为 Flex 的框架(查看 sitepoint.com 上的一些 [Flex 教程)。](https://www.sitepoint.com/web/flash-and-flex/)

Flex 最初是作为企业平台发布的(有与之匹配的企业价格)，现在是一个免费的开源框架，用于构建在 Flash 运行时中运行的应用程序。Flex 应用程序由两种语言组合而成:

MXML，一种基于 XML 的声明性语言，其中标签表示框架内的类；它主要用于 UI 布局和行为，以及 ActionScript 3，这是一种面向对象的语言，通常用于客户端逻辑。具有 Java 背景的开发人员会发现 ActionScript 非常直观，并且可能会完全用它来创作。

Flex 框架包含 100 多个可扩展的 UI 组件。它可以作为免费 Flex 3 SDK 的一部分免费下载，其中也包含一个独立的编译器。

![Flex Builder](img/1a7e8cb2ef74a2f231bfb097f6b00599.png)

出现了许多开源框架来帮助 Flex 应用程序开发。其中最流行的是由 Adobe 管理的开源项目 [Cairngorm](http://www.adobe.com/products/flex/flexdownloads/index.html) 。其他值得一看的包括 [Mate](http://mate.asfusion.com/) ，一个基于标签、事件驱动的框架；和 [PureMVC](http://puremvc.org/) ，一个轻量级的 ActionScript 3 框架，用于创建基于模型-视图-控制器概念的应用程序。

虽然 Flex 框架和 Flex SDK 是免费的，但 Flex 开发工具 Flex Builder 是一个商业产品([尽管它对教育用途是免费的](https://freeriatools.adobe.com/flex/))。Flex Builder 3 是一个基于 Eclipse 的 IDE，既可以作为独立的应用程序，也可以作为 Eclipse 的插件。有适用于 Windows 和 OS X 的 Flex Builder 版本，还有适用于 Linux 的测试版。

Flex Builder 支持编辑 MXML、ActionScript 和 CSS 文件，包括代码完成、标记洞察和语法着色。它还包括一个可视化布局工具，以方便组件在 WYSIWYG 环境中的定位。

[Flex Builder 有两个版本，标准版和专业版](http://www.adobe.com/products/flex/buy/)。专业版包括图表库、性能分析工具和对自动化功能测试的支持。使用 Flex Builder 构建的项目可以通过 Flash Player 在浏览器中部署，也可以通过 Adobe AIR 作为独立应用程序部署。

##### 服务器

Flash 应用程序能够通过 HTTP 调用从远程服务器检索数据；这可以用上面提到的任何一种 Adobe 工具来完成。在 Flash MX 中，Macromedia 引入了一种曾经专有的二进制数据传输格式，用于与应用服务器进行通信，称为动作消息格式(AMF)；这是最近被公开的源代码。AMF 从 [SOAP 协议](http://en.wikipedia.org/wiki/Simple_Object_Access_Protocol)中借用了描述数据结构的方法；ColdFusion 中包含的 Flash Remoting 类和服务提供了这种格式的自动转换。AMF 也已经过逆向工程，可用于许多应用服务器； [AMFPHP](http://www.amfphp.org/) 、 [WebORB](http://www.themidnightcoders.com/products/weborb-for-php/overview.html) 和 [RubyAMF](http://code.google.com/p/rubyamf/) 就是这种逆向工程的例子。

Adobe 现在也有一个开源的 Java remoting 和 web 消息技术，Flex 和 Adobe AIR 应用程序可以使用该技术连接到名为 [BlazeDS](http://opensource.adobe.com/wiki/display/blazeds/BlazeDS/) 的 Java 服务器逻辑。这使用了与上述相同的 AMF 协议。BlazeDS 集成最近被添加到了 Spring 框架中，使 Spring 驱动的 RIA 和 Flex 前端成为可能。

Adobe 有其他服务器产品也支持 alsoColdFusion 之前已经提到过，而另一个是 [LiveCycle 数据服务](http://www.adobe.com/products/livecycle/dataservices/)，它也包括用于实时数据推送的 Flex 消息传递和[发布/订阅消息传递](http://en.wikipedia.org/wiki/Publish/subscribe)。

Adobe Flash Media Server 产品组也包含在 Flash 平台中。Flash Media Interactive Server 可用于构建基于 Flash 的协作应用程序，其中包括流式 Flash 视频、VoIP、聊天、网络摄像头聊天和在线游戏等功能。Flash 流媒体服务器能够传输实时和点播 Flash 视频。

##### 不久的将来

Adobe 期待已久的技术预览版 Thermo 以新名称 [Flash Catalyst](http://labs.adobe.com/technologies/flashcatalyst/) 于 2008 年 11 月在 Adobe MAX 上向与会者发布。这是一个用于 RIAs 的交互设计工具，将在今年上半年作为公测版发布。Flash Catalyst 将导入在 Illustrator、Photoshop 或 Fireworks 中创建的设计构图，利用所有 CS4 设计工具中引入的新交换格式 FXG。

![Flash Catalyst](img/041b6c4af9e5c2ac68a415217dda6935.png)

Flash Catalyst 可以通过工作流将艺术作品中的设计元素转换为 RIA 项目的设计和交互元素；这使得像滚动条这样的交互式元素能够返回到 Illustrator 进行一些设计调整。Flash Catalyst 将按钮或滚动条等元素转换为 Flex 自定义组件。在 Flash Catalyst 中，交互和时间线事件以可视方式添加到项目中，但实际上，应用程序正在将整个项目创建为 MXML。就像 Flex Builder 一样，Flash Catalyst 是另一个基于 Eclipse 的应用程序。

Flash Catalyst 已经被列为 Flash 平台的一部分；它生产的 MXML 与 Flex 3 不兼容，但将与下一个版本 Flex 4(代号为 Gumbo)兼容，后者正在进行公开测试。Flex 4 具有图形增强功能，包括 FXG 的实现和可定制的组件。

![Flash Catalyst in Detail](img/9ee2c26df4b4659fce10cf1f89ae6bfe.png)

去年底在 Adobe 年度开发者大会 MAX 上发布的另一个与 Flash 相关的新项目是[Alchemy](http://labs.adobe.com/technologies/alchemy/)；这是一个研究项目，允许用户编译 C 和 C++代码在 Flash Player 10 上运行。有很多很好的例子说明了如何使用现有的库向基于 Flash 的应用程序添加额外的功能，包括加密、对原始数字照片的支持和 PDF 创建。

##### 结论

Adobe 的 Flash 平台在 Flash Player 中为 RIA 开发人员提供了一个强大的、无处不在的运行时；在浏览器中，只需编写一次代码，就可以随处部署，因为所有主要操作系统都有一个运行时版本。Flex 开源框架使开发人员能够构建丰富的交互式应用程序，通过 Adobe AIR 在浏览器和桌面上运行；它提供了通过许多不同的连接类型连接到数据的能力。

如果您正在构建一个富互联网应用程序，并且还没有了解构成 Adobe Flash 平台的技术，那么现在是开始的好时机。在接下来的几个月里，我们将在 sitepoint.com 上发布更多关于这些主题的文章，敬请关注！

同时，通过[参加](https://www.sitepoint.com/quiz/adobe/building-rias-with-flash-platform)的小测验来测试一下自己对本文内容的理解。前 200 名提交答案的人将收到一份印刷本*《Adobe Flash Platform action script Rich Internet Application Development》*,由 Adobe 免费送货上门。[立即参加测验！](https://www.sitepoint.com/quiz/adobe/building-rias-with-flash-platform)

## 分享这篇文章**