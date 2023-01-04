# 萨斯和波旁威士忌入门

> 原文：<https://www.sitepoint.com/getting-started-sass-bourbon/>

在本文中，我希望通过向您展示如何安装 [Sass](http://sass-lang.com/) 和 [Bourbon](http://bourbon.io/) ，并讨论如何使这些工具成为您工作流的一部分，让您开始预编译或预处理您的 CSS。

我还将向您介绍一些学习资源，这样您就可以掌握这些知识，并开始探索这种强大的 CSS 开发方法的潜力。

## 普通 CSS 有什么问题？

如果你和我一样热爱 CSS，你必须知道你是我们中为数不多的一个。对我来说，CSS 的强大和多功能性是一种享受，当我设法解决一个棘手的跨浏览器显示问题或用一些优雅的小 CSS 动画避开资源密集型 JavaScript 路由时，我会有一种身体上的兴奋。

但是那些不像你我一样对 CSS 着迷的人有一些合理的抱怨。例如，在不断增长的 CSS 代码库中，很难跟踪具有不同权重和位置的元素之间的属性继承。没有变量支持进行需要在多个地方引用的通用更改。为了使设计在不同的浏览器上保持一致，经常需要创建难以维护的冗余代码。

对我来说，处理这些和其他类似问题的最优雅的方法是由汉普顿·卡特林创造的，并且是开源的，所以整个社区都可以帮助它发展。我说的是 [Sass](https://www.sitepoint.com/sass-basics-operators/) ，CSS 的预处理器，让你写干净的，可维护的，结构化的代码，生成高效有用的 CSS。

由于 Sass 支持编写新的和原始的扩展，开发人员可以更容易地提出自己的改进。我首先去的是一个叫波旁的萨斯图书馆。虽然 Sass 提供了预编译 CSS 的通用工具集，但 Bourbon 以增强的 Sass 功能为基础，可以更快更容易地编写易于维护和共享的简洁代码。

## 使用 Sass

有几种方法可以开始使用 Sass。你可以安装 [Ruby gem](https://rubygems.org/) ，使用包含 Sass 的开发套件(比如 Mac app [CodeKit](https://incident57.com/codekit/) )，或者利用 LibSass 库在 Node 或 c 等语言中实现原生编译性能

一旦安装了 Sass，基本的工作流程就是使用 Sass 语言编写 CSS，然后将其编译成任何浏览器都可以阅读和理解的常规 CSS。

### 安装 Sass

您用来安装 Sass 的方法是个人的选择，它很大程度上取决于您的首选开发工作流和您的整体技能水平。Ruby 是运行 Sass 最方便的方式之一。我用的是 Mac，我敢肯定很多前端开发者都是这样，也就是说我预装了 Ruby。所以我假设您已经安装了 Ruby，并且正在工作站的终端上运行您的命令。(在不同的环境中安装和运行 Ruby 有很好的[指南，如果你使用的是 Windows，你可以查看](https://www.ruby-lang.org/en/installation/)[这篇文章](http://www.impressivewebs.com/sass-on-windows/)来帮助设置。)

一旦您进入终端，并且您已经验证您已经安装了 Ruby 1.9.3 或更高版本的稳定版本，您可以发出以下命令来安装 Sass gem:

```
gem install sass
```

然后，您可以通过对 Sass 进行版本检查来确保 Sass 正在工作:

```
sass -v
```

这应该向您显示 Sass 已经安装并正在运行，并告诉您您拥有的版本。对于本教程来说，高于 3.3 的任何版本都可以正常工作。

## Sass 基础

手动使用 sass 非常简单，只需传递. Sass 或。scss 文件，它包含一些用 Sass 编写的代码到`sass`命令，还有一个你希望编译的 css 的目的地。例如，打开文本编辑器，在新文件中编写以下内容。

```
.special
background: #990000
p
color: #ffffff
```

你会看到这看起来像 CSS，但它是最小的。而不是花括号。sass 语法(是旧的但仍然有效的 Sass 语法)使用嵌套缩进。在`.special`选择器中嵌套`p`选择器表明颜色将只应用于`.special`元素的子元素`<p>`元素。

您可以使用制表符或空格，但一定要保持缩进一致。使用`.sass`语法的 Sass 特别注重缩进。因为 Sass 使用文本的格式来反映选择器的嵌套层次，所以如果在它不期望的地方有多余的空间，它就不能正确编译。

将文件保存为当前目录中的“test.sass”。然后在命令提示符下运行以下命令:

```
sass test.sass test.css
```

这应该会在相同的目录中生成一个`test.css`文件，其中包含您的 Sass 代码的普通 CSS 编译版本，看起来像这样:

```
.special {
background: #990000; }
.special p {
color: white; }
```

你看到萨斯做了什么吗？除了添加花括号和分号之外，Sass 还解释了`.special`选择器中的`p`元素的嵌套，并创建了一个新的选择器，将属性定义限制为位于具有“特殊”类的元素中的`p`元素。格式可能有点奇怪，但是任何浏览器都会正确地解释这个 CSS。(是的，有不同的方法可以让 Sass 格式化你的 CSS 输出。)

## 选择语法

native 的最小语法。sass 文件很漂亮，但不是强制性的。引入 Sass 后，开发了一种新的语法，允许开发人员用更熟悉的类似 CSS 的语法(称为 SCSS，Sassy CSS)编写预编译代码。我们之前使用的相同代码可以这样编写并保存在. scss 文件中，Sass 编译器也会这样处理它:

```
.special {
background: #990000;
p {
color: #ffffff;
}
}
```

所以这段代码可以保存为`test.scss`，通过 Sass 编译器发送它将会得到与从`test.sass`文件生成的 CSS 功能相同的 CSS。

与`.sass`语法不同，使用`.scss`语法时，缩进是可选的(尽管为了使代码更容易阅读，缩进仍然很有用)。用`.scss`语法(允许常规 CSS)编写的选项改善了学习曲线，并帮助 Sass 在 web 开发人员中如此流行。

## 自动编译成 CSS

没有一个开发网页的人想在每次对样式进行调整时都要记住将他们的 Sass 编译成 CSS。为了使这个繁琐的过程更加方便，您可以给 Sass 一组首选项来定义您希望 CSS 文件生成的位置，并设置它在后台运行并“监视”您的目录`.sass`和/或。scss 文件，每次保存更改时自动将它们编译成原生 css。

在您之前保存测试文件的同一个目录中，尝试通过终端发出以下命令:

```
sass --watch test.sass:test.css
```

然后进入你的`test.sass`文件，做一个改变，就像这样:

```
.special
background: #990000
p
color: #ccccff
```

这里的`color`值已被修改。您应该能够立即查看您的`test.css`文件，并看到那里反映的更新:

```
.special {
background: #990000; }
.special p {
color: #ccccff; }
```

您还可以将一组目录传递给`--watch`选项，这些目录中的所有文件都将被自动管理。

一旦您安装并运行了 Sass，您将受益于许多非常有用的 Sass 特性，这些特性可以增强您的 CSS 管理。除了嵌套语法的灵活性之外，您还可以获得变量、计算、条件表达式、导入、扩展、继承以及大量其他特性和功能。

Sass 已经成为一个非常受欢迎的 CSS 预处理器，除了[一个非常全面的文档站点](http://sass-lang.com/guide)，还有很多关于[如何开始使用 Sass](https://www.sitepoint.com/getting-started-with-sass/) 的很棒的教程。

但是你不必把自己局限于基本的口语。

## 混合一些波旁威士忌

Sass 如此强大的原因之一是它提供的可扩展性。一旦您的项目中有了 Sass，您就可以构建自己的 mixins 来简化您的个人工作流，并自动化繁琐和重复的代码。一个有创造力的开发人员可以让 Sass 支持最复杂或最优雅的 CSS 设计方法。

为 Sass 构建一个定制工具库既实用又有趣，但是有一些明显的方便之处，您可能不想或不需要从头开始创建。幸运的是，有许多库可以帮助支持 Sass 开发人员。我最喜欢的一个是来自于友好的 thoughtbot 的人们，他们为我们所有人提供了一个轻量级的可扩展的 Sass 插件库，名为 T2 波旁酒，它为你提供了大量的基本工具，全部用 Sass 编写。

在 Sass 停止的地方，Bourbon 填充了一些功能，如跨浏览器供应商前缀、高级 CSS 功能(如动画和边框图像属性)的 mixins、包括渐变和颜色色调在内的计算功能，以及字体系列和按钮样式等附加功能。Bourbon 还支持自定义全局编译设置，因此您可以微调它在应用程序中的行为方式。

### 安装波本威士忌

再次假设您将使用 Ruby 1.9.3 或更高版本的命令行，并且 Sass 已经在工作，安装 Bourbon 几乎和安装 Sass 一样简单。只需输入以下命令，将波旁威士忌添加到您的系统中:

```
gem install bourbon
```

要查看 Bourbon 的工作情况，请确保您位于与上面的测试文件相同的目录中，并键入以下命令:

```
bourbon install
```

这将创建一个包含所有核心 Bourbon 文件的子目录。你应该把这个子目录当作第三方库，也就是说进去修改里面的文件不是个好主意。相反，您应该用 Sass 导入它们，然后在您自己的代码中使用它们提供的强大工具。

如果您让上面的`sass --watch test.sass:test.css`命令保持运行，您可能会注意到这样做不会触发对 CSS 的任何更新。这是因为 Bourbon 是一个 Sass 库，需要从您的主`.sass`或`.scss`文件中直接调用。

要导入波本威士忌，只需编辑您的`.sass`文件，如下所示:

```
@import bourbon/bourbon
.special
background: #990000
p
color: #ccccff
```

这一次，当您保存时，您将看到重新编译的 Sass 代码。但是输出的 CSS 会完全一样。这是萨斯和波旁威士忌的优点之一。您可以导入整个库，并且代码不会发生任何变化，除非您显式调用一些特殊的 Bourbon 功能。

### 你的第一杯波旁威士忌

Sass 允许您定义名为 [mixins](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#mixins) 的本机函数，它可以接受参数。您可以在 Sass 代码中用`@include`指令调用它们。波旁威士忌通过加入各种预定义的混音来施展魔法。例如，您可以从 Bourbon 免费获得的一个很棒的特性是能够生成特定于浏览器的 CSS 前缀，而无需所有重复的代码。

使用 Sass 和 Bourbon，您可以应用一个简单的 mixin 来添加特定于浏览器的支持，以便使用一行 Sass 实现更高级的 CSS 功能:

```
@import bourbon/bourbon
.special
@include linear-gradient(#990000, #000000)
p
color: #ccccff
```

您更新的 CSS 文件现在将有一些额外的行，反映了 Bourbon 所做的额外工作，以帮助您的线性渐变背景支持供应商前缀，并为本身不支持 CSS 渐变的浏览器优雅地降级:

```
body {
background-color: #990000;
background-image: -webkit-linear-gradient(#990000, black);
background-image: linear-gradient(#990000, black); }
body p {
color: #ccccff; }
```

## 了解更多信息

至此，我已经向您展示了如何安装 Sass 和 Bourbon，如何将 Sass 文件编译成 CSS，以及如何导入和利用 Bourbon 的补充功能。下一步是深入研究 Sass 的文档[和 Bourbon 的文档](http://sass-lang.com/guide)和，看看每个文档都提供了哪些功能，可以简化您的工作流程并提高您的工作效率。

网上也有很多学习如何日常使用 Sass 的优秀资源，包括来自 Sass 大师的例子，如 [Kitty Giraudel](https://www.sitepoint.com/author/kittygiraudel/) ，像[Sass Way](http://thesassway.com/)，一份[每周简讯](http://thesassway.com/)，以及[一个活跃的 Sass 社区，有会议和活动](http://sassconf.com/)，在那里你可以学到更多。甚至还有一本由 Dan Cedarholm 写的很棒的书叫做《网页设计师的 Sass》,这是一个很好的开始或者建立在我们这里讨论的基础上的方法。

关于波旁威士忌的在线资源不多，但你可能想看看这个视频和 T2 的 Thoughtbot 网站，它们展示了这个令人惊叹的图书馆的深度和力量。

萨斯和波旁威士忌的结合是一个强大的工具集，可以增加你的曲目，我甚至还没有提到一些可选的波旁威士忌的增强功能，如[纯](http://neat.bourbon.io)和[苦味酒](http://bitters.bourbon.io)。如果你很好奇，请给我留下反馈，让我知道你想了解更多的信息。

## 分享这篇文章