# 13 个 jQuery 选择框/下拉插件

> 原文：<https://www.sitepoint.com/13-jquery-selectboxdrop-down-plugins/>

**这篇受欢迎的文章于 2016 年 10 月 12 日更新，以反映选择/下拉插件的当前状态。**

select 元素的默认样式并不适合所有人。有时您可能想要控制它的外观(以保持它在不同浏览器和设备上的一致性)，或者您可能想要一些本机不支持的附加功能。

值得庆幸的是，有一堆很棒的 jQuery 插件可以简化这个过程。

我们将介绍一些插件，你可以将它们整合到你的下一个项目中。其中一些插件的选项、方法和事件是高度可配置的，而另一些插件则是为了样式和易用性而对 select 元素的简单替换。

## 挑选出来的

Chosen 是一个扩展插件，它不仅可以重新设计你选择的元素，还提供了额外的功能，比如选择内搜索、多元素选择和高亮显示。

![screenshot of dropdown box with and without the plugin](img/4644b05c037e83827d4d1e52724b34a0.png "chosen.jpg")

如果你想更新你选择的元素的默认样式，你可以使用这个插件，但是真正的力量来自于附加的功能:

*   处理多选选项的能力。每个选项都会保存下来，如果需要，可以轻松删除
*   通过搜索过滤元素的能力。当您有一个很长的选项列表(如国家名称)时，这是完美的选择

一切都可以用这个插件运行，桌面支持可以追溯到 IE8 兼容性。一个积极(或消极)的因素是，在移动设备上，选择元素恢复到它们的原始形式，让移动浏览器控制你如何与它们交互。

