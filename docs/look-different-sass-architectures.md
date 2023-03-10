# 看看不同的 Sass 架构

> 原文：<https://www.sitepoint.com/look-different-sass-architectures/>

随着项目规模和复杂性的增长，您需要某种逻辑来构建您的 Sass 文件。在大型团队和项目中工作时，遵循一些约定的创建文件和文件夹的准则会有所帮助。下面是对目前使用的一些技术的回顾。

## [Bootstrap-sass](https://github.com/twbs/bootstrap-sass)

Bootstrap 的目的是成为一个 UI 库，供 web 开发人员快速起步。对他们来说，将所有变量组合在一个文件中并隐藏所有 mixin 逻辑是合乎逻辑的。他们的 Sass 架构模仿了这个想法。每个组件都有自己的 Sass 文件，还有一个`./_variables.scss`文件，它允许你控制项目中的所有变量。

Bootstrap 的 Sass 架构的独特之处在于它如何布局其 mixins。有一个`./_mixins.scss`文件。该文件从一个`mixins`文件夹中导入所有文件，该文件夹包含一个单独的文件，用于每个使用的 mixin。虽然按钮样式是在`./_buttons.scss`中定义的，但是它所使用的混音是从`./_mixins.scss`中导入的。这反过来又从`mixins/_buttons.scss`引入了按钮混合。 [Yo Dawg](http://knowyourmeme.com/memes/xzibit-yo-dawg) ！

除了组件级 mixin，mixin 文件夹还包含全局 mixin，比如`mixins/_border-radius.scss`和`mixins/_responsive-visibility.scss`。尽管 bootstrap 的 mixin 并不太复杂，但是这种架构更适合于 mixin 非常*复杂并且需要分解成更小部分的项目。或者在您希望对组件的视觉样式隐藏该逻辑的情况下。总之，这种架构最适合 Bootstrap，但可能不适用于*你的* sass 项目。*

以下是文件夹结构的外观:

```
bootstrap/
|– bootstrap.scss   # Manifest file 
|– _alerts.scss     # Component file 
|– _buttons.scss    # Component file 
|– _mixins.scss     # Mixin file – imports all files from mixins folder
|–  ...             # Etc..
|– mixins/
|  |–  _alerts.scss # Alert mixin
|  |– _buttons.scss # Button mixin
|  |– ...           # Etc..
```

## [Zurb 基金会](https://github.com/zurb/foundation)

Foundation 有一个经过深思熟虑的 Sass 架构，它非常适合定制，这是它最大的优势之一。在根级别有一个`settings.scss`，它允许你覆盖任何用于构建组件的变量。但是，每个组件文件都包含特定于该组件的变量。

Foundation 还在一个单独的`functions.scss`文件中抽象了项目中使用的函数。这很好，因为这些函数是特定于框架的，您不应该编辑这个文件。

所有的全局混合，如边界半径和过渡效果在`components/_global.scss`中可用。它们的逻辑结构如下:

```
- Import - global mixins
 - Component specific variables (`$button-tny`, `$button-margin-bottom`) 
 - Component specific mixins
 - Extends - (not the @extend but actual style definitions, where mixins are called)
```

所有特定于组件的变量都被定义为`!default`，因此可以使用`_settings.scss`覆盖它们。如果你喜欢保持简单，你可以改变`_settings.scss`中的变量，然后就到此为止。如果您更喜欢冒险，您可以尝试特定于组件的变量，并轻松地改变组件的外观。

因为它们的组件是由混合和变量构建的，所以它提供了最大的灵活性。许多使用 Foundation 构建的网站最终看起来都不一样。

大多数中小型网站都可以采用 foundation 的 Sass 架构。进入任何组件的范围并在同一个文件中拥有所有需要的变量、混合和扩展变得非常容易。

以下是文件夹结构的外观:

```
foundation/
|– foundation.scss           # Manifest file 
|– foundation 
|  |– _functions.scss        # Library specific functions 
|  |– _settings.scss         # Change variables for the entire project
|  |– components
|  |  |– _buttons.scss       # Component file (will import _globals.scss)
|  |  |– _globals.scss       # Global mixins
|  |  |– _alerts.scss        # Component file (will import _globals.scss)
```

## 戴尔·桑德的建筑

Dale Sande 在他的演讲“清理你的 Sass 垃圾抽屉”中，推荐了一种模块化的方法来组织你的 Sass 文件。这在企业级项目中非常方便，在这些项目中，模块的数量和视觉复杂度可能非常高。这种方法有助于将与模块相关的所有逻辑保留在它自己的文件夹中，这允许子模块以限定范围的方式扩展和重用样式。如果您喜欢在文件中将表示与 Sass 逻辑分开，那么它在中小型项目中也很有用。

遵循 Dale Sande 方法的一个主要优点是，它使得为特定模块创建单独的样式表变得容易。在项目内部，您可以加载一个包含全局样式的基本样式表，并加载另一个特定于模块的样式表。这有助于消除大量代码膨胀，提高性能和加载速度。

文件夹结构如下:

```
sass/
|– style.scss                        # Manifest
|– modules/ 
|  |– registration/                  # Sub Module
|  |  |–  _extends.scss              # Functional 
|  |  |–  _functions.scss            # Functional 
|  |  |–  _mixin.scss                # Functional 
|  |  |–  _module_registration.scss  # Imports functional partials and contains presentational 
|  |  |–  _module_personalInfo.scss  # Imports functional partials
```

## [样式原型](https://github.com/north/generator-style-prototype)

Sam Richard 的 Style prototypes 是一款出色的工具，为使用 Sass 和 [Compass](http://compass-style.org/) 的浏览器设计提供了一套指导方针。在这种架构中，组件被逻辑地分组在特定的文件夹下，如基础、组件、布局(SMACSS)或原子、分子和组件([原子设计](http://bradfrostweb.com/blog/post/atomic-web-design/#molecules))。

此外，每个组件都有自己的部分集合:`_variables.scss`、`_mixins.scss`、`_extends.scss`和一个清单文件(`_buttons.scss`、`_alerts.scss`等)。

不过，这种方法有几个缺点:

1.  编译时间–更多的文件需要更多的时间来编译
2.  最初构建组件时，可能需要大量的文件切换。但是一旦你完成了，保持或者改变就变得轻而易举了。

这里的好处是能够在一个模块的单个*部分*工作。它清楚地划分了用于设计组件的*配置*(变量)*功能*(混合、扩展)和*表示*(组件部分)。全局配置与组件/模块级配置保持分离。

这种分离有助于在中型到大型项目中保持理智，在这些项目中，您有多个团队在模块上工作。需要时，创建特定于模块的样式表也变得更加容易。

文件夹结构如下:

```
scss/
|– style.scss    # Manifest
|– partials/
|  |– base/
|  |  |– _content.scss
|  |  |– content
|  |  |  |– _variables.scss    # Component specific variables 
|  |  |  |– _extends.scss      # Component specific extends
|  |  |  |– _mixins.scss       # Component specific mixins
|  |– components/
|  |  |– _message.scss
|  |  |– message
|  |  |  |– _variables.scss
|  |  |  |– _extends.scss
|  |  |  |– _mixins.scss
|  |– global/
|  |  |– _variables.scss
|  |  |– _extends.scss
|  |  |– _mixins.scss
|  |  |– _functions.scss
|  |– layouts/
|  |  |– _article.scss
|  |  |– article
|  |  |  |– _variables.scss
|  |  |  |– _extends.scss
|  |  |  |– _mixins.scss
```

## SMACSS

我认为 Hugo 在介绍基于 SMACSS 的方法方面做得很好，你可以在他的 [Sass architecture 帖子](https://www.sitepoint.com/architecture-sass-project/)中读到，所以我不会在这里讨论。米娜·马卡姆也提供了一个方便的 [SMACSS 入门套件。](https://github.com/minamarkham/sassy-starter)

## 结论

在本文中，我们研究了构建 Sass 文件的不同方法。要决定使用哪种方法，您需要在复杂性、编译时间和个人偏好之间进行权衡。

当在团队中工作时，这变得很棘手。确保每个人都对你的方法感到满意，并且可以随意调整方法来适应你的具体情况。

我最喜欢原型建筑风格，因为它符合我的思维方式。需要注意的重要一点是

> 嵌套越深，编译的时间就越长。

请在评论部分告诉我们您项目的 Sass 架构。

## 分享这篇文章