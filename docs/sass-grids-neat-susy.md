# 萨斯网格:从整洁到朴素

> 原文：<https://www.sitepoint.com/sass-grids-neat-susy/>

在过去的几个月里，我一直在使用一个名为 [Neat](http://neat.bourbon.io) 的 Sass 网格系统。它几乎完成了我所需要的一切，而且很容易上手。然而，最近我发现自己编写了额外的样式来增加整洁的网格。与其自己扩展它，我决定寻找一个更强大的 Sass 网格系统。那个系统就是 [Susy](http://susydocs.oddbird.net) 。

## 网格框架，而不是网格系统

在我们进入如何使用 Susy 之前，让我们先来谈谈使用一个 *Sass 网格框架*，而不是一个*网格系统*的重要性。由于我一直在使用 Sass 网格框架进行布局，我发现了 3 个主要优点。

首先，我可以给我的标记任何我喜欢的类。如果您喜欢 BEM、SMACSS、OOCSS 或任何其他类命名结构，都没有关系:您可以使用 Susy 这样的框架，而无需更改您的标记。另一方面，网格系统(例如 Bootstrap)要求您将它们的类名添加到标记中。

其次，网格框架更容易维护。你可以通过改变一些 Sass 行来改变列宽和其他视觉布局样式:你的设置或者一些 mixins。但是如果你试图用基于类的网格系统改变你的布局，你将不得不重写你的标记。

第三，预处理器网格框架对用户来说更轻更快。如果使用得当，框架将只输出您特定布局选择所需的样式。另一方面，一个网格系统将包括所有宽度的所有网格的所有样式，如果你想让你的站点保持整洁，你需要做一些修剪。

## 基本语法

让我们首先回顾一下构建一个简单的基于 Susy 的布局所需要知道的最基本的东西。在此过程中，我会将它与 Neat 的语法进行比较，以获得相同的输出:如果您也从 Neat 切换到 Susy，这将有助于您理解基本概念。

## Susy 配置:`$susy`地图

Susy 的所有全局设置都存储在一个名为 [`$susy`](http://susydocs.oddbird.net/en/latest/settings/) 的地图中。这使得将配置数据保存在一个地方变得容易。如果要以 960px 的分辨率生成 12 列网格，每列之间有 20px 的间距，可以使用下面的贴图:

```
$susy: (
 columns: 12,
 gutters: .25,
 container: 960px
);
```

`gutters`值对应于整个列宽的百分比。因为 960 像素容器中的 12 列每列是 80 像素:因此，0.25 将给出一个(粗略的)20 像素的装订线。

*注意，您可以使用一个分数`gutter`值(如 1em/8em)和一个`column-width`值和 [`math: static`](http://susydocs.oddbird.net/en/latest/settings/#math) ，但是四舍五入后的. 25 可能比这更简单。*

在 Neat 中，您将为这个设置使用一系列变量:

```
$grid-columns: 12;
$max-width: 960px;
$gutter: 20px;
```

一方面，Neat 的`$gutter`变量比 Susy 的`$gutter`值更强大:它将接受 px、em 或%值。但是另一方面，所有这些未被划分的变量带来了 Sass 插件冲突的更大风险。关于 Neat 变量的另一个“问题”是，最好在将 Neat 导入主 Sass 文件之前声明它们。

## 创建容器:`container()` Mixin

mixin [`container()`](http://susy.readthedocs.org/en/latest/toolkit/#container) 将使用`$susy`中的信息并为你的网格项目设置一个容器(clearfix，max-width，【T4 居中】)。它的使用非常简单:

```
.content {
 @include container;
}
```

Neat 对`outer-container()`和`row()`mixin 做了大致相同的事情。就个人而言，我更喜欢只担心一个 mixin:在某些情况下，`row()`可能会鼓励额外的标记模仿 Bootstrap 的`.container > .row > .col`模式，而额外的包装器是不必要的。

## 定义列:`span()` Mixin

要沿网格对齐子元素，请使用 [`span()`](http://susy.readthedocs.org/en/latest/toolkit/#span-mixin) mixin。这需要 Susy 中的 3 个参数:跨度、位置、布局。

```
.sidebar {
 @include span(5);
  // width = 5 columns of $susy: ( columns ) value
}
.sidebar {
 @include span(5 of 12);
  // width = 5/12 with margin specified by $susy: ( gutter )
}
.sidebar {
 @include span(5 at 8 of 12);
  // width = 5/12 with margin removed from right
  // because 5 col starting at 8 = last column
}
```

`location`参数也可以写关键字`first`和`last`:

```
.sidebar {
 @include span(5 last);
}
```

在 Neat 中，我使用了`span-columns()` mixin。它采用了一些不同的参数:column-count(带有可选的 context-column-count)和 display(允许您设置块/表格单元格布局)。

## 高级功能

既然我们已经看了用`$susy`、`container()`中的配置选项和几个带`span()`的子节点建立网格的基础，让我们看看 Susy 可以轻松完成的一些更强大的事情。

## 改变布局:`with-layout()`混合

如果您需要在不同的页面上设置不同的列数，该怎么办？苏西用 [`with-layout()`混音](http://susy.readthedocs.org/en/latest/settings/#with-layout)让这一切变得简单。这允许您临时覆盖单个代码块的全局`$susy`配置。(把它想象成一个变量的范围。)

```
.different-column-count {
 @with-layout(10);
  // deviates from the global 12 columns
  // & calculates span() based on 10
}
```

Neat 没有类似的功能:您需要显式地包含每个孩子的新列计数:`@include span-columns(X of 10)`。

## 改变输出:`output: isolate`设置

浏览器不擅长数学。我知道，它们只是计算机程序:它们应该是完美的。但是它们将百分比四舍五入到最接近的像素，在某些情况下，这可能会妨碍精确的网格对齐。Susy 支持一种叫做“isolate”的 CSS 浮动技术，这种技术用一些奇特的负边距魔法解决了这个问题。在您的`$susy`设置中包含 [`output: isolate`](http://susydocs.oddbird.net/en/latest/toolkit/#isolate) 即可。这可能并不总是必要的，但是如果你对一些浏览器的舍入选择不满意，这可能会帮你解决问题。

Neat 没有提供这个输出选项，但是它允许基于 CSS 表格的网格输出。

## 调整列:`last-flow: to`设置

有时您需要一个网格布局，其中子列总数小于网格列数，并且您希望中间有一个间隙。换句话说，您希望`:last-child`浮动到右边，而不是停留在它左边的邻居旁边。这个也有一个`$susy`设定: [`last-flow: to`](http://susy.readthedocs.org/en/latest/settings/#global-defaults) 。

Neat 没有这个特性:您需要为此编写普通的 CSS:

```
.container {
 :last-child {
 float: right;
  }
}
```

## 获取列宽:`span()`函数

除了网格布局之外，您可能需要在 CSS 的另一部分使用列宽。Susy 为此提供了一个名为 [`span()`](http://susy.readthedocs.org/en/latest/toolkit/#span-function) 的函数(不过不要和 mixin `span()`混淆！).例如，您可能想要所有内联图像都是 4/12 列宽，但是您没有将它们浮动在网格上:

```
.container img {
 width: span(4 of 12);
 height: auto;
}
```

如果 Neat 有这个功能，可能很难预料，但这让 Susy 遥遥领先。

## 获取装订线宽度:`gutter()`函数

这与上面的`span()`函数相同，但是 [`gutter()`](http://susy.readthedocs.org/en/latest/toolkit/#gutters) 返回网格使用的装订线宽度。如果您正在进行一些垂直对齐，并希望它与您的网格间距相匹配，您可以使用此选项:

```
.article-title {
 margin-bottom: gutter();
}
```

在 Neat 中，您可以通过调用`$gutter`变量来获得这个度量。

## 真实网格:`gallery()` Mixin

这是我最喜欢的苏西混音之一。大多数网格布局只是在一个 clearfix 容器内做几列。如果您希望在一个容器中有几行**实际网格**内容，该怎么办？Susy 给你强大的 [`gallery()`](http://susydocs.oddbird.net/en/latest/toolkit/#gallery) mixin。这为你解决了那些烦人的`:last-child`边缘修复。(你可以用 CSS `:nth-child(#{$grid-count}n) { margin-right: 0 }`手动完成这个，但是为什么不让 Susy 帮你做呢？)

```
.gallery-item {
 @include gallery(3 of 12);
}
```

Neat 有 mixin 来完成这个布局。不过，我宁愿用一个混音来节省时间。Susy 的`gallery()` mixin 在改变断点宽度方面做得更干净:

```
.grid-item {
 @include gallery(6);
 @media (min-width: 600px) {
 @include gallery(4);
  }
 @media (min-width: 800px) {
 @include gallery(3);
  }
}
```

## 响应网格

您可能已经注意到，我使用了普通的 CSS 媒体查询(Sass 嵌套，但仍然只是媒体查询)作为断点。Susy 没有完整的内置断点管理器；背后的开发者推荐使用[断点](http://breakpoint-sass.com/)，并提供 [`susy-breakpoint()`](http://susydocs.oddbird.net/en/latest/toolkit/#breakpoint) 一个调用断点的帮助器 mixin:

```
@include susy-breakpoint(720px, 8) {
  // At a min-width of 720px, Susy will use a 8-col grid
  .responsive-grid-item {
 @include span(4);
  }
}
```

Neat 有一组断点助手函数& mixins，但我从未使用过它们。我使用自己的断点续传，从来不需要 Neat 的工具。

## 结论

使用 Sass 管理网格和列布局是简化开发、简化维护和更快启动站点的好方法。Susy 提供了一个强大的工具包来定义和使用各种网格布局，同时让您可以自由地在 HTML 标记中编写您认为最好的类。

## 分享这篇文章