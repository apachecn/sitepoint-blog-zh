# 基础 6:新的灵活网格

> 原文：<https://www.sitepoint.com/foundation-6-the-new-flex-grid/>

上个月，我写了几篇文章，涵盖了[Foundation 6](https://www.sitepoint.com/whats-new-in-foundation-6/)的新内容，以及对 [Foundation 的新菜单组件](https://www.sitepoint.com/foundation-6-menu-component/)的深入探究。

可以说**基础最好的部分之一是网格系统**。该系统是 Foundation 响应式设计的支柱，让您可以灵活地构建任何东西，从简单的两列布局到多级深度、复杂的庞然大物。

网格是基于一个响应式浮动系统的，有行、列、偏移、清除(你在几个框架中看到的标准元素)。它的效果很好，总的来说，这是一个快速启动和运行您的设计的好方法，可以适应各种设备大小，并相应地调整您的 UI。

然而，在 Foundation 6 中有一个新的系统。

## 简介:新的 Flex 网格

在 Foundation 6 中，Zurb 引入了 [Flex Grid](http://foundation.zurb.com/sites/docs/flex-grid.html) ，这是标准网格的可选替代品。

正如你所猜测的，Flex Grid 是由 flexbox 驱动的。新的网格系统不使用标准网格使用的浮动、偏移定位和其他技巧，而是让您利用 flexbox 模型的一些强大的布局功能。

在你一头扎进去之前，先给你提个醒:由于这个新网格是由 flexbox 驱动的，自然它只能在支持浏览器的浏览器中工作。所以如果你需要支持 IE8 和 IE9 这样的传统浏览器，你必须坚持使用标准网格。

## 柔性网格是可选的

默认情况下，Zurb 不包含 Flex 网格；这是一个可选组件。如果您使用的是 Sass 版本的 Foundation，首先在项目中找到`app.scss`文件，然后在编辑器中打开它。在里面你会看到`@include`语句。您需要删除或注释掉标准网格，然后添加弹性网格，如下所示:

```
// @include foundation-grid;
@include foundation-flex-grid;
```

如果您没有使用 Sass 版本，而是[正在创建您自己的定制版本](http://foundation.zurb.com/sites/download/#customizeFoundation)，您可以通过从定制器中选择 flex grid 并下载您的组合 CSS 来包含它。

![Choosing components for Foundation 6](img/2b3b6e1a5827f81aa43bda6497303958.png)

flex grid 是标准 grid 的替代品，因此您不能开箱即用。这是因为它们共享类名，如`.row`和`.column`。

如果你想两者都用，你需要使用 Foundation 6 的 Sass 构建，并通过使用标准网格的 [mixin 来定义你自己的基本网格。这将允许您定义自己的结构，这样您就可以将两者保存在同一个项目中(例如，您可以将行称为`.row-old`，将列称为`.column-old`)。](http://foundation.zurb.com/sites/docs/grid.html#sass-mixins)

## 基本的 Flex 网格布局

要使用新的网格，您需要在`rows`和`columns`中定义您的部分，并为每个设备配置文件添加尺寸调整。以下是 3 列 flex 网格的基本标记:

```
<div class="row">
  <div class="column small-12 medium-6 large-4">Column 1</div>
  <div class="column small-12 medium-6 large-4">Column 2</div>
  <div class="column small-12 medium-6 large-4">Column 3</div>
</div>
```

有了这种结构，您可以向`.row`或`.column`元素应用额外的类来改变您的布局。

flexbox 最棒的一点是它很容易控制元素的排序。使用标准浮动，几乎不可能以一种方式对一系列元素进行排序，然后根据某个特定的因素来切换顺序。有了 flexbox，我们可以使用 [source ordering](http://foundation.zurb.com/sites/docs/flex-grid.html#source-ordering) 选项来告诉 Foundation 我们希望如何组织我们的项目。

在每个`column`上，我们添加了`order-{value}`属性来对每个元素进行排序。

```
<div class="row">
  <div class="column order-2">I come second!</div>
  <div class="column order-1">I come first!</div>
</div>
```

更有用的是，我们可以使用`{size}-order-{value}`属性来指定设备的大小，以便对元素进行重新排序。指定的大小按升序使用，这意味着如果您指定`small-order-1`，它将用于小、中、大等等。

```
<div class="row">
  <div class="column small-order-1 medium-order-2">
    I come first on small, but second on anything larger
  </div>
  <div class="column small-order-2 medium-order-1">
    I come second on small, but first on anything larger
  </div>
</div>
```

[**看看这个示例演示页面**](http://codepen.io/SitePoint/pen/mVGpbB/) ，它展示了如何使用 ordering 属性根据设备大小切换用户界面。

## 水平和垂直定位

你可以用标准网格把东西居中，但是这通常很痛苦。通常情况下，你需要摆弄东西或使用保证金把戏，让事情刚刚好。使用 flex grid，您可以轻松地横向和纵向布局您的内容。

首先，您需要向`row`元素添加您的[水平对齐](http://foundation.zurb.com/sites/docs/flex-grid.html#column-alignment)和/或[垂直对齐](http://foundation.zurb.com/sites/docs/flex-grid.html#vertical-alignment)类。这将导致该行中的所有元素按照您的定义对齐。如果您不想这样做，您可以将这些相同的类添加到每个元素中，以指定每个元素将如何对齐。

要水平对齐项目，您需要添加一个支持的对齐类，如`align-right`、`align-left`、`align-center`、`align-justify`或`align-spaced`。例如，要创建一个均匀间隔的菜单，其中每个导航元素周围都有足够的空间，我们可以使用`align-spaced`类。

```
<div class="row align-spaced">
  <div class="column small-3">Home</div>
  <div class="column small-3">About</div>
  <div class="column small-3">Contact</div>
</div>
```

因为 12 列网格中的空间总计为 9，所以我们将剩下 3 个网格单位的空间，这些空间将平均分布在项目周围。

使用 flex grids 进行垂直对齐也非常容易，这应该会让许多开发人员感到高兴(考虑到这通常有多痛苦)。为了让它运行，我们[需要添加一个支持的对齐类](http://foundation.zurb.com/sites/docs/flex-grid.html#vertical-alignment)，就像我们对水平对齐所做的一样。这些等级是`align-top`、`align-middle`、`align-bottom`和`align-stretch`。

如果我们想将行内的所有元素垂直居中对齐，只需将`align-middle`类添加到`row`中即可:

```
<div class="row align-spaced">
  <div class="column small-6">
    I am a big big column with heaps of text. My siblings will be
    positioned in the center of the row based on my height.
  </div>
  <div class="column small-6">
    I'm going to be vertically centered.
  </div>
</div>
```

使用 flex grid 可以实现的功能非常酷。 [**如果你热衷于查看一些水平和垂直排列，看看这个演示页面**](http://codepen.io/SitePoint/pen/WrgdvY/) 。

## 一个改进的 Sass 网格系统

[基础网格](http://foundation.zurb.com/sites/docs/grid.html)是您创建响应能力的核心组件，它是一个相当标准的网格，支持基于您的设备配置文件的不同宽度和布局。

![The revised grid in Foundation 6](img/aebfdafa9494f8f40968c876ab0a101d.png)

在每个版本中，Zurb 都调整了网格的工作方式，总体来说非常棒。随着 Foundation 6 的推出，他们已经修改了他们的主 mixin，让你可以更好地控制如何使用他们的网格系统。

以前的版本允许您定义您的网格系统将基于多少列，让您选择接受默认的 12 列系统或者选择另一个适合您的数字。虽然这很棒，但最终这意味着如果您想使用网格系统，您会被一个网格所困(意味着所有内容都需要在 12 列中定义，等等)。

在 Foundation 6 中，grid mixin 已经更新，可以让您[轻松定义多个网格元素](http://foundation.zurb.com/sites/docs/grid.html#multiple-grids)，每个元素都有自己的布局。

例如，您可以定义一个包含 18 列的新布局组，如下所示:

```
.row-listing {
  // row of 18 columns
 @include grid-row(18) {
    // primary element, used for page content
    .primary {
 @include grid-column(10);
    }
    // secondary, used for blog listing
    .secondary {
 @include grid-column(4);
    }
    // tertiary, used for side advetising
    .tertiary {
 @include grid-column(4);
    }
  }
}
```

这是对网格系统的一次坚实的升级，将帮助许多人完全按照他们想要的方式定制他们的布局。

## 结论

[Flex Grid](http://foundation.zurb.com/sites/docs/flex-grid.html)是你所知道和喜爱的标准基础网格，只是更灵活和可控。你可以调整排序，水平和垂直定位，以及其他有用的东西。

如果你有能力切断对 IE8/IE9 等传统浏览器的支持，那么你没有理由不使用 Foundation 的新 Flex Grid。它使开发变得更加容易，并将帮助您创建真正灵活的布局和设计。

## 分享这篇文章