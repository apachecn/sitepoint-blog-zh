# Sass 映射与嵌套列表

> 原文：<https://www.sitepoint.com/sass-maps-vs-nested-lists/>

这篇文章的标题可能会让你们中的一些人感到惊讶。如果您是 Sass 老手，您可能还记得使用列表的[列表](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#lists)来模拟嵌套数据数组的日子(Ruby-Sass-3.3 之前)。(Ruby) Sass 3.3 增加了一种新的数据类型，叫做[地图](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps)。列表的列表可以以嵌套的格式保存复杂的数据，但是没有键值对。映射添加的键值对，并让我们创建数据数组。

随着地图的出现，许多美国 Sass 用户开始把所有东西都放到地图上(而且理由很充分！).你所有的[断点宽度](/css/sass-maps-breakpoint-mixin/)、[颜色值](https://www.sitepoint.com/managing-color-values-with-sass/)、[网格布局](/css/sass/breaking-free-bootstrap/)、[字体比例](https://www.sitepoint.com/using-sass-build-custom-type-scale-vertical-rhythm/)等[响应式排版](https://www.sitepoint.com/sass-responsive-typography/)细节都可以进入地图！

既然 Sass 有了带键值对的映射，那么使用列表的列表有什么好的理由吗？一个理论上的原因是向后兼容:如果您的 Sass 可能是由安装了旧版本的开发人员维护的，那么列表会对他们有所帮助。然而，在实践中，Sass 版本通常由一个`package.json`或其他项目配置控制，而 Ruby gem 只需一个命令(`gem update sass`)就可以更新。

您可能选择使用嵌套列表而不是映射的一个更实际的原因是输入更少。让我们比较一个映射和一个嵌套列表，看看它们在各自的语法上是如何比较的，以及我们是如何遍历它们的。

## 比较语法

对于我们的例子，让我们创建一个数据结构来控制响应式排版。它将存储四个断点(其中一个是默认的最小视图)。对于每个断点，我们将存储`min-width`、`max-width`、一个基址`font-size`和一个基址`line-height`。

### 复杂地图语法

这就是我们在地图中存储数据的方式。一个大的映射将包含四个键(断点标签)，它们的值是我们需要存储和使用的变量的映射。在这样的可读格式中，我们有 450 多个字符和 26 行。

```
$breakpoint-map: (
 small: (
 min-width: null,
 max-width: 479px,
 base-font: 16px,
 vertical-rhythm: 1.3
  ),
 medium: (
 min-width: 480px,
 max-width: 959px,
 base-font: 18px,
 vertical-rhythm: 1.414
  ),
 large: (
 min-width: 960px,
 max-width: 1099px,
 base-font: 18px,
 vertical-rhythm: 1.5
  ),
 xlarge: (
 min-width: 1100px,
 max-width: null,
 base-font: 21px,
 vertical-rhythm: 1.618
  )
);
```

### 嵌套列表语法

用于存储相同数据的嵌套列表要短得多。然而，我们不再将键附加到我们的数据上，所以我们必须依赖于循环通过它，或者用 [`nth()`函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#nth-instance_method)调用它。也就是说，它比地图短得多:不到 180 个字符，只有 6 行。

```
$breakpoint-list: (
  (small, null, 479px, 16px, 1.3),
  (medium, 480px, 959px, 18px, 1.414),
  (large, 960px, 1099px, 18px, 1.5),
  (xlarge, 1100px, null, 21px, 1.618)
);
```

## 比较循环

当涉及到输入我们的数据结构时，输入列表大约需要输入地图时间的三分之一。但是，如果我们需要循环遍历这些值，那该如何比较呢？

### 复杂地图循环

我们可以使用下面的代码来遍历这个映射中的顶级项目:

```
@each $label, $map in $breakpoint-map {}
```

这一行开头的两个变量(`$label`和`$map`)是在循环遍历 map 中的数据时动态分配的。每个顶层数据都有两个组成部分:一个键和值。我们将键分配给`$label`，值(嵌套的映射)分配给`$map`。在这个循环中，我们可以使用变量`$label`和`$map`，它们将自动表示当前条目的键和值。

该循环将迭代四次，每个嵌套地图一次。然而，为了从嵌套的地图中获取有用的数据，我们需要使用[`map-get()`函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_get-instance_method)。该函数有两个参数——映射的名称和所需键的名称——并返回与该键相关的值。它相当于 PHP 的`$array['key']`和`$object->key`或者 JavaScript 的`object.key`语法。

为了用`@each`遍历所有子映射，并用`map-get()`将它们的值赋给有用的变量，我们最终得到了一个 6 行 220 字符的循环。

```
@each $label, $map in $breakpoint-map {
 $min-width: map-get($map, min-width);
 $max-width: map-get($map, max-width);
 $base-font: map-get($map, base-font);
 $vertical-rhythm: map-get($map, vertical-rhythm);
}
```

### 嵌套列表循环

嵌套列表确实提高了循环的效率。使用映射，我们必须将一个映射分配给一个动态循环变量，然后使用`map-get()`将其所有值分配给变量，但是使用列表，我们可以快速地将所有值分配给变量。

因为顶级列表中的每一项都有相同顺序的五个值，所以我们可以立即将每个值赋给一个动态变量，以便在循环中使用。有了这些变量，我们就不需要使用`map-get()`来给可用的变量分配子值。对于嵌套列表，我们需要的循环只有两行，少于 100 个字符。

```
@each $label, $min-width, $max-width, $base-font, $vertical-rhythm in $breakpoint-list {
}
```

## 嵌套列表警告

嵌套列表是开发人员的一个主要性能优势:总的来说，与使用地图相比，您输入的内容可能少于一半。然而，将映射添加到 Sass 是有原因的:它们提供了一个特性列表没有提供的特性:键值映射。

### 缺少值

如果你要依赖嵌套列表，你必须绝对确定你知道每个列表有多少项以及它们的顺序。让我们看看，如果我们在一个列表中遗漏了一个项目，上面的例子会发生什么:

```
$breakpoint-list: (
  (small, null, 479px, 16px, 1.3),
  (medium, 480px, 959px, 18px, 1.414),
  (large, 960px, 1099px, 18px, 1.5),
  (xlarge, 1100px, 21px, 1.618)
);

p {
 @each $label, $min-width, $max-width, $base-font, $vertical-rhythm in $breakpoint-list {
 @if $min-width {
 @include breakpoint( $min-width ) {
 font-size: $base-font;
 line-height: $vertical-rhythm;
      }
    } @else {
 font-size: $base-font;
 line-height: $vertical-rhythm;
    }
  }
}
```

如果我们尝试运行该代码，最后一个列表将会被破坏。它会正确地将“xlarge”分配给`$label`，将“1100px”分配给`$min-width`，但随后它会将“21px”分配给`$max-width`，将“1.618”分配给`$base-font`，而将`$vertical-rhythm`留空。结果，我们在最后一个断点中得到一个无效的`font-size`声明和一个丢失的`line-height`属性。此外，Sass 没有报告这方面的错误，所以我们不知道事情是否顺利。如果我们尝试使用`max-width`进行媒体查询，我们最终会得到`font-size`值(只是`21px`)——我认为这是一个非常无用的最大宽度！

如果我们使用 map，那么即使其中一个值丢失了,`map-get()`函数也会给出我们需要的东西。这就是我们的权衡:我们在列表的简单性和速度上获得的，我们在地图的特异性和防错性上失去了。

### 查询特定列表

使用嵌套列表的一个相关问题是查询特定的列表。由于地图有键，您可以使用`map-get()`快速访问任何子地图:

```
$medium-map: map-get($maps, medium);
```

为了从嵌套列表中的`medium`列表中获取数据，我们需要一个更复杂的函数:

```
@function get-list($label) {
 @each $list in $breakpoint-list {
 @if nth($list, 1) == $label {
 @return $list;
    }
  }
 @return null;
}
$medium-list: get-list(medium);
```

该函数遍历`$breakpoint-list`中的所有列表，检查我们想要的标签的第一个值，如果找到匹配，则返回列表。如果它到达`@each`循环的末尾而没有找到匹配，它将返回`null`。对于使用第一个值作为伪键的列表，它基本上是对`map-get()`的快速自制解释。

### 缺少地图功能

Sass 有很多处理映射的有用函数:这些函数对于嵌套列表是不存在的。例如，您可以使用 [`map-merge()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_merge-instance_method) 向映射添加额外的键值对。使用带有共享密钥的`map-merge()`将更新共享密钥的值。您可以使用 [`join()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#join-instance_method) 或 [`append()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#append-instance_method) 添加一个新列表，但是伪造`map-merge()`的更新特性将需要另一个定制的 Sass 函数。

另外一个有用的地图功能是 [`map-has-key()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_has_key-instance_method) 。这个函数对于验证任何依赖于`map-get()`的自定义函数非常有用。然而，列表没有类似的功能。

您可以使用 [SassyLists](https://github.com/at-import/SassyLists) 来模拟带有列表的地图功能。(这个库在 Sass 添加地图支持之前就提供了这些函数。)

## 结论

映射比列表更强大，因为它们使用键值对。附加的 Sass 地图函数提供了查找数据和验证地图值的有用方法。

嵌套的 Sass 列表可以更快地编写和维护，但它们可能不像地图那样适合错误检查或详细查询。大多数时候，我相信地图是更好的选择，尽管越来越冗长。对于较小的代码块和一次性循环，我偶尔会使用嵌套列表，但是映射更适合项目范围的设置和数据存储。

在你的工作中，你有没有比较过映射和嵌套列表，或者重构过的代码来选择一个？在评论中分享你的经验吧！

你可以在这个 [Sassmeister 要点](http://sassmeister.com/gist/598bce41ebe7305cfacd)中看到用于本教程的代码。

## 分享这篇文章