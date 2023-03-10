# 喧嚣炒作的视觉广告创作流程

> 原文：<https://www.sitepoint.com/visual-ad-creation-workflow-with-tumult-hype/>

*本文由[骚动](https://tumult.com/)赞助。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

这篇文章的目标是展示如何创建一个丰富的媒体广告与喧嚣炒作。它涵盖了在构建广告的背景下使用应用程序的基础知识，并讨论了特定于广告的提示和技巧。阅读结束后，你将能够快速、漂亮地制作 HTML5 广告！

## HTML5 广告的兴起

众所周知，在数字广告中添加动画和交互性可以使广告更有效。除了吸引访问者的眼球，富媒体广告可以更好地传达产品或品牌，并增加参与度，从而带来更多点击。

由于富媒体广告本质上是可视化的，所以使用可视化工具来构建它们是有意义的。变化马上就能看到。Adobe 的 Flash 多年来一直是视觉广告制作工具的王者，但被 HTML5 在移动设备上的兼容性所颠覆。作为一项技术，HTML5 广告具有额外的优势，通常更容易访问，同时具有较低的功耗要求。但 HTML5 本身只是一种标记语言，并没有固有的可视化工具，就像 Flash 对 Flash Professional 应用程序所做的那样。通过手工编码构建广告将是一个费力而缓慢的过程。幸运的是，现在有复杂的可视化工具可以输出 HTML5。

## 喧嚣炒作是你的视觉设计工具

[Tumult Hype](https://tumult.com/hype/) 是 macOS 上领先的 HTML5 动画和交互性应用。Hype 的存在理由是易于使用和快速学习。布局类似于 Keynote，并且可以使用其快速“录制”功能自动创建每个属性的动画关键帧。Hype 提供了一组强大的动画功能，例如沿弯曲运动路径的动画、可定制的季节/定时功能、同时运行的时间轴以及封装的可重用符号。虽然 JavaScript 动作可以通过与其他库和服务集成来扩展其功能，但大多数常见的交互并不需要编码。

虽然 Tumult Hype 可以用于广泛的 HTML5 内容，但它特别适合富媒体广告创作。一个名为“导出脚本”的插件系统可以集成广告平台功能，并帮助打包其导出输出以进行交付。高级导出功能可以进一步帮助自动执行导出任务。Hype 的运行时库非常小，压缩后只有 25KB，为您的资产提供了充足的空间。该运行时驱动动画并管理浏览器兼容性问题(尽管如果某个功能在特定浏览器上不起作用，应用程序仍会发出警告)。

## 喧嚣炒作的广告创作流程

### 抓住喧嚣炒作和出口脚本

第零步是[下载喧嚣炒作](https://tumult.com/hype/download/)。该试验为期 14 天，完全正常。喧嚣炒作有两种味道模式:标准和专业。对于广告创作(以及跟随这篇文章！)，你会想用炒作专业。

然后，您可以选择[获取一个导出脚本](https://tumult.com/hype/export-scripts/)，这将增加额外的广告相关功能。目前有 Sizmek，DoubleClick Studio 和 DoubleClick Campaign Manager 的脚本。如果您使用不同的 ad 平台，调整标准输出的步骤通常很简单。

您可以通过查看 Hype 的首选项来验证是否安装了导出脚本。导出脚本也可能有选项显示在**文档检查器**中。

![Export Script options in the Document Inspector](img/bff2d6b37c0625ed34a09155d91490bd.png)

### 选择广告尺寸

在开始宣传之后，你会想要设置场景大小来匹配你正在创建的广告。这可以在**场景检查器**中通过设置宽度和高度或者选择一个预设尺寸来完成。Hype 包含常见广告横幅和矩形的预设。

![Scene Inspector](img/c82e94ee36014e2f242a8f24d17759aa.png) ![Scene Inspector](img/9e1f7f0e8f3adb00e8ebf26b9709ee7d.png)

如果你的广告有不同的尺寸表现，你可以利用[响应式布局](https://tumult.com/hype/documentation/3.0/#responsive-layouts)和[灵活布局](https://tumult.com/hype/documentation/3.0/#flexible-layout)系统。响应式布局允许在基于宽度的断点上显示不同的内容。以后可以单独导出它们。灵活布局系统将根据容器尺寸调整元素位置和大小。

### 导入资产

Tumult Hype 有基本的形状和文本原语，但最有可能的是，您将从另一个程序(如 Photoshop、Illustrator 或 Sketch)导入图像资源。这些可以简单地拖拽到场景编辑器或者通过**插入…** 菜单项添加。Hype 保留了资产的一个副本，但是如果它注意到原始资产的变化，就会要求更新，以便在迭代时提供帮助。

稍后您可以在**资源库**中找到资产。这里的一个选项是**在导出**时自动优化。默认情况下，此功能处于启用状态，可以自动执行一些任务:

1.  生成图像的视网膜(@2x)和非视网膜(@1x)变体(Hype 运行时选择加载哪个)
2.  将图像尺寸缩小到文档中使用的最大尺寸
3.  如果可行并且文件大小有所改善，尝试压缩图像
4.  转换非网页安全图像格式

虽然这通常是有用的，但它可能会违背广告的文件大小目标。例如，总的广告文件大小通常是从 zip 文件中测量的，而不是下载的实际内容——因此，同时拥有 retina 和非 retina 变体会增加权重。此外，在视觉压缩质量或大小方面，你可能比 Hype 在自动方式下做得更好(使用类似于 [ImageOptim](https://imageoptim.com/mac) 的工具)。您可能需要取消选中此框。

![Resource Librarys](img/c68a2684b5784607f52b0108603849ba.png)

无论如何，基于位图的资源可以快速增加。你可以考虑使用 SVG，它被浏览器广泛支持。炒作将这些视为图像，但不会试图优化它们。

顺便提一下，IAB 建议初始加载的资源不要超过 15 个，以帮助保持页面简洁。

### 动画 101

一旦通过拖入资源或插入文本/形状创建了元素，就可以通过以下步骤轻松地制作它们的动画:

1.  点击**记录**按钮
2.  将播放头拖到稍后的时间
3.  更改元素的位置或修改任何其他属性(如不透明度)

![Using Record to make a quick animation](img/b342304177ea76315def6163d18b09ad.png)

录制是添加每个属性关键帧的用户界面快捷方式。您可以随时手动创建或编辑它们。这包括改变他们的计时功能，以适应不同的季节或效果，如弹性和反弹。通过拖动运动路径使其形成曲线，可以进一步细化位置动画。

炒作中的时间线可以想象成一串动画。炒作中的一个场景总有至少一个时间轴——主时间轴。可以创建多个时间线并并行运行。这通常有助于制作特定元素的动画以响应动作，或者保持循环。

符号可以扮演类似的角色——它们代表可重复使用的“场景中的场景”,包含自己的元素和独立运行的时间轴动画。与其他带有符号的应用程序一样，一个实例中的更改将反映在所有其他实例中。

### 添加交互性和动作

选择 HTML5 而不是视频/动画 GIF 的好处在于，它允许与用户进行互动和参与。交互性是对事件的响应。这些事件可以是鼠标点击、拖动、滑动、场景加载、航路点点击，甚至是按键。行动反应取决于创意者的目标。您可能想要启动动画、将动画绑定到拖拽、改变场景或者甚至触发定制代码。在广告领域，典型的任务是让一个按钮点击转到一个外部 URL(也称为“退出”)或触发计数器，这些计数器可以报告给广告服务器。

在大多数情况下，无需代码就可以创建交互性。以下是运行动画以响应鼠标单击的基本步骤:

1.  选择一个元素
2.  转到**动作检查器**，在鼠标点击时按下**旁边的 **+****
3.  为动作选择**开始时间线…** ，在时间线弹出按钮中选择**新时间线…**
4.  命名时间轴，然后创建动画

![Actions Inspector](img/ca0ebf5d0da12173996e5b844734a43b.png)

导出脚本还可以提供特定于广告的操作，可以选择这些操作来响应事件。这些将出现在同一个动作弹出列表中。

![Popup List](img/d17762793699b81fee289be18bd5361d.png)

![Exit Override](img/40fe5ed3150c43f3dc2dcb62671dd817.png)

此外， **Run JavaScript…** 动作可以用于任何定制代码，包括调用广告相关的功能或调用 [Hype API](http://tumult.com/hype/documentation/3.0/#api-functions) 。

### 预视

在制作过程中经常预览广告以确保最终的动画和交互性如您所愿是非常重要的。预览菜单允许在计算机上的不同浏览器之间快速切换。如果按住 option 键，可以预览 Hype 中当前选中的特定场景/布局。Hype 还可以使用免费的 [Hype Reflect](https://bit.ly/HypeReflectApp) 应用程序在 iOS 设备上预览。

如果使用导出脚本，其输出也可以包含在预览中。为此，打开预览菜单，使用导出脚本检查**预览下的脚本。**

![Previewing with an Export Script](img/21d2ca1841fc1a2766232997da4522ed.png)

### 出口和交付

一旦你的广告完成，你会想出口的最终交付。**文件>导出为 HTML5** 菜单包含**文件夹…** 选项，产生默认的`.html`页面和相应的资源文件夹。该菜单还提供任何导出脚本提供的选项。这些将自动打包，通常剥离不必要的资源并压缩成一个单独的`.zip`文件。

![Export as HTML5 Menu](img/64c4c71276e1068b60efbcd1b92009fb.png)

为了进一步自动化，您可能想要使用**文件>高级导出…** 工具。这可以将广告分割成特定的场景或布局，也可以使用导出脚本。**文件>导出为电影**如果需要，还提供一些静态图像/视频选项。

## 结论

这些是创建一个丰富的媒体宣传 HTML5 广告的主要步骤！作为一种可视化工具，Hype 可以显著地加速创建和迭代过程，而不会限制特性或功能。导出脚本中特定于广告的工作流程功能可以自动执行数字广告制作的许多常见任务。任何人都可以构建或修改这些导出脚本，因此如果有不存在的广告系统或自动化，您可以自由地[构建它](https://github.com/tumult/hype-export-scripts/blob/master/README.md)！

如果你有兴趣了解更多，Tumult 网站上有[文档](http://tumult.com/hype/documentation/)和[教程](http://tumult.com/hype/tutorials/)。Hype 可用于 [14 天全功能试用](http://tumult.com/hype/download/)，完全专业版售价 99.99 美元。

但是现在，作为 SitePoint 用户，你可以在这里用 74.99 美元 [买到专业版。那是七五折！](https://sites.fastspring.com/tumultco/instant/hype3-pro?coupon=SITEPOINTDEALPRO)

## 分享这篇文章