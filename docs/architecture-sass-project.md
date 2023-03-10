# Sass 项目的架构

> 原文：<https://www.sitepoint.com/architecture-sass-project/>

还记得我们以前做任何事情都只使用普通的 CSS 吗？我们只有一个 CSS 文件，比一个不眠之夜还要长。耶！成千上万行 CSS 代码——通常写得很糟糕——我们努力寻找一个值，我们必须改变它来修复一些模糊和令人沮丧的 IE bug。

伙计们，那些日子已经过去了。处理 CSS 变得更加有趣和复杂。大概是后者因为前者。现在我们有了 CSS 预处理器，响应性网页设计，渐进增强，优雅退化，以及酷孩子谈论的所有其他东西，CSS 变得比以往任何时候都更强大。

> " CSS 变得更加有趣和复杂."
> —我。

所以因为我们有很多事情要处理，所以保持组织性是很重要的。我们可能都同意，要做到这一点并不容易。所以我想在这篇文章中，我可以帮助你理解你应该如何思考(而不是你应该如何做；我将把那留给你)。

## 画出你的建筑

使用 CSS 预处理器的一个主要好处是能够在不影响性能的情况下将代码分成几个文件。多亏了 Sass 的`@import`指令，你可以在你的开发环境中拥有尽可能多的文件，这将在生产中编译成一个文件。

> 开发中有多个文件，生产中有一个文件
> —李小龙

要有条理，首先要把你的 CSS 正确地分割成几个文件和文件夹。正如我的一位老师曾经说过的*“每样东西都有它正确的位置，每个位置都有它正确的东西”*。我喜欢这样做！

### 文件夹很酷，使用它们

文件夹必不可少。在家里，你不会把每张纸都放进同一个盒子里。你可能有文件夹。一个用于房子/公寓，一个用于银行，一个用于账单，等等。

