# 6 个 jQuery 表单向导插件

> 原文：<https://www.sitepoint.com/jquery-form-wizard-plugins/>

jQuery 表单向导是一个 jQuery 插件，它通过某种表单流来帮助创建表单(无需刷新页面)。例如，如果您有一个用于输入用户数据的大型表单，您可以使用表单向导将它分成一系列相关的步骤。这样做的好处是不会让用户被冗长的表单弄得不知所措，并且在他们输入信息的时候也能给他们一些进度提示。

在这篇文章中，我们列出了 6 个我们最喜欢的 jQuery 表单向导，分析了它们不同的特性，最后看了几个付费选项，以及如何创建自己的向导。这并不是一个详尽的列表，但是如果您正在寻找一个 jQuery 表单向导，那么希望它能为您指明正确的方向。

> 这篇热门文章更新于 2017 年 8 月 30 日。损坏/废弃的插件被从列表中移除，新的插件被添加以反映人们在评论中要求的功能。

## 1.jQuery 步骤

jQuery Steps 是一个智能 UI 组件，允许您轻松创建类似向导的界面。这个插件将内容分组为更有结构和有序的页面视图。它有很多特性，比如异步内容加载、状态持久化(它在步骤之间保存你的输入)和部分之间的转换效果。它可以通过 NuGet 或 bower 安装，并且有一个文档完善、功能丰富的 API。

![jQuery Steps](img/7b10ea9b755a4927ffe28e14dd6f101a.png)

[首页](http://www.jquery-steps.com/) | [GitHub](https://github.com/rstaib/jquery-steps/) | [试玩](http://www.jquery-steps.com/Examples)

## 2.jQuery 智能向导

智能向导是一个灵活的、高度可定制的 jQuery 步骤向导插件，支持引导。它很容易实现，并为您的表单、结帐屏幕、注册步骤等提供了一个整洁而时尚的界面。它的功能包括主题支持(包括各种主题)、URL 导航和步骤选择，以及动态隐藏或禁用步骤的能力。它可以通过 npm、bower 或 composer 安装，并且有一个文档完善、功能丰富的 API。

![jQuery Smart Wizard](img/17a10643f40808e2b891be9b2efea150.png)

[首页](http://techlaboratory.net/smartwizard) | [GitHub](https://github.com/techlab/SmartWizard) | [试玩](http://techlaboratory.net/smartwizard/demo)

## 3.formToWizard

这个轻量级插件通过 jQuery 将任何 webform 变成多步向导，每个表单

<fieldset>通过前进和后退按钮变成一个单独的步骤。它没有前两个插件那么多的特性，但是集成了 jQuery 验证插件来提供验证功能。它是一个单独的文件(所以你可以从 GitHub 上获取它，然后运行),如果 JavaScript 不可用，它会优雅地降级。</fieldset>

![formToWizard](img/1f749d08413ef10892818b087d81b1f9.png)

[GitHub](https://github.com/artoodetoo/formToWizard) | [试玩](https://github.com/artoodetoo/formToWizard#live-examples-in-jsfiddle)

## 4.jQuery 步骤

jQuery Stepy 是一个插件，可以生成可定制的表单向导。它假设您的表单有特定的标记结构，并且标记上有特定的类。之后你只需要初始化插件，瞧，你就有了一个表单向导。

这个插件没有任何预定义的样式。然而，它确实有多种选择，例如能够在步骤之间导航，提供*前进*、*后退*和*完成*按钮，过渡效果以及与您选择的任何验证插件的集成。它提供了一个合理的 API，例如，允许您在步骤之间触发回调，或者在呈现当前步骤时触发回调。

![jQuery Stepy](img/eaf5d86bf0165ac3c8826e788ffb5ae7.png)

[首页](https://wbotelhos.com/stepy) | [GitHub](https://github.com/wbotelhos/stepy)

## 5.Twitter 引导向导

这个 Twitter 引导插件使用格式化的表格结构构建了一个向导。它允许用户使用按钮通过不同的向导步骤和事件来构建向导功能，以分别挂钩到每个步骤。这个插件的主要优势是它与 Bootstrap 的紧密集成。它附带了一些基本的特性，比如验证、进度条以及通过编程启用和删除步骤的能力。除了从 GitHub 克隆插件之外，它还可以通过 Bower 安装(尽管[不建议这样做](https://github.com/VinceG/twitter-bootstrap-wizard/issues/81#issuecomment-42909528))。它有很好的文档记录，并提供了一个基本的 API。

![twitter-bootstrap-wizard](img/b9fb008a64cacfcae4714a201a0638b3.png)

[首页](http://vinceg.github.io/twitter-bootstrap-wizard/) | [GitHub](https://github.com/VinceG/twitter-bootstrap-wizard) | [试玩](http://vinceg.github.io/twitter-bootstrap-wizard/#examples)

## 6.jQuery.wizard

最后，排在第六位的是 jQuery.wizard，这是一个支持分支的异步表单向导——这是人们在评论中要求的。

向导的基本结构围绕着步骤和分支，后者是可选的。简单的线性表单可能只需要一个包含所有步骤的分支，而复杂的表单可能需要使用几个分支，甚至是嵌套的分支。因此，表单中步骤和分支的数量完全由开发人员决定。该向导使用异步[有限状态机](https://en.wikipedia.org/wiki/Finite-state_machine)来决定如何导航。

这个插件是非常好的文档，并提供了广泛的 API。它可以通过 npm 或 Bower 安装，并且可以一直兼容到 Internet Explorer 6。它被设计成能够与各种其他插件很好地集成，包括一直流行的 jQuery 验证。您可以通过克隆 repo 并在浏览器中查看`examples/index.html`来查看各种示例的演示。

![jQuery.wizard](img/40e1c4e7d6a22b78f804d3fd4f0dd015.png)

[GitHub](https://github.com/kflorence/jquery-wizard#integration)

## 奖金

开源软件的世界是伟大的，当你选择一个小部件用于你的下一个项目时，你经常会被宠坏。然而，这并不意味着我们应该回避为实际上给我们带来价值的东西付费。因此，这里有两个付费的表单向导插件。如果它们适合你，我鼓励你去看看并购买它们:

*   [表单向导–多步骤表单验证](https://codecanyon.net/item/form-wizard-multi-step-form-validation/19613591)
*   [Timon–步骤表单向导+ jQuery 步骤表单生成器](https://codecanyon.net/item/timon-step-form-wizard/15830006)

最后，如果你是那种宁愿了解幕后发生的事情，而不是抓住一个插件的人，那么也许你会喜欢看看这些教程，它们向你展示了如何从头开始创建一个表单向导。

*   [用 jQuery 将任何 web 表单变成一个强大的向导](http://www.jankoatwarpspeed.com/turn-any-webform-into-a-powerful-wizard-with-jquery-formtowizard-plugin/)
*   [使用 jQuery 和 CSS 的带进度条的多步表单](http://talkerscode.com/webtricks/multistep-form-with-progress-bar-using-jquery-and-css.php)

尽情享受吧！

## 结论

现在您已经有了它——六个 jQuery 表单向导插件的高级概述，每个插件都有稍微不同的特性和优势。您选择哪一个将取决于您的项目需求。我希望你能在这篇文章中找到对你有用的东西。如果我们遗漏了什么，或者哪个插件是你最喜欢的，请在下面发表评论。

## 分享这篇文章