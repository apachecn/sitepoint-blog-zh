# 面向 WordPress 开发者的 Sass

> 原文：<https://www.sitepoint.com/sass-for-wordpress-developers/>

![Sass for WordPress Developers](img/9ba69d4e27600ea52303714a4a59b7ca.png)

Sass 对 WordPress 开发者有很多好处。到目前为止，您可能已经听到了许多使用预处理器的理由。CSS 预处理程序通过使用片段和嵌套样式为更好的代码组织提供了机会。预处理器通过编写混合和函数来帮助开发人员更快地设计风格。预处理器还允许我们用逻辑和变量编写更易维护、更易扩展的代码。

## 将样式表转换为 Sass

在 WordPress 开发中开始使用 Sass 的最好方式是使用包含 Sass 文件的主题。下划线主题是我最喜欢的新主题的开始。但是，如果您从一个不包含 Sass 文件的主题开始，您需要将现有的样式表转换成 Sass。

好消息是如果你使用。scss 语法(我推荐)，你现有的 css 都是有效的 Sass。你可以简单地将`style.css`复制到`style.scss`中。scss 文件将正确编译。

### 设置分音

在这一点上，你有很长的时间。scss 文件，代码量与原始 css 文件一样多。您现在可以使用变量和混合，但是这仍然没有组织得更好。我们可以通过将长样式表分解成更小的文件来得到一些非常需要的组织。将`style.css`的每个“部分”复制到一个单独的。Sass 文件夹中的 scss 文件。以包含的内容命名该文件，并带有下划线前缀。例如，与导航相关的风格会转到`_navigation.scss`。

将代码移动到分部之后，需要将该分部导入到主 style.scss 文件中。本质上，您将把所有的导航代码复制到`_navigation.scss`，并在`style.scss`中用一个导入命令替换它。*注意:导入中的文件名没有下划线或扩展名。*

【sass】
@导入‘导航’；
[/萨斯]

CSS 仍然是级联的(早期的样式被后来的相同特性的样式覆盖)，所以一定要保证这些`@import`语句按照你最终输出需要的顺序。

### 必要时重构

一旦将长样式表分解成较小的部分(这样可以更容易地找到代码)，就可以对这些部分进行重构。这一步有助于使事情更容易维护，但我不认为这是必不可少的。重构的一种方法是嵌套选择器。如果您在某个父选择器中定义了几个样式，那么您的原始样式表中就会有这样的样式:

【萨斯】
。标题。logo {
//样式
}
。标题。标语{
//风格
}
。标题。菜单{
//样式
}
[/sass]

通过像这样嵌套子选择器，可以重构这些样式，使它们更容易扫描和编辑。下面的 Sass 块将编译成上面的 CSS。

【萨斯】
。表头{
。logo {
//样式
}
。标语{
//样式
}
。菜单{
//样式
}
}
[/sass]

您还可以通过用短混合替换重复的前缀属性来进行重构。

【萨斯】
。菜单 a {
-WebKit-过渡:彩色 0.2s
过渡:颜色 0.2s
}
[/萨斯]

一个混血儿也可以做同样的事情。*注意:我在这里没有包含`@mixin`语句:我推荐使用 Bourbon 或 Compass mixin 库来实现这样的标准功能。*

【萨斯】
。菜单 a {
@包含过渡(颜色 0.2s)；
}
[/萨斯]