![What if I told you, you don't have to put all your Sass files in the same folder?](img/ff573ec5e421946515506e588aefc180.png)

当规划你的 CSS 架构时，这是完全相同的事情:你不只是把每个 Sass 文件放到同一个文件夹中，你把它们分类。

我们可以这样组织我们的文件:

```
sass/ 
| 
|– base/ 
|   |– _reset.scss       # Reset/normalize 
|   |– _typography.scss  # Typography rules 
|   ...                  # Etc… 
| 
|– components/ 
|   |– _buttons.scss     # Buttons 
|   |– _carousel.scss    # Carousel 
|   |– _cover.scss       # Cover 
|   |– _dropdown.scss    # Dropdown 
|   |– _navigation.scss  # Navigation 
|   ...                  # Etc… 
| 
|– helpers/ 
|   |– _variables.scss   # Sass Variables 
|   |– _functions.scss   # Sass Functions 
|   |– _mixins.scss      # Sass Mixins 
|   |– _helpers.scss     # Class & placeholders helpers 
|   ...                  # Etc… 
| 
|– layout/ 
|   |– _grid.scss        # Grid system 
|   |– _header.scss      # Header 
|   |– _footer.scss      # Footer 
|   |– _sidebar.scss     # Sidebar 
|   |– _forms.scss       # Forms 
|   ...                  # Etc… 
| 
|– pages/ 
|   |– _home.scss        # Home specific styles 
|   |– _contact.scss     # Contact specific styles 
|   ...                  # Etc… 
| 
|– themes/ 
|   |– _theme.scss       # Default theme 
|   |– _admin.scss       # Admin theme 
|   ...                  # Etc… 
| 
|– vendors/ 
|   |– _bootstrap.scss   # Bootstrap 
|   |– _jquery-ui.scss   # jQuery UI 
|   ...                  # Etc… 
| 
| 
`– main.scss             # primary Sass file 

```

可以看到，根级别只有一个 Sass 文件:`main.scss`。所有其他文件被划分到适当的文件夹中，并以下划线(`_`)作为前缀，告诉 Sass 它们是[部分的。不应该编译成`.css`文件的 scss 文件](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#partials)。事实上，导入和合并所有这些内容是基础文件的任务。

> “一个文件来统治他们所有人，
> 一个文件来寻找他们，
> 一个文件来带来他们所有人，
> 并以萨斯的方式合并他们。”
> — J.R.R .托尔金

现在让我们来看看架构中的每个文件夹。

### 基础

文件夹保存着我们称之为项目样板文件的东西。在那里，您可能会找到 reset(或 Normalize.css，或其他什么)，可能是一些处理排版的东西，根据项目的不同，可能还有一些其他文件。

*   `_reset.scss`或`_normalize.scss`
*   `_typography.scss`

### 助手

`helpers/`文件夹(有时称为`utils/`)收集了我们将在项目中使用的所有 Sass 工具和助手。有功能吗？一个混血儿？把它放在那里。这个文件夹还包含一个`_variables.scss`文件(有时是`_config.scss`，它保存了项目的所有全局变量(用于排版、配色方案等等)。

*   `_variables.scss`
*   `_mixins.scss`
*   `_functions.scss`
*   `_placeholders.scss`(通常称为`_helpers.scss`)

### 布局

`layout/`目录(有时称为`partials/`)通常包含许多文件，每个文件为布局的主要部分(页眉、页脚等)设置一些样式。它还包含`_grid`文件，这是用于构建布局的网格系统。

*   `_grid.scss`
*   `_header.scss`
*   `_footer.scss`
*   `_sidebar.scss`
*   `_forms.scss`

将导航文件(`_navigation.scss`)放在这个文件夹中可能是有意义的，尽管我习惯将它放在`components/`(见下一节)。我认为放在`/layout`文件夹里会更好，但我会让你决定。

### 成分

对于较小的组件，有一个`components/`文件夹(通常称为`modules/`)。虽然`layout/`是一种*宏*(定义全局线框)，但`components/`更像是*微*。它可以包含各种特定的模块，如滑块、加载器、小部件或其他类似的东西。由于你的站点~~是~~通常会有很多文件在`components/`中，应该大部分是由微小的模块组成的。

*   `_media.scss`
*   `_carousel.scss`
*   `_thumbnails.scss`

### 页

如果你有特定于页面的样式，我认为把它们放在一个`pages/`文件夹和一个以页面命名的文件中是很酷的。例如，主页有非常特殊的样式并不少见，所以在`pages/`中有一个`_home.scss`文件来处理这个问题。

*   `_home.scss`
*   `_contact.scss`

根据您的部署过程，这些文件可以单独调用，以避免在生成的样式表中与其他文件合并。这真的取决于你。在我工作的地方，我们决定把它们变成非部分，以便只在需要它们的页面上包含它们。例如，我们的主页有一个非常具体的布局，编译成大约 200 行 CSS。为了防止这些规则被加载到每个页面上，我们只在主页上包含这个文件。

### 主题

如果你像我一样在一个有多个主题的大型网站上工作，拥有一个`themes/`文件夹是有意义的。你可以把你所有的主题/设计风格都放在那里。这肯定是特定于项目的，所以确保只有在你觉得有必要的时候才包括它。

*   `_theme.scss`
*   `_admin.scss`

### 供应商

最后但同样重要的是，您可能会有一个`vendors/`文件夹，其中包含来自外部库和框架的所有 CSS 文件——Bootstrap、jQueryUI、FancyCarouselSliderjQueryPowered 等等。将这些放在同一个文件夹中是一个很好的方式来告诉“嘿，这不是我写的，不是我的代码，不是我的责任”。

示例:

*   `bootstrap.scss`
*   `jquery-ui.scss`
*   `select2.scss`

顺便说一下，在我工作的地方，我们也有一个`vendors-extensions/`文件夹，在那里我们存储文件，覆盖来自供应商的一些小文件。例如，我们有一个`_bootstrap.scss`文件，我们可以用它来改变 Bootstrap 中的一些组件。这是为了避免编辑供应商文件本身，这通常不是一个好主意。

* * *

差不多就是这样。这种架构可能会根据项目的不同而有所不同，但我相信您已经了解了这个概念。在文件夹中嵌套文件夹，我不总是反对，但我不喜欢这样。我发现在大多数情况下，一个层次的架构足以保持事物的整洁和有序，而不会增加太多的复杂性。但是如果你认为你的项目需要一个更深层次的结构，请随意。

*专业提示:如果你觉得你的架构对任何打开`scss`文件夹的人来说都不明显，你可以考虑在根级别添加一个`README.md`文件(与`main.scss`并排)来解释一切。*

### 文件也很酷！

我经常被问到的一个问题是*“多少个文件是太多的文件？”对此我的回答是:文件永远不会太多。将你的工作分成几个文件旨在帮助你组织你的代码。如果你觉得某样东西应该被分成很多份，那就尽情享受吧！正如克里斯·科伊尔在他的[萨斯风格指南](http://css-tricks.com/sass-style-guide/)中所说:*

> "分成尽可能多的小文件."
> —克里斯·科伊尔

但是我建议不要将一个组件分解成几个文件，除非你有很好的理由这样做。通常我倾向于每个文件有一个模块——不多不少——有一个干净的名字，就像它所代表的模块的名字一样。这样，当我在寻找某样东西时，我可以在 Sublime text 中快速“找到”。

## 概括起来

本文中的所有建议都是基于我在法国大型银行集团农业信贷银行的网络分支机构中担任唯一前端开发人员的个人经历。你自己的环境和经历可能需要不同的方法。

如果我们可以选择一个构建 Sass 项目的黄金法则，它可能是简单的:选择有意义的东西。如果你在前端作为一个团队工作，确保每个人都对所选择的结构感到舒适，否则在某个地方发布文档，以便每个人都可以理解发生了什么。

你对 Sass 架构有什么想法或建议吗？我们很想听听你的意见。

> “权力越大，责任越大。”
> —潜水侠

*这篇文章在[网上其实也有韩文](http://www.webactually.co.kr/archives/13106)* 。

## 分享这篇文章