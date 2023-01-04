# 指南针 1.0 概述

> 原文：<https://www.sitepoint.com/an-overview-compass-1-0/>

[指南针](http://compass-style.org/reference/compass/) 1.0 于今年 8 月 15 日正式发布。许多人避开 Compass，因为他们认为在项目中集成 Compass 很困难。当您考虑到 Compass 命令并不比编写一行 JavaScript 更复杂时，这种说法有点荒谬。

如果您正在编写 Sass，Compass 是一个强大的工具，它内置的 mixins 和函数使编写复杂的站点变得轻而易举。大多数人不使用 Compass 是因为感觉到学习曲线，但是正如你将看到的，1.0 版本使得使用 Compass 比以前更容易。

## 没有命令行的指南针

在正常情况下，你可以用 Compass 初始化一个项目。这又创建了一个用于配置各种选项的`config.rb`文件。这些选项中有许多与 Compass 编译器的行为有关。即使您没有使用 Compass 来编译 Sass，您仍然可以在您的项目中使用 Compass。

这一切都始于一个名为`project-setup`的分部。将此文件导入到您想要与 Compass 一起使用的 Sass 文件中。

### _ 项目-设置

```
$project-path: absolute-path("..");
$debug-configuration: true;
@import "compass/configuration";
@include compass-configuration($reconfigure:true);
http-path = "/";
images-dir = "img";
fonts-dir = "fonts";
```

让我们来看看组成这个`project-setup`文件的部分。

```
$project-path: absolute-path("..");
```

设置为项目的绝对路径，这个全局路径用于在导入`compass\configuration`时初始化 Compass

```
$debug-configuration: true;
```

Compass 将输出关于配置的调试信息。

```
@import "compass/configuration";
```

初始化指南针，并使一些工具可用。

```
@include compass-configuration($reconfigure:true);
```

根据您的需求配置指南针。如果你需要不止一次地调用`@include compass-configuration`(比如当你需要配置多个选项时)，那么我们把`$reconfigure:true`选项传递给 mixin。这将允许您设置多个选项，例如:

```
http-path = "/";
images-dir = "img";
fonts-dir = "fonts";
```

任何熟悉从命令行使用 Compass 的人都会注意到这些是我们在`config.rb`中配置的许多设置。查看 [Compass 配置选项](http://compass-style.org/help/documentation/sass-based-configuration-options/),获得完整的选项列表和更多关于在没有命令行的情况下使用 Compass 的信息。您会注意到，我们没有像使用 Compass 那样指定 CSS 和 Sass 目录的选项。记住我们可能没有用 Compass 编译，只是使用了它的库。

## 供应商前缀

Compass 使用来自[caniuse.com](http://caniuse.com/)的数据来让你决定你打算为传统浏览器提供的支持级别。通过设置阈值，您可以告诉 Compass 您希望允许哪些功能正常降级或中断。Compass 将根据 caniuse.com 的数据自动添加或删除供应商前缀。

```
.round_border { @include border-radius(2px 5px, 3px 6px);}
```

这个 mixin 将产生这个 CSS

```
.round_border {
 -webkit-border-radius: 2px 3px;
 -moz-border-radius: 2px 5px / 3px 6px;
 border-radius: 2px 5px / 3px 6px;
}
```

正如你所看到的，Compass 为不同的浏览器添加了合适的厂商前缀(Chrome 使用 webkit，Firefox 使用-moz)。还要注意，webkit 前缀不支持简写，所以只传递第一个值。让我们看一下影响供应商前缀的一些关键变量:

```
$graceful-usage-threshold
```

这个变量是针对那些可以适度降级的特性(比如[动画](http://compass-style.org/reference/compass/css3/animation/))。默认值为 0.1 (0.1%)，当 1，000 个用户中有 1 个会受到删除前缀的影响时，该前缀将被删除。在上面的例子中，一旦 Firefox 的使用率达到 0.1%

```
-moz-border-radius: 2px 5px / 3px 6px;
```

因为这是 Firefox 的前缀

```
$critical-usage-threshold
```

对于不会优雅降级的 CSS 特征，比如[背景剪辑](http://compass-style.org/reference/compass/css3/background_clip/)，其默认值为 0.01 (0.01%)。当 10，000 个用户中有 1 个会受到影响时，前缀将被删除。

```
$browser-minimum-versions
```

在某些情况下，我们可能希望支持一个浏览器，不管有多少人在使用它。在这种情况下，我们可以为每个已知的浏览器设置最低的浏览器版本。

```
$supported-browsers
```

定义您的站点支持哪些浏览器。默认情况下，所有浏览器都是启用的，但是您可以通过从列表中拒绝特定的浏览器来禁用它们。点击此处了解更多关于[厂商前缀](http://compass-style.org/help/documentation/tuning-vendor-prefixes/)的信息。

## 源地图支持

如果你曾经开发过一个基于 Sass 的大型网站，你就会知道追踪 bug 是一个挑战。源代码映射允许您查看生成 CSS 的底层 Sass，使开发人员能够快速跟踪问题代码。要在 Compass 中启用源地图支持，只需将这一行添加到您的`config.rb`文件中:

```
sourcemap = true
```

现在 compass watch 命令将生成 CSS 地图文件。之后你所要做的就是启用 Chrome DevTools 或 T2 Firefox developer tools。

## 支持较新的 CSS

使用 Compass 的真正原因是因为在您的项目中可以使用所有的工具。这延伸到较新的 CSS 开发，如动画。Compass 包含大量的 mixins，可以轻松地在项目中创建动画。除此之外，Compass 还包括大量工具和实用程序来使用这些新特性。更多信息请查看 Compass [CSS 页面](http://beta.compass-style.org/reference/compass/css3/)。

## 结论

这些只是 Compass 正式版提供的部分功能。如果你还没有试用过 Compass，我建议你尝试一下。我保证你会找到一个在你的项目中有用的函数或 mixin。

## 分享这篇文章