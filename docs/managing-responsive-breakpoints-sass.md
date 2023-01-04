# 用 Sass 管理响应断点

> 原文：<https://www.sitepoint.com/managing-responsive-breakpoints-sass/>

在处理响应式设计时，您经常会发现自己定义了许多媒体查询，用各种值检查各种属性。因为它会很快变得混乱，所以使用 Sass(或任何其他预处理程序)来处理响应断点通常被认为是一个好的实践。

也就是说，我想到有很多不同的方法来处理这个问题，人们一直问我哪一个是最好的。就像我们这个领域的所有事情一样，没有直接的答案，通常情况下，这要看情况。更准确地说，我注意到困难不在于想出一个系统；它将推出一个既足够灵活以涵盖大多数情况又不太复杂的系统，否则会导致代码膨胀。

在今天的文章中，我将向您介绍几种使用 Sass 来管理响应断点的方法。所有这些都完全正确，但有些显然比其他的更好。话虽如此，我还是会让你对这个话题做出自己的看法。

## 带变量

首先， [Bootstrap](https://github.com/twbs/bootstrap/blob/master/less/variables.less#L259) 和 [Foundation](http://foundation.zurb.com/docs/media-queries.html) 都使用的方法，包括定义变量，然后在`@media`指令中使用它们。换句话说，您将在某个地方拥有一个包含大量变量的配置文件，可以按原样使用。

Bootstrap 是这样做的:

```
// Defining values
$screen-sm-min: 768px;
$screen-xs-max: ($screen-sm-min - 1);
$screen-md-min: 992px;
$screen-sm-max: ($screen-md-min - 1);
$screen-lg-min: 1200px;
$screen-md-max: ($screen-lg-min - 1);

// Usage
@media (max-width: $screen-xs-max) { ... }
@media (min-width: $screen-sm-min) { ... }
@media (max-width: $screen-sm-max) { ... }
@media (min-width: $screen-md-min) { ... }
@media (max-width: $screen-md-max) { ... }
@media (min-width: $screen-lg-min) { ... }
```

Foundation 更进一步，通过一起处理字符串化的媒体查询而不是像素值，消除了键入`min-width`和`max-width`的需要。

```
// Defining values
$small-range:   (0em, 40em);       /* 0, 640px */
$medium-range:  (40.063em, 64em);  /* 641px, 1024px */
$large-range:   (64.063em, 90em);  /* 1025px, 1440px */
$xlarge-range:  (90.063em, 120em); /* 1441px, 1920px */
$xxlarge-range: (120.063em);       /* 1921px */

// Defining media queries
$screen:       "only screen" !default;
$landscape:    "#{$screen} and (orientation: landscape)" !default;
$portrait:     "#{$screen} and (orientation: portrait)" !default;
$small-up:     $screen !default;
$small-only:   "#{$screen} and (max-width: #{upper-bound($small-range)})" !default;
$medium-up:    "#{$screen} and (min-width:#{lower-bound($medium-range)})" !default;
$medium-only:  "#{$screen} and (min-width:#{lower-bound($medium-range)}) and (max-width:#{upper-bound($medium-range)})" !default;
$large-up:     "#{$screen} and (min-width:#{lower-bound($large-range)})" !default;
$large-only:   "#{$screen} and (min-width:#{lower-bound($large-range)}) and (max-width:#{upper-bound($large-range)})" !default;
$xlarge-up:    "#{$screen} and (min-width:#{lower-bound($xlarge-range)})" !default;
$xlarge-only:  "#{$screen} and (min-width:#{lower-bound($xlarge-range)}) and (max-width:#{upper-bound($xlarge-range)})" !default;
$xxlarge-up:   "#{$screen} and (min-width:#{lower-bound($xxlarge-range)})" !default;
$xxlarge-only: "#{$screen} and (min-width:#{lower-bound($xxlarge-range)}) and (max-width:#{upper-bound($xxlarge-range)})" !default;

// Usage
@media #{$small-up}     { ... }
@media #{$small-only}   { ... }
@media #{$medium-up}    { ... }
@media #{$medium-only}  { ... }
@media #{$large-up}     { ... }
@media #{$large-only}   { ... }
@media #{$xlarge-up}    { ... }
@media #{$xlarge-only}  { ... }
@media #{$xxlarge-up}   { ... }
@media #{$xxlarge-only} { ... }
```

每种方法都有一点我不喜欢:在 Bootstrap 中，我每次都必须键入`(min-width: ...)`。在 Foundation 中，我需要插入一个既难看又烦人的变量。当然，我们可以想出一个办法来弥补这两个缺陷。

## 带独立混音

我相信 CSS-Tricks 最受欢迎的帖子之一是关于 Sass 3.2 中的[媒体查询的。在这篇文章中，Chris Coyier 解释了他如何使用梅森·温德尔的](http://css-tricks.com/media-queries-sass-3-2-and-codekit/)[和杰夫·克罗夫特的](http://thesassway.com/intermediate/responsive-web-design-in-sass-using-media-queries-in-sass-32)[来命名他的 Sass 响应断点。](http://jeffcroft.com/blog/2012/mar/02/implementing-responsive-design/)

命名断点很重要，因为它赋予抽象值以意义。你总是知道`767px`指的是什么吗？我不知道。我宁愿知道我们在和小屏幕 T2 打交道。这是 Bootstrap 和 Foundation 在变量中存储媒体查询时开始做的事情；你知道，变量是有名字的。

因此，我们可以创建一个 mixin，它接受一个名称为 T1(基本上是一个字符串)的唯一参数，发出一个媒体查询。对吗？

```
@mixin respond-to($breakpoint) {
  @if $breakpoint == "small" {
    @media (min-width: 767px) {
      @content;
    }
  }

  @else if $breakpoint == "medium" {
    @media (min-width: 992px) {
      @content;
    }
  }

  @else if $breakpoint == "large" {
    @media (min-width: 1200px) {
      @content;
    }
  }
}
```

然后，我们可以这样使用它:

```
@include respond-to(small) { ... }
@include respond-to(medium) { ... }
@include respond-to(large) { ... }
```

这实际上很好，有两个原因:不仅它本身有意义，而且它还将所有断点集中在一个地方:mixin 核心中。如果您必须将这个`992px`断点改为`970px`，您不必遍历所有的样式表；你所要做的就是更新 mixin，然后一切都会变得很好。

然而，这个 mixin 仍然有两件事不太对劲:

1.  断点不容易从 mixin 拖到配置文件中
2.  真是太多余了！

## 带有可配置混音

为了解决我们的两个新缺陷，我们需要从我们的断点做一些列表。然后，这个列表可以到处移动，放在配置文件中，留在 mixin 核心中，等等。

将名称映射到值的方法将会非常酷。使用 Sass 3.3+时，有一个非常简单的方法: [maps](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps) 。Sass 3.3 中引入的映射正是这样做的:它们将键和值关联起来。

```
$breakpoints: (
  'small'  : 767px,
  'medium' : 992px,
  'large'  : 1200px
);
```

整洁！现在，我们只需要调整我们以前的 mixin 来从 map 中检索值，而不是硬编码所有内容:

```
@mixin respond-to($breakpoint) {
  // Retrieves the value from the key
  $value: map-get($breakpoints, $breakpoint);

  // If the key exists in the map
  @if $value != null {
    // Prints a media query based on the value
    @media (min-width: $value) {
      @content;
    }
  }

  // If the key doesn't exist in the map
  @else {
    @warn "Unfortunately, no value could be retrieved from `#{$breakpoint}`. "
        + "Please make sure it is defined in `$breakpoints` map.";
  }
}
```

还要注意这个很酷的小改进:如果在断点图中找不到`$breakpoint`，由于有了 [`@warn`](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_6) 指令，用户会得到警告。这有助于在出现问题时进行调试。

现在，我们的 mixin 不仅非常干燥，而且还能很好地处理错误。与此同时，我们从系统中删除了一个特性:检查我们想要的属性的能力(`min-width`、`max-width`、`max-height`……)。也就是说，如果你使用[移动优先的方法](http://www.lukew.com/ff/entry.asp?933)，这个版本应该非常适合你，因为除了`min-width`媒体查询，你不需要其他任何东西。

但是如果您想要控制要转储的媒体查询的类型，您可能想要将该特性添加回我们的系统。为此，我最近提出了一个相当优雅的解决方案，它没有增加任何代码复杂性。实际上，它依赖于这样一个事实，即 Sass 映射使用与 CSS 媒体查询相同的语法(例如`(property: value)`)。

> [地图]在 CSS 中没有直接的相似之处，尽管它们在语法上类似于媒体查询表达式
> — [Sass reference](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps)

```
$breakpoints: (
  'small'  : ( min-width:  767px ),
  'medium' : ( min-width:  992px ),
  'large'  : ( min-width: 1200px )
);

