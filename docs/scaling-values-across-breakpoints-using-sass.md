# 使用 Sass 跨断点缩放值

> 原文：<https://www.sitepoint.com/scaling-values-across-breakpoints-using-sass/>

如果你以前来过这里，请打断我:你得到了一个网站的桌面版，然后在你建立它之后，你得到了一个“移动视图”的口头反馈(或者另一个版本，如果你幸运的话)。然后，您需要使站点具有响应性(包括两个中间的断点)，但是您害怕手动计算 CSS 属性的所有值，这些值从小屏幕到大屏幕变化很大(字体大小、边距、宽度等)。如果有一种方法可以跨所有断点自动调整这些值，并生成您需要的媒体查询，那会怎么样？

这就是我们今天要打造的工具。

## 管理断点

我们将从一个 [Sass 映射](https://www.sitepoint.com/using-sass-maps/)开始存储我们的断点。*注意:这是为移动优先(最小宽度)的媒体查询设计的，所以键值对应该从小到大排序。*

```
$breakpoints: (
 small: 320px,
 medium: 480px,
 large: 960px,
 xlarge: 1200px
);
```

我不会详细解释`bp()` mixin 是如何工作的，但是你可以在这里查看代码(阅读这篇文章可以获得关于[断点 mixin](http://jamessteinbach.com/css/sass-maps-breakpoint-mixin/) 的更详细的解释)。

```
@mixin bp($name) {
 @if not map-has-key($breakpoints, $name) {
 @warn "Invalid breakpoint `#{$name}`.";
    } @else {
 @if map-get($breakpoints, $name) {
 @media (min-width: map-get($breakpoints, $name)) {
 @content;
            }
        } @else {
 @content;
        }
    }
}
```

## 助手功能

我从主 mixin 中取出了一部分代码，这样它们就可以作为可重用的函数。它们相当简单，所以我们很快地看一下。

## 获取断点值

第一个功能只是 [`map-get()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_get-instance_method) 的简写。它接受一个`$breakpoints`键并返回它的测量值。

```
@function get_bp($label) {
 @return map-get($breakpoints, $label);
}
```

## 舍入数字

我们还可以简单地将数字四舍五入到两位小数，这里没有什么特别的:

```
@function round-num($num) {
 @return round($num*100)/100;
}
```

## 缩放值

现在我们需要一个 mixin 来完成在我们指定的断点上传播值变化的艰苦工作。这个 mixin 接受五个参数(其中两个是可选的)。`$property`是被更改的 CSS 属性的名称。`$value-start`和`$value-end`是属性的开始和结束值。默认情况下，`$bp-start`和`$bp-end`被设置为小断点和大断点，但是您可以使用`$breakpoints`中任何其他键的名称来覆盖这些默认值。

```
@mixin spread-value($property, $value-start, $value-end, $bp-start: small, $bp-end: xlarge) {

 @if type-of($value-start) != number or type-of($value-end) != number {
 @warn "Either $value-start or $value-end is not a number: `#{$value-start}` | `#{$value-end}`"
  } @else {
 #{$property}: #{$value-start};
 $value-distance: $value-end - $value-start;
 $bp-distance: get_bp($bp-end) - get_bp($bp-start);
 $bp-keys: map-keys($breakpoints);
 $bp-list: ();

 $i: index($bp-keys, $bp-start);
 @while $i <= length($bp-keys) and nth($bp-keys, $i) != $bp-end {
 $i: $i + 1;
 $bp-list: join($bp-list, nth($bp-keys, $i));
    }

 @each $key in $bp-list {
 $percentage: ( get-bp($key) - get_bp($bp-start) ) / $bp-distance;
 @include bp($key) {
 #{$property}: round-num( ( $value-distance * $percentage ) + $value-start );
      }
    }
  }
}
```

## 验证值

我们让 mixin 做的第一件事是确保`$value-start`和`$value-end`是数字。数字测量是我们唯一可以缩放的东西(没有蔓延的字体或背景图片！).我们通过确保它们的[类型](http://sass-lang.com/documentation/Sass/Script/Functions.html#type_of-instance_method)是一个数字来验证这两个值参数。这个 Sass 函数会将任何度量单位计算为数字，因此 px、em、%如果`$value-start`或`$value-end`不是一个数字，我们将向编译器抛出一个警告。

```
@if type-of($value-start) != number or type-of($value-end) != number {
 @warn "Either $value-start or $value-end is not a number: `#{$value-start}` | `#{$value-end}`"
  } @else {
  // …
  }
