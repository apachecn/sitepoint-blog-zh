# 获得粘性标题和 WP 管理栏的行为

> 原文：<https://www.sitepoint.com/getting-sticky-headers-wordpress-admin-bar-behave/>

将一个网站的 logo 和主导航放入粘性标题(或者固定位置的标题)是现在一个明确的趋势。WordPress 主题也变得流行起来:查看[帖子](http://smashinghub.com/top-10-fixed-header-wordpress-themes-for-2013.htm) [像](https://managewp.com/best-wordpress-themes-sticky-navigation) [所有](http://themesandco.com/snippet/make-header-sticky/) [的](http://ozzyrodriguez.com/tutorials/fixed-header-wordpress/) [这些](http://thematosoup.com/sticky-header-plugin-for-wordpress/)。

然而，在 WordPress 站点中，当管理栏可见时，粘性标题会遇到问题。管理栏和大多数粘性标题都依赖于`position: fixed; top: 0;`，并根据视窗定位。它们最终会重叠。由于管理栏有一个`99999`的`z-index`，它通常会覆盖主题的粘性标题(或者如果标题赢得了`z-index`战争，那么它会覆盖管理栏)。让我们看看如何使用 CSS(和 Sass)来解决这个问题。

![broken header with WordPress admin bar](img/3cd295531509c304c713675210909775.png)

[快速提示:一些主题使用 JavaScript 在页面滚动时定位粘性元素。如果 JavaScript 不断更新内联的`top`属性，下面概述的技术不能解决这个问题。]

## 移动标题的 CSS

为了简单起见，我们将使用类`.sticky-header`来引用固定在页面顶部的元素。你应该为你的主题找到正确的选择器并使用它。我们还假设它的`top`位置值为 0。如果`top`被其他东西抵消了，你需要计算并调整下面的测量。

当管理栏在前端可见时，WordPress 将类`.admin-bar`附加到页面的`<body>`上。(这大部分时间由`header.php`中的`body_class();`函数处理。)那个类会让我们调整我们粘性头的`top`位置。

```
/* This is the existing CSS... */
.sticky-header {
  position: fixed;
  top: 0;
}
/* Here's the new CSS to add... */
.admin-bar .sticky-header {
  top: 32px;
}
```

这很容易控制，对吧？管理栏有 32px 高，所以我们只需向下移动粘性标题，两者都是可见的。唯一的问题是，它并不总是那么简单:管理栏并不总是 32px 高。在这种情况下，你需要使用 CSS。

## 小屏幕的 CSS

在比 783 像素窄的屏幕上，管理栏的高度是 46 像素。我们需要修改代码来弥补这一点:

```
.admin-bar .sticky-header {
  top: 32px;
}
@media screen and (max-width: 782px) {
  .admin-bar .sticky-header {
    top: 46px;
  }
}
```

如果你喜欢移动优先的 CSS(像我一样)，使用下面的代码:

```
.admin-bar .sticky-header {
  top: 46px;
}
@media screen and (min-width: 783px) {
  .admin-bar .sticky-header {
    top: 32px;
  }
}
```

现在，无论屏幕有多宽，您都可以正确定位粘性标题了！不过，我们还没完。如果你使用 Sass 来构建你的主题，我们可以把它整合成一个可重用的 mixin。

如果你是一个主题开发者或者正在构建一个子主题，你已经知道在你现有的样式表中把这个 CSS 放在哪里。另一方面，如果你正在使用一个现有的主题，它不能很好地处理它的粘性头，你只需要插入这段代码，你可以使用一个插件，允许你插入 CSS，比如 [WP 添加定制 CSS](https://wordpress.org/plugins/wp-add-custom-css/) 或者[简单定制 CSS](https://wordpress.org/plugins/simple-custom-css/) 。

## Sass 移动标题

我要把这个叫做 mixin `admin-sticky-fix()`。我们将看一下整个代码块，然后看看每个部分是如何工作的。

```
@mixin admin-sticky-fix( $offset: 0 ) {
  $narrow-offset: 46px;
  $wide-offset: 32px;
  @if $offset != 0 and type-of($offset) == 'number' {
    $narrow-offset: $narrow-offset + $offset;
    $wide-offset: $wide-offset + $offset;
  }
  .admin-bar & {
    top: $narrow-offset;
    @media screen and (min-width: 783px) {
      top: $wide-offset;
    }
  }
}
```

mixin 采用一个可选参数:`$offset`。这允许您指定元素具有除了`0`之外的`top`值。如果你不指定任何偏移量，mixin 将采用`0`并使用它。如果您手动指定一个`$offset`，那么`@if`条件将修改默认的管理栏高度值来匹配。*注意:只使用一个整数:不需要`px`单位，但是如果你包含它，它不会破坏东西。*

我们的 mixin 使用 Sass 嵌套来简化事情:`&`表示这个 mixin 在其中被调用的任何选择器。所以如果你在`.sticky-header`中`@include`它，你会得到输出选择器`.admin-bar .sticky-header`。您会注意到媒体查询也嵌套在选择器中。Sass 自动将`@media [etc]`移回根目录，并将当前选择器复制到其中。这个嵌套技巧只是让我们写一个更短的 mixin。

现在让我们看看这个 mixin 是如何工作的:

```
/* Sass we write */
.sticky-header {
  position: fixed;
  top: 0;

  @include admin-sticky-fix;
}

/* CSS Output */
.sticky-header {
  position: fixed;
  top: 0;
}
.admin-bar .sticky-header {
  top: 46px;
}
@media screen and (min-width: 783px) {
  .admin-bar .sticky-header {
    top: 32px;
  }
}
```

如果需要匹配偏移值，请像这样使用 mixin:

```
/*  Sass we write */
.sticky-header-offset {
  position: fixed;
  top: 20px;

  @include admin-sticky-fix(20);
}

/* CSS Output */
.sticky-header-offset {
  position: fixed;
  top: 20px;
}
.admin-bar .sticky-header-offset {
  top: 66px;
}
@media screen and (min-width: 783px) {
  .admin-bar .sticky-header-offset {
    top: 52px;
  }
}
```

拥有一个可重用的 mixin 允许我们快速调整任何固定位置的项目来补偿 WordPress 的管理栏。我的移动菜单通常是固定位置的，这也让我很容易调整它们的`top`值。

## 结论

默认情况下，粘性标题会与 WordPress 管理栏冲突，但是有了这些代码片段，修复是非常容易的。这个 Sassmeister 要点包含了 mixin 和一些它如何工作的例子。你在哪里看到过粘性标题趋势的好的实现，你如何在你的 WordPress 主题中设计它的风格？

## 分享这篇文章