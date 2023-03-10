# Flex 2.0 以更实惠的价格发布

> 原文：<https://www.sitepoint.com/flex-20-announced-with-more-affordable-pricing/>

Macromedia 今天[发布了 Flex 2](http://www.macromedia.com/macromedia/proom/pr/2005/announcing_flex2.html) ，这是其框架的一个重要新版本，用于构建具有丰富的客户端 Flash 界面的 Web 应用程序。本周早些时候，我有机会与 Macromedia 讨论即将发布的版本的细节。

Flex 2 将包括 Flash Player 8.5、Flex Framework 2、Flex Builder 2 和 Flex Enterprise Services 2。尽管更新后的软件要到 2006 年上半年才能发布，Macromedia 计划在本月晚些时候发布 alpha 版本，与 10 月 16 日的 MAX 会议相结合。

**Flash Player 8.5** 将增加一个新的 ActionScript 虚拟机(AVM2)，支持 action script 3.0(AS3)——这是一个脚本语言的更新版本，将符合最新的 [ECMAScript 标准](http://www.ecma-international.org/publications/standards/ECMA-262.HTM)，包括 [ECMAScript for XML](http://www.ecma-international.org/publications/standards/Ecma-357.htm) (E4X)。AVM2 将比现有的 AVM 运行速度快得多，并将支持许多高级语言功能，最显著的是改进的调试和错误报告。

AVM2 将与现有的 AVM 一起运行，只有为 AS3 编译的 Flash 电影才能在这个新的虚拟机上运行。这种架构的缺点是，使用 AS3 的电影和组件将无法与使用 AS2 的电影和组件进行互操作(例如，加载和显示使用 AS2 的嵌套电影的 AS3 电影将无法访问该电影中的函数和变量)。因此，为现有版本的 Flex 编译的组件需要重新编译，才能与 Flex 2 一起使用。

Flex Framework 2 将是这个新版本的升级类库和用户界面组件。它将被更新以利用 AS3，具有更干净的 API，并充分利用 Flash Player 8 中引入的新效果。

之前代号为左恩的 Flex Builder 2 将成为 Flex 的新 IDE，从头开始重写，以便在 [Eclipse 平台](http://eclipse.org/)上运行。与当前版本的 Flex Builder 一样，它将提供一个分离的图形视图(具有拖放式 GUI 构建)和代码视图(具有完整的代码提示和调试支持)。此版本中的新功能是开发人员管理“视图状态”的生产力特性，这是 Flex 组件的离散操作模式。

当前版本的 Flex 价格约为 12，000 美元，而 Flex 2 的上述基本组件价格不到 1，000 美元。尽管您只能通过 XML 数据传输和 SOAP Web 服务与服务器进行通信，但是您当然可以实现使用 AJAX 和 DHTML 所能做的任何事情，只是使用了更丰富的 GUI。软件包中缺少的是 Flex 框架的服务器端组件，它已经被拆分为一个单独的 Flex 2 产品:Flex Enterprise Services 2。

Flex Enterprise Services 2 的每 CPU 价格会很高，但会从 Flex 1 提供的服务器端设施进行大幅升级。增强包的主要焦点是 Flex 应用程序中服务器端资源(如数据库记录和企业服务)的透明可用性。

虽然对于不需要企业服务包的开发人员来说，大幅降低价格是一个好消息，但 Macromedia 不打算继续提供免费的非商业/非机构许可,就像他们现在对 Flex 1 所做的那样。随着学生和业余用户获得免费许可证并使用 Flex 1 开发应用程序，他们将不得不预先申请 Flex 2 的许可证，或者被冷落。在我看来，这似乎是一个非常不幸的举动，我希望 Macromedia 会重新考虑。

关于 Flex 2 的更多细节，请查看 Macromedia 为开发者提供的[介绍](http://www.macromedia.com/go/flex2intro)。

## 分享这篇文章