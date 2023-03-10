# 用 Susy 和断点创建响应式网格系统

> 原文：<https://www.sitepoint.com/creating-responsive-grid-system-susy-breakpoint/>

CSS 框架(如 Bootstrap 和 Foundation)背后的巨大吸引力在于，它们带有一个响应性网格系统，一切就绪，随时可用。缺点是你必须满足于它们的列数，它们的宽度，以及它们在你的标记中的所有类。如果你的项目需求超出了一个预设的 CSS 网格框架的默认范围，该怎么办？本文将展示如何使用两个 Sass 库:Susy 和 Breakpoint 来构建和维护自己独特的响应网格。

## Susy 基础

关于使用 Susy 管理网格的更详细的介绍，请查看这篇 SitePoint 文章或[这张幻灯片](http://www.slideshare.net/JamesSteinbach/breaking-free-from-bootstrap)。我们将只触及这里的高点，以便您对接下来的内容有所了解。

你所有的网格配置设置都存在一个名为 [`$susy`](http://susydocs.oddbird.net/en/latest/settings/) 的地图中。您可以使用该映射中的键值对来设置最大宽度(`container`)、列数(`columns`)和装订线宽度(`gutters`)。

使用 Susy，您可以通过使用`container()` mixin 为网格元素指定一个容器元素。然后，包含该混音的选择器将从`$susy`中的`container`值被分配最大宽度，居中，左右边距被设置为`auto`，并给出一个 clearfix。

容器内的每一列都由`span()` mixin 控制。这个 mixin 通常有三个参数:span(它将填充的父列的数量)、layout(如果与缺省值不同，则为父列的数量)和 location(第一个/alpha、最后一个/omega 或一个列号)。

```
$susy: (
 container: 70em,
 columns: 12,
 gutters: 1/6
);
.main {
 @include container;
}
.content {
 @include span(8 of 12 first);
}
.sidebar {
 @include span(4 of 12 last);
}
```

如果你想看更详细的 Susy 网格，这里有一个 [Sassmeister 要点](http://sassmeister.com/gist/6d866241070d5a7c2cd2)供你参考。

## 断点基础

如果你想详细了解如何使用 Breakpoint，一个管理媒体查询的 Sass 插件，[这篇 SitePoint 文章](https://www.sitepoint.com/sass-based-media-queries-with-breakpoint/)应该可以回答你的大部分问题。同样，为了便于介绍，我们将涉及一些基础知识。

断点提供了一个强大的 mixin 来处理几乎任何种类的媒体查询: [`breakpoint()`](http://breakpoint-sass.com/) 。如果您以单个度量值(如 960px)传递 mixin，它将使用该数字作为最小宽度媒体查询。如果您提供两个测量值，它将使用它们作为最小宽度和最大宽度值。如果传递一个关键字/值对，就可以创建任何需要的媒体查询:高度、方向、分辨率等。您还可以传递几个关键字/值对来创建复杂的查询:只需将每一对用括号括起来:

```
.element {
 font-size: 1.3em;
 @include breakpoint((min-width 30em) (max-width 60em) (orientation portrait)) {
 font-size: 1.5em;
  }
}
```

## Mixin: `susy-breakpoint()`

现在我们已经有了 Susy & Breakpoint 的基础知识，让我们看看它们是如何协同工作来帮助我们快速构建响应性网格布局的。如果您已经将二者导入到您的项目中，您可以使用`susy-breakpoint()` mixin 来组合它们的功能。

[`susy-breakpoint()`](http://susydocs.oddbird.net/en/latest/toolkit/#breakpoint) mixin 将断点的媒体查询包装在 Susy 的网格输出周围。它需要两个参数(并带有可选的第三个参数)。

您传递给`susy-breakpoint()`的第一个参数是您计划传递给断点的媒体查询数据。这些数据被直接传递给断点 mixin，并用于为这部分代码生成媒体查询。

第二个参数是 Susy 网格布局:最简单地说，它可以是一个数字:列数。你也可以给它传递一个包含和`$susy`一样的键的地图。这个列计数为 Susy 在这个 mixin 中计算的所有内容设置“上下文”。例如，如果您将“16”作为布局传递，`susy-breakpoint()`中的所有 Susy 网格混合和函数将使用 16 作为列计数值，而不管`$susy`配置图中的`columns`值是什么。

如果您熟悉作用域变量的概念，这只是将新的列计数作用域到当前媒体查询的一种方法。事实上，如果您传递一个遵循`$susy`模式的映射，那么该映射中的任何值都将被限定在生成的媒体查询上下文的范围内。

`susy-breakpoint()`的第三个(可选)参数是断点的非查询值。此参数允许您处理不支持媒体查询的浏览器的回退。如果启用`$breakpoint-no-query-fallbacks`并传递一个类作为第三个参数，它将被用作当前选择器的包装类(而不是媒体查询)。

```
$breakpoint-no-query-fallbacks: true;
.element {
 color: blue;
 @include susy-breakpoint(480px, $susy, '.no-mq') {
 color: red;
  }
}

// output
.element {
 color: blue;
}
@media screen and (min-width: 480px) {
  .element {
 color: red;
  }
}
.no-mq .element {
 color: red;
}
```

注意:上面的例子没有在查询中使用 Susy grid mixins:它只是演示了如何使用断点的无查询。

## 一个包含可变列元素的容器网格

有两种方法来管理响应式网格。您可能最熟悉第一种方法:所有的列度量都基于相同的列计数，元素在不同的断点改变跨度计数。这是几乎所有 CSS 网格框架的运行方式。让我们看看如何用 Susy 和断点来创建它。

我们首先需要的是我们的`$susy`地图和几个断点变量。我们还将打开断点的无查询特性。

```
$susy: (
 container: 64em,
 columns: 12,
 gutters: 1/6
);
$bp-medium: 32em;
$bp-large: 60em;
$breakpoint-no-query-fallbacks: true;
```

设置好地图后，我们将声明容器元素:

```
.container {
 @include container;
}
```

在该容器中，我们将把两个子元素设置为全幅跨度。这将是我们对小屏幕的默认设置:“列”是垂直堆叠的。

```
.content {
 @include span(12 last);
}
.sidebar {
 @include span(12 last);
}
```

让我们向上移动到中断点和大断点。我们将使用`susy-breakpoint()` mixin 来完成这项工作。我们将`$susy`作为布局参数传递。我们也可以使用`12`(`$susy`中的`columns`值)。我知道，现在这可能感觉有点多余，但稍后我们将看到传递一个新地图作为布局是多么有用。

```
.content {
 @include span(12 last);
 @include susy-breakpoint($bp-medium, $susy) {
 @include span(6 first);
  }
 @include susy-breakpoint($bp-large, $susy) {
 @include span(8 first);
  }
}
.sidebar {
 @include span(12 last);
 @include susy-breakpoint($bp-medium, $susy) {
 @include span(6 last);
  }
 @include susy-breakpoint($bp-large, $susy) {
 @include span(4 last);
  }
}
```

现在我们有了一个基本的响应网格。在任何小于 32em 的屏幕上，内容和侧边栏将是全幅的，垂直堆叠。在 32em 处，这些元素将并排出现在总宽度的一半处。在 60em 处，它们的宽度之比从 1/1 变为 2/1。在 64em 处，容器将停止扩展，并将在视口内居中。

## 使用稳定的列跨度更改容器网格

建立响应式网格的另一种方法(当然也更复杂)是为列设置固定的跨度值，并更改容器列的属性。为此，您需要通过使用每个断点的`$susy`样式映射的`columns`值中的列列表来更改每个断点处的列比率:

```
$susy: (
 container: 70em,
 columns: 1,
 gutters: 1/16
);
$medium: (
 columns: 1 1
);
$large: (
 columns: 2 1
);

$bp-medium: 32em;
$bp-large: 60em;
```

在`columns`值中放入数字列表*告诉 Susy 创建与这些数字的比例相匹配的列宽。`span()` mixin 将根据元素的位置调整它们的大小以匹配这些比例。因此`columns: 1 1`创建了两个等宽的列(就像`columns: 2`会做的那样)，而`columns: 2 1`创建了两列，并使第一列的宽度是第二列的两倍。*

为了使用这些比率，我们将按如下方式调用`susy-breakpoint()`和`span()`:

```
.container {
 @include container;
}
.primary {
 @include span(1 at 1);
 @include susy-breakpoint($bp-medium, $medium) {
 @include span(1 at 1);
  }
 @include susy-breakpoint($bp-large, $large) {
 @include span(1 at 1);
  }
}
.secondary {
 @include span(1 at 1);
 @include susy-breakpoint($bp-medium, $medium) {
 @include span(1 at 2);
  }
 @include susy-breakpoint($bp-large, $large) {
 @include span(1 at 2);
  }
}
```

`at`位置在这里提供了魔力:在大断点中，第一列比第二列宽，因此指定位置告诉 Susy 使`.primary`成为`columns`列表中第一个数字的宽度。指定`.secondary`元素在位置`2`告诉 Susy 它应该匹配`columns`列表中第二个数的比率(并且它是最后一个元素，应该移除它的`margin-right`)。我们必须在每个`susy-breakpoint()`中重复`span()` mixin，因为 Susy 需要新的`columns`度量来重新计算每个断点处的宽度百分比。在现实生活中，用这些 Sassmeister gists 试试这个吧:[一个](http://sassmeister.com/gist/79db35b7027613d78b1f)，[两个](http://sassmeister.com/gist/07f48839b9d67191e00d)。

## 结论

Susy 和 Breakpoint 在这里组成了一个非常强大的团队。您不仅可以根据自己的需要创建简单或复杂的网格，还可以根据项目的需要在任何断点处更改这些网格。请在评论中提出任何后续问题，或留下评论来分享您用 Susy 和 Breakpoint 创建的智能网格！

## 分享这篇文章