[options 文档](https://harvesthq.github.io/chosen/options.html)概述了所有的设置、方法和事件，您可以与之交互来定制您的元素。插件本身由开发 Harvest 项目管理工具的开发人员维护。他们的 GitHub 库不断更新，插件中加入了额外的特性、错误修复和优化。

[网站](https://harvesthq.github.io/chosen/) / [GitHub](https://github.com/harvesthq/chosen)

## 选择 2

Select2 是一个全功能、丰富的选择替换/增强插件。它不仅重新设计了选择元素的样式，还用附加功能扩展了它们。

![screenshot of dropdown box with and without the plugin](img/02a15052e0e89e6addcb74ac27f850bf.png "select2.jpg")

与其他高级选择插件非常相似，它包含了一大堆可定制的功能，例如:

*   单一选择元素/具有 optgroups 的元素
*   多选元素
*   用于选择元素的可排序/可过滤搜索字段
*   从远程数据源加载数据的能力(例如，从 API 动态更改选项)
*   标签支持(从预定义列表中选择/动态添加动态标签)

Select2 从 2012 年就开始开发了。开发人员已经从版本 3 转移到版本 4，并在此过程中重新编写了插件，以使其更快，更灵敏，更便于移动。Select2 的 GitHub 页面令人印象深刻，团队的努力使得每个版本的插件都更好。

该插件明显更关注开发者，他们的[选项页面显示了你如何使用每一个不同特性的综合示例](https://select2.github.io/options.html)。

这个插件提供了高度的定制，如果你正在寻找一个可以定制来匹配你的项目的插件，它是一个很好的解决方案。

[网站](https://select2.github.io/) / [演示](https://select2.github.io/examples.html) / [Github](https://github.com/select2/select2/)

## jQuery 不错的选择

这个插件是一个轻量级的选择替换库。jQuery Nice Select 用重新设计的下拉菜单替换了默认的本地选择元素。

这个插件没什么特别之处，因为它旨在作为一种快速的方式来重新设计选择的元素，不需要太多的努力就能提供一些视觉上吸引人的东西。该插件正在 GitHub repo 上积极开发和改进，在移动和桌面浏览器上都运行良好。

![screenshot of dropdown box with and without the plugin](img/8c49a460bc0f4d3046bca8ae5554b841.png "jquery_nice_select.jpg")

如果你正在寻找一个快速简单的东西，只需要重新设计你选择的元素，不需要太多的努力，那么这个插件就是你要的。

[网站&演示](http://hernansartorio.com/jquery-nice-select/) / [Github](https://github.com/hernansartorio/jquery-nice-select)

## 选择的

Selectize 是一个非常注重开发者的全包插件，它不仅为你提供了一个更好看的选择列表，还增加了新的有用的功能，如标签，远程数据绑定和动态下拉。

![screenshot showing various styling options](img/2944ba60984fd11409e90d5007059945.png "selectize-jquery.jpg")

与 Select2 和 Chosen 非常相似，Selectize 非常关注开发人员，让您可以控制下拉列表的工作方式。他们的[文档相当全面](https://github.com/selectize/selectize.js/blob/master/docs/usage.md)，概述了一系列选项和几个回调函数，你可以通过它们来进一步定制插件。

该插件一直在不断更新，开发者在去年发布了几次迭代。有时你不确定你刚刚发现的一个很棒的插件是否被积极维护，或者它是否只是“作为”提供，但是对于 Selectize 来说，它似乎仍然是活跃的，几乎所有的问题/ bug 报告都被解决了，相当透明的讨论正在进行。

功能性选择效果很好。这个插件不仅改变了默认控件的样式，还扩展了它们的强大功能，如搜索过滤、多选功能和远程数据绑定。所有这些在桌面和移动浏览器上都运行良好。

这个插件总体来说是一个很好的选择，如果你想重新设计和扩展选择元素的功能，它应该是你的首选之一。

[网站&演示](https://selectize.github.io/selectize.js/) / [GitHub](https://github.com/selectize/selectize.js)

## 图像组合框

图像组合框是一个简单的插件，允许你为选择控件中的每个选项定义图像和描述。它主要用于展示每个选项的相关图像，因此当您想要显示每个选项的可视化表示时，它非常有用。

![screenshot of dropdown box styled with flag images](img/b6887d5e1ceaed5c23ed7d41dad9c7db.png "combo_box_dropdown.jpg")

这个插件拥有你所期望的所有基本特性，比如键盘导航、一系列可挂钩的事件和一个相对简单的定制样式标记。

组合框在多种浏览器上运行良好，但是它已经很久没有更新了，而且在[插件 GitHub 页面](https://github.com/marghoobsuleman/ms-Dropdown)上几乎没有交互。这并不是说这是一个不好的插件，只是你必须在它出现的时候使用它，并在你的项目中彻底测试它，因为支持可能会很困难。

如果你的主要目标是让你可以用下拉元素显示图片和/或描述，那么这个插件值得一看。

[网站](http://www.marghoobsuleman.com/jquery-image-dropdown) / [试玩](http://www.marghoobsuleman.com/mywork/jcomponents/image-dropdown/samples/index.html) / [Github](https://github.com/marghoobsuleman/ms-Dropdown)

## jQuery 可搜索下拉插件

可搜索下拉插件是你可能在网上找到的较早的插件之一。创建于 2012 年(最近没有更新)。它的目的是将您的基本选择元素转换成一个可搜索的单一列表。

![screenshot of dropdown boxes styled with the plugin](img/b1d1d3c3f49c3b498050b1708cd76aa6.png "jquery_searchable_dropdown.jpg")

这个插件没有网站，而是存在于 SourceForge 上。除了展示其工作原理的一个演示页面之外，这里没有太多的文档。

当与其他高度可扩展的选择替换插件相比时，这个插件让人感觉格格不入。然而，这个库的一个优点是它支持 IE7 的所有版本，并且在桌面和(令人惊讶的)移动浏览器上都能很好地工作。

如果你正在寻找一个能够排序的最低限度的替代品，这可能是你想要的

[网站](https://sourceforge.net/projects/jsearchdropdown/) / [演示](http://jsearchdropdown.sourceforge.net/)

## 多选组合框

这个插件与我们讨论过的其他插件有些不同。它一次处理两个或多个 select 元素，创建一个 UI，可以在列表之间来回移动元素。它类似于其他拖放解决方案，但是完全由标准的选择元素组成。为了让它运行起来，你可能需要设计一些风格来符合现代设计标准(因为它有点难看)

![screenshot of multi-select boxes styled with the plugin](img/d5564ab4fa82d8afd57c5575f77c7a7c.png "jquery-select-list-actions.jpg")

如果你想在两个或者更多的容器之间排序一个条目列表，这种类型的控件是非常有用的，例如排序任务或者组织内容。

演示和 GitHub 页面概述了如何进行设置，因为几乎没有控制选项，所以学习曲线相当浅。

[网站](http://www.jqueryscript.net/form/Lightweight-Multi-select-Combo-Box-Plugin-For-jQuery-SelectListActions.html) / [试玩](http://www.jqueryscript.net/demo/Lightweight-Multi-select-Combo-Box-Plugin-For-jQuery-SelectListActions/) / [GitHub](https://github.com/esausilva/jquery.selectlistactions.js)

## jQuery 选择框

jQuery Selectbox 是较早的选择替换插件之一。然而，与其他增加大量功能的插件不同，这个插件的主要目的是重新设计你的控件，并且保持功能不变。

![screenshot of dropdown boxes styled with the plugin](img/cb7e1e1927e77b0cf17205b40a4f84bd.png "jQuery-selectbox.jpg")

jQuery Selectbox 确实有几个有用的选项可以控制，还有所有你期望的标准事件，比如打开、关闭、选项选择等等。浏览器支持也很全面，支持 IE7 以下的旧浏览器，同时也支持在手机上使用系统默认设置(如果你愿意的话)。作为一个老插件，它可能不会很快改变。维护这个插件的开发者已经多年没有活动了，所以它现在“按原样”提供。

这个插件的优势在于它简单的样式，让你可以很容易地用你自己的样式来匹配你的设计。如果你想做一些基本的事情，比如设计你的下拉菜单，或者定制一些设置，那么这个插件可能就是你想要的。

[网站&试玩](http://marcj.github.io/jquery-selectBox/) / [GitHub](https://github.com/marcj/jquery-selectBox)

## Multiselect.js

另一个 jQuery 支持的库，可以让您快速轻松地创建基于多选的列表。当[在他们的演示](http://loudev.com/#demos)中展示时，很容易看出它们是多么有用。您可以创建单个 select 元素，然后轻松地在列表之间来回移动项目。

![screenshot of multiselect boxes styled with the plugin](img/8abd2bb6638c08aafbd87bd2e81eb7b7.png "jquery-multi-select.jpg")

该插件似乎被其开发者更新得相当频繁，而且 [GitHub](https://github.com/lou/multi-select) 页面有大量已关闭/已解决的标签。

虽然最近在 GitHub 上标记的一些问题还没有得到回复，但是这个插件本身是相当可靠的，有许多选项、方法和事件供你使用。它在多种浏览器上都能很好地工作，并且包含很少的样式(让您可以轻松地指定它应该是什么样子)。

如果你在寻找一个简单的多选插件，这是一个很好的起点。

[网站](http://loudev.com/#home) / [GitHub](https://github.com/lou/multi-select/)

## JQuery SumoSelect

SumoSelect 拥有选择列表替换中所需的所有功能。这个插件处理单选和多选元素，以及搜索和过滤，由一组可靠的选项和事件支持。

![screenshot of dropdown boxes styled with the plugin](img/5ff3591392bfe078c3825b10bd767ec8.png "jquery-sumo-select.jpg")

SumoSelect 的外观和感觉很轻，强调极简风格，您可以为您的项目定制。有几个演示展示了如何配置所有不同的选项。

这个插件在过去的几年里一直在不断改进，并且每个版本都越来越好。不像其他插件可能会被放弃，留下“原样”，看起来这个插件会随着每次修订变得更好。

另一个值得注意的因素是，这个插件提供了全面的浏览器支持。SumoSelect 在桌面上的工作时间可以追溯到 IE8，并为移动设备提供支持，可以正常工作，也可以返回到本机控制(取决于您在设置时提供的选项)。

[网站](http://hemantnegi.github.io/jquery.sumoselect/) / [演示](http://hemantnegi.github.io/jquery.sumoselect/sumoselect_demo.html) / [GitHub](https://github.com/HemantNegi/jquery.sumoselect)

## jQuery UI Selectmenu

这里有一点不同。与其他独立插件不同，比如 Chosen 或 Select2(虽然流行，但并不普遍)，这个插件是 jQuery UI 框架的一部分，是一个名为 selectmenu 的[可扩展小部件](http://jqueryui.com/selectmenu/)

![screenshot of dropdown box styled with the plugin](img/0eabd91ae81a7b94b2efc1b09efa2220.png "jquery-ui-selectmenu.jpg")

jQuery UI 小部件是面向开发人员的扩展，可以集成到您自己的项目中。这些小部件已经被大量开发并且高度稳定，由一系列选项、方法和事件支持，你可以挂钩来定制它如何工作。

Selectmenu 的目的是扩展本地 select 元素的功能和设计。这些元素是完全主题化的，并且基于 jQuery UI CSS 框架。虽然它没有其他大型插件的所有功能(如过滤搜索、动画或 JSON 数据绑定)，但它具有强大的跨浏览器支持和持续更新。

如果您希望快速设置选择元素的样式以匹配 jQuery UI 的主题，或者您想要一个可以自定义和调整的综合库，那么这个库将非常有用。

[网站&演示](http://jqueryui.com/selectmenu/)

## jQuery 可过滤引导选择

可过滤的 Bootstrap Select 是一个插件，它扩展了 Bootstrap 3 中提供的默认选择样式。

这个插件结合了几个资源，比如图标的 [FontAwesome](http://fontawesome.io/) ，过滤机制的 [jQuery LiveFilter](https://github.com/Xarksass/livefilter) ，自动预测的 [jQuery tabcomplete](http://erming.io/tabcomplete/) 。

![screenshot of dropdown boxes styled with the plugin](img/d961afda3a78f1450a7e99974c378ec7.png "jquery_bootstrap_select.jpg")

和大多数其他插件一样，你可以根据自己的需要定制一系列的选项。它应该提供所有你需要的，为你工作的一个基本的可过滤列表。

主插件本身存在于 GitHub 上，并且在一年的时间里偶尔会更新。然而，大多数项目都是由一个人维护的，你永远不知道什么时候或者你是否能够得到支持。如果您对演示的工作方式感到满意，并且想“原样”使用它，那么如果您已经在 Bootstrap powered 站点上工作，那么它是一个很好的资源。

[Demo](http://www.jqueryscript.net/demo/jQuery-Plugin-For-Filterable-Bootstrap-Dropdown-Select-Bootstrap-Select/) / [GitHub](https://github.com/Xarksass/BootstrapSelect)

## DDSlick jQuery 下拉列表

DDSlick 将你选择的元素转换成样式简单的下拉菜单。每个选项都包含正常的标题和值，还包含可选的图像和描述。

您可以定制几个不同的选项，还可以挂接一些事件来进一步调整。

**一个真正的问题是网站被破坏了**，所有页面上的例子都触发了错误。看起来这些资源都指向一个不再存在的 DropBox 帐户。然而，脚本本身并没有丢失，几个网站都有它的副本，[包括 JSDeliver](http://www.jsdelivr.com/projects/ddslick) 。

通常我们会跳过有坏的演示页面的插件。然而，这个插件真的很好，它的文档是健全的。如果您对以下内容感兴趣，这是一个不错的插件:

*   动态绑定来自 JSON 数组的选择值(允许您动态定义选项)
*   显示图像，说明和标题，展示每个选项(有几个布局)
*   用回调定制代码

如果你想知道这是如何工作的，你可以将网站 HTML 和相应的 JS 样本复制并粘贴到 JS playground 网站，如 [Codepen.io](http://codepen.io/) 中，它就会工作(假设你链接到 jQuery 和插件的主 JS 文件)。

[网站](http://designwithpc.com/Plugins/ddSlick)

## 把一切都包起来

现在选择替换插件有很大的选择余地。在过去的几年中，几个比较流行的库已经被改进，引入了新的特性，同时改进了它们的整体功能。

您需要搜索我今天提到的各种库，看看哪一个最符合您的需要。例如，如果您只想重新设计选择元素的样式，那么您真的需要一个还提供过滤和远程数据绑定的库吗？

对于大多数这些库，您应该可以相对较快地启动并运行。他们中的大多数只需要 jQuery 和相关的插件文件，你就可以开始了。

如果您热衷于定制您的选项或挂钩回调，您可能需要选择一个更侧重于开发人员的库，如 Selectize、Chosen 或 Select2。找到一个适合你的将是一个个人观点的问题。

请告诉我们，在选择替换时，您是否使用过任何其他出色的库。我们希望收到您的来信！

## 分享这篇文章