```

## 设置默认值

接下来要做的是设置默认值。对于移动优先的媒体查询，这是`$value-start`。不管发生什么情况，我们都将属性-值对打印到样式表中:

```
#{$property}: #{$value-start};
```

## 开始使用数学

现在我们准备开始计算我们需要的距离，以准确地传播我们的值。我们将把`$value-distance`设置为`$value-start`和`$value-end`之间的距离，将`$bp-distance`设置为`$bp-start`和`$bp-end`的值之间的距离。

```
$value-distance: $value-end - $value-start;
 $bp-distance: get_bp($bp-end) - get_bp($bp-start);
```

接下来，我们将使用 [`map-keys()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_keys-instance_method) 来创建所有断点名称的列表。这将更容易计算出我们需要计算多少个断点的值。我们还将初始化一个空列表`$bp-list`，我们将使用它来存储所需断点的键。

```
$bp-keys: map-keys($breakpoints);
 $bp-list: ();
```

下面的 [`@while`](http://thesassway.com/intermediate/if-for-each-while#while) 循环从断点列表中`$bp-start`的位置`$i`开始。我们将在两种情况下停止循环:如果`$i`超过了`$breakpoints`图的长度，或者如果`$i`是指定为`$bp-end`的断点的索引。当这个循环结束运行时，`$bp-list`将是我们需要计算值的所有断点的列表。

```
$i: index($bp-keys, $bp-start);
 @while $i <= length($bp-keys) and nth($bp-keys, $i) != $bp-end {
 $i: $i + 1;
 $bp-list: join($bp-list, nth($bp-keys, $i));
    }
```

我们现在需要 mixin 遍历列表，生成所需的媒体查询和值。`$percentage`变量计算一个百分比，标记当前断点在`$bp-start`和`$bp-end`之间的距离。然后`@include bp($key) {}`为当前断点创建媒体查询。

在断点内部，我们用调整后的值声明属性。为了得到缩放后的值，我们用断点距离的`$percentage`乘以`$value-distance`，然后加上起始值。这给了我们与当前断点的宽度成比例的值变化。你会注意到我们将输出包装在我们的`round-num()`函数中，以保持事情的完整性。

```
@each $key in $bp-list {
 $percentage: ( get-bp($key) - get_bp($bp-start) ) / $bp-distance;
 @include bp($key) {
 #{$property}: round-num( ( $value-distance * $percentage ) + $value-start );
      }
    }
```

## 使用

要使用此 mixin，请将其包含在任何选择器中:

```
p {
 @include spread-value(margin-bottom, .6em, 1.6em);
}
```

使用地图中的默认断点，它将生成以下 CSS:

```
p {
  margin-bottom: 0.6em;
}
@media (min-width: 480px) {
  p {
    margin-bottom: 0.78em;
  }
}
@media (min-width: 960px) {
  p {
    margin-bottom: 1.33em;
  }
}
@media (min-width: 1200px) {
  p {
    margin-bottom: 1.6em;
  }
}
```

您还可以指定在哪个断点开始或结束缩放:

```
h1 {
 @include spread-value(font-size, 1.6em, 2.6em, medium, xlarge);
}
```

这将生成以下 CSS:

```
h1 {
  font-size: 1.6em;
}
@media (min-width: 960px) {
  h1 {
    font-size: 2.27em;
  }
}
@media (min-width: 1200px) {
  h1 {
    font-size: 2.6em;
  }
}
```

## 结论

现在我们有了一个非常有用的混合，可以在所有断点上成比例地增加或减少值。这可以非常方便地缩短开发时间。它还为其他坐在浏览器中调整页面大小的开发人员和管理人员提供了更加流畅的体验！它也是完全可定制的，因为它只依赖于你选择的断点。

如果你对此 mixin 有一些很棒的用例，请在评论中分享！

## 分享这篇文章