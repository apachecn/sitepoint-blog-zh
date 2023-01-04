# 使用 Sass 构建具有垂直节奏的自定义字体标尺

> 原文：<https://www.sitepoint.com/using-sass-build-custom-type-scale-vertical-rhythm/>

在网页设计中实现视觉一致性的一个方法是使用[垂直节奏](http://webtypography.net/2.2.2)。对于一个网站来说，这意味着无论任何文本元素是什么，它的 T1 总是一个一致节奏单位的偶数倍。当这一步完成后，你可以在你的页面后面放置一个条纹背景，每个文本块(段落、标题、块引用等)将沿着网格中的线条排列。

可以想象，手动设置需要大量的数学和打字工作。如果您想相应地更改网格的比例，您将为每个断点重复该工作。如您所料，Sass 提供了一个强大的工具箱来自动化所有的工作，并更容易地生成具有一致的垂直节奏的自定义字体缩放。

首先，我承认已经有一些很好的 Sass 插件可以帮助构建一个具有一致垂直节奏的自定义字体标尺。如果你只是想抓取一段预先构建好的代码，试试[排版](https://github.com/ianrose/typesettings)、[字体](https://github.com/andrejmlinarevic/typomatic)或[字体集](https://github.com/csswizardry/typecsset/tree/gh-pages)。

## 为什么要建立我们自己的？

这就提出了一个很大的问题:既然有那么多可用的工具，我们为什么还要尝试构建自己的工具呢？有几个原因。事实上，有三种不同的工具已经意味着有不同的方法来成功地解决这个问题。此外，用 Sass 构建我们自己的类型标度是超越基础并学习一些更高级的 Sass 的好方法。

在我们开始之前，让我们定义一下我们要构建什么样的类型标尺。标尺将以基数`font-size`和`line-height`开始。从那个`font-size`，它将生成几个成比例的更大的`font-size`值。这个 Sass 代码还将控制它生成的所有尺寸的行距，允许我们将行高与基数`line-height`的倍数联系起来。

最后一个有趣的细节:我们将能够通过一个简短的媒体查询 mixin 为各种屏幕宽度调整基数`font-size`和`line-height`。

## 设置变量

我们将把这些[变量](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#variables_)放在文件顶部附近，因为我们希望通过将所有“配置”内容放在一个容易找到的地方来组织我们的代码。

如果我们不想让它有响应性，这就是我们需要的基本`font-size`和`line-height`值:

```
$base-font-size: 16px;
$base-line-height: 1.3;
```

我们还将使用一个变量来表示`font-size`值将按比例增加的比例:

```
$scale: 1.5;
```

然而，我们将使其具有响应性，因此 [Sass map](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps) 是存储该信息的更好方式:

```
$bp-sizes: (
 small: (
 base-font-size: 16px,
 base-line-height: 1.3,
 scale: 1.25
  ),
 medium: (
 base-font-size: 18px,
 base-line-height: 1.4,
 scale: 1.4
  ),
 large: (
 base-font-size: 22px,
 base-line-height: 1.5,
 scale: 1.5
  )
);
```

我们还需要一组用于媒体查询的断点值。您可以使`$bp-sizes`图更复杂，并添加一个`min-width`值。但是在本教程中，我将允许你可能已经有了一个你喜欢的断点插件，所以我们将把它们分开。只要确保断点标签匹配即可:

```
$breakpoints: (
 small: 480px,
 medium: 960px,
 large: 1200px
);
```

稍后，我们将看看如何使用该地图中的值。现在，让我们继续我们需要的最后一组变量——我们计划使用的字体大小的[列表](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#lists)。

```
$font-sizes: ( p, bq, ssh, sh, h, hero );
```

在这种情况下，我们使用`p`表示段落，`bq`表示块引用，`ssh`表示子副标题，`sh`表示副标题，`h`表示标题，`hero`表示英雄文本。

## 设置混音

现在我们已经在变量中获得了所有需要的数据，是时候开始处理它了。为了得到我们想要的 CSS，我们需要一个 [mixin](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#mixins) ，为`$font-sizes`地图中的每个标签创建`font-size`和`line-height`属性。然后，我们将使用来自`$bp-sizes`图中每个断点的数据重复这种混合，将每组样式包装在适当的媒体查询中。

### 生成`font-size`和`line-height`

下面是我们用来生成`font-size`和`line-height`值的 mixin:

```
@mixin generate-font-properties($label, $base-font-size, $base-line-height, $scale) {
 $scale-value: index($font-sizes, $label) - 1;

 $fs: $base-font-size * pow($scale, $scale-value);
 $lh: $base-font-size * $base-line-height / $fs;

 @while $lh < 1 {
 $lh: $lh + $lh;
  }

 font-size: $fs;
 line-height: $lh;
}
```

这个 mixin 接受四个参数:字体大小的标签(来自`$font-sizes`地图)、基本字体大小、基线高度和比例(全部来自`$bp-sizes`)。

这个 mixin 的第一行获得了标签在`$font-sizes`图中的索引，并减去 1，这样生成的字体大小就会正确地增加。

*注意: [Sass 索引值从 1](http://sass-lang.com/documentation/Sass/Script/Functions.html#index-instance_method) 开始计数，不像其他大多数编程语言从 0 开始计数。*

第二行将变量`$fs`设置为当前基数`font-size`乘以当前比例的比例指数的幂。换句话说，如果是第一个生成的字体大小，`$fs`将是基础字体大小乘以 1(缩放到 0 的幂)。如果是第二次生成的字号，`$fs`将是基础字号乘以缩放的一次幂。

*注意:如果你正在使用指南针，`pow()`是一个助手功能；如果没有，你可能想要包含一个像这个一样的`pow()`函数[。](http://sassmeister.com/gist/10620fefd1ed75189f1b)*

此后，`$lh`(作为最终行高的占位符)被设置为基本垂直节奏(字体大小*行高)除以当前计算的字体大小。这使得任何字体大小的起始行高与基本垂直节奏相同。对于较大的字体大小，此度量小于 1。 [`@while`循环](http://thesassway.com/intermediate/if-for-each-while)一次增加一个垂直节奏单位的`$lh`变量，直到行高至少为 1。

*注意:我们正在生成一个无单位的行高值，这样我们就可以在这个 mixin 中使用任何有效的字体大小度量。你可以在`$bp-sizes`图中使用任何你喜欢的度量单位(px，rem，em，vh)。*

最后，这个 mixin 输出字体大小作为`$fs`值，行高作为`$lh`值。

### 生成断点

我们还需要一个 mixin 来生成断点的媒体查询。以下是我喜欢用的:

```
@mixin breakpoint($name) {
 @if not map-has-key($breakpoints, $name) {
 @warn "Invalid breakpoint `#{$name}`.";
  } @else {
 @if map-get($breakpoints, $name) == '' {
 @content;
      } @else {
 @media (min-width: map-get($breakpoints, $name)) {
 @content;
      }
    }
  }
}
```

这个 mixin 根据`$breakpoints`图中的标签列表检查传递给它的断点的名称。如果缺少该名称，它将返回一条错误消息。

如果`$name`在`$breakpoints`图中，下一个`@if`语句寻找与名称配对的测量值。如果没有测量值，则编译样式时不会进行媒体查询。这允许我们在`$bp-sizes`图中设置一组默认的字体配置值。

如果`$name`有测量值，则用作`min-width`媒体查询值。(是的，我非常相信移动优先的媒体查询。)所有的`@content`风格都包含在那个媒体查询中。

## 遍历值和断点

现在我们有了一个生成字体大小和行高的工作`@mixin`，我们需要对`$font-sizes`列表中的所有值重复这个操作。为此，我们将使用一个 [`@each`循环](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#each-directive):

```
@each $label in $font-sizes {
  %#{$label} {
 @include generate-font-properties($label, $base-font-size, $base-line-height, $scale);
  } 
}
```

剩下的就是再后退一步，将这个循环放入一个穿过`$bp-sizes`地图的循环中。这个循环还将包括我们的`breakpoint()` mixin 来生成媒体查询。为了从`$bp-sizes`地图中获取必要的信息，这个循环将使用`get-breakpoint-property()`辅助函数。它搜索`$bp-sizes`，找到断点大小映射，并从该映射返回正确的属性值:

```
@function get-breakpoint-property($prop, $bp) {
 @return map-get(map-get($bp-sizes, $bp), $prop);
}

@each $size, $data in $bp-sizes {

 $bsf: get-breakpoint-property(base-font-size, $size);
 $blh: get-breakpoint-property(base-line-height, $size);
 $s: get-breakpoint-property(scale, $size);

 @include breakpoint($size) {
 @each $label in $font-sizes {
      #{$label} {
 @include generate-font-properties($label, $bsf, $blh, $s);
      } 
    }
  }
}
```

然后，循环调用`breakpoint()` mixin 来生成每个必要的媒体查询。在 mixin 中，它运行`$font-sizes`循环，并使用当前断点的适当字体大小、行高和比例重新生成字体占位符。输出连接到[占位符选择器](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#placeholder_selectors_)，然后[在需要的地方扩展](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#extend):

```
.page-title {
 @extend %h;
}
```

您可以在[这个代码栏](http://codepen.io/SitePoint/pen/IzAin/?editors=010)中看到运行中的代码:

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[带垂直节奏的萨斯式音阶](http://codepen.io/SitePoint/pen/IzAin/)。