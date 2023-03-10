# Sympli 是开发者不会讨厌的设计协作工具吗？

> 原文：<https://www.sitepoint.com/is-sympli-is-the-design-handoff-tool-that-developers-wont-hate/>

设计协作工具在 2016 年风靡一时。

在过去，大多数项目团队都是将日历和待办事项应用拼凑在一起，以满足他们的需求，而且大多效果良好。但是随着设计和开发过程变得越来越复杂，专业化协作工具的论点变得越来越有说服力。

但是，有谁离完善设计协作流程有多近呢？我觉得这有点棘手。最近，我关注了该领域一个有趣的竞争者——symp Li。让我带你走一遍。

![Sympli Integrations](img/de4ea1352456db619c12631e54348ac4.png)

**偏好**是选择设计协作工具的决定性因素，但是谁的呢？设计协作工具倾向于面向设计者和管理者，而开发人员经常被排除在外，这导致了不稳定的设计移交。

让我们来看看与 [Sympli](https://sympli.io/) 的合作是如何在不给设计师制造困难的情况下改善设计交付(以及最终的开发者关系)的。*另外，对于一个有无限合作者的单个项目来说，它是免费的！*

## 开始一个 Sympli 项目

最初真正给我留下深刻印象的是我如何能够立即开始合作——从我的注册表格中只获得了最少的信息，我被告知我可以立即开始一个“新项目”。Sympli 需要您提供三个信息:*项目名称*、*平台*和*决议*，所以继续提供吧。

![Starting a New Project](img/41782b7ae75979fbafabb383e0b64388.png)

## 安装 Photoshop 或 Sketch 的插件

在建立一个“新项目”后，你会被要求从 Photoshop 插件或草图插件上传设计。如果你不是设计师，你可以邀请*他们*来做这件事，这让我觉得开发者在这里掌控全局是完全没问题的(稍后会有更多相关内容)。

暂时离开此网页。

![Installing the Sketch Plugin](img/e4a555d0052765367eef42b63701e12d.png)

我将带你完成从 Sketch 导入设计资产和样式的过程，但是，你也可以使用 Photoshop 来完成这个过程，步骤基本相同。从下载 Photoshop 插件或者素描插件[这里](https://app.sympli.io/download)开始；安装本身应该相对容易，但对于那些以前从未安装过扩展的人有详细的说明。

正如你在右边看到的，也有一些开发人员的扩展(Xcode，Android Studio ),帮助从 Sympli 中提取设计资产*。*

再一次，很快会有更多。

![Sympli Extension Page](img/7373805b87c4f77d8b81ee3161a86cea.png)

## 将设计导入 Sympli

出于本教程的考虑，我将使用我自己的[用户界面工具包](https://creativemarket.com/mrdanielschwarz/668399-Solar-.sketch-for-Landing-Pages)，但是如果你需要一个样本文件来使用，请随意从 [Sketch App Sources](http://www.sketchappsources.com/) 获取一些东西。

选择一个想要导出的图层(或多个图层)，点击*检查器*中的“可导出”按钮。到目前为止，这很正常，你可以通过常用的键盘命令**命令+shift+E** 看到所有可导出的区域。

![Making Image Assets Exportable](img/fbf4d87bed8409a958cc76ecb5167aef.png)

但是，我们将使用键盘快捷键( **command+Y** )将资产导出到 Sympli，而不是将这些层实际导出到我们的本地计算机。Sympli 将导出整个选定的画板，其所有层保持不变**，或者如果没有选择任何层，它将导出所有画板。先登录…**

 **![Logging-in to Sympli from Sketch](img/4defeefac5d685bef6178cb000e5b553.png)

…然后从“我的项目”列表中选择您要将资产导出到的位置。如果您之前跳过了这一步，您可以在这里“创建一个新项目”(事实上，设计人员几乎不需要使用 Sympli web 界面)。然后，Sympli 将开始导出设计，几分钟后(取决于设计的复杂程度)，您将看到它出现在 Sympli web 应用程序中，因此让我们回到浏览器。

![Exporting from Sketch to Sympli](img/595b776655b078c356ffddb4b10cca82.png)

## 设计移交如何进行

首先点击标题中的“项目”,导航到我们导出的设计——在这里，设计师、开发人员和经理可以浏览和检查设计，直到特定的层，独立于任何实际的设计应用。

![Navigating Back to the Project](img/79926a7e620e8bb205d51ab721de3729.png)

如您所见，在左侧(如果您单击图层图标)，您可以在图层列表中浏览这些图层，在右侧，我们在 Sketch 中声明为可导出的任何图像资产。您可以从这里下载所有图像资源，或者单击实际图像来观察其尺寸。

![Extracting Image Assets](img/88ae93cee0066b6867cc76ecf95cfbde.png)

当然，如果我们选择一个不同的层，例如一个文本层，他们会有更多的可用信息:颜色，使用的字体，字体大小，文本值，对齐等等。您可以将这些样式信息(作为代码)复制到剪贴板，其中的代码语言取决于您正在为哪个*平台*进行设计(例如，如果您选择“Web ”,它将是 CSS)。

![Extracting Styles as Code](img/7f2281cc6c8c85e77d11ec13fb9e69ee.png)

非常适合开发人员。

你也可以预先指定你想要使用的度量单位( *px，em，等等*)以及一些其他的设置，当然这是由开发者来处理的。就像我之前说的，开发者可以访问一个 [Xcode 和 Android Studio 插件](https://app.sympli.io/download)，他们可以直接访问所有这些信息，也不必使用 Sympli 接口。实际上，您的整个团队都可以使用 Sympli，而不必访问 web 界面！

注意:开发者仍然需要使用 Sympli web 界面来浏览 HTML 设计和提取 CSS 数据。

### 还有什么？

除了标尺和缩放等不太重要的工具之外，右侧的另外两个选项卡显示了整个设计中使用的颜色和字体的简要概述，您还可以将这些信息复制到剪贴板。

![Observing Colour Guides](img/e9f5d4ffb5b6a9ecb87bc8ec61439815.png)

![Observing Font Styles](img/09cf58c64d149e4a8e34a2ba6e309800.png)

## 其他合作者入职

你可以在应用程序的任何地方邀请你的队友与你协作；通常从头部开始。真的很标准，但对我来说最突出的是 Slack 集成，它让 Sympli 在您的 Slack 团队中共享您的设计更新，从而实现交流和反馈。如果您导航回“项目”，您可以将特定的设计链接到特定的松弛渠道。非常适合从事不同工作的大型团队！

![Sharing Sympli Projects in Slack](img/0833833bbb27cc14d7a1320e7258e2ba.png)

## 结论

那么，为什么是辛普利？因为它不仅允许设计者和开发者同时协作，还允许他们以自己的方式使用 Sympli 作为一个中心、中立的位置；在这里，导出高粒度布局和从中提取资源/风格到现实生活中的网站/应用程序非常容易。

一句话: [Sympli](https://sympli.io/) 实际上设法让每个人的合作变得有趣。

## 分享这篇文章**