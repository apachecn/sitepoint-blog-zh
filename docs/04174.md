# inuitcss 简介:一种不同的 css 框架

> 原文：<https://www.sitepoint.com/introduction-inuitcss-different-kind-css-framework/>

在过去的几年中，前端框架的使用越来越多。这些工具，如 Bootstrap 和 Foundation，允许开发者快速创建网站和应用程序。这些框架为你做了所有繁重的工作，而不需要你自己设计按钮和导航条。

这些框架的问题是，开发人员下载他们庞大的样式表是很常见的，即使框架的大部分功能都不会被使用。但是，如果我们使用一个对我们的设计假设很少的框架，并鼓励更模块化的架构，这不是很好吗？

## inuitcss 简介

inuitcss 是一个基于 Sass 的框架，由 [Harry Roberts](http://csswizardry.com/) 创建。因纽特人的美丽在于，你是如何创造它的。inuitcss 由一组独立的模块组成，而不是一个中央代码库。inuitCSS 让我们拼凑出自己的架构，而不是包含一堆我们永远不会用到的模块。

与其他框架不同，inuitcss 没有为您提供很多 UI 元素。通常，当使用一个框架时，我们会根据自己的喜好调整现有的组件。Inuitcss 拒绝这种方法，而是支持让您做出自己的设计选择。

## 安装 inuitcss

我们可以手动下载并导入组成 inuitcss 的模块，但更简单的选择是使用[鲍尔](http://bower.io/)或 [NPM](https://www.npmjs.com/) 。Bower 和 npm 是包管理器，为您管理依赖项和脚手架项目。因为两者都是基于 [Node.js](https://nodejs.org/) 的，你需要先安装 Node.js。

我们可以使用 Bower 创建一个安装了所有 intuitcss 依赖项的新项目。我们还可以用它来初始化一个现有的项目。

### 安装和使用凉亭

安装 Node.js 后，打开命令提示符并键入:

```
npm install -g bower
```

这么简单，你已经安装了鲍尔。如果您有一个现有的项目，现在可以使用 Bower 来添加 inuitcss。导航到您的项目文件夹并运行:

```
bower init
```

这将创建一个`bower_components`目录。要手动导入单个模块，请使用以下命令:

```
bower install --save inuit-(module-name)
```

例如，要导入 inuitcss 布局模块，我们将使用:

```
bower install --save inuit-layout
```

这将模块安装在`bower_components`目录中。或者，我们可以使用 inuitcss 初学者工具包来搭建一个新项目或添加到我们现有的项目中。

```
bower install --save inuit-starter-kit
```

这将为一个 inuitcss 项目安装基本的依赖项，而不是手动一个接一个地安装它们。

## 设置和导入订单

设置好初学者工具包后，我们需要导入组件。进口订单至关重要，需要遵守。如您所知，如果我们导入需要尚未导入的变量的组件，我们的 Sass 将会给出错误。按此顺序从初学者工具包导入以下文件(或手动安装以下模块)。

```
@import "bower_components/inuit-defaults/settings.defaults";
@import "bower_components/inuit-functions/tools.functions";
@import "bower_components/inuit-mixins/tools.mixins";
@import "bower_components/inuit-normalize/generic.normalize";
@import "bower_components/inuit-box-sizing/generic.box-sizing";
@import "bower_components/inuit-page/base.page";
```

由于 inuitcss 的模块化本质，按照正确的顺序将东西组合在一起是非常重要的。

**设置:**全局变量、站点范围设置、配置

**工具:**站点范围的混合和功能

**通用:**影响深远的规则集

**基本:**未分类的 HTML 元素

**对象:**对象、抽象和设计模式

**组件:** UI

**Trumps:** 助手类和覆盖

## 核心功能

即使 inuitcss 在设计上是模块化的，仍然有一些必需的模块。

所需的第一个模块是`settings.defaults`。这包括诸如`font-size`和`line-height`的设置，这些设置将在其余模块中使用。

`tools.functions`模块包含数学助手函数。这些函数用于创建对象的尺寸变量。

最后需要的模块是`tools.mixins`。这有一个字体大小混合，用于某些基于类型的模块。

除此之外，inuitcss 没有太多其他依赖项。如果任何模块有依赖关系，它们将由 Bower 管理(这就是我们使用 Bower 的原因)。

## 修改 inuitcss

即使 inuitcss 是完全可定制的，我们也不应该直接编辑框架的底层代码。通常我们会创建一个我们自己的覆盖文件，并操作框架的设置和变量。虽然这是可以接受的，但 Harry 建议使用另一种方法，即在导入文件之前传递变量。

记得我提到过带有变量`font-size`和`line-height`的`settings-defaults`文件。如果我们想改变它们的默认值，我们可以在导入之前传递这些变量。

```
$inuit-base-font-size:   12px;
$inuit-base-line-height: 18px;
@import "bower_components/inuit-defaults/settings.defaults";
```

我们能够像这样为所有模块传递变量。我们也可以创建一个覆盖文件，但是如果您这样做，请确保首先导入它。

## 模块和组件

默认情况下，inuitcss 模块的任何变体都是关闭的。例如， [inuitcss 按钮对象](https://github.com/inuitcss/objects.buttons)根据大小有不同的变体。默认情况下，这些是关闭的。启用它们就像在模块的 import 语句之前打开它们一样简单。

```
$inuit-enable-btn--large:   true;
@import "bower_components/inuit-buttons/objects.buttons";
```

关于 inuitcss 需要理解的一件主要事情是，除了 UI 组件中的一些按钮之外，它什么也不提供。Harry 将各个组件留给了您。如果你在寻找一个有现成 UI 组件的框架，那么 inuitcss 不适合你。

## 结论

inuitcss 是一种不同的框架，它让您自己选择设计。与一个有很多你永远不会使用的规则的大框架相反，inuitcss 真正让你挑选你想要使用的。框架是有用的，因为它不碍事，让你开始工作。

inuitcss 并不适合所有人。作为一个库，它仍然是一项正在进行的工作，但是整体概念是非常有前途的，并且当您需要一两个模块，但是不需要其他框架提供的臃肿的规则集时，它是完美的。

## 分享这篇文章