@mixin respond-to($name) {
  // If the key exists in the map
  @if map-has-key($breakpoints, $name) {
    // Prints a media query based on the value
    @media #{inspect(map-get($breakpoints, $name))} {
      @content;
    }
  }

  // If the key doesn't exist in the map
  @else {
    @warn "Unfortunately, no value could be retrieved from `#{$breakpoint}`. "
        + "Please make sure it is defined in `$breakpoints` map.";
  }
}
```

如你所见，这不再是我们的 mixin 了。事实上，我们甚至设法重构它，使它更轻！在这些变化中，我们需要一种打印映射的方法，因为您可能已经注意到来自断点映射**的值是映射**以及有效的 CSS 媒体查询。不幸的是，如果您尝试打印 Sass 地图，您可能会遇到以下错误:

> (最小宽度:767 像素)不是有效的 CSS 值。

这就是 [`inspect`](http://sass-lang.com/documentation/Sass/Script/Functions.html#inspect-instance_method) 函数派上用场的地方。根据 Sass 引用，它*返回一个包含值的字符串作为它的 Sass 表示*。换句话说，这是打印 Sass 映射而不使编译器崩溃的唯一方法。

总结一下我们 mixin 的工作:

1.  它检查断点映射中是否存在请求的断点
2.  如果存在，它会根据所请求断点的值打印一个媒体查询
3.  如果没有，它会警告用户检查他们的代码

简单！如果我们回头看看我们以前的解决方案的缺陷，我们注意到没有更多的问题与湿(*写两遍*)代码，或不灵活的媒体查询。然而，有一件事这个系统不允许:复杂的媒体查询。所谓*复杂*，我指的是涉及几个组件的媒体查询(例如`screen and (min-width: 767px)`)。

因为它依赖于简单的 CSS 媒体查询是有效的 Sass 映射这一事实，所以很难声明更复杂的媒体查询。也就是说，我不仅发现我们不太可能需要使用这样的查询，而且我们以前的解决方案(除了来自 Bootstrap 和 Foundation 的纯变量解决方案)也阻止了我们这样做。

## 使用外部工具

最后但同样重要的是，如果您出于某种原因不想构建自己的 mixin，您可以使用现有的工具来处理媒体查询断点。有几个有趣的 Sass 扩展可以很好地完成这项工作:

*   Kaelig 的 SassMQ
*   梅森·温德尔和萨姆·理查德的《断点》
*   本·斯科特的《分手》

这三个都是顶级的 Sass 工具，所以可以随意选择其中任何一个。如果你觉得很难做出选择，这里有一个小小的比较板:

|   | [sasmq](https://github.com/guardian/sass-mq) | [断点](http://breakpoint-sass.com/) | [分手](https://github.com/BPScott/breakup) |
| --- | --- | --- | --- |
| MQ 类型 | `*-width` | 任何的 | 任何的 |
| 没有查询回退 | 是 | 是 | 是 |
| API 复杂性 | 简单的 | 非常简单 | 媒介 |
| 代码复杂性 | 非常简单 | 复杂的 | 简单的 |
| 额外的 | [调试模式](https://github.com/guardian/sass-mq#seeing-the-currently-active-breakpoint) | [奇点. gs](http://singularity.gs/) | — |

如果我错过了什么，或者如果有需要补充的信息，一定要分享。

### SassMQ

```
// Configuration
$mq-responsive: true;
$mq-static-breakpoint: desktop;
$mq-breakpoints: (
  mobile:  320px,
  tablet:  740px,
  desktop: 980px,
  wide:    1300px
);

// Example
selector {
  @include mq($from: mobile) {
    property: value;
  }
}
```

### 断点

```
$high-tide: 500px;
$ex-presidents: 600px 800px;
$surfboard-width: max-width 1000px;
$surfboard-height: (min-height 1000px) (orientation portrait);

selector {
  @include breakpoint($high-tide) {
    property: value;
  }
}
```

### 崩溃

```
$breakup-breakpoints: (
  'thin' '(max-width: 35.999em)',
  'wide' '(min-width: 36em)',
  'full' '(min-width: 61em)'
);

selector {
  @include breakup-block('thin') {
    property: value;
  }
}
```

## 最后的想法

正如我们在本文中看到的，有很多方法可以处理媒体查询断点。它们各有利弊，因为没有完美的系统。最后，我觉得应该由您来决定如何在可用特性和代码复杂性之间取得平衡。

写一段代码来涵盖每一种可能的边缘情况，为你煮咖啡，洗衣服，这总是很诱人的，但是总有一个风险，那就是除了你之外，这个工具最终不能被任何人使用。在团队中工作时，简单很重要。

无论如何，一定要为正确的工作选择正确的工具。；)

## 分享这篇文章