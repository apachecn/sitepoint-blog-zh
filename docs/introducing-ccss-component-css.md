# CCSS 简介(组件 CSS)

> 原文：<https://www.sitepoint.com/introducing-ccss-component-css/>

CCSS(Component CSS)是一种架构，它简化了大型 web 应用的 CSS 创作体验。

大型 web 应用程序通常有许多 CSS 文件，并且经常有许多开发人员同时处理这些文件。随着如此多的框架、指南、工具和方法(OOCSS、SMACSS、BEM 等)的出现。)，开发者需要一个可维护、可管理、可伸缩的 CSS 架构。

作为一名前端工程师，我相信基于组件的 web 开发是前进的方向。Web 组件是 W3C 的一系列标准。它们允许我们将标记和样式打包成可重用的 HTML 元素，这些元素是真正封装的 T2。这意味着我们需要开始考虑**基于组件的 CSS 开发**。当浏览器制造商正在实现这些标准时，我们可以同时使用*软封装*。

让我们来看看 CCSS 到底是什么，以及在为一个复杂的 web 应用程序设置 CSS 架构时如何使用它。

## CCSS 元素

以下是为实现 CCSS 体系结构的最佳配置而完全使用或修改使用的主要元素。

### SMACSS

由 Jonathan Snook 创建的 SMACSS 代表 CSS 的可扩展和模块化架构。与其说它是一个僵化的框架，不如说它是一个风格指南。阅读[smacs](http://smacss.com)了解 CCSS 使用该结构的背景。

### 不列颠帝国勋章

BEM，由开发者在 [Yandex](https://www.yandex.com/) 创建，代表“块”、“元素”、“修改器”。它是一种前端方法论，是开发 web 界面时的一种新的思维方式。Yandex 的人想出了 BEM，更多信息可以在 Harry Roberts 的优秀文章中找到[。](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)

### 厚颜无耻

[萨斯](http://sass-lang.com/)是拥有[超能力](http://davidwalsh.name/future-sass)的 CSS。我强烈推荐它，但是如果你喜欢，你也可以少用。更多信息请参考 [Sass 文档](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)。

### 指南针

[指南针](http://compass-style.org/)没有类别定义；它是 Sass 的扩展，提供了许多实用程序。它用于一般有用的 mixins 和 sass 编译。在需要厂商前缀的情况下，Compass mixins 应该几乎总是使用*。这又是一款好喝的[波本](http://bourbon.io/)，乍一看是一个很棒的替代品。*

 *## CCSS 原则

现在让我们看看 CCSS 的主要原则。

### 基于组件的

编写可重用的小而独立的组件。可重用的 CSS 组件不仅仅存在于 DOM 树的特定部分，也不需要使用某些元素类型。如果有必要，应该使用额外的 HTML 元素来使组件可重用。

### 模块化和孤立的

组件应该拥有用户界面某一部分所必需的一切，并且有一个焦点。每个组件都应该是独立的，这意味着它不直接修改或依赖于另一个组件。

隔离比跨组件的代码重用更重要，因为重用会增加依赖性和紧密耦合，最终使 CSS 更难管理。

### (音乐)可设定的

当以一种旨在减少花费在编写 CSS 上的时间的方式创作 CSS 时，人们应该考虑这样一种方式，即花费更多的时间来修改元素上的 HTML 类以修改或添加样式。对于所有开发者来说，编写 CSS 就像组装乐高积木一样，这比打一场 [CSS 战争](https://s3.amazonaws.com/zeroviscosity/d3-js-step-by-step/step-3-adding-a-legend/css.gif)要容易得多。CSS 类是构建样式的基础。

### 可预言的

可预测性意味着当你创作 CSS 时，你的规则会如你所愿。这对于拥有许多页面的大型应用程序非常重要。避免使用过于复杂的选择器和类名，因为这些会导致不可预测的 CSS。

### 证明文件

大多数人认为 CSS 是不言自明的。其实通常不是这样！CSS 组件必须有清晰的文档来描述它们做什么以及应该如何使用它们。

## 目录结构

下面是一个便于可视化的目录结构示例。我还在 [CCSS GitHub repo](https://github.com/sathify/CCSS) 中包含了一个示例设置。

```
styles
    ├── bootstrap.css
    ├── ext
    │   ├── bootstrap
    │   │   ├── _settings.scss
    │   │   └── bootstrap.scss
    │   └── font-awesome
    │       └── font-awesome.scss
    ├── font-awesome.css
    ├── images.css
    ├── main.css
    └── scss
        ├── _config.scss
        ├── base
        │   ├── _animation-classes.scss
        │   ├── _base-classes.scss
        │   ├── _base.scss
        │   └── images.scss
        ├── components
        │   ├── directives
        │   │   ├── _empty-state.scss
        │   │   ├── _legend.scss
        │   │   └── _status-message.scss
        │   ├── pages
        │   │   ├── _404.scss
        │   │   └── _redirect.scss
        │   └── standard
        │       ├── _alarm-state.scss
        │       ├── _graph-message.scss
        │       └── _panel.scss
        ├── main.scss
        ├── mixins
        │   ├── _animation.scss
        │   ├── _bem.scss
        │   └── _icon.scss
        └── themes
            └── _light.scss
```

仅编辑/创作上述文件夹树中`scss/`文件夹中的文件。这允许容易地更新外部库，它们在`ext/`文件夹中。许多应用程序从外部 CSS 框架开始，如 Bootstrap 或 Foundation，所以我在`ext/`的示例设置中添加了它们。但是让所有的 CSS 从头开始写绝对没问题；上面提到的其他一切仍然适用。

示例目录`components/`非常适合 AngularJS 应用程序，但也可以为其他框架或应用程序定制。更多信息在下面的“架构”部分。

在 HTML 页面中，包含所有来自`style/`文件夹的`.css`文件，其中包含所有编译好的 CSS(来自 Grunt、Compass 等。).永远不要改变它们。

## 命名惯例–简化边界元法

*   `u-className`–全球基础/公用事业类
*   `img-className`–全局图像类别
*   `animate-className`–全球动画课程
*   `ComponentName`–标准组件(B)
*   `ComponentName-elementName`–组件的元素(E)
*   `ComponentName--modifierName`–组件的修改量(M)

注意`UpperCamelCase`组件名称样式，以表明它是主元素；这表示它是组件的*边界。元素和修改器的名字分别是`elementName`和`modifierName`。不要使用连字符(`-`)来分隔组件名称，因为这些表示元素/元素名称的开始。*

## 建筑和设计

让我们考虑一下 CCSS 鼓励的建筑。

### 咕哝

Grunt 是一个很棒的任务运行器，它可以自动化许多常见的杂务(比如编译 CSS 或验证 HTML)。还有其他的任务跑者；一个理想的工作流包括使用一个来监视开发中的文件，并在发生变化时重新编译 CSS。

### 文件组织

再看一下目录结构，它是从 SMACSS 派生出来的。注意`ext/`目录，它包含所有外部框架(比如 Bootstrap)。为了保持升级容易，这些不应该被修改；相反，覆盖和扩展应该放在`base/`目录中。

是保存应用程序范围内使用的全局基本样式的地方。

`_base.scss`仅是元素选择器的基本样式。这是一种“CSS 重置”。

是否所有实用程序类都在应用程序范围内跨多个页面、视图和组件使用。给类名加上前缀`u-`

`images.scss`用作 SCSS 编译源。它应该定义和内联所有网站图像作为数据 URIs。`/app/styles/images.css`就是从这个文件生成的。

`_animate.scss`保存所有应用程序范围的动画类。

`_bootstrap-overrides.scss`仅保存框架覆盖。有时，框架选择器的特定性水平如此之高，以至于覆盖它们需要很长的特定选择器。全球级别的覆盖不应在 SCSS 组件的环境中进行。相反，所有全局覆盖都在这里。

### 成分

上面没有提到的任何可重用 CSS 单元都被认为是一个“组件”。我们使用 AngularJS，所以我把它们分为 3 类 CSS 组件:视图/页面、指令和标准；因此，目录结构是从 SMACSS 派生出来的。

在 [GitHub 库](https://github.com/sathify/ccss/tree/master/styles)的示例设置中，为了清晰起见，我创建了显式文件夹。如果你的应用程序很小，你可以把它们放在一个文件夹里。所有组件都遵循[修改后的 BEM 命名惯例](https://github.com/sathify/ccss#naming)与茶包相结合。这让我在鼓励其他团队成员遵循 BEM 风格的语法方面取得了巨大的成功。当不再使用带有`-`、`--`和`__`字符的典型 BEM 样式时，它也避免了许多混淆，这些字符会生成类似`module-name__child-name--modifier-name`的类名！

组件中的 CSS 类定义顺序反映 HTML 视图也很重要。这使得扫描、样式化、编辑和应用类变得更加容易。最后，为 web 应用程序准备一个广泛的风格指南，并遵循 CSS 和 Sass 的[指南(例如，避免`@extend`)是一个好主意。](https://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml)

## CCSS 的例子

[参考代码](https://github.com/sathify/ccss/tree/master/styles)了解 CSS 的示例设置。

以下是 Sass 中的一个示例组件:

```
.ProductRating {
  // nested element
 @include e(title) {
    ...
  }
  // nested element
 @include e(star) {
    ...
    // nested element's modifier
 @include m(active) {
      ...
    }
  }
}
```

这会编译成以下 CSS:

```
.ProductRating {
  ...
}
// nested element
.ProductRating-title {
  ...
}
// nested element
.ProductRating-star {
  ...
}
// nested element's modifier
.ProductRating-star--active {
  ...
}
```

您的 HTML 可能看起来像这样:

```
<div class="ProductRating">
  <img alt="Company logo" class="img-logo">
  <h3 class="ProductRating-title">Title</h3>
  <div class="u-starHolder">
    <span class="ProductRating-star ProductRating-star--active"></span>
    <span class="ProductRating-star ProductRating-star--active"></span>
    <span class="ProductRating-star ProductRating-star--active"></span>
    <span class="ProductRating-star"></span>
  </div>
</div>
```

可以参考简化的 [BEM mixin](https://github.com/sathify/ccss/blob/master/styles/scss/mixins/_bem.scss) ，它使用参考选择器来实现这个，比`@at-root`更简单。在 Sass 3.3+中，使用 BEM 变得更加容易，这使我们能够编写易于理解的可维护代码。

## 贡献的

在[GitHub repo](https://github.com/sathify/CCSS)中以 issues/PRs 的形式做出贡献，用于添加更多示例、改进后处理、澄清等。非常有帮助。

一定要查看回购信用和有用的资源与 CCSS 有关。如果您有任何问题或意见，请在下面的评论中或 GitHub repo 上发表。

## 分享这篇文章*