*进一步注意:我实际上不推荐使用 mixins 作为前缀: [AutoPrefixer](https://github.com/postcss/autoprefixer) 是一个好得多的解决方案，但那是另一篇文章。*

## 为 WordPress 编译你的 Sass

既然我们已经分解了这个大文件:我们如何把它放回一起？

### 编译器要求

WordPress 对 CSS 输出有一些独特的要求。这个文件需要被命名为 style.css，它必须位于主题根目录中，并且它需要在顶部有一个特殊的 WordPress 注释块。

除了那些 WordPress 特有的考虑因素，我还会为我们的编译过程添加以下需求。最好将所有的 Sass 文件保存在一个子目录(`/sass/`)中，这样我们可以保持主题根的干净。我们还应该能够为我们部署的代码压缩 CSS 输出。

### GUI 编译方法

如果您喜欢使用应用程序来编译 Sass，以下应用程序提供了一个 GUI 来选择和查看的文件夹。scss 文件。

*   [码数](http://incident57.com/codekit)(30 美元)
*   [考拉](http://koala-app.com)(免费)
*   [侦察兵](http://mhs.github.io/scout-app)(免费)
*   [compass . app](http://compass.kkbox.com)(10 美元)

### CLI 编译方法

我不经常使用这些应用程序:我更喜欢命令行。有两个工具可以从命令行编译 Sass:Sass 和 Compass。可以找到安装 Sass 和 Compass 作为命令行工具(Ruby gems)的说明: [Sass](http://sass-lang.com/install) / [Compass](http://compass-style.org/install/) 。

Sass 的 CLI 提供了`sass`命令，用于将 Sass 输入文件编译成 CSS 输出:`sass style.scss:style.css`。当然，每次修改 Sass 部分内容时重新运行该命令并不理想。您可以添加`--watch`参数，Sass 将观察。scss 文件(及其导入的部分)并在每次检测到变化时编译:`sass --watch style.scss:style.css`。

通过使用 Compass 而不是 Sass，我们可以进一步改进我们的编译方法。Compass 最大的好处是它对一个`config.rb`文件进行操作。您可以将您的配置选项(扩展与压缩、输入&输出目录等)保存在一个文件中，然后告诉 Compass 通过在包含`config.rb`的目录中运行`compass watch`来监视和编译您的 Sass。

以下是我如何使用我的指南针文件:

*   它生活在`/{theme-name}/sass/`
*   它输出`style.css`到主题根:`css_dir = '..'`
*   的。scss 文件位于`/sass/`目录中:`sass_dir = ''`
*   开发期间:`output_style = :expanded`
*   对于部署:`output_style = :compressed`

### WordPress style.css 注释

正如我们上面提到的，WordPress 要求在主题样式表的顶部有一些特定的注释。我们可以在我们的`/sass/`目录中控制`style.scss`中的那些。由于 Compass 将删除带有`output_style = :compressed`的注释，我们需要在注释块的开头加一个感叹号(！)来保留这些评论。

【萨斯】
/*！
主题名称:萨斯主题
主题 http://jamessteinbach.com/themes/sassy/ URI
作者:詹姆斯·斯坦巴克
作者 URI:http://jamessteinbach.com
描述:从 CSS 到萨斯样本主题代码
*/

//导入您的所有片段
[/sass]

## 组织你的部分

现在我们知道了如何将样式表转换成 Sass，然后再将它们组合起来，接下来让我们讨论如何组织 Sass 片段。首先要记住的是级联仍然重要。确保首先导入最广泛的样式，最后导入最具体的样式。

您可以使用文件夹来组织您的部分。对于文件夹中的一部分，您的`@import`语句将包括目录名，如下所示:

【sass】
@ import ' base/variables '；
//从/base/目录
[/sass]导入 _variables.scss

有很多种方法可以将你的部分归类到文件夹中。下面的“Sass 资源”列表包含了一些组织片段的好方法。这里有一个例子:

*   /base/(变量、混合、重置、排版)
*   /layout/(网格、页眉、页脚)
*   /供应商/(插件、供应商等)
*   /组件/(按钮、菜单、表单、小部件)
*   /pages/(主页、登录页、作品集)

![Underscores.me Sass Structure](img/c27e6d441833bb74dc5388e5ff828107.png)

## Sass 资源

我们只是触及了使用 Sass 构建 WordPress 主题的表面。如果这是你第一次阅读这个主题，你可能会想，“我在哪里可以得到更多的信息？”下面的列表提供了更多关于使用 Sass 进行 WordPress 开发的方法的信息。他们的一些建议与这篇文章不同:你可以遵循最适合你的道路。

### 附加 Sass 阅读

*   [“指南针编译和 WordPress 主题”，克里斯·科伊尔](http://css-tricks.com/compass-compiling-and-wordpress-themes/)
*   [“如何在 WordPress 中使用 Sass”，Andy Leverenz](http://elegantthemes.com/blog/tips-tricks/how-to-use-sass-with-wordpress-a-step-by-step-guide)
*   [“萨斯项目的架构”，雨果·吉劳德](https://www.sitepoint.com/architecture-sass-project/)

如果你有其他的资源推荐或者经验分享，请在评论中发表！

## 分享这篇文章